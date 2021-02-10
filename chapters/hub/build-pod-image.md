# Build Your Pod into a Docker Image

## Goal
The objective of this tutorial is to serve as a guide for Pod image usage.
Users can use Pod images in several ways:

- Run with Docker (`docker run`)
  - ```bash
    docker run jinaai/hub.examples.mwu_encoder --port-in 55555 --port-out 55556
    ```
    
- Flow API
  - ```python
    from jina.flow import Flow

    f = (Flow()
        .add(name='my-encoder', image='jinaai/hub.examples.mwu_encoder', port_in=55555, port_out=55556)
        .add(name='my-indexer', uses='indexer.yml'))
    ```
    
- Jina CLI
  - ```bash
    jina pod --uses jinaai/hub.examples.mwu_encoder --port-in 55555 --port-out 55556
    ```
    
- Conventional local usage with `uses` argument
  - ```bash
    jina pod --uses hub/example/mwu_encoder.yml --port-in 55555 --port-out 55556
    ```
    
More information about [the usage can be found here](./use-your-pod.html#use-your-pod-image).


## Why?

So you have implemented an Executor and you would want to reuse it in another Jina application or share it with people around the world. 

You might also want to offer people a ready-to-use interface without the hassle of repeating the pitfalls you faced.

The best way to do this is to pack everything (Python file, `YAML` config, `pre-trained` data, dependencies) into a container image and use Jina as the entry point. You can also annotate your image with some meta information to facilitate the search, archive and classification.

Here is a list of motivating reasons for building a Pod image:

- You want to use one of the built-in Executor (e.g. PyTorch-based) and you don't want to install PyTorch dependencies on the host.
- You modify or write a new Executor and want to reuse it in another project, without touching [Jina's core](https://github.com/jina-ai/jina/).
- You customize the driver and want to reuse it in another project, without touching [Jina's core](https://github.com/jina-ai/jina/).
- You have a self-built library optimized for your architecture (e.g. tensorflow/numpy on GPU/CPU/x64/arm64), and you want this specific Pod to benefit from it.
- Your Executor requires certain Linux headers that can only be installed via `apt` or `yum`, but you don't have `sudo` on the host.
- Your Executor relies on a pretrained model, you want to include this 100MB file into the image so that people don't need to download it again.  
- You use Kubernetes or Docker Swarm and this orchestration framework requires each microservice to run as a Docker container.
- You are using Jina on the cloud and you want to deploy an immutable Pod and version control it.
- You have figured out a set of parameters that works best for an Executor, you want to write it down in a YAML config and share it to others.
- You are debugging, doing try-and-error on exploring new packages, and you don't want ruin your local dev environments. 


## What files should be in the Pod image?

Typically, the following files are packed into the container image.

| File             | Descriptions                                                                                        |
|------------------|-----------------------------------------------------------------------------------------------------|
| `Dockerfile`     | describes the dependency setup and expose the entry point;                                          |
| `build.args`     | metadata of the image, author, tags, etc. help the Hub to index and classify your image             |
| `*.py`           | describes the Executor logic written in Python, if applicable;                                      |
| `*.yml`          | a YAML file describes the Executor arguments and configs, if you want users to use your config;     |
| Other data files | may be required to run the Executor, e.g. pre-trained model, fine-tuned model, home-made data.      |

Except `Dockerfile`, all other options to build a valid Pod image depending on your case. `build.args` is only required when you want to [upload your image to Jina Hub](./publish-your-pod-image.html#publish-your-pod-image-to-jina-hub).

## How to change the default drivers of the Executor that is running inside the Docker image?

Jina allows `uses_internal` as an argument while initialising Flow for this purpose.

  - ```python
    from jina.flow import Flow

    f = (Flow()
        .add(name='my-encoder', image='jinaai/hub.examples.mwu_encoder', port_in=55555, port_out=55556)
        .add(name='my-indexer', uses='{{image_name}}', uses_internal='indexer.yml'))
    ```
    
## Step-by-Step Example

In this example, we consider the scenario where we create a new Executor and want to reuse it in another project, without tweaking any code in [`jina-ai/jina`](https://github.com/jina-ai/jina/).

Note: All files mentioned in this guide are available at [`hub/examples/mwu_encoder`](/hub/examples/mwu_encoder).

### 1. Code your Executor and write its config

We write a new dummy encoder named `MWUEncoder` in [`mwu_encoder.py`](hub/examples/mwu_encoder/mwu_encoder.py) which encodes any input into a random 3-dimensional vector. This encoder has a dummy parameter `greetings` which prints a greeting message on start and on every encode. In [`mwu_encoder.yml`](hub/examples/mwu_encoder/mwu_encoder.yml), the `metas.py_modules` helps Jina to load the class of this Executor from `mwu_encoder.py`.

```yaml
!MWUEncoder
with:
  greetings: im from internal yaml!
metas:
  name: my-mwu-encoder
  py_modules: mwu_encoder.py
  workspace: ./
```

The documentation of the YAML syntax [can be found at here](../yaml/yaml.html#executor-yaml-syntax). 

### 2. Write a 3-line `Dockerfile`

The `Dockerfile` in this example is a simple three-line snippet, 

```Dockerfile
FROM jinaai/jina

ADD *.py mwu_encoder.yml ./

ENTRYPOINT ["jina", "pod", "--uses", "mwu_encoder.yml"]
```

Let's try to understand them one by one.

>
```Dockerfile
FROM jinaai/jina
``` 

In the first line, we choose `jinaai/jina` as [the base image](https://docs.docker.com/glossary/#base-image), which corresponds to the latest master of [`jina-ai/jina`](https://github.com/jina-ai/jina). You are free to use others as well, e.g. `tensorflow/tensorflow:nightly-gpu-jupyter`. 

In practice, whether to use Jina base image depends on the dependencies you would like to introduce. For example, someone provides a hard-to-compile package as a Docker image, which is much harder than compiling/installing Jina itself. In this case, you may want to use this image as the base image to save some troubles. Nevertheless, don't forget to install Python >=3.7 (if not included) and Jina afterwards, e.g.

> 
```Dockerfile
FROM awesome-gpu-optimized-kit

RUN pip install jina --no-cache-dir --compile
```

The ways of [installing Jina can be found here](https://github.com/jina-ai/jina#run-without-docker).

In this example, our dummy `MWUEncoder` only requires Jina and does not need any third-party framework. Thus, `jinaai/jina` is used.

```Dockerfile
ADD *.py mwu_encoder.yml ./
```

The second step is to add *all* necessary files to the image. Typically, Python codes, YAML config and some data files.

In this example, our dummy `MWUEncoder` does not require any extra data files.

> 
```Dockerfile
ENTRYPOINT ["jina", "pod", "--uses", "mwu_encoder.yml"]
``` 

The last step is to specify the entrypoint of this image, which is usually via `jina pod`.

In this example, we set `mwu_encoder.yml` as a default YAML config. So, if the user later runs

```bash
docker run jinaai/hub.examples.mwu_encoder
```
 
This is equivalent to running:
```bash
jina pod --uses hub/example/mwu_encoder.yml
```

Any key-value arguments followed after `docker run jinaai/hub.examples.mwu_encoder` will be passed to `jina pod`. For example,

```bash
docker run jinaai/hub.examples.mwu_encoder --port-in 55555 --port-out 55556
```
 
This is the same as running:
```bash
jina pod --uses hub/example/mwu_encoder.yml --port-in 55555 --port-out 55556
```

One can also override the internal YAML config by specifying an out-of-Docker external `YAML` config via:

```bash
docker run -v $(pwd)/hub/example/mwu_encoder_ext.yml:/ext.yml jinaai/hub.examples.mwu_encoder --uses /ext.yml
```


### 3. Build the Pod image

You can build the Pod image now via `docker build`:

```bash
cd hub/example
docker build -t jinaai/hub.examples.mwu_encoder .
```

Depending on whether you want to use the latest Jina image, you may first pull it via `docker pull jinaai/jina` before the build. For the sake of immutability, `docker build` will not automatically pull the latest image for you.

Congratulations! You can now re-use this Pod image however and wherever you want.
