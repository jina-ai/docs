# Jina Hub Builder

Jina’s hub-builder is a simple interface for building & validating Jina Hub executors. It is built on top of jina hub interface. It can be used as a Github action in the CICD workflow, or via CLI.

## Github action usage:
One can use the `hub builder` as a part of the CI pipeline, to build and test images in the Pull Request. The following YAML could be directly used in the workflow `.github/workflows/hub-builder.yml`.
```
name: Hub Builder

on: [pull_request]

jobs:
  hub-builder:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout
        uses: actions/checkout@v2
      - name: Jina Hub Image Builder
        uses: jina-ai/hub-builder@master
```
On every new PR, the builder finds modifications in `manifest.yml` recursively and tries to build a Hub image from it, one by one. That means, when you update an image, you must change manifest.yml to trigger the build, e.g. you can simply bump version field in `manifest.yml`.

### Hub builder action accepts following parameters:
1. `push` (boolean): if push to Docker Hub and MongoDB
2. `dockerhub_username` and `dockerhub_password`: user-name and password of docker registry
3. `dockerhub_registry`: URL to the registry
4. `slack_webhook`: webhook for Slack notification
5. `jina_version`: version of Jina for building hub image
 
Note: the input argument jina_version is different from the version of jina for running the pod in the container. The later should be set in your`_jina_pod/requirements.txt`. The jina_version is the version of jina when building the pod image.

### Output of the Action
There are two outputs you can use in the post-action:
1. `success_targets`: Successfully built targets with their paths
2. `failed_targets`: Failed built targets with their paths
 
