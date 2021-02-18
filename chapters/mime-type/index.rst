A guide on mime types in Jina
=============================

Summary
-------
The mime type of a document is a string property (``document.mime_type``).
It is used in order to store the type of the document.
This information can be used by the drivers to handle documents differently based on their mime type.
The mime type of a document can be set to one of the values in ``mimetypes.types_map.values()`` (e.g. video/mpeg, text/html, image/jpeg, application/msword...).
It can be automatically derived from the content of the document or being overwritten manually.
The mime type is set automatically when defining one of the content attributes ``uri``, ``text`` or ``buffer``.

Automatic mime type assignment
------------------------------
The following example illustrates which mime types are automatically derived from the content attributes:

.. confval:: auto_assignment.py

    .. highlight:: python
    .. code-block:: python

        from jina import Document

        d1 = Document()
        d1.text = 'my text 📩'
        assert d1.mime_type == 'text/plain'

        d2 = Document()
        d2.uri = 'https://upload.wikimedia.org/wikipedia/commons/a/a9/Example.jpg'
        assert d2.mime_type == 'image/jpeg'

        d3 = Document()
        d3.buffer = (b'\x89PNG\r\n\x1a\n\x00\x00\x00\rIHDR...')
        assert d3.mime_type == 'image/png'

Manual mime type assignment
---------------------------
To have the full control over the mime type, it is also possible to set it manually like shown in the following example:

.. confval:: manual_assignment.py

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
        assert d.mime_type == 'image/svg'
        # the mime type can be overwritten
        d.mime_type = 'text/plain'
        # assigning an invalid mime type leads to a ``ValueError``
        d.mime_type = 'invalid/type' # raises exception

Mime type in chunks
-------------------
For a variety of use cases, chunks have the same mime type as their parent documents.
Examples are text documents where each sentence (chunk) is a text document on its own.
Also when segmenting images or audio, most applications are creating chunks of the same mime type.
However, there are scenarios where it makes sense to change the mime type on chunk level.
Common examples would be for instance video documents where the chunks are images or images with text chunks (OCR).
The ``Segmenters`` are responsible for assigning the right mime type to the chunks they create.
In case no mime type is set, the ``SegmentDriver`` assigns the the mime type of the parent document as default value.
The following example illustrates a simple segmenter, which sets the ``mime_type`` for each chunk it creates.

.. confval:: dummy_segmenter.py

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


Usage in driver
---------------
Drivers can access the mime type of the documents in order to handle them accordingly.
The following driver only encodes documents where the ``mime_type`` is ``'text/plain'``:

.. confval:: special_segment_driver.py

    .. highlight:: python
    .. code-block:: python

        from ..types.document import Document
        from .. import DocumentSet

         class EncodeDriver(FastRecursiveMixin, BaseEncodeDriver):
            """Extract the content from documents and call executor and do encoding
            """

            def _apply_all(self, leaves: Iterable['DocumentSet'], *args, **kwargs) -> None:
                docs = DocumentSet.flatten(leaves)
                contents, docs_pts = docs.all_contents
                if docs_pts:
                    if doc.mime_type == 'text/plain':
                        embeds = self.exec_fn(contents)
                        if len(docs_pts) != embeds.shape[0]:
                            self.logger.error(
                                f'mismatched {len(docs_pts)} docs from level {docs_pts[0].granularity} '
                                f'and a {embeds.shape} shape embedding, the first dimension must be the same')
                        for doc, embedding in zip(docs_pts, embeds):
                            doc.embedding = embedding