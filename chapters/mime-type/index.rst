=============================
A guide on mime types in Jina
=============================

The mime type of a document is a string property (``document.mime_type``).
It is used to store the type of the document.
This information can be used by the drivers to handle documents differently based on their mime type.
The mime type of a document can be set to one of the values in ``mimetypes.types_map.values()`` (e.g. video/mpeg, text/html, image/jpeg, application/msword...).
It can be automatically derived from the content of the document or being overwritten manually.
The mime type is set automatically when setting one of the content attributes ``uri``, ``text`` and ``buffer``.
The following example illustrates which mime types are automatically derived from the content attributes:

.. highlight:: python
.. code-block:: python
    from jina import Document

    d1 = Document()
    d1.text = 'my text 📩'
    print(d1.mime_type)  # mime_type is 'text/plain'

    d2 = Document()
    d2.uri = 'https://upload.wikimedia.org/wikipedia/commons/a/a9/Example.jpg'
    print(d2.mime_type)  # mime_type is 'image/jpeg'

    d3 = Document()
    d3.buffer = (
        b'\x89PNG\r\n\x1a\n\x00\x00\x00\rIHDR...'

    )
    print(d3.mime_type)  # mime_type is 'image/png'

To have the full control over the mime_type, it is also possible to set it manually like shown in the following example:

.. highlight:: python
.. code-block:: python
    from jina import Document

    svg_content = (
        '<svg height="100" width="100">'
        '  <circle cx="50" cy="50" r="40" stroke="black" stroke-width="3" fill="red" />'
        '</svg>'
    ).encode('utf8')
    d = Document()
    d.buffer = (svg_content)
    print(d.mime_type)  # automatically detects that mime_type is 'image/svg'
    # the mime type can be overwritten
    d.mime_type = 'text/plain'
    # assigning an invalid mime type leads to a ``ValueError``
    d.mime_type = 'invalid/type' # raises exception

Drivers derived from ``SegmentDriver`` can access the meme type of the documents to treat them differently.

.. highlight:: python
.. code-block:: python
    from ..types.document import Document
    from .. import DocumentSet

    class SpecialSegmentDriver(SegmentDriver):
        def __init__(
                self,
                *args,
                **kwargs
        ):
            super().__init__(*args, **kwargs)

        def _apply_all(self, leaves, *args, **kwargs):
            docs = DocumentSet.flatten(leaves)
            for doc in docs:
                if doc.mime_type == 'text/plain':
                    _args_dict = doc.get_attrs(*self.exec.required_keys)
                    ret = self.exec_fn(**_args_dict)
                    if ret:
                        self._update(doc, ret)

For a variety of use cases, chunks have the same mime type as their parent documents.
Examples are text documents where each sentence (chunk) is a text document on its own.
Also when segmenting images or audio, most applications are creating chunks of the same mime type.
However, there are scenarios where it makes sense to change the mime type on chunk level.
A common example would be video documents where the chunks are images.
The ``Segmenters`` are responsible for assigning the right mime type to the chunks they create.

.. highlight:: python
.. code-block:: python
    from jina.executors.segmenters import BaseSegmenter

    class DummySegmenter(BaseSegmenter):
        def __init__(self, *args, **kwargs):
            super().__init__(*args, **kwargs)

        def segment(self, text: str, *args, **kwargs):
            results = [{
                'text': word,
                'mime_type': 'text/plain'
            } for word in text.split()]
            return results

In case no mime type is set, the ``SegmentDriver`` assigns the the mime type of the parent document as default value.
