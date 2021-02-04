# Hub API

Jina Hub exposes several handy commands, namely `list`, `build` and `push` with configurable parameters. These APIs constitute the user’s experience using `jina cli` for a seamless experience with the Hub.


## `jina hub` installation:
`hub cli` can be installed using pip, run `pip install "jina[hub]"`

## `jina hub list`:
`hub list` interface helps the user to retrieve information related to executor images in the Hub.
It accepts filters like:
 `name` corresponding to image name
 `kind` corresponding to the kind of hub image (indexer / encoder / segmenter / crafter / evaluator / ranker etc)
 `type` indicating either pod or app
 `keywords` for filtering on specific terms like `sklearn`,  etc.

`list` could be used for listing both local as well as remote images.


## `jina hub new`:
Jina provides an easy to use interface for spinning up an executor or an app
### Create a new executor
`jina hub new --type pod`
It will start a wizard in CLI to guide you create your first executor. The resulted file structure should look like the following:
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
It will start a wizard in CLI to guide you create your app.

## `jina hub login`

Login to Jina Hub using your Github credentials. This is required for pushing your executor to Jina Hub.
Usage:
`jina hub login` : 
Copy/paste the token into GitHub to verify your account

## `jina hub build`
To test your pod/app locally, use the `build` command as follows:

jina hub build /MyAwesomeExecutor/
More Hub CLI usage can be found via jina hub build --help
 
## `jina hub push`
`push` is the command for publishing your Pod to the Hub
`jina hub push jinahub/type.kind.jina-image-name:image-jina_version`
 
### Naming conventions
All apps and executors should follow the naming convention:
`jinahub/type.kind.jina-image-name:image-jina_version`
 
For example:
`jinahub/app.app.jina-wikipedia-sentences-30k:0.2.0-0.8.2`
 
For detailed information, please refer to the [jina-hub repository here].(https://github.com/jina-ai/jina-hub)
 
