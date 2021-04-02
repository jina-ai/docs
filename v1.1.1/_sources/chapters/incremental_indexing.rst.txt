How to Use Incremental Indexing
====================

Summary
-------
When you might have a huge amount of data to index and the indexing process may take a while, You want to have the partial indexed documents searchable. In this case, you can use incremental indexing.


Feature Description
-------------------
:class:`NumpyIndexer` is the built-in vector indexer shipped with jina. When indexing Documents, :class:`NumpyIndexer` stores the Documents in the file system. After indexing part of the dataset, one can start query the index with other advanced vector indexers, e.g. :class:`FaissIndexer`, :class:`AnnoyIndexer`, and etc. These advanced indexers can be built from the index of :class:`NumpyIndexer` directly.



Implementation
--------------

We start with indexing 5 Document with :class:`NumpyIndexer`. With the following codes, the documents are stored in ``numpy_ws/numpy_vec.gz``.

.. confval:: index_five_docs.py

    .. highlight:: python
    .. code-block:: python

        from jina import Flow, Document
        import numpy as np

        docs = [Document(text=f'doc{idx}', embedding=np.random.rand(10)) for idx in range(5)]

        index_f = Flow().add(uses='numpy_idx.yml')

        with index_f:
            index_f.index(docs)

        index_f.save_config('flow.yml')

.. confval:: numpy_idx.yml

    .. highlight:: yaml
    .. code-block:: yaml

        !NumpyIndexer
        with:
          index_filename: numpy_vec.gz
        metas:
          name: numpy_idx
          workspace: numpy_ws



.. highlight:: text
.. code-block:: text

   ...
   pod0@92386[I]:indexer size: 5 physical size: 0 Bytes
   ...


In the above step, we save the Flow config at ``flow.yml``. Afterwards, we can build a Flow from the same config to load indexed documents and do query.

.. confval:: query_docs.py

    .. highlight:: python
    .. code-block:: python

        query_f = Flow.load_config('flow.yml')

        with query_f:
            query_f.search([Document(text=f'doc{idx}', embedding=np.random.rand(10)), ])

Now you might want to incrementally index another five documents.

.. confval:: incremental_indexing_docs.py

    .. highlight:: python
    .. code-block:: python

        docs = [Document(text=f'doc{idx+5}', embedding=np.random.rand(10)) for idx in range(5)]

        index_f = Flow.load_config('flow.yml')

        with index_f:
            index_f.index(docs)


.. highlight:: text
.. code-block:: text

   ...
   pod0@91600[I]:indexer size: 10 physical size: 3.1 KB
   ...

Limitations
-----------

Query-while-indexing is not supported yet and therefore one can **NOT** doing indexing and querying with the same Flow at the same time.
