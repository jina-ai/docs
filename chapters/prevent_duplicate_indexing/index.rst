Prevent Indexing Duplicates
---------------------------

Summary
#######

When indexing documents, it is common to have duplicate documents received by the search system. This document will show you how to prevent indexing duplicates in Jina.


Feature Description
###################

One can either remove the duplicates before sending the duplicates to Jina or leave it to Jina for handling the duplicates.

Before you start
################

Make sure you are familiar with `Executor YAML Sytax <https://docs.jina.ai/chapters/yaml/executor.html>`_.


Implementation
##############

Use ``_unique``
***************

To prevent indexing duplicates, one needs to add ``_unique`` for the ``uses_before`` option. In the following example, three Documents are indexed by the Flow. We use `on_done` argument to check how many Documents are indexed after.

.. confval:: Python API

    .. highlight:: python
    .. code-block:: python

        from jina import Flow, Document
        import numpy as np

        doc_0 = Document(text='I am doc0', embedding=np.random.rand(10))
        doc_1 = Document(text='I am doc1', embedding=np.random.rand(10))


        def assert_only_two_docs_indexed(rsp):
            assert len(rsp.index.docs) == 2


        f = Flow().add(
            uses='NumpyIndexer', uses_before='_unique')

        with f:
            f.index(
                [doc_0, doc_0, doc_1],
                on_done=lambda rsp: assert_only_two_docs_indexed(rsp))

Under the hood, the configuration yaml file, :file:``jina/resources/executors._unique.yml``, is used. The yaml file is defined as below


.. confval:: YAML spec

    .. highlight:: yaml
    .. code-block:: yaml
        !DocCache
        with:
          index_path: unique.tmp
        metas:
          name: unique
        requests:
          on:
            [SearchRequest, TrainRequest, IndexRequest, DeleteRequest, UpdateRequest, ControlRequest]:
              - !RouteDriver {}
            IndexRequest:
              - !TaggingCacheDriver
                with:
                  tags:
                    is_indexed: true
              - !FilterQL
                with:
                  lookups: {tags__is_indexed__neq: true}


:class:`jina.executors.indexers.cache.DocCache` uses document ID to detect the duplicates. The documents with the same ID are considered as the same one. :class:`jina.drivers.cache.TaggingCacheDriver` keep a set of the indexed keys and check against the cache for a hit. If the document id exists, :class:`jina.drivers.cache.TaggingCacheDriver` sets the customized keys in the `tags` field to the predefined value. In the above configuration, ``is_indexed`` in the ``tags`` field is set to ``true`` when the document id hit the cached indexed keys. Afterwards, :class:`jina.drivers.querylang.filter.FilterQL` is used to filter out the duplicate documents from the request.


Limitations
###########
Be careful when using `_unique` keyword as a cache executor, it will not set any `workspace` where the data is stored.
By default, it uses the folder where it runs as `workspace`, which may not be where the actual `indexers` store their data. If you want to store the cache in a specific workspace while keeping the same functionality,
    you need to define `unique_customized.yml` as below to set the desired `workspace` under metas.

    .. highlight:: yaml
    .. code-block:: yaml

        !DocCache
        with:
          index_path: cache.tmp
        metas:
          name: cache
          workspace: $WORKSPACE
          ...
