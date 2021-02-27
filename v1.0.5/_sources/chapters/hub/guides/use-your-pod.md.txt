# Use Your Pod Image

## Use the Pod image via Docker CLI

The most powerful way to use this Pod image is via Docker CLI directly:

```bash
docker run --rm -p 55555:55555 -p 55556:55556 jinaai/hub.examples.mwu_encoder --port-in 55555 --port-out 55556
```

Note, the exposure of ports `-p 55555:55555 -p 55556:55556` is required for other Pods (local/remote) to communicate this Pod. One may also want to use `--network host` and let the Pod share the network layer of the host.
 
All parameters supported by `jina pod --help` can be followed after `docker run jinaai/hub.examples.mwu_encoder`.

One can mount a host path to the container via `--volumes` or `-v`. For example, to override the internal YAML config, one can do

```bash
# assuming $pwd is the root dir of this repo 
docker run --rm -v $(pwd)/hub/example/mwu_encoder_ext.yml:/ext.yml jinaai/hub.examples.mwu_encoder
```

```text
MWUEncoder@ 1[S]:look at me! im from an external yaml!
MWUEncoder@ 1[S]:initialize MWUEncoder from a yaml config
 BasePea-0@ 1[I]:setting up sockets...
 BasePea-0@ 1[I]:input tcp://0.0.0.0:36109 (PULL_BIND) 	 output tcp://0.0.0.0:58191 (PUSH_BIND)	 control over tcp://0.0.0.0:52365 (PAIR_BIND)
 BasePea-0@ 1[S]:ready and listening
```

To override the predefined entrypoint via `--entrypoint`, e.g.

```bash
docker run --rm --entrypoint "jina" jinaai/hub.examples.mwu_encoder check
```

## Use the Pod image via Jina CLI

Another way to use the Pod image is simply give it to `jina pod` via `--uses`,
```bash
jina pod --uses docker://jinaai/hub.examples.mwu_encoder
```

```text
🐳 MWUEncoder@ 1[S]:look at me! im from internal yaml!
🐳 MWUEncoder@ 1[S]:initialize MWUEncoder from a yaml config
🐳 BasePea-0@ 1[I]:setting up sockets...
🐳 BasePea-0@ 1[I]:input tcp://0.0.0.0:59608 (PULL_BIND) 	 output tcp://0.0.0.0:59609 (PUSH_BIND)	 control over tcp://0.0.0.0:59610 (PAIR_BIND)
ContainerP@69041[S]:ready and listening
🐳 BasePea-0@ 1[S]:ready and listening
```

Note the 🐳 represents that the log is piping from a Docker container.

See `jina pod --help` for more usage.

## Use the Pod image via Flow API

Finally, one can use it via Flow API as well, e.g.

```python
from jina.flow import Flow

f = (Flow()
        .add(name='my-encoder', uses='docker://jinaai/hub.examples.mwu_encoder',
             volumes='./abc', 
             port_in=55555, port_out=55556)
        .add(name='my-indexer', uses='indexer.yml'))
```

## How to change the default drivers of the Executor that is running inside the Docker image?

Jina allows `uses_internal` as an argument while initialising Flow for this purpose. Pod images are docker images that normally have an 
`entrypoint` that looks like this:

`ENTRYPOINT ["jina", "pod", "--uses", "config.yml"]`

This indicates that when the `pod` image is started, inside the docker container, a `Pod` is started with the preset `config.yml` containing
the `yaml` instance of the specific `executor`. By using `uses_internal`, jina overrides the `entrypoint` forcing the `Pod` inside the `docker` container
to use the `yaml` configuration in `uses_internal`. This allows to use the same image while changing parameters of the `Executor` or using different `Drivers 
than the default ones.

For instance, the `config.yml` for the 'jinaai/hub.examples.mwu_encoder' may look like this:

```yaml
!MWUEncoder
with:
  {}
metas:
  py_modules: 
    - __init__.py
```

Imagine we want to have this `Executor` do encoding for [`chunks` of `chunks` of the `root` documents](https://docs.jina.ai/chapters/traversal.html). Then we may want to 
change the default driver's settings and build a 'custom_mwu_encoder.yml' to be passed in `uses_internal` argument.

```yaml
!MWUEncoder
with:
  {}
metas:
  py_modules: 
    - __init__.py
requests:
  on:
    [SearchRequest, IndexRequest]:
      - !EncodeDriver
        with:
          traversal_paths: ['cc']
```

Then, the Flow instantiation would look like:

```python
from jina.flow import Flow

f = (Flow()
        .add(name='my-encoder', uses='docker://jinaai/hub.examples.mwu_encoder',
             volumes='./abc',  uses_internal='custom_mwu_encoder.yml',
             port_in=55555, port_out=55556)
        .add(name='my-indexer', uses='indexer.yml'))
```