==========================================
Multi-modal and Cross-modal Search in Jina
==========================================

.. meta::
   :description: Multi-modal and cross-modal search in Jina
   :keywords: Jina, multi-modal search, cross-modal search

.. note:: This guide assumes you have a basic understanding of Jina, if you haven't, please check out `Jina 101 <https://docs.jina.ai/chapters/101/index.html>`_ first.

Jina is a *data type-agnostic framework*, letting you work with any type of data and run cross- and multi-modal search Flows.
To better understand what this implies we first need to understand the concept of *modality*.

.. contents:: Table of Contents
    :depth: 2

Feature description
--------------------

You may think that different modalities correspond to different kinds of data (images and text in this case).
However, this is not accurate.
For example, you can do cross-modal search by searching images from different perspectives,
or searching for matching titles for given paragraph text.
Therefore, we can consider that a modality is related to a given data distribution from which input may come.


For this reason, and to have first-class support for cross and multi-modal search,
Jina offers modality as an attribute in its Document primitive type.
Now that we agreed on the concept of modality,
we can describe cross-modal and multi-modal search.

 - Cross-modal search can be defined as a set of retrieval applications that try to effectively find relevant documents of modality A by querying with documents from modality B.
 - Multi-modal search can be defined as a set of retrieval applications that try to effectively project documents of different modalities into a common embedding space, and find relevant documents with respect to the fusion of multiple modalities

The main difference between these two search modes is that for cross-modal, there is a direct mapping between a single document and a
vector in embedding space, while for multi-modal this does not hold true, since 2 or more documents might be combined into a single vector.

This unlocks a lot of powerful patterns and makes Jina fully flexible and agnostic to what can be searched.

Cross modal search
--------------------

Supporting cross-modal search in Jina is very easy.
A user just needs to properly set the modality field of the input documents and design the Flow in such a way that the queries target the desired embedding space.

We have created an example project that follows the cross-modal search manner.
The `Image Search using Captions <https://github.com/jina-ai/examples/tree/master/cross-modal-search>`_ example allows users to search for images by giving corresponding caption descriptions.

We encode images and its captions (any descriptive text of the image) in separate indexes,
which are later queried in a cross-modal fashion.
It queries the text index using image embeddings and queries the image index using text embeddings.

Multi modal search
--------------------

In order to support multi-modal search and to make it easy to build such applications, Jina provides three new components:

``MultiModalDocument`` is a Document composed by multiple documents with different modalities.
It makes it easy for the client and for the multi-modal Drivers to build and work with these constructions.

``MultiModalEncoder`` is a new family of Executors, derived from the Encoders,
that encodes data coming from multiple modalities into a single embedding vector.

``MultiModalDriver`` is a new Driver designed to extract the expected content from every document inside ``MultimodalDocument`` and to provide it to the executor.

In Jina, we created an example to build a multi-modal search engine for image retrieval using `Composing Text and Image for Image Retrieval <https://github.com/jina-ai/examples/tree/master/multimodal-search-tirg>`_.
We use the `Fashion200k <https://github.com/xthan/fashion-200k>`_ dataset, where the input query is in the form of a clothing image plus some text that describes the desired modifications to the image.

What's Next
--------------------

Thanks for your time & effort while reading this documentation.
Please go to the example projects and start to get your hands dirty!
If you still have questions, feel free to `submit an issue <https://github.com/jina-ai/jina/issues>`_ or post a message in our `community slack channel <https://docs.jina.ai/chapters/CONTRIBUTING.html#join-us-on-slack>`_ .