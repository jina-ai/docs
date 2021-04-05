================================================
Understanding Jina Executors and How to Use Them
================================================

.. meta::
   :description: A Guide on What Are Jina Executors and How to Use Them
   :keywords: Jina, Executors, Drivers

.. note:: This guide assumes you have a basic understanding of Jina, if you haven't, please check out `Jina 101 <https://101.jina.ai>`_ first.

.. contents:: Table of Contents
    :depth: 2

Motivation
--------------------

In Jina the Executors represent an algorithmic class and they are a key part to take the full potential of Jina. We have `several of them <https://docs.jina.ai/chapters/all_exec.html>`_  and they all live in the `Hub <https://hub.jina.ai/#/home>`_, facilitating the work-flow while creating your app. Here we will see what exactly are they and how you can use them.


Before you start
-------------------

You have installed the latest stable release of Jina Core according to the instructions found `here <https://docs.jina.ai/chapters/core/setup/index.html>`_ and read `Understand Jina Recursive Document Representation guide. <https://docs.jina.ai/chapters/traversal.html?highlight=recursive>`_

Overview
-----------------

In Jina we want to facilitate your work while building up a search system by managing a lot of the heavy work on the background, but at the same time we want to maintain Jina Core as light-weight and with the least dependencies as possible. This is usually a difficult trade-off, and for this we wanted to keep that heavy-work separate from the Core. This is where the :term:`Executors` come into play. Think of them as the ones managing all the algorithmic part, so you can use connect them to your :term:`Flow` without having to understand everything that is happening under the hood.

Executors
^^^^^^^^^^^^^^^

In Jina we have several :term:`Executors`:

1. Crafter
2. Segmenter
3. Encoder
4. Indexer
5. Ranker
6. Classifier
7. Evaluator

All of them are specialized in one task and take care only of that, isolating the workflow. This is possible because each Executor is wrapped up by a :term:`Pea` as you can see here:

|

.. image:: /chapters/images/pea_exec_driver.png
	:width: 60%

|

It can also be the case that you need multiple executors at once, and for that you can use a `CompoundExecutor`. With this you can chain a pipeline of executors, where the input of the current is the output of the former.

We can see an example to make it more clear:

.. highlight:: python
.. code-block:: python

    !CompoundExecutor
    components:
      - !NumpyIndexer
        with:
          index_filename: vec.gz
        metas:
          name: vecidx_exec  # a customized name
          workspace: ${{TEST_WORKDIR}}
      - !BinaryPbIndexer
        with:
          index_filename: doc.gz
        metas:
          name: docidx_exec
          workspace: ${{TEST_WORKDIR}}
    metas:
      name: doc_compound_indexer
      workspace: ${{TEST_WORKDIR}}
    requests:
      on:
        SearchRequest:
          - !VectorSearchDriver
            with:
              executor: vecidx_exec
        IndexRequest:
          - !VectorIndexDriver
            with:
              executor: vecidx_exec
        ControlRequest:
          - !ControlReqDriver {}

In this example we have a `CompoundExecutor` that chains together a `NumpyIndexer` and a `BinaryPbIndexer`.


The problem now is how can they communicate the data they are processing. And this is where we meet the :term:`Driver`.

Drivers
^^^^^^^^^^^^^^^

The :term:`Driver` are the ones that handle the input and output messages from the :term:`Executor`.

As well as we have different Executors for different tasks, we also need different Drivers for different Executors, and you can find the `list here <https://docs.jina.ai/chapters/all_driver.html>`_.

Executors in action
----------------------

:term:`Executors` can be used in several ways in Jina.

Like we said, we have seven types of Execturos in Jina, so let's see an example with one of them and how we could see it in action.

Run with Docker (docker run)
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

First Let's use a `Ranker <https://docs.jina.ai/chapters/ranker>`_ just for this example, but this can be used for any other type of Exectuor that you need.


.. highlight:: bash
.. code-block:: bash

    docker run jinahub/pod.ranker.simpleaggregateranker:MODULE_VERSION-JINA_VERSION --port-in 55555 --port-out 55556

Run with Flow API
^^^^^^^^^^^^^^^^^^

Another way to use them is directly in your Python file. Let's use a :term:`Crafter` this time, for example the `Image Normalizer <https://github.com/jina-ai/jina-hub/tree/master/crafters/image/ImageNormalizer>`_

.. highlight:: python
.. code-block:: python

     from jina.flow import Flow

     f = (Flow().add(name='my_crafter', uses='docker://jinahub/pod.crafter.imagenormalizer:MODULE_VERSION-JINA_VERSION'))
     # Or use YAML file.
     #f = (Flow().add(name='my_crafter', uses='imagenormalizer.yml'))

Run with Jina CLI
^^^^^^^^^^^^^^^^^^

Or directly with Jina CLI. Now let's use an Indexer, the `RedisBDIndexer <https://github.com/jina-ai/jina-hub/tree/master/indexers/keyvalue/RedisDBIndexer>`_

.. highlight:: bash
.. code-block:: bash

        jina pod --uses docker://jinahub/pod.indexer.redisdbindexer:MODULE_VERSION-JINA_VERSION

Conclusion
-----------------

In this guide, we introduced why we need and how to use :term:`Executors` and how they need :term:`Drivers` to communicate. Apart from that, we provided some concrete examples of how to use them. Now that you now what they are and how to use them, you might be wondering how to create them, we have a guide on that for `Executors <https://docs.jina.ai/api/jina.executors.html>`_ and `Drivers <https://docs.jina.ai/api/jina.drivers.html>`_.

What's next
-----------------

If you still have questions, feel free to `submit an issue <https://github.com/jina-ai/jina/issues>`_ or post a message in our `community slack channel <https://slack.jina.ai>`_ .

To gain a deeper knowledge on the implementation of Jina Ranker, you can find the source code `here <https://github.com/jina-ai/jina/tree/master/jina/executors/rankers>`_.
