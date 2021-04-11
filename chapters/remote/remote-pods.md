# Remote Pods with JinaD

### Prerequisites

Before you start, make sure you have read the [prerequisites for using JinaD](https://docs.jina.ai/chapters/remote/jinad.html#prerequisites).

## Inside a Flow

A common case of using Jina remotely is to have a Flow running locally with Pod on the remote. Following code creates a Flow with a remote Pod on `1.2.3.4` and uploads the pod yml to remote using `upload_files`. All remote logs are collected by the Flow.

#### Python

```python
from jina import Flow, Document
f = (Flow()
     .add(uses='_logforward',
          host='1.2.3.4:8000',
          upload_files=['custom_encoder.yml']))
with f:
    f.index(Document())
```

#### YAML

```yaml
jtype: Flow
pods:
    - uses: _logforward
      host: 1.2.3.4:8000
      upload-files:
        - custom_encoder.yml
```


## REST API

[Refer the detailed API reference for JinaD](https://api.jina.ai/daemon/#tag/pods)

## What's next?

In case you've missed, you may want to check out the following article.
[Remote Flows with JinaD](https://docs.jina.ai/chapters/remote/remote-flows.html)
