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

To achieve our objective, we designed a family of :term:`Executor` named :term:`Evaluator`.
These evaluators evaluate messages coming from any kind of executor.
As a new type of :term:`Pod`, evaluators inspect documents from the request and comparing them with ground truth.

The evaluation, in general, follows a two step approach: *diff extraction* and *quantization*.
Jina :term:`Driver` extracts diff information from :term:`Protobuf`,
and pass diff information to executor.
The second steps happens inside the executor: quantize the diffed object to a number.

Jina evaluators can be categorised into **ranking evaluators**, **text evaluators** and **embedding evaluators**.

.. list-table:: Jina Evaluator Types
   :widths: 25 25 50
   :header-rows: 1

   * - Name
     - Example
     - Description
   * - Ranking evaluators
     - Precision, Recall, F1, aP, nDCG, mRR
     - Evaluate messages coming out from Indexers and Rankers and compares matches with groundtruths
   * - Text evaluators
     - Length, Bleu, Edit Distance, Gleu, Hamming Distance, Jaccard Distance
     - Evaluates the difference between actual and desired text
   * - Embedding evaluators
     - Cosine Distance, Euclidean Distance, L1 Norm, Minkowski Distance
     - Evaluates the difference between actual and desired embeddings


Evaluation in Action
----------------------

Conclusion
-----------------

What's next
-----------------