*************************
Jina "Hello, World!" 👋🌍
*************************

.. meta::
   :description: Jina "Hello, World!"s
   :keywords: Jina, hello world

.. note:: This guide expects you have a basic understanding of Jina, if you haven't, please check out `Jina 101 <https://docs.jina.ai/chapters/101/index.html>`_ first.

.. contents:: Table of Contents
    :depth: 2


Run the example
====================

There are 2 ways to run this example:

* With Docker
* With Jina installed

If you have Jina installed you don't need any extra dependencies, simply run:

.. highlight:: bash
.. code-block:: bash

    jina hello-world


If you have Docker you don't even Jina installed for this example, simply run:

On MacOS:


.. highlight:: bash
.. code-block:: bash

    docker run -v "$(pwd)/j:/j" jinaai/jina hello-world --workdir /j && open j/hello-world.html

On Linux:

.. highlight:: bash
.. code-block:: bash

    docker run -v "$(pwd)/j:/j" jinaai/jina hello-world --workdir /j && xdg-open j/hello-world.html


.. image:: hello-world-demo.png
   :width: 70%
   :align: center


Overview
====================


With this script you will:

#. Download the **Fashion-MNIST** training and test data
#. :term:`Index<indexing>` 60,000 images from the training set
#. Use random images from the test set as *queries*
#. Retrieve the relevant results
#. After around 1 minute, a web page will open and show results like this:


.. image:: hello-world.gif
   :width: 70%
   :align: center

And the implementation behind it? It's simple:

.. confval:: Python API

    .. highlight:: python
    .. code-block:: python

        from jina.flow import Flow

        f = Flow.load_config('helloworld.flow.index.yml')

        with f:
            f.index_ndarray(fashion_mnist)

.. confval:: YAML spec

    .. highlight:: yaml
    .. code-block:: yaml

        !Flow
        pods:
          encode:
            uses: helloworld.encoder.yml
            parallel: 2
          index:
            uses: helloworld.indexer.yml
            shards: 2


.. confval:: Flow in Dashboard

    .. image:: hello-world-flow.png
       :align: center

All the big words you can name: computer vision, :term:`neural search`, microservices, :term:`indexing`, :term:`querying/searching<searching>`, and :term:`shards` all happened in just one minute!

View "Hello World" in Jina Dashboard
====================================


You can see the the logs and get insight into the health of your Flow with `Jina Dashboard <https://docs.jina.ai/chapters/dashboard/introduction/index.html>`_. In order to do that you’ll need 2 steps:

1. Connect to :term:`JinaD`.
2. Set the right port in **Dashboard**.

You can follow the detailed steps `here <https://docs.jina.ai/chapters/dashboard/connect-jinaD.html>`_.



More Options for "Hello, World"
================================

Intrigued? Play with different options via:


.. highlight:: bash
.. code-block:: bash

    jina hello-world --help


.. argparse::
   :noepilog:
   :ref: jina.parsers.get_main_parser
   :prog: jina
   :path: hello-world



