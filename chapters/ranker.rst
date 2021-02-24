==========================================
A Guide to Jina Ranker
==========================================

.. meta::
   :description: A guide on Jina Ranker
   :keywords: Jina, Ranker

.. note:: This guide assumes you have a basic understanding of Jina, if you haven't, please check out `Jina 101 <https://101.jina.ai>`_ first.

.. contents:: Table of Contents
    :depth: 2

Motivation
--------------------

In a searching system, query accuracy is one of the most important aspects that users will concern. In most cases, we expect well-ordered query results to be presented.
For one search request, we may have multiple matches and we want to have the most relevant match or probably top-K matches presented to users. On the other hand, we may also want to aggregate the top-K chunks to top-K matches.


Before you start
-------------------

We expect you have a clean Python 3.7/3.8/3.9 (virtual) environment.
Install Jina on your machine:

.. highlight:: bash
.. code-block:: bash

    pip install -U jina


Overview
-----------------

To achieve our objectives, Jina has a family of :term:`Executor` named :term:`Ranker`.
Jina :term:`Ranker` inherits either the :class:`Match2DocRanker` or the :class:`Chunk2DocRanker`.
For the :class:`Match2DocRanker`, it re-scores the matches with different mechanisms. For the :class:`Chunk2DocRanker`, it aggegrates the chunks to documents.


Match2DocRanker
^^^^^^^^^^^^^^^^

:class:`Match2DocRanker` re-scores the matches for a document. This Ranker is only responsible for calculating new scores and not for the actual sorting. The sorting is handled in the respective :class:`Matches2DocRankDriver`.


Chunk2DocRanker
^^^^^^^^^^^^^^^

:class:`Chunk2DocRanker` translates the chunk-wise score to the doc-wise score. If you want to aggregate the already existed top-k chunks into top-k  documents, :class:`Chunk2DocRanker` will be your choice.


Drivers
^^^^^^^^^
Different :term:`Rankers` will need different :term:`Drivers` to be equipped with.
For :class:`Match2DocRanker`, we need to choose :class:`Matches2DocRankDriver` to get the scores from a :term:`Ranker` and help to resort the scores.
For :class:`Chunk2DocRanker`, we need to choose :class:`Chunk2DocRankDriver` to extract matches score from chunks and use the executor to compute the rank and assign the resulting matches to the level above.

Currently, Jina has 3 Rankers in Jina hub.

.. list-table:: Jina Rankers
   :widths: 25 25 50 25 25
   :header-rows: 1

   * - Name
     - Type
     - Description
     - Drivers
     - Documentation
   * - :class:`BiMatchRanker`
     - :class:`Chunk2DocRanker`
     - Computes document relevance givena a query taking into account the best chunk-hit from both query and doc perspective.
     - :class:`Chunk2DocRankDriver`
     - https://github.com/jina-ai/jina-hub/blob/master/rankers/BiMatchRanker/README.md
   * - :class:`SimpleAggregateRanker`
     - :class:`Chunk2DocRanker`
     - aggregates the score of the matched doc from the matched chunks. For each matched doc, the score is aggregated from all the matched chunks belonging to that doc.
     - :class:`Chunk2DocRankDriver`
     - https://github.com/jina-ai/jina-hub/blob/master/rankers/SimpleAggregateRanker/README.md
   * - :class:`LevenshteinRanker`
     - :class:`Match2DocRanker`
     - Computes the Levenshtein distance between a query and its matches.
     - :class:`Matches2DocRankDriver`
     - https://github.com/jina-ai/jina-hub/blob/master/rankers/LevenshteinRanker/README.md


Ranker in action
----------------------

:term:`Ranker` can be used in several ways in Jina.

Run with Docker (docker run)
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

.. highlight:: bash
.. code-block:: bash

    docker run jinahub/pod.ranker.simpleaggregateranker:0.0.13-1.0.4 --port-in 55555 --port-out 55556

Run with Flow API
^^^^^^^^^^^^^^^^^^

.. highlight:: python
.. code-block:: python

     from jina.flow import Flow

     f = (Flow()
         .add(name='my_encoder', uses='docker://jinahub/pod.ranker.simpleaggregateranker:0.0.13-1.0.4'))

Run with Jina CLI
^^^^^^^^^^^^^^^^^^

.. highlight:: bash
.. code-block:: bash

        jina pod --uses docker://jinahub/pod.ranker.simpleaggregateranker:0.0.13-1.0.4


Conventional local usage with uses argument (YAML configuration)
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

.. highlight:: bash
.. code-block:: bash

        jina pod --uses SimpleAggregateRanker.yml

``SimpleAggregateRanker.yml`` can be configured as following:

.. highlight:: yaml
.. code-block:: yaml

    !SimpleAggregateRanker
    with:
      aggregate_function: 'min'
      inverse_score: true
    requests:
      on:
        ControlRequest:
          - !ControlReqDriver {}
        SearchRequest:
          - !Chunk2DocRankDriver {}


Conclusion
-----------------

In this guide, we introduce why we need and how to use :term:`Ranker`. Apart from that, we provide some concrete examples of how to use :term:`Ranker`.

What's next
-----------------

If you still have questions, feel free to `submit an issue <https://github.com/jina-ai/jina/issues>`_ or post a message in our `community slack channel <https://slack.jina.ai>`_ .

To gain a deeper knowledge on the implementation of Jina Ranker, you can find the source code `here <https://github.com/jina-ai/jina/tree/master/jina/executors/rankers>`_.
