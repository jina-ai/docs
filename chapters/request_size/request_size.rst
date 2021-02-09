=============================================
 Understanding the Request Size parameter
=============================================

In some use cases, you may wish to vary the number of :term:``Documents`` a single Request will receive. You can achieve this by adjusting the ``request_size`` parameter when setting the Flow. This guide covers the different configuration options Jina offers.

.. contents:: Table of Contents
    :depth: 2

Request Size
------------
Jina defines the ``request_size`` as the parameter on the client side. By adjusting the ``request_size`` in the Flow's API you can define the number of :term:``Documents`` contained in every ``Request``.

Batch Size
------------
Batch size, commonly used in machine learning, often refers to the number of datapoints that we feed into the model in one iteration.

In Jina ``batch_size`` is used by :term:``Driver`` and :term:``Executor`` to guarantee that the :term:``Executor`` processes data in pieces of a specific size.

Before you start
------------------
Make sure you intall latest version of Jina on your local machine.

.. highlight:: bash
.. code-block:: bash

    pip install -U jina

Implementation
--------------------
In order to have a better understanding of the influence of ``request_size`` and how it is used, let's take the following code snippets as examples.

We first import the necessary modules.

.. highlight:: python
.. code-block:: python

    import time
    from typing import Iterator

    import numpy as np
    from jina import Document
    from jina.executors.crafters import BaseCrafter
    from jina.flow import Flow

Then we define a ``SimpleCrafter`` which will just forward the data.

.. highlight:: python
.. code-block:: python

    class SimpleCrafter(BaseCrafter):
        def craft(self, id, *args, **kwargs):
            return {'id': id}


For this example, we will index 100 documents and use 10 parallel :term:``Crafters``. The ``request_size`` is set to 20. So the 100 :term:``Documents`` will be divided into 5 parts and each ``Request`` contains 20 :term:``Documents``.

.. highlight:: python
.. code-block:: python

    def main():

        request_size=20
        start_time = time.time()
        f = Flow(runtime='process').add(
            name='simple_crafter',
            uses='SimpleCrafter',
            parallel=10)
        with f:
            f.index_ndarray(np.random.random([100, 10]), request_size=request_size)
        end_time = time.time()
        seconds_elapsed = end_time - start_time
        print(seconds_elapsed)


    if __name__ == '__main__':
        main()


Choosing different request size
------------------------------
Different settings of ``request_size`` may influence the running performance. A higher value means a large number :term:``Documents`` will be fed into the :term:``Pea`` and will demand more memory. A lower value will decrease the cost of memory but may increase the running time since we need to send more ``requests``.

A simple extension of the above example generates a box plot showing the relationship between ``request_size`` and running time when we have 100 :term:``Documents`` to be indexed. This may help you to get more insights on choosing the ``request_size``.

.. image:: request_size_runtime.png
    :alt: request_size vs running time