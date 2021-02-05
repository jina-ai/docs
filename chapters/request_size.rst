=================
Request Size
=================

``request_size`` is the parameter that we could set for the Flow.
It defines the number of data sent into parallel ``Peas``.

.. contents:: Table of Contents
    :depth: 2

Request Size
------------
Jina defines the ``request_size`` as the parameter at client side. By setting the ``request_size`` in Flow's API
we could define the number of data sent to each ``Peas``.

Batch Size
------------
Batch size, commonly used in machine learning, often refers to the number of data that we feed into
the model in one iteration.

In Jina, ``batch_size`` is used at ``Driver`` and ``Executor`` level. Where
we align the usage of machine learning terms for ``batch_size``.

Before you start
------------------
Make sure you intall Numpy and latest version of Jina on your local machine.

.. highlight:: bash
.. code-block:: bash

    pip install numpy
    pip install -U jina

Implementation
--------------------
In order to have a better understanding of the influence of ``request_size`` and how it works, let's take the following
codes as example.

We first import the necessary modules.

.. highlight:: python
.. code-block:: python

    import time
    from typing import Iterator

    import numpy as np
    from jina import Document
    from jina.executors.crafters import BaseCrafter
    from jina.flow import Flow

Then we define a simple crafter which will just forward the data.

.. highlight:: python
.. code-block:: python

    class SimpleCrafter(BaseCrafter):
        def craft(self, id, *args, **kwargs):
            return {'id': id}


Next we define the data generator.

.. highlight:: python
.. code-block:: python

    def random_docs(num_docs, chunks_per_doc=5, embed_dim=10, jitter=1, start_id=0, embedding=True) -> Iterator['Document']:
        next_chunk_doc_id = start_id + num_docs
        for j in range(num_docs):
            doc_id = start_id + j

            d = Document(id=doc_id)
            d.text = b'hello world'
            d.tags['id'] = doc_id
            if embedding:
                d.embedding = np.random.random([embed_dim + np.random.randint(0, jitter)])
            d.update_content_hash()

            for _ in range(chunks_per_doc):
                chunk_doc_id = next_chunk_doc_id

                c = Document(id=chunk_doc_id)
                c.text = 'i\'m chunk %d from doc %d' % (chunk_doc_id, doc_id)
                if embedding:
                    c.embedding = np.random.random([embed_dim + np.random.randint(0, jitter)])
                c.tags['parent_id'] = doc_id
                c.tags['id'] = chunk_doc_id
                c.update_content_hash()
                d.chunks.append(c)
                next_chunk_doc_id += 1

            yield d


For this example, we will index 100 documents and use 10 parallel crafters. The ``request_size``
is set as 20. So the 100 documents will be divided into 5 parts and then distributed to the crafters.

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
            f.index(input_fn=random_docs(100), request_size=request_size)

        end_time = time.time()

        seconds_elapsed = end_time - start_time
        print(seconds_elapsed)


    if __name__ == '__main__':
        main()


Choose different request size
--------------------
Different setting of ``request_size`` may influence the running performance.
A higher value means large size data will be fed into the ``Pea`` and will demand more memory.
A lower value will decrease the cost of memory but may increase the running time.

An simple extension of the above example generate a box plot showing the relationship between ``request_size`` and running time
which may help you to get more insights on choosing the ``request_size``.

.. image:: request_size_runtime.png
    :alt: request_size vs running time