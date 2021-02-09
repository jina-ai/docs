==========================================
A guide on Jina Flow Evaluation Mode
==========================================

.. meta::
   :description: A guide on Jina Flow Evaluation Mode
   :keywords: Jina, flow evaluation

.. note:: This guide assumes you have a basic understanding of Jina, if you haven't, please check out `Jina 101 <https://docs.jina.ai/chapters/101/index.html>`_ first.

.. contents:: Table of Contents
    :depth: 2

Motivation
--------------------

As a :term:`Neural Search` system,
Jina employs deep neural networks to find the best :term:`Match` based on the user query.
Measuring system performance is critical important for Jina.

Users generally place an emphasis on evaluating final document ranking result using information retrieval metrics,
such as Precision, Recall, mAP or nDCG.
However, it ignores the fact that a search system is often composed by multiple components,
whereas evaluation on the final results hardly reveal useful insights about the system.
It is not straightforward to see the problematic component.

Jina organize such components in a :term:`Flow`.
For this reason, we expect Jina evaluator has the capability to work anywhere in the workflow,
at the :term:`Pod` level.


Before you start
-------------------

We expect you have a clean Python 3.7/3.8/3.9 (virtual) build.
With Jina installed on your machine:

.. highlight:: bash
.. code-block:: bash

    pip install -U jina

Overview
-----------------

Evaluation in Action
----------------------

Conclusion
-----------------

What's next
-----------------