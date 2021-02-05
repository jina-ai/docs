=======================================
A guide on Jina Primitive Data Types
=======================================

.. meta::
   :description: A guide on Jina Primitive Data Types
   :keywords: Jina, primitive data types

.. note:: This guide assumes you have a basic understanding of Jina, if you haven't, please check out `Jina 101 <https://docs.jina.ai/chapters/101/index.html>`_ first.

A primitive data type is a data type for which the programming language provides built-in support.
For example, when writing a Numpy or Tensorflow program, users perform matrix manipulation on multi-dimensional
arrays, such as ``np.ndarray`` or ``tensor``.

Jina introduced the new ``jina.types`` module since ``v0.8``.
Primitive data types complete Jina’s design by clarifying the low-level data representation in Jina, yielding a much simpler, safer, and faster interface on the high-level.
More importantly, they ensure the universality and extensibility for Jina in the long-term.

.. contents:: Table of Contents
    :depth: 2

Motivation
====================

Following a progressive manner of software design principle, Jina is shipped with multiple layers of abstractions.
Each layer targets a specific developer group.
As a consequence, developers can choose different levels of API to interact with Jina and accomplish their tasks.

Before we introduce the Jina primitive data types, ``drivers`` help the ``executors`` to handle the network traffic by directly interacting with the Protobuf messages.
Thus our back-end engineers have to generate or parse a stream of bytes in the network layer.
This is not aligned with the design principle of Jina.


Before you start
====================

We expect you have a clean Python 3.7/3.8/3.9 (virtual) build.
With Jina installed on your machine:

.. highlight:: bash
.. code-block:: bash

    pip install -U jina


Overview
====================

Jina primitive data types can be categorised into **basic types**, **composite types** and **derived types**.
A **basic data type** represents a single real-world object, such as :class:`Document`, :class:`Querylang`, :class:`NdArray`.
To enable a Pythonic interface and keeps the data safe, we introduced **composite data types**, such as :class:`DocumentSet`, :class:`QueryLangSet`, :class:`Request`.
Besides, we created several **derived types**, such as :class:`MultimodalDocument`.

.. list-table:: List of Jina Data Types
   :widths: 25 25 50
   :header-rows: 1

   * - Name
     - Type
     - Description
   * - Document
     - basic
     - A Pythonic interface to access and manipulate :class:`DocumentProto`.
   * - MultimodalDocument
     - derived
     - A Pythonic interface to access and manipulate modalities at chunk level derived from :class:`Document`.
   * - DocumentSet
     - composite
     - A mutable sequence of :class:`Document`.
   * - ChunkSet
     - derived
     - A view of a sequence of :class:`DocumentSet` at a higher granularity level derived from :class:`DocumentSet`.
   * - MatchSet
     - derived
     - A view of a sequence of matched :class:`DocumentSet` derived from :class:`DocumentSet`.
   * - Message
     - composite
     - A Pythonic interface to access and manipulate :class:`MessageProto`.
   * - NdArray
     - basic
     - Representing fixed-size multidimensional items.
   * - DenseNdArray
     - derived
     - A derived type based on :class:`NdArray` which supports quantization.
   * - SparseNdArray
     - derived
     - A derived type based on :class:`NdArray` which stores non-zero entries.
   * - QueryLang
     - basic
     - A Pythonic interface to access and manipulate :class:`QueryLangProto`.
   * - QueryLangSet
     - composite
     - A mutable sequence of :class:`QueryLang`.
   * - Request
     - basic
     - A Pythonic interface to access and manipulate :class:`RequestProto`.
   * - NamedScore
     - basic
     - A Pythonic interface to access and manipulate :class:`NamedScoreProto`.

Jina Types in Action
====================

In this section, we will introduce how to use Jina types.
More specifically, we will be focusing on jina :class:`Document` primitive data type.
Since as a user, you might use :class:`Document` primitive type daily.
Besides, the other types shares the same design rationale as :class:`Document` primitive data type.

We have three properties designed to access a :class:`Document`, include :meth:`text`, :meth:`blob` and :meth:`buffer`.
A Jina :class:`Document` object is expected to have **one of** these three properties as the :meth:`content` of a :class:`Document`.
For example:

.. highlight:: python
.. code-block:: python
.. testcode:: python

    import numpy as np
    from jina import Document

    d = Document()
    # set content to text, same as d.text = ...
    d.content = 'hello jina'
    # set content to buffer, same as d.buffer = ...
    d.content = b'1e2f2c'
    # set content to blob, same as `d.blob = ...
    d.content = np.random.random([3,4,5])

Jina will automatically infer to MIME type based on the :meth:`content` of the :class:`Document`.
The use case of the :class:`Document` is dependent on your data:

* Use :meth:`text` if you want to index/query textual data.
* Use :meth:`blob` if you want to index/query image/video/audio.
* Use :meth:`buffer` if you are not sure about the exact data format.

To create a document from constructor:

.. highlight:: python
.. code-block:: python
.. testcode:: python

    from jina import Document

    # Create a document from constructor
    d0 = Document('hello jina!') # from string
    d1 = Document({'text': 'hello jina!'}) # from dict
    d2 = Document(b'j\x0chello jina!') # from buffer
    d3 = Document('{"text": "hello jina!"}') # from json

    # Create a document from protobuf
    from jina.proto import jina_pb2
    d = jina_pb2.DocumentProto()
    d.text = 'hello jina!'
    d4 = Document(d)

As was introduced before, a :class:`DocumentSet` is a mutable sequence of :class:`Document`.
To create & access a :class:`DocumentSet`:

.. highlight:: python
.. code-block:: python
.. testcode:: python

    from jina import Document
    from jina.types.sets.document import DocumentSet

    # First, create 2 documents
    d0 = Document(content='doc0')
    d1 = Document(content='doc1')
    # Initialize a document set
    ds = DocumentSet([d0, d1])
    # Add a new document.
    d2 = Document(content='doc2')
    ds.add(d2)

Once you create an instance of :class:`DocumentSet`, Jina offers you a Pythonic interface to manipulate the set.
For example:

.. highlight:: python
.. code-block:: python
.. testcode:: python

    from jina import Document
    from jina.types.sets.document import DocumentSet

    # First, create 2 documents
    d0 = Document(content='doc0')
    d1 = Document(content='doc1')
    # Initialize a document set
    ds = DocumentSet([d0, d1])
    # Get the number of docs inside the set.
    len(ds)
    # Get document by index
    ds[0]
    # Reverse a documentset
    ds.reverse()
    # Remove all contents from a document set
    ds.clear()

You might be wondering *why do we need a document set*?
The answer is Jina's recursive data structure.
To put it simply, Jina offers a way to represent documents in a recursive manner.
A Jina :class:`Document` might contain a list of child :class:`Document`.
This recursive data structure allows us to query :class:`Document` at different granularity levels.
Such as match at the paragraph level, or even at the sentence level.
For example:

.. highlight:: python
.. code-block:: python
.. testcode:: python

    from jina import Document

    # First, create 2 documents
    chunk0 = Document(content='sentence0')
    chunk1 = Document(content='sentence1')
    document = Document()
    # Add chunks to the document
    document.chunks.append(chunk0)
    document.chunks.append(chunk1)
    # Check the type of chunks
    type(document.chunks)

If you print the type of :attr:`chunks`, you will find out it's named ``<class 'jina.types.sets.chunk.ChunkSet'>``, a derived data type based on :class:`DocumentSet`.
:class:`ChunkSet` added extra logic to handle logics such as  :meth:`granularity` and :meth:`adjacency`.
Similarly, we have :class:`MatchSet` manage the matched documents given a user query.

Last but now least, if you are working on the document with different modalities, :class:`MultimodalDocument` is the right Jina data type to use.
For example:

.. highlight:: python
.. code-block:: python
.. testcode:: python

    import numpy as np
    from jina.types.document.multimodal import MultimodalDocument

    visual_content = np.random.random([3,4,5])
    textual_content = 'hello jina!'
    multimodal_document = MultimodalDocument(
        modality_content_map={'visual': visual_content, 'textual': textual_content}
    )
    # Check the modalities of the document
    multimodal_document.modalities
    # Get the content of document by modality name
    content = multimodal_document['visual']


Design Decisions
====================

While designing and implementing Jina primitive data types, we have been always kept the following principles in mind:

**View, not copy**

We do not want another storage layer upon Protobuf.
The objective of Jina primitive data type is to provide an enhanced **view** of the protobuf **storage** by maintaining a reference.

**Delegate, not replicate**

Protobuf object provides attribute access already.
For simple data types such as :attr:`str`, :attr:`float`, :attr:`int`, the experience is good enough.
We do not want to replicate every attribute defined in Protobuf again in the Jina data type, but focus on the ones that need unique logic or particular attention.

**More than a Pythonic interface**

Jina data type is compatible with the Python idiom.
Moreover, it summarizes common patterns used in the drivers and the client and makes those patterns safer and easier to use.
For example, :attr:`doc_id` conversion is previously implemented inside different drivers, which is error-prone.

Reference to the design decisions can be find `here <https://hanxiao.io/2020/11/22/Primitive-Data-Types-in-Neural-Search-System/#design-decisions>`_ .


Final Words
====================

In this guide, we introduced why we need Jina primitive data types,
how we organize Jina primitive data types.
Apart from that, we gave some concrete examples of how to use Jina primitive data types.
Finally, we recapped the design decisions made while designing Jina primitive data types.
We hope now you have a better understanding of Jina primitive data types.


What's Next
====================

Thanks for your time & effort while reading this guide!
If you still have questions, feel free to `submit an issue <https://github.com/jina-ai/jina/issues>`_ or post a message in our `community slack channel <https://docs.jina.ai/chapters/CONTRIBUTING.html#join-us-on-slack>`_ .

To gain a deeper knowledge on the implementation of Jina primitive data types, you can find the source code `here <https://github.com/jina-ai/jina/tree/master/jina/types>`_.
