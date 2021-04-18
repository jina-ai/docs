# Jina Hub Builder

Jina’s hub-builder is a simple interface for building & validating Jina Hub Executors. It is built on top of Jina Hub interface. It can be used as a GitHub action in the CI/CD workflow, or via CLI.

## Github action usage:
You can use the `hub builder` as a part of the CICD pipeline, to build and test images in the Pull Request. The following YAML can be directly used in the workflow `.github/workflows/hub-builder.yml`.
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
        jina_hub_token: ${{ secrets.JINA_HUB_TOKEN }}
```
On every new PR, the builder finds modifications in `manifest.yml` recursively and tries to build a Hub image using the latest files. That means, when you update an image, **you must change `manifest.yml` to trigger the build**, i.e. you can simply bump the version field in `manifest.yml`. This is done one by one for each modified Executor.

### Hub builder action accepts the following parameters:

| Parameter | Type | Required | Description |
| -- | -- | -- | -- |
| push | boolean | yes | true if you want to upload to Docker Hub & make it available for everyone's use (usually in CD) <br /> false during tests run (usually in CI) |
| jina_hub_token | string | yes | [Personal Access Token](https://docs.github.com/en/github/authenticating-to-github/creating-a-personal-access-token) for your GitHub user |
| dockerhub_username | string | no | Username for Docker Hub <br /> (Ignore if you want us to host your pod in Jina's repository) |
| dockerhub_password | string | no | Password for Docker Hub <br /> (Ignore if you want us to host your pod in Jina's repository) |
| dockerhub_registry | string | no | Docker Hub Registry <br /> (Ignore if you want us to host your pod in Jina's repository) |
| slack_webhook | string | no | Slack webhook if you want custom notifications |
| jina_version | string | no | version of Jina for building Hub image |

> For authorization, you'd need to create a [Personal Access Token](https://docs.github.com/en/github/authenticating-to-github/creating-a-personal-access-token) in your github settings & pass the same. e.g., `jina_hub_token: ${{ secrets.JINA_HUB_TOKEN }}`

> The input argument `jina_version` can be different from the version of Jina for running the Pod in the container. The latter should be set in your `_jina_pod/requirements.txt`. The `jina_version` is the version of Jina when building the Pod image.

### Output of the Action
There are two outputs you can use in the post-action:
1. `success_targets`: Successfully built targets with their paths
2. `failed_targets`: Failed built targets with their paths

