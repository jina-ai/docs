Development Guide: Add new Drivers
====================================

.. meta::
   :description: Development Guide: Add new Drivers
   :keywords: Jina, driver

.. note:: This guide assumes you have a basic understanding of Jina, if you haven't, please check out `Jina 101 <https://101.jina.ai>`_ first.
.. note:: Before reading this guide, you might want to read `Development Guide: Add new Executors <../executor.rst>`_ first.

.. contents:: Table of Contents
    :depth: 2

Motivation
^^^^^^^^^^^

As you might have already learned from `Jina 101 <https://101.jina.ai>`_,
a :term:`Driver` interprets incoming messages into :term:`Document` and extracts required fields for an :term:`Executor`.

Jina already created `several drivers <https://docs.jina.ai/chapters/all_driver/>`_ drivers inside Core,
and these `Drivers` should already fulfill most of the scenarios.
However, when the existing solutions do not fit your specific use case,
you might be curious on how to **extend** Jina.
For instance, if you have created a customised `Executor`,
and figure out the existent `Drivers` can not fit for your customised `Executor`,
you might want to create a customised `Driver`.

Overview
^^^^^^^^^

To make an extension of a Jina `Driver`, please follow the steps listed below:

1. Decide which `Driver` class to inherit from.
2. Overwrite the **Core** method of the `Driver`.

Implementation
^^^^^^^^^^^^^^^

.. list-table:: Built-in Driver to Inherit
   :widths: 25 25 50 25
   :header-rows: 1

   * - Name
     - Corresponded Executor
     - Description
     - Core method
   * - `BaseEncodeDriver`
     - `BaseEncoder`
     - Driver bind with :meth:`encode` in the Executor.
     - `_apply_all`
   * - `BaseIndexDriver`
     - `BaseIndexer`
     - Driver bind with :meth:`add` in the Executor.
     - `_apply_all`
   * - `BaseSearchDriver`
     - `BaseIndexer`
     - Driver bind with :meth:`query` in the Executor.
     - `_apply_all`
   * - `BasePredictDriver`
     - `BaseClassifier`
     - Driver bind with :meth:`predict` in the Executor.
     - `_apply_all`
   * - `BaseLabelPredictDriver`
     - `BaseClassifier`
     - Driver bind with :meth:`predict` for label prediction.
     - `prediction2label`
   * - `BaseEvaluateDriver`
     - `BaseEvaluator`
     - Driver bind with :meth:`evaluate` in the Executor.
     - `extract`

This is to say, if you want to create a customized `Executor` and it's associated `Driver`,
Follow the table above to decide which `BaseDriver` class to inherit from.
After creating your customised `Driver` class, you need to implement your own Core method based on your specific need.


Customize Driver in Action: X Driver
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

What's next
^^^^^^^^^^^




