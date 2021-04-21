Use Sparse Embedding in Jina
==============================

.. meta::
   :description: Sparse Embedding in Jina
   :keywords: Jina, sparse, coo, csr

.. contents:: Table of Contents
    :depth: 2

Motivation
------------

A sparse matrix is a special case of a matrix in which the number of zero elements is much higher than the number of non-zero elements.
The space used for representing data and the time for scanning the matrix can be reduced significantly using a sparse representation.
In this guideline, we'll introduce how to use sparse matrix in Jina.

Before you start
----------------

Before you begin, make sure you meet these prerequisites:

-  Make sure you have basic understanding on Jina.
-  Make sure you have basic understanding on sparse matrices.
-  We assume you have some experience with `sparse module in scipy <https://docs.scipy.org/doc/scipy/reference/sparse.html>`_, or you have used `Tnesorflow Sparse Tensor <https://www.tensorflow.org/api_docs/python/tf/sparse/SparseTensor>`_ / `PyTorch Sparse COO Tensor <https://pytorch.org/docs/stable/sparse.html#sparse-coo-tensors>`_.

Behind Jina Sparse Matrix
-------------------------

In Jina, we support three `backends` to create your sparse matrix/Tensor:
`Scipy`, `Tensorflow` and `Pytorch`.
You might noticed that `Scipy.sparse` supports different sparse formats,
while Jina only supports `COO`, `BSR`, `CSR` and `CSC`.

When creating your own sparse matrix,
we suggest you use `COO` as default matrix type.

.. list-table:: Sparse Matrix Formats
   :widths: 25 25 25 25 25
   :header-rows: 1

   * - ShortName
     - FullName
     - Scipy
     - Tensorflow
     - Pytorch
   * - COO
     - COOrdinate format
     - Yes
     - Yes
     - Yes
   * - BSR
     - Block Sparse Row matrix
     - Yes
     - No
     - No
   * - CSC
     - Compressed Sparse Column matrix
     - Yes
     - No
     - No
   * - CSR
     - Compressed Sparse Row matrix
     - Yes
     - No
     - No
   * - DIA
     - Sparse matrix with DIAgonal storage
     - Yes
     - No
     - No
   * - DOK
     - Dictionary Of Keys based sparse matrix.
     - Yes
     - No
     - No
   * - LIL
     - Row-based list of lists sparse matrix
     - Yes
     - No
     - No


Define your Jina Sparse Encoder
-----------------------------------

After defining your Jina `Document` with Jina's Primitive Types,
You need to use a sparse encoder to encode the `content` into a sparse representation,
for instance, a Scipy `COO` matrix.
We'll create a simple COO Encoder first

.. highlight:: python
.. code-block:: python

    from scipy.sparse import coo_matrix
    from jina.executors.encoders import BaseEncoder

    class SimpleScipyCOOEncoder(BaseEncoder):
        def __init__(self, *args, **kwargs):
            super().__init__(*args, **kwargs)

        def encode(self, content: 'np.ndarray', *args, **kwargs) -> Any:
            """Encode document content into `coo` format."""
            return coo_matrix(content)

Then we're able to make use of the `SimpleScipyCOOEncoder` defined above,
inside the Jina Index and Search Flow.

In Jina-Hub, we have created the `TFIDFTextEncoder <https://github.com/jina-ai/jina-hub/tree/master/encoders/nlp/TFIDFTextEncoder>`_ to encode text into sparse representation,
the ``TFIDFTextEncoder`` is a wrapper on top of scikit-learn ``TfidfVectorizer`` to encode text data into a Scipy sparse matrix.


You can create your own Encoder to encode your Document content into the expected format.

Use a Jina Sparse Indexer
--------------------------

In Jina, we've created several Indexers to help you encode your Document content into sparse format.
For instance, `PysparnnIndexer <https://github.com/jina-ai/jina-hub/tree/master/indexers/vector/PysparnnIndexer>`_
is a library for fast similarity search of Sparse Scipy vectors.
In contains an algorithm that can be used to perform fast approximate search with sparse inputs.
Developed by Facebook AI Research.

Build your Sparse Pipeline In Action
-----------------------------------------------------------

In this pipeline, we will make use of Jina's ``TFIDFTextEncoder`` together with ``PysparnnIndexer`` for encoding and indexing.

As was mentioned before, ``TFIDFTextEncoder`` was created based on Scikit-learn,
before using the Encoder, you need to ``fit`` the vectorizer with your training data.

.. highlight:: python
.. code-block:: python

    import pickle
    from sklearn.feature_extraction.text import TfidfVectorizer

    corpus = [
        'This is the first document.',
        'This document is the second document.',
        'And this is the third one.',
        'Is this the first document?',
    ]

    vectorizer = TfidfVectorizer()
    vectorizer.fit(corpus)
    # Dump the vectorizer fitted on your training data.
    pickle.dump(tfidf_vectorizer, open("./tfidf_vectorizer.pickle", "wb"))

Then you are able to define the Jina YAML configuration for your Encoder:

.. highlight:: yaml
.. code-block:: yaml

    !TFIDFTextEncoder
    metas:
      name: tfidf_encoder
    with:
      path_vectorizer: ./tfidf_vectorizer.pickle

For the indexer,
we will use the ``PysparnnIndexer`` with approximate nearest neighbor for sparse data.
Since we want to store the indexed result, we combined ``PysparnnIndexer`` and ``BinaryPbIndexer`` together.

.. highlight:: yaml
.. code-block:: yaml

    !CompoundIndexer
    components:
      - !PysparnnIndexer
        with:
          prefix_filename: 'pysparnn'
        metas:
          name: vecidx
      - !BinaryPbIndexer
        with:
          index_filename: doc.gz
        metas:
          name: docidx
    metas:
      name: doc_compound_indexer
      workspace: $WORKDIR

And we're able to create our Index Flow:

.. highlight:: yaml
.. code-block:: yaml

    jtype: Flow
    pods:
      encoder:
        uses: encode.yml
        show_exc_info: true
        parallel: 1
        timeout_ready: 600000
        read_only: true
      doc_indexer:
        uses: indexer.yml
        shards: 1
        separated_workspace: true

And Query Flow:

.. highlight:: yaml
.. code-block:: yaml

    jtype: Flow
    with:
      read_only: true
    pods:
      encoder:
        uses: encode.yml
        parallel: 1
        timeout_ready: 600000
        read_only: true
      doc_indexer:
        uses: indexer.yml
        shards: 1
        separated_workspace: true
        polling: all
        uses_reducing: _merge_all
        timeout_ready: 100000

To run the Index and Query Flow:

.. highlight:: python
.. code-block:: python

    from jina import Flow

    f = Flow.load_config('index.yml')
    with f:
        # index_generator is a function yields a jina Document per iteration
        f.index(input_fn=index_generator, request_size=16)

    f = Flow.load_config('flows/query.yml')
    with f:
        f.search_lines(lines=['my query', ], top_k=3)

Limitations [optional]
------------------------
It should be noted that sparse indexers in the hub do not support ACID features.

What's Next
------------

If you still have questions, feel free to `submit an issue <https://github.com/jina-ai/jina/issues>`_ or post a message in our `community slack channel <https://docs.jina.ai/chapters/CONTRIBUTING.html#join-us-on-slack>`_ .

To gain a deeper knowledge on the implementation of Jina's primitive data types, you can find the source code `here <https://github.com/jina-ai/jina/tree/master/jina/types>`_.