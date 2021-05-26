==================================
Tutorial 1
==================================

.. contents:: Table of Contents
    :depth: 3


We will use the hello world chatbot for this tutorial. You can find the complete code `here <https://github.com/jina-ai/jina/tree/master/jina/helloworld/chatbot>`_ but we will go step by step in this tutorial.

At the end of this tutorial, you will have your own chatbot. You will use text as an input and get a matching text as output.
For this example, we will use a `covid dataset <https://www.kaggle.com/xhlulu/covidqa>`_.
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

Let's see each part of our Flow in detail.

Tutorial
---------

Define data and work directory
++++++++++++++++++++++++++++++++++++

We can start creating an empty folder, I'll call mine `tutorial` and that's the name you'll see through the tutorial but feel free to use whatever you wish.

We will display our results in our browser, so download the `static` folder from `here <https://github.com/jina-ai/jina/tree/master/jina/helloworld/chatbot/static>`_, and paste it in in your `tutorial` folder. We will use a dataset in a .csv format. I'll use the `COVID <https://www.kaggle.com/xhlulu/covidqa>`_ dataset from Kaggle. You don't need to download this by hand, we'll do it in our code.

Create a Flow
++++++++++++++++++++++++++++++++++++

To create a Flow you only need to import it from Jina:

.. code-block:: python

    from jina import Flow
    f = Flow()

But this is an empty Flow, since we want to encode our data and then index it, we need to add elements to it.

Add elements to a Flow
++++++++++++++++++++++++++++++++++++

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

Right now we haven't defined `MyTransformer` or `MyIndexer`, let's create some dummy `Executors` so we can try our code.

Create dummy Executors
++++++++++++++++++++++++++++++++++++

So now we have a Flow with two elements. Those elements are two `Executors`. We haven't formally talked about them, but for the moment let's see a very basic example of them:

.. code-block:: python

    class MyTransformer(Executor):
        def foo(self, **kwargs):
            print(f'foo is doing cool stuff: {kwargs}')

    class MyIndexer(Executor):
        def bar(self, **kwargs):
            print(f'bar is doing cool stuff: {kwargs}')

We will have more complex Executors later, for now, the only important part for you to understand is that you can create any Executor you want inheriting from the `Executor` class.
In this case, our two executors are only printing some information.

It's been a lot of information so far, so let's run this to see what happens.

.. image:: res/executors_print.png
   :width: 600

If you run this you should see something similar to this. Somewhere in the output, you should see the messages we defined in our Executors, along with its information.

Since we have our Flow ready, but sometimes it can get messy if we start adding many elements to it. So it is very useful to have a tool to visualize our Flow.


Visualize a Flow
++++++++++++++++++++++++++++++

By now, you should have this:

.. code-block:: python
    from jina import Flow, Document

    class MyTransformer(Executor):
        def foo(self, **kwargs):
            print(f'foo is doing cool stuff: {kwargs}')

    class MyIndexer(Executor):
        def bar(self, **kwargs):
            print(f'bar is doing cool stuff: {kwargs}')

    f = (
            Flow()
            .add(uses=MyTransformer)
            .add(uses=MyIndexer)
        )

But what if you want to visualize your Flow? you can do that with `plot`. For example:

.. code-block:: python

    from jina import Flow

    f = (
            Flow()
            .add(uses=MyTransformer)
            .add(uses=MyIndexer)
            .plot('our_flow.svg')
        )

Let's run the code we have so far. If you try it, not much will happen since we are not indexing anything yet, but you will see the new file `our_flow.svg` created on your working folder, and if you open it you would see this:

.. image:: res/plot_flow1.png
   :width: 600

You can see a Flow with two pods, but what if you have many pods? this can quickly become very messy, so it is best practice to name all the Executors with `name='CoolName`. So in our example, we use:

.. code-block:: python

    from jina import Flow

    f = (
            Flow()
            .add(name='MyTransformer', uses=MyTransformer)
            .add(name='MyIndexer', uses=MyIndexer)
            .plot('our_flow.svg')
        )

Now if you run this, you should have a Flow that is more explicit:

.. image:: res/plot_flow2.png
   :width: 600


Use a Flow
++++++++++++++++++++++++++++++++++++

Ok, we have our Flow created and visualized. Let's put it to use now. The correct way to use a Flow is to open it as a context manager, with the `with` keyword:

.. code-block:: python

    with f:
        ...

Let's recap a bit what we have seen:

.. code-block:: python

    from jina import Flow
    f = Flow()          # Create Flow

    f.add().add()       # Add elements to Flow
    f.plot()            # Plot a Flow

    with f:             # Use Flow as a context manager
        f.index()

In our example, we have a Flow with two executors (`MyTransformer` and `MyIndexer`) and we want to use our Flow to index our data. But in this case, our data is a `csv` file, so we need to open it first

.. code-block:: python

    with f, open('our_dataset.csv') as fp:
            f.index()

Now we have our Flow ready, we can start to index. But we can't just pass the dataset in the original format to our Flow, we need to create a Document with the data we want to use.

Create a Document
++++++++++++++++++++++++++++++++++++
To create a Document, we do it like this:

.. code-block:: python

    from jina import Document
    d = Document(content='hello, world!')

But in our case, the content of our Document needs to be the dataset we want to use, so we do it like this:

.. code-block:: python

    from jina import Document
    d = Document.from_csv(fp, field_resolver={'question': 'text'})

So what happened there? We created a Document `d`, and we use `from_csv` to load our dataset.
We use `field_resolver` to map the text from our dataset to the Document attributes.

Get our data
++++++++++++++++++++++++++++++++++++

We have everything ready to use our Flow, but so far we have been using dummy data, let's download our dataset now. We will use this snippet and we don't need to go into the details for this. What it does is to download the `covid dataset <https://www.kaggle.com/xhlulu/covidqa>`_.

.. code-block:: python

    def download_data(targets, download_proxy=None, task_name='download covid-dataset'):
    """
    Download data.

    :param targets: target path for data.
    :param download_proxy: download proxy (e.g. 'http', 'https')
    :param task_name: name of the task
    """
    opener = urllib.request.build_opener()
    opener.addheaders = [('User-agent', 'Mozilla/5.0')]
    if download_proxy:
        proxy = urllib.request.ProxyHandler(
            {'http': download_proxy, 'https': download_proxy}
        )
        opener.add_handler(proxy)
    urllib.request.install_opener(opener)
    with ProgressBar(task_name=task_name, batch_unit='') as t:
        for k, v in targets.items():
            if not os.path.exists(v['filename']):
                urllib.request.urlretrieve(
                    v['url'], v['filename'], reporthook=lambda *x: t.update_tick(0.01)
                )

Let's re-organize our code a little bit. First, we should import everything we need:

.. code-block:: python

    import os
    import urllib.request
    import webbrowser
    from pathlib import Path

    from jina import Flow, Executor
    from jina.logging import default_logger
    from jina.logging.profile import ProgressBar
    from jina.parsers.helloworld import set_hw_chatbot_parser
    from jina.types.document.generators import from_csv

Then we should have our `main`, a `donwload_data` function to get our data and a `tutorial` function for all the rest

.. code-block:: python

    def download_data(targets, download_proxy=None, task_name='download covid-dataset'):
        #This is exactly as the previous snippet we just saw

    def tutorial(args):
        #Here we will have everything for our tutorial

    if __name__ == '__main__':
        args = set_hw_chatbot_parser().parse_args()
        tutorial(args)

Now let's see our `tutorial` function with all the code we've done so far:

.. code-block:: python

    def tutorial(args):
        Path(args.workdir).mkdir(parents=True, exist_ok=True)

        class MyTransformer(Executor):
            def foo(self, **kwargs):
                print(f'foo is doing cool stuff: {kwargs}')

        class MyIndexer(Executor):
            def bar(self, **kwargs):
                print(f'bar is doing cool stuff: {kwargs}')

        targets = {
            'covid-csv': {
                'url': args.index_data_url,
                'filename': os.path.join(args.workdir, 'dataset.csv'),
            }
        }

        # download the data
        download_data(targets, args.download_proxy, task_name='download covid-dataset')

        f = (
            Flow()
                .add(name='MyTransformer', uses=MyTransformer)
                .add(name='MyIndexer', uses=MyIndexer)
                .plot('test.svg')
        )

        with f, open(targets['covid-csv']['filename']) as fp:
            f.index(from_csv(fp, field_resolver={'question': 'text'}))

If you run this, it should finish without errors. You won't see much yet because we are not showing anything after we index. But you should see a new folder created with the downloaded dataset:

.. image:: res/downloaded_dataset.png
   :width: 600

To actually see something we need to specify where we will see it, we will display it in our browser, so we need to add the following after indexing:

.. code-block:: python

        f.use_rest_gateway(args.port_expose)

        url_html_path = 'file://' + os.path.abspath(
            os.path.join(
                os.path.dirname(os.path.realpath(__file__)), 'static/index.html'
            )
        )
        try:
            webbrowser.open(url_html_path, new=2)
        except:
            pass  # intentional pass, browser support isn't cross-platform
        finally:
            default_logger.success(
                f'You should see a demo page opened in your browser, '
                f'if not, you may open {url_html_path} manually'
            )

        if not args.unblock_query_flow:
            f.block()

For more information on what the Flow is doing, specially in `f.use_rest_gateway(args.port_expose)` and `f.block()` check our `cookbook <https://github.com/jina-ai/jina/blob/master/.github/2.0/cookbooks/Flow.md>`_

Ok, so it seems that we have work done already. If you run this you will see a new tab in your browser open, and there you will have a text box ready for you to input some text. However, if you try to enter anything you won't have any results. This is because we are using very dummy Executors. Our `MyTransformer` and `MyIndexer` aren't actually doing anything. So far they only print a line when they are called. So we need real `Executors`.

This has been already plenty of new information you've learned so far, so we won't go into `Executors` today, instead you can copy-paste the ones we are using for `this example <https://github.com/jina-ai/jina/blob/master/jina/helloworld/chatbot/executors.py>`_. The important part for you to understand is that it's here where you'll define exactly what you want your `Executors` to do. It can be something as simple as printing a line as we did today. Or something more complex as in the example.

To try the `Executors` from the Github repo, just add this before the `download_data` function:

.. code-block:: python

    if __name__ == '__main__':
        from executors import MyTransformer, MyIndexer
    else:
        from .executors import MyTransformer, MyIndexer

And remove the dummy executors we made.

And we are done!!! If you followed all the steps, now you should have something like this in your browser:

.. image:: res/results.png
   :width: 600

There are still a lot of concepts to learn. So stay tuned for the next tutorials we'll have.

If you have any issues following this tutorial, you can always get support from our [Slack community](https://join.slack.com/t/jina-ai/shared_invite/zt-dkl7x8p0-rVCv~3Fdc3~Dpwx7T7XG8w).

Community
----------------------------------

- [Slack channel](slack.jina.ai) - a communication platform for developers to discuss Jina.
- [LinkedIn](https://www.linkedin.com/company/jinaai/) - get to know Jina AI as a company and find job opportunities.
- [![Twitter Follow](https://img.shields.io/twitter/follow/JinaAI_?label=Follow%20%40JinaAI_&style=social)](https://twitter.com/JinaAI_) - follow us and interact with us using hashtag `#JinaSearch`.
- [Company](https://jina.ai) - know more about our company, we are fully committed to open-source!

License
----------------------------------

Copyright (c) 2021 Jina AI Limited. All rights reserved.

Jina is licensed under the Apache License, Version 2.0. See [LICENSE](https://github.com/jina-ai/jina/blob/master/LICENSE) for the full license text.
