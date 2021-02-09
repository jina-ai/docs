================================================
Jina 102: How basic components work together
================================================


In Jina 102, you’ll learn how Jina’s basic components work together. Before you start, make sure you’ve already read through `Jina 101`_.

.. _Jina 101: https://101.jina.ai 


.. contents:: Table of Contents
    :depth: 3


Search workflows
==================


First, we’ll show you how a Flow works in Jina. Be aware, there are many different ways to build a Flow, and this is just one way.

Let’s use a simple text-to-text search application as an example — searching for sentences in a book. We use two Flows for this:

*   An **indexing** Flow makes the whole book searchable by sentence
*   A **querying** Flow handles a user query (in this case, a sentence) and returns search results

A working example can be found `here`_. (Bear in mind that this example uses pre-processed sentences, so the segmenting step is skipped)

The indexing Flow prepares data to be searched. Input Documents are fed in, processed, and output at the other end as searchable indexes in storage:

.. _here: https://github.com/jina-ai/examples/tree/master/wikipedia-sentences


.. image:: /chapters/102/images/index_flow.png 


Then, the querying Flow takes a user query as its input Document, and returns a list of ranked matches:

.. image:: /chapters/102/images/query_flow.png 


Inside a Flow
-----------------

Here, we’ll use the indexing Flow as an example:

.. image:: /chapters/102/images/flow_pods.png 

In our Flow, different Pods perform different tasks. Documents (in this case a book) are:

1. Input Documents segmented into chunks (sentences)
2. Chunks transformed into vector embeddings
3. Chunks indexed in key-value pairs and saved to storage

Inside a Pod
----------------------

.. image:: /chapters/102/images/pod_peas.png 

In the Segmenter Pod, there’s only one Pea. But sometimes you group multiple copies of the same Peas in a Pod to increase reliability or performance. To implement sharing and parallelization, check `this section`_.

.. _this section: link to sharing section


Inside a Pea
----------------------


Let’s take a closer look at the Pea in the Segmenter Pod:

.. image:: /chapters/102/images/pea-exec_driver.png 

As you can see, a Pea is a wrapper for an Executor and its Driver. 

While Peas and Pods perform the communication in a Flow, the messages themselves come from the Executors. And each Executor needs a specific Driver to handle its input and output messages.

You can see an example of how Flow, Pods, Peas, Executors, and Drivers work together in our `Wikipedia sentence search example`_.

.. _Wikipedia sentence search example: https://github.com/jina-ai/examples/tree/master/wikipedia-sentences


YAML Configuration
=======================

From Flows to Executors, every part of Jina is configurable with YAML files. YAML files let you change the behavior of an object without touching its code.

.. image:: /chapters/102/images/yaml.png 

Besides YAML, you can also design Flows in `our Dashboard`_ or `Python code`_.

.. _our Dashboard: link to Dashboard section

.. _Python code: link to IO section

Search Modality
=====================
Our example above shows searching using a single type of data, but what about going further?


Cross-modal search
--------------------

An example of cross-modal search is using text to search for images, or the other way around. 


.. image:: /chapters/102/images/cross.png 


Multi-modal search
----------------------

You can use multiple modalities as input for your search applications. For example, a search query input can be an image plus some descriptive text.

.. image:: /chapters/102/images/multi.png 

To implement cross-modality or multi-modality, visit the `dedicated section`_ in our documentation.

.. _dedicated section: link to cross- multi- modality


JinaD
==================

`JinaD`_ (Jina Daemon) enables orchestration and management of Jina Flows in distributed search systems through an API endpoint.

.. _JinaD: link to jina D



Jina Suite
==================

Jina Suite consists of several different open-source products - **Core, Hub, Dashboard and Box**.

What you’ve learned in the previous sections are the most important components in Core (Jina Core). But there’s more to explore.


.. image:: /chapters/102/images/suite.png


Core
------

You construct and manage your search workflows in Core. It consists of distributed microservices (Flows, Peas, Pods, and Executors, etc.) Optionally, you can use JinaD to orchestrate these microservices.


Hub
------


`Jina Hub`_ is a centralized registry for the developer community. You can share and discover customized Jina Pods or Apps. 


.. _Jina Hub: link to Hub section


Dashboard
-----------


`Jina Dashboard`_ is a low-code monitoring and management environment for Jina. With Dashboard you can:

.. _Jina Dashboard: link to Dashboard

*   Build your Flows
*   Monitor log stream
*   Browse Hub images


Box
------

`Jina Box`_ is a frontend for Jina. It’s a lightweight, customizable omnibox. You can use it to search text, images, videos, audio or any kind of data. 

.. _Jina Box: link to Box section