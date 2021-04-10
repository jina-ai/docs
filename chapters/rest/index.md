# RESTful CRUD Operations with Jina

## Enable REST/Websockets Gateway

By default, Jina uses gRPC for its gateway to send protobuf[^1] messages. To enable browsers to communicate with Jina, we provide an optional REST[^2] Gateway.

[^1]: [Protobuf Specification](https://docs.jina.ai/chapters/proto/)
[^2]: To use Jina with REST API, you need to install jina via :command:`pip install "jina[http]"`

##### Python API

```python
from jina.flow import Flow
f = (Flow(restful=True, port_expose=23456)
     .add(...)
     .add(...))
```

##### YAML

```yaml
jtype: Flow
with:
    restful: true
    port_expose: 23456
pods:
    - name: pod1
      uses: _index
```

## API Docs
[Refer detailed API reference using redoc](https://api.jina.ai/rest/)

## CRUD Examples [^3][^4]

[^3]: Read more about [CRUD implementation in Jina](https://docs.jina.ai/chapters/crud/).
[^4]: The endpoint `/api/{mode}` is deprecated. Please move to the corresponding CRUD endpoint.

##### 1. Index

```bash
curl --request POST \
     --url http://localhost:23456/index \
     --data '{
        "data": [
            {
                "id": "temp-id-1",
                "text": "Hello world",
                "key": "value-1"
            },
            {
                "id": "temp-id-2",
                "text": "Jina is cool",
                "key": "value-2"
            }
        ]
     }'
```

##### 2. Query

```bash
curl --request POST \
     --url http://localhost:23456/search \
     --data '{
        "data": [ "Hello world" ],
        "top_k": 2
    }'
```

##### 3. Update

```bash
curl --request PUT \
     --url http://localhost:23456/update \
     --data '{
        "data": [
            {
                "id": "temp-id-1",
                "text": "Hello another world",
                "key": "value-3"
            },
        ]
    }'
```

##### 4. Delete

```bash
curl --request DELETE \
     --url http://localhost:23456/delete \
     --data '{
        "data": [ "temp-id-1", "temp-id-2" ]
    }'
```

## WebSockets[^5]

[^5]: This is an experimental endpoint & is subject to change

REST doesn't support bi-directional streaming, which hampers the performance especially during batch operations. We have enabled an WebSocket endpoint at `http://localhost:<port-expose>/stream`. Following code sends requests to a Websocket gateway using `(Async)WebSocketClient`.

```python
from jina.flow import Flow
f = (Flow(restful=True, port_expose=23456)
     .add(...)
     .add(...))
with f:
    f.index(...)
```
