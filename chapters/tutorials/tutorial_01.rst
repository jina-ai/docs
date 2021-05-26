==================================
Tutorial 1
==================================

.. contents:: Table of Contents
    :depth: 2

What is Jina?
----------------------------------

Tutorial
----------------------------------

At the end of this tutorial, you will have built your search engine using text. You will use text strings as input and get a matching text string as output.
For this example, you will use the `Kaggle Wikipedia Corpus <https://www.kaggle.com/mikeortman/wikipedia-sentences>`_
You will understand how every part of this example works and how you can create new apps with different datasets on your own.

Set-up & overview
----------------------------------

The first part is to install Jina:

  .. code-block:: python

    pip install jina

And you will also need the following dependencies:

  .. code-block:: python

    pip install click==7.1.2
    pip install transformers==4.1.1
    pip install torch==1.7.1


Once you have Jina installed, let's get a broad overview of what you should do:

.. image:: res/flow.png
   :width: 600

At the beginning of the Flow, you have your data. This can be any type of data:

* Text
* Image
* Audio
* Video

In this case, you are using text, so in the diagram above you only see text. But as explained it can be whatever data type you want.

Once you have your data, as usual in machine learning, you'll probably need to process it. To keep this as simple as possible for this first tutorial, and since you are using the #TBD (dataset used TBD), you won't need to do any pre-processing. But just remember that this is a possibility for other use cases.
Once that is ready, you can encode your data into vectors and finally store those vectors, so they are ready for indexing and then querying.

You will need to:

1. Define your data
2. (Optional) Pre-process your data (not needed for this example)
3. Encode your data
4. Index / Query your data

As you can see, the last part can be either Indexing or Querying, so it means you will need to do these steps twice: Once when you want to Index and once you want to Search. So let's look at each Flow in detail:

Index Flow
----------------------------------

1. Define data
+++++++++++++++

First you need to `download your data <https://www.kaggle.com/mikeortman/wikipedia-sentences>`_, extract it and store it in a `/data` folder. So you should have something like this:

.. image:: res/data.png
   :width: 600

2. Encode data
+++++++++++++++

You have your data ready. So, what now? Well, you can't use your data directly from its original data type, text in this case. You first need to transform that data into vectors. This way, it doesn't matter if you have an image, video, text, 3D mesh, or any other type of data. All of them will be transformed into vector embeddings so you can all treat them the same way.

Let's start creating your Index Flow so you can encode your data there.

To create a Flow all you need is:

.. code-block:: python

    from jina import Flow
    f = Flow()

But this is an empty Flow. Since you want to encode and index your data, you will need your Flow to have these two elements:

1. A transformer (to encode your data)
2. An indexer

.. code-block:: python

    from jina import Flow
    f = (
            Flow()
            .add(uses=MyTransformer)
            .add(uses=MyIndexer)
        )

Right now you haven't defined `MyTransformer` or `MyIndexer` - you will do that later. But for now, you should understand that this is where you will use the command `add` to add elements to your Flow. You can refer to our cookbook on how to create a `Flow <https://github.com/jina-ai/jina/blob/master/.github/2.0/cookbooks/Flow.md#minimum-working-example>`_

In Jina, all data is considered a "Document". A Document could be a snippet of text, a piece of an image, a video clip, a genome, or any other kind of data. Documents are Jina's primitive data type, so any data you work with must be converted into a Document before you can pass it into your Flow:

.. code-block:: python

    from jina import Document
    d = Document(content='hello, world!')

But in your case, the content of your Document needs to be your dataset, so you do it like this:

.. code-block:: python

    from jina import Document
    doc = Document.from_csv(fp, field_resolver={'question': 'text'})

So what happened there? You created a Document `doc`, and you used `from_csv` to load your dataset
and `field_resolver` to map the text from your dataset to the Document attributes.

By now you should have this:

.. code-block:: python
    from jina import Flow, Document

    f = (
            Flow()
            .add(uses=MyTransformer)
            .add(uses=MyIndexer)
        )

    with f, open('our_dataset.csv']) as fp:
        f.index(Document.from_csv(fp, field_resolver={'question': 'text'}))


6. Explain what Flow is and plot
7. Index and interpret output

Query Flow
----------------------------------
1. Get data
2. Create Document
3. Encode data
4. Query and interpret results. In terminal and Jina Box
