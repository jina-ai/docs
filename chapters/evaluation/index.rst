==========================================
A Guide to Jina Flow Evaluation Mode
==========================================

.. meta::
   :description: A guide on Jina Flow Evaluation Mode
   :keywords: Jina, flow evaluation

.. note:: This guide assumes you have a basic understanding of Jina, if you haven't, please check out `Jina 101 <https://docs.jina.ai/chapters/101/index.html>`_ first.

.. contents:: Table of Contents
    :depth: 2

Motivation
--------------------

In Jina, just like in any search system, it's critical to measure system performance.

Generally, evaluation is about computing the difference between an output and ideal results,
users generally place an emphasis on evaluating final document ranking results using information retrieval metrics,
such as Precision, Recall, mAP or nDCG.
However, it ignores the fact that a search system is often composed of multiple components,
whereas evaluation on the final results hardly reveals useful insights about the system. Jina allows the user
to evaluate any part of the system with arbitrary metrics.


Before you start
-------------------

We expect you have a clean Python 3.7/3.8/3.9 (virtual) build.
With Jina installed on your machine:

.. highlight:: bash
.. code-block:: bash

    pip install -U jina


Overview
-----------------

To achieve our objective, Jina has a family of :term:`Executor` named :term:`Evaluator`.
These evaluators are meant to capture Documents from any part of the Flow in order to evaluate them.

As a new type of :term:`Executor`, evaluators inspect documents from the request and comparing them with ground truth.
This executors can be wrapped in a :term:`Pod` as any other kind of executor and placed anywhere in the :term:`Flow`.
They tend to be placed after the :term:`Pod` applying the transformation that wants to be evaluated by the specific `Evaluator`.

In order to be able to Evaluate the performance of a transformation applied to a Document by any part of a Jina
:term:`Flow`, we need to know what the desired state of the :term:`Document` is. This desired state is called
:term:`GroundTruth` and can be passed in Jina in every ``IndexRequest`` and ``SearchRequest``. This :term:`GroundTruth`
in Jina is nothing else than another :term:`Document`.

``IndexRequest`` and ``SearchRequest`` are formed by streams of pairs and `Documents` and `GroundTruths`. When no evaluation
is involved, `GroundTruth` tends to be empty, however when an `Evaluation` pod is involved in the Flow, it will actually
take the information of every `GroundTruth` to feed both `Document` and `GroundTruth` information to the evaluator :term:`Executor`

Evaluation consists of extraction and evaluation.
In Jina, a :term:`Driver` extracts Document and GroundTruth information from a :term:`Protobuf` message,
and pass this information to the executor.
The second steps happens inside the executor: evaluate the difference between these two documents into a number.
Afterwards, the :term:`Driver` will add the results of the evaluation into the `evaluations` field of the `Document`.


Since Evaluation tends to focus only on some small parts of the `Documents` (IDs of the matches when evaluating Rankers,
embedding when evaluating Encoders), it is not needed for `GroundTruth` to contain more information from the `Document` than
the one that will be used by the Evaluators.

It is important to note that the `Documents` inside the `IndexRequest` and `SerchRequest` are transformed by the `Drivers`
inside the `Flow` while the `GroundTruth` is never changed, since is only used to analyze and compare to its paired `Document`
at any point of the `Flow`.

Currently, Jina evaluators can be categorised into **ranking evaluators**, **text evaluators** and **embedding evaluators**,
but these can be extended to evaluate any kind of information inside a `Document`.

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

Evaluation works in parallel with ``IndexRequest`` and ``SearchRequest``.

While `Evaluation` :term:`Pods` can be added at arbitrary points of the :term:`Flow` as any other `Pod`,
the :term:`Flow` API :meth:`inspect` allows users to add pods with close to zero-overhead with the rest of the `Flow`.

.. highlight:: python
.. code-block:: python

    from jina import Flow

    f = Flow(inspect='HANG').add(
        uses='!BaseCrafter', name='crafter').add(
        uses='!BaseEncoder', name='encoder').inspect(
        uses='!BaseEmbeddingEvaluator', name='embed_eval').add(
        uses='!CompoundIndexer', name='indexer').add(
        uses='!BaseRanker', name='ranker').inspect(
        uses='!BaseRankingEvaluator', name='rank_eval')

.. image:: hang.svg

The above example illustrates how the evaluation Pods are introduced using :meth:`inspect` without introducing any side-effect to the flow.

1. The evaluations are running as *side task* in parallel. They deviate from the main task and are not required to complete the request. Thus, it won’t slow down the flow on the main task.
2. Attaching an inspect Pod to the flow does not change the socket type between the original Pod and its neighbours.
3. All inspect Pods can be removed from the Flow by setting ``Flow(inspect='REMOVE')``.

You might noticed that we defined ``Flow(inspect='HANG')`` in the above code example as inspect type.
The :class:`FlowInspectType` has three types: ``HANG``, ``COLLECT`` and ``REMOVE``.
The differences are shown in the figures below:

``Flow(inspect='HANG')``

.. image:: hang.svg

``Flow(inspect='COLLECT')``

.. image:: collect.svg

``Flow(inspect='REMOVE')``

.. image:: remove.svg


Conclusion
-----------------

In this guide, we introduced why we need Jina evaluators,
how we organize Jina evaluators.
Apart from that, we gave some concrete examples of how to use Jina evaluators.

What's next
-----------------

If you still have questions, feel free to `submit an issue <https://github.com/jina-ai/jina/issues>`_ or post a message in our `community slack channel <slack.jina.ai>`_ .

To gain a deeper knowledge on the implementation of Jina Evaluators, you can find the source code `here <https://github.com/jina-ai/jina/tree/master/jina/executors/evaluators>`_.