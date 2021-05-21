===============================
A guide on Jina Ranker Trainer
===============================

.. meta::
   :description: A guide on Jina Ranker Trainer
   :keywords: Jina, Ranker Trainer

.. contents:: Table of Contents
    :depth: 2

Motivation
--------------------

In a search system, query accuracy is one of the most important aspects that will concern users.
In most cases, we expect well-ordered query results to be presented.
For one search request, we may have multiple matches and we want to have the most relevant match or top-K matches presented to users.

In the Information Retrieval and Machine Learning community,
researchers has developed Learning-to-Rank technique to allow users to train a ``Ranker`` in a supervised fashion.
In this guide, we will introduce how to train your ``Ranker`` with Jina ``RankerTrainer``.


Before you start
-------------------

* This guide assumes you have a good understanding of Jina, if you haven't, please check out `Jina 101 <https://101.jina.ai>`_ first.
* This guide assumes you have a good understanding of Jina ``Rankers``.
* This guide assumes you have a good understanding of Machine Learning (Supervised Statistical Learning).
* You have installed the latest stable release of Jina Core according to the instructions found `here <https://docs.jina.ai/chapters/core/setup/index.html>`_.

Overview
-----------------

Imaging you are a data scientist, and working on building a model to predict the house price.
You collected historical property selling price,
for each property you designed a list of features, such as #bedrooms, #size, #primary schools near property, #supermarkets near property etc.
Equipped with labels (house price) and features,
you trained a regression model to predict the house price.

The idea also applies to Search/Retrieval,
usually referred to Learning-to-Rank.
The features can be the score of Bm25, #tokens in the document, #links in the document etc,
the labels are the user annotated relevance score.
Thus we can train a supervised model to optimise the ``Ranker``.

In Jina, we created a new type of ``Executor`` named ``RankerTrainer``.
The ``RankerTrainer`` needs user to implement a `train` and a `save` method.
The ``train`` method takes a machine learning model to train the ``Ranker``,
and ``save`` method saves the re-trained model into a directory.
This feature could be beneficial to continuously improve our ranking model in an incremental manner.

Ranker Trainer in Action: Optimize a LightGBMRanker
---------------------------------------------------

`LightGBM <https://lightgbm.readthedocs.io/en/latest/index.html#>`_ is a gradient boosting framework that uses tree based learning algorithms.
One of LightGBM's application is optimize Ranking using the algorithm ``LambdaRank`` to optimize ``nDCG``.

In `Jina Hub <https://github.com/jina-ai/jina-hub/tree/master/rankers/LightGBMRanker>`_, we've created the ``LightGBMRanker``, which allows you to load a LightGBM model with Jina.
In this guideline, we'll demonstrate a simple use case: Optimize the ranking for a online shopping website:

Imaging you're running a online-mall sells shoes, and you want to optimize your product ranking using a list of three features:
`price`, `brand` and `color`.
The labels are collected `num_clicks` in the past month.
Your training data might looks like this:

.. list-table:: List of Jina Data Types
   :widths: 50 25 50 50
   :header-rows: 1

   * - price
     - brand
     - color
     - num_clicks
   * - 100
     - nike
     - white
     - 768
   * - 600
     - addidas
     - red
     - 54
   * - 68
     - asics
     - black
     - 691
   * - ...
     - ...
     - ...
     - ...








What's next
-----------------

If you still have questions, feel free to `submit an issue <https://github.com/jina-ai/jina/issues>`_ or post a message in our `community slack channel <https://slack.jina.ai>`_ .

To gain a deeper knowledge of the implementation of Jina Ranker, you can find the source code `here <https://github.com/jina-ai/jina/tree/master/jina/executors/rankers>`_.
