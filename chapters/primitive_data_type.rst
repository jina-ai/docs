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

Before the introduction of Jina Primitive Data Types, drivers helps the executors to handle the network traffic by directly
interact with the Protobuf messages.
This is not aligned with the design principle of Jina.


Before you start
====================

Overview
====================

Jina Types in Action
====================

Design Decisions
====================

Final Words
====================

What's Next
====================









