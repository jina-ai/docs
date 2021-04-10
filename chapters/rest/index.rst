#################################
RESTful CRUD Operations with Jina
#################################

By default, Jina uses gRPC gateway to send protobuf [1]_ messages. To enable browsers to communicate with Jina, we provide an optional REST [2]_ Gateway.

Python API
""""""""""

.. code:: python

    from jina.flow import Flow
    f = (Flow(restful=True, port_expose=23456)
         .add(...)
         .add(...))

YAML
""""

.. code:: yaml

    jtype: Flow
    with:
        restful: true
        port_expose: 23456
    pods:
        - name: pod1
          uses: _index

********
API Docs
********

`Refer detailed API reference using redoc <https://api.jina.ai/rest/>`__

************************
CRUD Examples  [3]_ [4]_
************************

1. Index
"""""""""

.. code:: bash

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

2. Query
"""""""""

.. code:: bash

    curl --request POST \
         --url http://localhost:23456/search \
         --data '{
            "data": [ "Hello world" ],
            "top_k": 2
        }'

3. Update
""""""""""

.. code:: bash

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

4. Delete
"""""""""

.. code:: bash

    curl --request DELETE \
         --url http://localhost:23456/delete \
         --data '{
            "data": [ "temp-id-1", "temp-id-2" ]
        }'

***************
WebSockets [5]_
***************

REST doesn't support bi-directional streaming, which hampers the performance especially during batch operations. We have enabled an WebSocket endpoint at ``http://localhost:<port-expose>/stream``.
Following code sends requests to a Websocket gateway using ``(Async)WebSocketClient``.

.. code:: python

    from jina.flow import Flow
    f = (Flow(restful=True, port_expose=23456)
         .add(...)
         .add(...))
    with f:
        f.index(...)


.. [1] `Protobuf Specification <https://docs.jina.ai/chapters/proto/>`__

.. [2] To use Jina with REST API, you need to install jina via :command:``pip install "jina[http]"``

.. [3] Read more about `CRUD implementation in Jina <https://docs.jina.ai/chapters/crud/>`__.

.. [4] The endpoint ``/api/{mode}`` is deprecated. Please move to the corresponding CRUD endpoint.

.. [5] This is an experimental endpoint & is subject to change.
