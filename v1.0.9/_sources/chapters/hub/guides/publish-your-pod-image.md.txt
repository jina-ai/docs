
# Publish Your Pod Image to Jina Hub

You can publish your custom Executor Pod to Jina Hub by contributing your Pod files to the Jina repository. You can raise a PR with all the relevant Pod files bundled as described below. Jina's CI/CD pipeline will automatically use the PR to build an image and publish it to Jina Hub.

## Required files

| File              | Descriptions                                                                                        |
|-------------------|-----------------------------------------------------------------------------------------------------|
| `Dockerfile`      | describes the dependency setup and exposes the entry point                                          |
| `manifest.yml`    | metadata info like image, author, tags, etc. helps the Hub to index and classify the image          |
| `README.md`       | an instruction guide describing the image usage                                                     | 
| `*.py`            | describes the executor logic written in Python, if applicable                                       |
| `*.yml`           | a YAML file describes the executor arguments and configs, if you want users to use your config      |
| `requirements.txt`| describes installation packages necessary for the executor pod                                      |
| `tests/`          | directory with unit tests for the executor                                                          |

Note, large binary files (such as pretrained models, auxiliary data) are **not** recommended to upload to this repository. You can use `RUN wget ...` or `RUN curl` inside the `Dockerfile` to download it from the web during the build.

## File structure and schema for Pods:

Your custom `Pod` file bundle needs to follow a structure and certain schema rules for annotating the image successfully.
For instance, your pod is a kind of `encoder` named `AwesomeEncoder`. The file bundle should be uploaded to the Hub `executors` under kind `encoders`
with the following structure:
```text
hub/
  |
   - executors/
       |
        - encoders/
            |
            - AwesomeEncoder/
                |
                |- tests/
                     |
                     |- __init__.py
                     |- test_awesome_encoder.py
                |- __init__.py
                |- Dockerfile
                |- config.yml
                |- manifest.yml
                |- README.md
                |- requirements.txt
```

Your image will be published as `jinahub/pod.encoder.awesomeencoder`.

## Schema of `manifest.yml`

`manifest.yml` annotates your image so that it can be managed by Jina Hub. To ensure better compatibility with Jina Hub, you should carefully set `manifest.yml` to use correct values.

| Key | Description | Default |
| --- | --- | --- |
| `name` | Human-readable title of the image (must match with `^[a-zA-Z_$][a-zA-Z_\s\-$0-9]{3,20}$`) | Required |
| `kind` | the kind of executor, i.e. `indexer`, `encoder`, `crafter`, etc. | Required |
| `type` | type, i.e. `pod`, `app` | Required |
| `description` | Human-readable description of the software packaged in the image | Required |
| `author` | contact details of the people or organization responsible for the image (string) | `Jina AI Dev-Team (dev-team@jina.ai)` |
| `url` | URL to find more information on the image (string) | `https://jina.ai` |
| `documentation` | URL to get documentation on the image (string) | `https://docs.jina.ai` |
| `version` | version of the image, it should be [Semantic versioning-compatible](http://semver.org/) | `0.0.0` |
| `vendor` | the name of the distributing entity, organization or individual (string) | `Jina AI Limited` |
| `license` | license under which contained software is distributed, it should be [in this list](https://github.com/jina-ai/jina/blob/master/jina/resources/hub-builder/osi-approved.yml) | `apache-2.0` |
| `keywords` | a list of keywords describing the pod image | None |

Please refer to [VSETextEncoder/manifest.yml](https://github.com/jina-ai/jina-hub/blob/master/encoders/nlp/VSETextEncoder/manifest.yml) for the example.

## Updating schema of Pods

If you have made code changes to a Pod, you must update the appropriate fields like `version` inside `manifest.yml` for updating the schema. With every change,
the version will increment following `semantic versioning`. For instance, `0.0.9` becomes `0.0.10` on an update

## Publish your image

All you need to do is to publish your file bundle into the Jina Hub repo. Subsequently, the Docker image building, uploading and tagging are all handled automatically by our CICD pipeline. 

1. Let's say you organize [all files mentioned in here](#what-files-need-to-be-uploaded) in a folder called `AwesomeEncoder`. Depending on what you are contributing, you can put it into `hub/executors/encoder/AwesomeEncoder`.
2. Make a Pull Request and commit your changes into this repository, remember to follow the commit lint style.
3. Wait until the CICD finish and you get at least one reviewer's approval.
4. Merge it! 

The CICD pipeline will work on building, uploading and tagging the image on the Jina Hub.

The image will be available at `jinahub/pod.encoder.awesomeencoder:0.0.0` assuming your version number is defined as `0.0.0` in `manifest.yml`.

You can use the image with [the ways described here](./use-your-pod.md).  


## Why does my upload fail on CI/CD?

Common causes why your upload could fail:

- [ ] Required file `Dockerfile`, `manifest.yml`, `README.md` is missing. 
- [ ] The required field in `manifest.yml` is missing.
- [ ] Some field value is not in the correct format, not passing the sanity check.
- [ ] The pod bundle is badly placed.
- [ ] The build is success but it fails on [three basic usage tests](./use-your-pod.md).

Click "Details" and check out the log of the CICD pipeline:

![](img/5f4181e9.png)
