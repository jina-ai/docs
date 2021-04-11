# Remote Flows with JinaD

### Prerequisites

Before you start, make sure you have read the [prerequisites for using JinaD](https://docs.jina.ai/chapters/remote/jinad.html#prerequisites).

## REST API

[Refer the detailed API reference for JinaD](https://api.jina.ai/daemon/#tag/flows)

A common use case is running the Flow on the remote with pods distributed across different machines.

#### Examples

Define `flow.yml` with the correct `JinaD` host details.

```yaml
jtype: Flow
pods:
    - uses: _logforward
      host: 1.2.3.4:8000
      upload-files:
        - custom_encoder_1.yml
    - uses: _logforward
      host: 2.3.4.5:8000
      upload-files:
        - custom_encoder_2.yml
```

###### 1. Create a Flow

`POST /flows` endpoint accepts a flow yaml file and returns an id to track the Flow.

```bash
curl --request POST \
  --url 'http://1.2.3.4:8000/flows' \
  --form 'flow=@flow.yml'
```

###### 2. Fetch a Flow on JinaD

```bash
curl --request GET \
  --url 'http://1.2.3.4:8000/flows?id=<flow-id>'
```

###### 3. Fetch all running Flows on JinaD

```bash
curl --request GET \
  --url 'http://1.2.3.4:8000/flows'
```

###### 4. Terminate a Flow

```bash
curl --request DELETE \
    --url 'http://1.2.3.4:8000/flows?id=<flow-id>'
```

###### 5. Terminate all running Flows

```bash
curl --request DELETE \
    --url 'http://1.2.3.4:8000/flows'
```
