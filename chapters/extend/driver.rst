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
and you find the current `Drivers` can not fit for your customised `Executor`,
you might want to create a customised `Driver`.

Overview
^^^^^^^^^

Implementation
^^^^^^^^^^^^^^^

Customize Driver in Action: X Driver
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

What's next
^^^^^^^^^^^






