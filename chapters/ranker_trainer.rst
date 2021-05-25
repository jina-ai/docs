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
researchers have developed the Learning-to-Rank technique to allow users to train a ``Ranker`` in a supervised fashion.
In this guide, we will introduce how to train your ``Ranker`` with Jina ``RankerTrainer``.


Before you start
-------------------

* This guide assumes you have a good understanding of Jina, if you haven't, please check out `Jina 101 <https://101.jina.ai>`_ first.
* This guide assumes you have a good understanding of Jina ``Rankers``.
* This guide assumes you have a good understanding of Machine Learning (Supervised Statistical Learning).
* You have installed the latest stable release of Jina Core according to the instructions found `here <https://docs.jina.ai/chapters/core/setup/index.html>`_.

Overview
-----------------

Imagine you are a data scientist, and working on building a model to predict the house price.
You collected historical property selling price,
for each property, you designed a list of features, such as #bedrooms, #size, #primary schools near the property, #supermarkets near the property, etc.
Equipped with labels (house price) and features,
you trained a regression model to predict the house price.

The idea also applies to Search/Retrieval,
usually referred to Learning-to-Rank.
The features can be the score of Bm25, #tokens in the document, #links in the document, etc,
the labels are the user annotated relevance score.
Thus we can train a supervised model to optimize the ``Ranker``.

In Jina, we created a new type of ``Executor`` named ``RankerTrainer``.
The ``RankerTrainer`` needs a user to implement a ``train`` and a ``save`` method.
The ``train`` method takes a machine learning model to train the ``Ranker``,
and the ``save`` method saves the re-trained model into a directory.
This feature could be beneficial to continuously improve our ranking model incrementally.

Ranker Trainer in Action: Optimize a LightGBMRanker
---------------------------------------------------

Context
^^^^^^^

`LightGBM <https://lightgbm.readthedocs.io/en/latest/index.html#>`_ is a gradient boosting framework that uses tree-based learning algorithms.
One of LightGBM's applications is optimize Ranking using the algorithm ``LambdaRank`` to optimize ``nDCG``.

In `Jina Hub <https://github.com/jina-ai/jina-hub/tree/master/rankers/LightGBMRanker>`_, we've created the ``LightGBMRanker``, which allows you to load a LightGBM model with Jina.
In this guideline, we'll demonstrate a simple use case: Optimize the ranking for a online shopping website:

Imagine you're running a online mall that sells shoes, and you want to optimize your product ranking using a list of three features:
`price`, `brand`, and `color`.
The labels are collected `num_clicks` in the past month.
Your training data might looks like this:

.. list-table:: Sample Training Data
   :widths: 50 50 50 50
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

Organize You Training Data
^^^^^^^^^^^^^^^^^^^^^^^^^^^

The features and labels to train the model should be stored in `tags` in a Jina `Document`.
For example:

.. highlight:: python
.. code-block:: python

    from jina import Document

    d1 = Document(tags={'price': 100, 'brand': 'nike', 'color': 'white', 'num_clicks': 768})
    d2 = Document(tags={'price': 600, 'brand': 'addidas', 'color': 'red', 'num_clicks': 54})
    d3 = Document(tags={'price': 68, 'brand': 'asics', 'color': 'black', 'num_clicks': 691})

Train your model using LightGBMRankerTrainer
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

``LightGBMRankerTrainer`` is specifically designed for training a ``LightGBMRanker``.
While using it, please pass the feature names and label name as well as the parameters in the YAML configuration (``train.yml``):

.. highlight:: yaml
.. code-block:: yaml

    # train.yml
    jtype: LightGBMRankerTrainer
    with:
      model_path: './lightgbm-model.txt'
      query_feature_names: ['tags__price', 'tags__brand', 'tags__color']
      match_feature_names: ['tags__price', 'tags__brand', 'tags__color']
      label_feature_name: ['tags__num_clicks']

The meaning of these parameters are:

* ``model_path``: The model you want to optimize, if the ``model_path`` does not exist, the ranker trainer will train the model from scratch. Otherwise will incrementally train the model.
* ``query_feature_names``: Feature names are used to extract from query ``Documents``.
* ``match_feature_names``: Feature names used to extract from match ``Documents``.
* ``label_feature_name``: Feature name used to train the model as the label.

Use RankerTrainer in a Train Flow
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

You might be concerning that now we know the features and labels,
but we haven't specify the ``query`` yet.
Since each ``query`` should have a corresponded list of feature values and label(relevance score).
It works as follows:

.. highlight:: python
.. code-block:: python

    from jina import Document

    queries = ['shoe'] # assume we have one query to train
    for query in queries:
        q = Document(content=query)
        # assume we have three matches as listed in the above table, i.e. nike, addidas and asics.
        m1 = Document(tags={'price': 100, 'brand': 'nike', 'color': 'white', 'num_clicks': 768})
        m2 = Document(tags={'price': 600, 'brand': 'addidas', 'color': 'red', 'num_clicks': 54})
        m3 = Document(tags={'price': 68, 'brand': 'asics', 'color': 'black', 'num_clicks': 691})
        q.matches.extend([m1, m2, m3])
    query_docs = DocumentSet([q])

The above ``DocumentSet`` of query match pairs with tags and labels will be passed into ``Flow``.
If you're familiar with Jina, you should already know that normally we have two ``Flows``: Index and Search.
For the training purpose, we need to add a new flow called ``Train``, and the flow only has one pod: the ``RankerTrainer``:

.. highlight:: yaml
.. code-block:: yaml

    # flow_train.yml
    jtype: Flow
    version: '1'
    pods:
      - name: trainer
        uses: yaml/train.yml

and to use the ``Flow``:

.. highlight:: python
.. code-block:: python

    from jina import Flow

    with Flow.load_config('flow_train.yml')) as f:
        f.train(inputs=query_docs) # the query match pair we created above

As a result, the model will be saved in the ``model_path`` as we specified in the ``Pod`` YAML.
After training, re-run your search ``Flow`` will use the new model.

What's next
-----------------

If you still have questions, feel free to `submit an issue <https://github.com/jina-ai/jina/issues>`_ or post a message in our `community slack channel <https://slack.jina.ai>`_ .