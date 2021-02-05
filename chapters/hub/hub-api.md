# Jina Hub API Reference

Jina Hub API allows users to :`login`: to Hub, :`list`:, :`build`:, :`push`: and create :`new`: Hub images with configurable parameters. This guide covers installing and setting up the API for a seamless experience with the Hub.


## Install:
Run `pip install "jina[hub]"`

## `jina hub list`:
`hub list` interface helps the user to retrieve information related to Executor images in the Hub.
The following filters are accepted:
 `name` corresponding to image name
 `kind` corresponding to the kind of hub image (Indexer / Encoder / Segmenter / Crafter / Evaluator / Ranker etc)
 `type` indicating either Pod or app
 `keywords` for filtering on specific terms like `sklearn`,  etc.

`list` could be used for listing both local as well as remote images.

## `jina hub new`:
Jina provides an easy-to-use interface for spinning up an Executor or an app
### Create a new executor
`jina hub new --type pod`
It will start a wizard in the CLI to guide you in creating your first executor. The resulting file structure should look like the following:
```
MyAwesomeExecutor/
├── Dockerfile
├── manifest.yml
├── README.md
├── requirements.txt
├── __init__.py
└── tests/
    ├── test_MyAwesomeExecutor.py
    └── __init__.py
```


### Create a new app
`jina hub new --type app`
It will start a wizard in the CLI to guide you in creating your app.

## `jina hub login`

Login to Jina Hub using your GitHub credentials. This is required for pushing your executor to Jina Hub.
Usage:
`jina hub login` : 
Copy/paste the token into GitHub to verify your account

## `jina hub build`
To test your Pod/app locally, use the `build` command as follows:

`jina hub build /MyAwesomeExecutor/`
More Hub CLI usage can be found via `jina hub build --help`
 
## `jina hub push`
`push` is the command for publishing your Pod to the Hub
`jina hub push jinahub/type.kind.jina-image-name:image-jina_version`
 
### Naming conventions
All apps and executors should follow the naming convention:
`jinahub/type.kind.jina-image-name:image-jina_version`
 
For example:
`jinahub/app.app.wikipedia-sentences:0.0.1-1.0.0` (use default jina hub new version, and stable Jina version) 
 
For detailed information, please refer to the [Jina Hub repository].(https://github.com/jina-ai/jina-hub)
 
