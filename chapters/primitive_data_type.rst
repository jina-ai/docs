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
     - test
   * - DocumentSet
     - composite
     - test
   * - ChunkSet
     - composite
     - test
   * - MatchSet
     - composite
     - test
   * - MultimodalDocument
     - derived
     - test
   * - Message
     - composite
     - test
   * - NdArray
     - basic
     - test
   * - DenseNdArray
     - derived
     - test
   * - SparseNdArray
     - derived
     - test
   * - QueryLang
     - basic
     - test
   * - QueryLangSet
     - composite
     - test
   * - Request
     - basic
     - test
   * - NameScore
     - basic
     - test

Jina Types in Action
====================

Design Decisions
====================

Final Words
====================

What's Next
====================









