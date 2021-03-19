A Guide on MIME Types in Jina
=============================

This guide explains what a mime type is, how to assign them manually or automatically to a Document
and how you can work with them on a Chunk or Driver level.

Summary
-------
The MIME type of a Document is a string property (``document.mime_type``).
It is used to store the type of the Document.
This information can be used by the Drivers to handle Documents differently based on their MIME type.
The MIME type of a Document can be set to one of the values in ``mimetypes.types_map.values()`` (e.g. video/mpeg, text/html, image/jpeg, application/msword...).
It can be automatically derived from the content of the Document or being overwritten manually.
The MIME type is set automatically when defining one of the content attributes ``uri``, ``text`` or ``buffer``.

Automatic MIME Type Assignment
------------------------------
The following example shows which MIME types are automatically derived from the content attributes:

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

Manual MIME Type Assignment
---------------------------
To have full control over the MIME type, it is also possible to set it manually as shown in the following example:

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
        # the MIME type can be overwritten
        d.mime_type = 'text/plain'
        # assigning an invalid MIME type leads to a ``ValueError``
        d.mime_type = 'invalid/type' # raises exception

MIME Type in Chunks
-------------------
Chunks can be created by ``Segmenters``.
Also, Chunks can be created by the user and attached to the Documents before feeding them into the flow.
There are many use cases where Chunks have the same MIME type as their parent Documents.
For instance, when segmenting images or audio, Chunks of the same MIME type are created.

In some use cases, a different parent and chunk mime type is required.
Such as processing video, where the chunk would be images.
A Segmenter is responsible for assigning the correct mime type to the chunks when they are created.

In case no MIME type is set, the ``SegmentDriver`` assigns the MIME type of the parent Document as default value.
The following example shows a simple Segmenter, which sets the ``mime_type`` for each Chunk it creates.

.. confval:: dummy_segmenter.py

    .. highlight:: python
    .. code-block:: python
        
        from jina.executors.decorator import single
        from jina.executors.segmenters import BaseSegmenter

        class DummySegmenter(BaseSegmenter):
            def __init__(self, *args, **kwargs):
                super().__init__(*args, **kwargs)

            @single
            def segment(self, text: str, *args, **kwargs):
                results = [{
                    'text': word,
                    'mime_type': 'text/plain'
                } for word in text.split()]
                return results


Usage in Driver
---------------
Drivers can access the MIME type of the Document to handle them accordingly.
The following Driver only encodes Documents where the ``mime_type`` is ``'text/plain'``:

.. confval:: special_segment_driver.py

    .. highlight:: python
    .. code-block:: python

         class EncodeTextDriver(...):
            def _apply_all(...) -> None:
                for doc in docs:
                    if doc.mime_type == 'text/plain':
                        embeds = self.exec_fn(contents)
