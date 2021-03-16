Development Guide: Add new Drivers
====================================

.. meta::
   :description: Development Guide: Add new Drivers
   :keywords: Jina, driver

.. note:: This guide assumes you have a basic understanding of Jina, if you haven't, please check out `Jina 101 <https://101.jina.ai>`_ first.

.. contents:: Table of Contents
    :depth: 2

Motivation
^^^^^^^^^^^

As you might already learned from `Jina 101 <https://101.jina.ai>`_,
a :term:`Driver` interprets incoming messages into :term:`Document` and extracts required fields for an :term:`Executor`.

Jina already created `72 drivers <https://docs.jina.ai/chapters/all_driver/>`_ drivers inside Core,
and these `Drivers` should already fulfill most of the scenarios.
However, when the existing Executors does not fit your specific use case,
you might be curious on how to **extend** Jina.
For instance, if you have created a customised `Executor`,
and figure out the existent `Drivers` can not fit for your customised `Executor`,
you might want to create a customised `Driver`.

Overview
^^^^^^^^^

To make an extension of a Jina `Driver`, please follow the steps listed below:

1. Decide which `Driver` class to inherit from.
2. Override :meth:`__init__`.
3. Overwrite the **Core** method of the `Driver`.

Implementation
^^^^^^^^^^^^^^^

Decide which :class:`Driver` class to inherit from
-----------------------------------------------------

.. list-table:: Built-in Driver to Inherit
   :widths: 25 25 50
   :header-rows: 1

   * - Name
     - Corresponded Executor
     - Description
   * - `BaseEncodeDriver`
     - `BaseEncoder`
     - Driver bind with :meth:`encode` in the Executor.
   * - `BaseIndexDriver`
     - `BaseIndexer`
     - Driver bind with :meth:`add` in the Executor.
   * - `BasePredictDriver`
     - `BaseClassifier`
     - Driver bind with :meth:`predict` in the Executor.
   * - `BaseEvaluateDriver`
     - `BaseEvaluator`
     - Driver bind with :meth:`evaluate` in the Executor.


Customize Driver in Action: X Driver
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

What's next
^^^^^^^^^^^






