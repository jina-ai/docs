==================================
Tutorial 1
==================================

.. contents:: Table of Contents
    :depth: 3

What is Jina
----------------------------------

Tutorial
----------------------------------

At the end of this tutorial, you will have your own search engine. You will use text as an input and get a matching text as output.
For this example, we will use the `Kaggle Wikipedia Corpus <https://www.kaggle.com/mikeortman/wikipedia-sentences>`_
You will understand how every part of this example works and how you could create new apps with different datasets on your own.

Set-up & overview
----------------------------------

We recommend creating a `new python virtual environment <https://docs.python.org/3/tutorial/venv.html>`_ to have a clean install of Jina and prevent dependency clashing.

Then we can install Jina:

  .. code-block:: python

    pip install jina

For more information on installing Jina, refer to this `page <https://docs.jina.ai/chapters/install/os/via-pip>`_.

And we will also need the following dependencies:

  .. code-block:: python

    pip install click==7.1.2
    pip install transformers==4.1.1
    pip install torch==1.7.1


Once you have Jina installed, let's take a broad overview of what we should do:

.. image:: res/flow.png
   :width: 600

At the beginning of the Flow, you have your data, and this can be any type:

* Audio
* Image
* Video
* Text

In this case, we are using text, so in the image, you see text only. But it can be whatever type you want.

Once we have our data, as usual in Machine Learning, it's probable that you might need to pre-process that data. To keep this as simple as possible for this first tutorial, and since we are using the #TBD (dataset used TBD), we won't need to do any pre-processing. But remember that this is a possibility for another use case.
Once that is ready, we can encode our data into vectors and finally store those vectors, so it is ready for indexing and then querying.

So the steps to build a search engine are:

1. Define data
2. Pre-process it (we won't need this for this example)
3. Encode data
4. Index / Query

As you can see, the last part can be Indexing or Querying, so it means we will need to do these steps twice, one for when we want to index and one for we want to search. So let's see each Flow in detail.

Index Flow
----------------------------------

1. Define data
+++++++++++++++

We can start creating an empty folder, I'll call mine `wikipedia-sentences` and that's the name you'll see through the tutorial but feel free to use whatever you wish.

Now let's create a `/data` folder inside your current working environment. We `download our data <https://www.kaggle.com/mikeortman/wikipedia-sentences>`_, and extract it under the `/data` folder . You should have something like this:

.. image:: res/data.png
   :width: 600

2. Encode data
+++++++++++++++


We have our data ready. What now? Well, we can't use our data directly from its original data type, text in this case. We need first to transform that data into vectors, and this way, it doesn't matter if we have an image, video, text, 3D mesh, or any other type. All of them will be transformed into vector embeddings so we can all treat them the same way.

Let's talk a little bit about the Flow before moving. You can refer to our cookbook to see more details on the `Flow <https://github.com/jina-ai/jina/blob/master/.github/2.0/cookbooks/Flow.md#minimum-working-example>`_, but let's quickly see some details. The most urgent bits are:

1. Create a Flow
2. Add elements to a Flow
3. Use a Flow
3.1. Create a Document. We will need a Document to pass to our Flow
4. Visualize a Flow. This is an extra, but it can be very useful.

1 Create a Flow
*******************
To create a Flow you only need to import it from Jina:

.. code-block:: python

    from jina import Flow
    f = Flow()

But this is an empty Flow, since we want to encode our data and then index it, we need to add elements to it.

2 Add elements to a Flow
***************************

To add elements to your Flow you just need to use the `add` keyword. You can add as many pods as you wish.

.. code-block:: python

    from jina import Flow

    f = Flow().add().add().add()

And for our example, we need to add two elements:

1. A transformer (to encode our data)
2. An indexer

.. code-block:: python

    from jina import Flow
    f = (
            Flow()
            .add(uses=MyTransformer)
            .add(uses=MyIndexer)
        )

Right now we haven't defined `MyTransformer` or `MyIndexer`, we will do that later. But for now, you should understand that this is where you will use the command `add` to add any elements that you will need in your Flow.

So now we have a Flow with two elements. Those elements are two `Executors`. We haven't formally talked about them, but you don't need to know the details yet, so for now don't worry too much about them.

Since we have our Flow ready, the next step is to actually use it.

3 Use a Flow
****************

The correct way to use a Flow is to open it as a context manager, with the `with` keyword:

.. code-block:: python

    with f:
        ...

So let's recap a bit what we have seen:

.. code-block:: python

    from jina import Flow
    f = Flow()          # Create Flow

    f.add().add()       # Add elements to Flow

    with f:             # Use Flow as context manager
        f.index()

So in our example, we have a Flow with two executors (`MyTransformer` and `MyIndexer`) and we want to use our Flow to index our data. But in this case our data is a `csv` file, so we need to open it first

.. code-block:: python

    with f, open('our_dataset.csv']) as fp:
            f.index()

Now we have our Flow ready, we can start to index. But we can't just pass the dataset in the original format to our Flow, we need to create a Document with the data we want to use.

3.1 Create a Document
***************************

To create a Document, we do it like this:

.. code-block:: python

    from jina import Document
    d = Document(content='hello, world!')

But in our case, the content of our Document needs to be the dataset set we want to use, so we do it like this:

.. code-block:: python

    from jina import Document
    d = Document.from_csv(fp, field_resolver={'question': 'text'})

So what happened there? We created a Document `d`, and we uses `from_csv` to load our dataset.
We use `field_resolver` to map the text from our dataset to the Document attributes.

By now, you should have this:

.. code-block:: python
    from jina import Flow, Document

    f = (
            Flow()
            .add(uses=MyTransformer)
            .add(uses=MyIndexer)
        )

    with f, open('our_dataset.csv']) as fp:
        f.index(Document.from_csv(fp, field_resolver={'question': 'text'}))

2.2 Plot a Flow
****************

7. Index and interpret output

Query Flow
----------------------------------
1. Get data
2. Create Document
3. Encode data
4. Query and interpret results. In terminal and Jina Box
