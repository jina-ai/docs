How to handle Inputs and Outputs for Flows
==========================================

This chapter explains how input and output data is handled by the ``Flow API``.

Input
-----
The input data for the ``Flow`` functions ``flow.index(...)``, ``flow.update(...)`` and ``flow.search(...)`` can be provided in three different ways.
In the following example, the input functionality is shown using the ``Flor.index(...)`` function.
For ``flow.update(...)`` and ``flow.search(...)``, the input is provided the same way.

#. A single ``Document`` can be sent through the ``Flow`` as shown below.

    .. highlight:: python
    .. code-block:: python

        with f:
            f.index(Document('doc content'))

#. An Iterable of ``Documents`` can be provided instead. The following example shows a ``Generator`` fed into a ``Flow``.

    .. highlight:: python
    .. code-block:: python

        with f:
            f.index((Document(f'doc content {i}') for i in range(10)))


#. The data can also be wrapped in a parameter-less function.

    .. highlight:: python
    .. code-block:: python

        def input_function():
            for i in range(10):
                yield Document(f'doc content {i}')
        with f:
            f.index(input_function)

#. An ``AsyncIterable`` can be used as well.

    .. highlight:: python
    .. code-block:: python

        async def async_input_fn():
            for _ in range(10):
                yield np.random.random([4])
                await asyncio.sleep(0.1)

        with AsyncFlow() as f:
            async f.index(input_fn()):

The ``flow.delete(...)`` function accepts ``Document`` ids instead of ``Documents``.

    .. highlight:: python
    .. code-block:: python

        with f:
            f.delete('DOC_ID')

        with f:
            f.delete(['DOC_ID_1', 'DOC_ID_2', 'DOC_ID_3'])

        def input_function_delete():
            for i in range(10):
                yield f'{i}'
        with f:
            f.delete(input_function_delete)

Special input functions
-----------------------
There are some functions of the ``Flow API`` which simplify the input handling:
``flow.index_lines(...)``, ``flow.index_ndjson(...)``, ``flow.index_csv(...)``, ``flow.index_files(...)``, ``flow.index_ndarray(...)``
``flow.search_lines(...)``, ``flow.search_ndjson(...)``, ``flow.search_csv(...)``, ``flow.search_files(...)`` and ``flow.search_ndarray(...)``

The following examples show the usage of the ``flow.index_*(...)`` functions.
Providing ``Documents`` to search for works in the same way using the respective functions.
Here, a ``CSV`` file is used to index ``Documents``. The possible ways of feeding in the ``CSV`` are shown.
The function ``flow.index_lines(...)`` can be used in combination with ``line_format = 'CSV'``.
A simpler version is to use ``F.index_csv(...)`` where the ``line_format`` parameter is not needed.
The ``CSV`` data can be provided as a file handler or directly as ``str array``.

    .. highlight:: csv
    .. code-block:: csv
       :caption: input.csv

        id,text
        1,first text
        5,second text

    .. highlight:: python
    .. code-block:: python

        with f:
            f.index_csv(open('input.csv'))

        with f:
            f.index_lines(open('input.csv'), line_format='csv')

        with f:
            f.index_csv(open('input.csv').readlines())

        with f:
            f.index_csv(open('input.csv').readlines(), line_format='csv')


It's similar when using JSON lines.

    .. highlight:: js
    .. code-block:: js
       :caption: input.jsonlines

        {"id": 1,"text": "first text"}
        {"id": 5,"text": "second text"}

    .. highlight:: python
    .. code-block:: python

        with f:
            f.index_ndjson(open('input.jsonlines'))

        with f:
            f.index_csv(open('input.jsonlines'), line_format='json')

        with f:
            f.index_ndjson(open('input.jsonlines').readlines())

        with f:
            f.index_csv(open('input.jsonlines').readlines(), line_format='json')

The ``flow.index_files(...)`` function can be used if multiple files have to be fed into the ``Flow``.

    .. highlight:: python
    .. code-block:: python

        with f:
            f.index_files('*.png', on_done=print)

Using ``flow.index_ndarray(...)`` and ``flow.search_ndarray(...)``, numpy arrays can be fed into the ``Flow``.

    .. highlight:: python
    .. code-block:: python

        import numpy
        with f:
            f.index_ndarray(numpy.random.random([5, 4]))


A field resolver can be used in case the fields of the source file have to be mapped.

    .. highlight:: csv
    .. code-block:: csv
       :caption: input2.csv

        identifier,paragraph
        1,first text
        5,second text

    .. highlight:: python
    .. code-block:: python

        with f:
            f.index_csv(open('input2.csv'), field_resolver={'identifier': 'id', 'paragraph': 'text'})

Output
------
The output of the ``Flow`` operations is handled via callback functions ``on_done``, ``on_error`` and ``on_always``.
In addition, it is possible to retrieve the results directly when setting the attribute ``return_results = True``.
The following example shows how to handle the output via callback functions.

    .. highlight:: python
    .. code-block:: python

        def handle_response(resp):
            # the response handler is only called if the flow execution does not encounter exceptions
            # handle the response
            for d in resp.search.docs:
                ...
                for m in d.matches:
                   ...


        def handle_error():
            # in case of an Exception, the flow execution continues and calls this ``on_error`` handler

        def handle_search_done():
            # this handler is always called regardless of Exceptions

        with Flow.load_config(os.path.join(cur_dir, 'flow.yml')) as f:
            f.search([doc], on_done=handle_response, on_error=handle_error, on_always=handle_search_done)


It can be useful to use the built-in ``print`` function as ``on_done`` callback.

    .. highlight:: python
    .. code-block:: python

        with f:
            f.search(input_fn, on_done=print)

When setting ``return_results = True``, the results are returned directly.
It can be used in combination with ``Callbacks`` as well.

    .. highlight:: python
    .. code-block:: python

        with Flow(return_results=True) as f:
            result = f.search('first', on_done=handle)


Insights
--------
When using the ``flow.*`` functions, ``Jina`` builds and sends Protobuf messages to the relevant ``Pods``.
For instance calling the ``index_ndarray(...)`` function sends the following message to the first ``Pod``.

    .. highlight:: protobuf
    .. code-block:: protobuf

        request {
          request_id: 1
          index {
            docs {
              id: 1
              weight: 1.0
              blob {
                buffer: "\004@\316\362/D\333?\244>\235\305\027\311\336?\267\210\251\311^\260\345?\366\n(\014\022m\356?\374\262\017\030\036\357\351?-c\300\337\217V\345?\241G\241\352\233\024\356?\340\346lUf\353\350?"
                shape: 8
                dtype: "float64"
              }
            }
            docs {
              id: 2
              weight: 1.0
              blob {
                buffer: "\312Wm\337\250\217\354?t\212\326\020\261\r\320?\254\262\300u<O\323?\340\210\222$\321\216\314?\310.q,+\347\311?&\316\361\310\252R\331?\214\016\201a\231\262\330?\342\231\262\221\343%\324?"
                shape: 8
                dtype: "float64"
              }
            }
            docs {
              id: 3
              weight: 1.0
              blob {
                buffer: "kT\250\372K%\345?\237\017+u\300\227\353?\3668\256\340\251\227\350?\327\006$\032$\002\341?\274\300\3573\371\262\343?\346\371\265dV\330\342?\370\210\360\002P3\340?\022i-\016\374\320\331?"
                shape: 8
                dtype: "float64"
              }
            }
          }
        }


The structure of this message is defined in the format of `protobuf <https://docs.jina.ai/chapters/proto/docs.html>`_
Find more details of the data structure at `jina.proto <https://docs.jina.ai/chapters/proto/docs.html#jina.proto>`_

``request`` contains input data and related metadata.
The input is a 3*8 matrix that is sent to the ``Flow``, which matches 3 ``request.index.docs``,
and the ``request.index.docs.blog.shape`` is 8.
The vector of the matrix is stored in ``request.index.docs.blob``,
and the ``request.index.docs.blob.dtype`` indicates the type of the vector.


Request size
------------
The functions ``flow.index(...)``, ``flow.update(...)``, ``flow.delete(...)``, ``flow.search(...)`` and ``flow.train(...)``
accept the ``request_size`` parameter. It sets the limit for ``Documents`` sent in one request.
In case more ``Documents`` are provided, they split up into multiple requests.
