=======================================
A guide on Jina Primitive Data Types
=======================================

.. meta::
   :description: A guide on Jina Primitive Data Types
   :keywords: Jina, primitive data types

.. note:: This guide expect you have a basic understanding of Jina, if you haven't, please check out `Jina 101 <https://docs.jina.ai/chapters/101/index.html>`_ first.

A primitive data type is a data type for which the programming language provides built-in support.
For example, when writing a Numpy or Tensorflow program, uses perform matrix manipulation on mutl-dimensional
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

Before we introduce the Jina primitive data types, ``drivers`` helps the ``executors`` to handle the network traffic by directly interacting with the Protobuf messages.
Thus our back-end engineers have to generate or parse stream of bytes in the network layer.
This is not aligned with the design principle of Jina.


Before you start
====================

We expect you have a clean Python 3.7/3.8/3.9 (virtual) build.
WIth Jina installed on your machine:

.. highlight:: bash
.. code-block:: bash

    pip install -U jina>=0.8


Overview
====================

Jina primitive data types can be categorised into **basic types**, **composite types** and **derived types**.
A **basic data type** represents a single real-world object, such as ``Document``, ``Querylang``, ``NdArray``.
To enable a Pythonic interface and keeps the data safe, we introduced **composite data types**, such as ``DocumentSet``, ``QueryLangSet``, ``Request``.
Besides, we created several **derived types**, such as ``MultimodalDocument``.

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
     - A view of a sequence of :class:`Document` at a higher granularity level derived from :class:`DocumentSet`.
   * - MatchSet
     - derived
     - A view of a sequence of matched :class:`Document` derived from :class:`DocumentSet`.
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
   * - NameScore
     - basic
     - A Pythonic interface to access and manipulate :class:`NamedScoreProto`.

Jina Types in Action
====================



Design Decisions
====================

While designing and implementing Jina primitive data types, we have been always keep the following principles in mind:

**View, not copy**

We do not want to another storage layer upon Protobuf.
The objective of Jina primitive data type is to provide an enhanced **view**
 of the protobuf **storage** by maintaining a reference.

**Delegate, not replicate**

Protobuf object provides attribute access already.
For simple data types such as ``str``, ``float``, ``int``, the experience is good enough.
We do not want to replicate every attribute defined in Protobuf again in the Jina data type, but really focus on the ones that need unique logic or particular attention.

**More than a Pythonic interface**

Jina data type is compatible with the Python idiom.
Moreover, it summarizes common patterns used in the drivers and the client and makes those patterns safer and easier to use.
For example, ``doc_id`` conversion is previously implemented inside different drivers, which is error-prone.

Reference to the design decisions can be find `here <https://hanxiao.io/2020/11/22/Primitive-Data-Types-in-Neural-Search-System/#design-decisions>`_ .


Final Words
====================

In this guide, we introduced why we need Jina Primitive data types,
how we organize Jina primitive data types.
Apart from that, we gave some concrete examples on how to use Jina primitive data types.
Finally, we recapped the design decisions made while designing Jina primitive data types.
We hope now you have a better understanding of Jina primitive data types.


What's Next
====================

Thanks for your time & effort while reading this guide!
If you still have questions, feel free to `submit an issue <https://github.com/jina-ai/jina/issues>`_ or post a message in our `community slack channel <https://docs.jina.ai/chapters/CONTRIBUTING.html#join-us-on-slack>`_ .

To gain a deeper knowledge on the implementation of Jina primitive data types, you can find the source code `here <https://github.com/jina-ai/jina/tree/master/jina/types>`_.
