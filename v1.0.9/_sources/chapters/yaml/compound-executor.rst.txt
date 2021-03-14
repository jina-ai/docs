:class:`CompoundExecutor` YAML Syntax
=====================================

A compound executor is a set of executors bundled together, as defined in :mod:`jina.executors.compound`. It follows the syntax above with an additional feature: `routing`.

.. highlight:: yaml
.. code-block:: yaml

    !CompoundExecutor
    components:
    - !NumpyIndexer
      with:
        index_filename: vec.idx
      metas:
        name: my_vec_indexer
    - !BasePbIndexer
      with:
        index_filename: chunk.gzip
      metas:
        name: doc_meta_indexer
    metas:
      name: compound_indexer
      workspace: $TEST_WORKDIR

.. confval:: components

    A list of executors specified. Note that ``metas.name`` must be specified if you want to later attach this specific :term:`Executor`
        to a specific :term:`Driver`.
