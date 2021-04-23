How to use Sparse Embeddings in Jina
=====================================

.. meta::
   :description: How to use Sparse Embeddings in Jina
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
-  We assume you have some experience with `sparse module in scipy <https://docs.scipy.org/doc/scipy/reference/sparse.html>`_, or you have used `TensorFlow Sparse Tensor <https://www.tensorflow.org/api_docs/python/tf/sparse/SparseTensor>`_ / `PyTorch Sparse COO Tensor <https://pytorch.org/docs/stable/sparse.html#sparse-coo-tensors>`_.

Behind Jina Sparse Matrix
-------------------------

As a framework of search, Jina don't have a native sparse matrix support.
The `Ndarray.sparse` module in Jina's Primitive Types is an adapter between Jina and other sparse backends,
such as `Scipy.sparse`.
In Jina, we support three `backends` to create your sparse matrix/Tensor:
`Scipy`, `TensorFlow` and `Pytorch`.
You might noticed that `Scipy.sparse` supports different sparse formats,
while Jina only supports `COO`, `BSR`, `CSR` and `CSC`.

When creating your own sparse matrix,
we suggest you use `CSR` as default matrix type.

.. list-table:: Sparse Matrix Formats
   :widths: 25 25 25 25 25
   :header-rows: 1

   * - ShortName
     - FullName
     - Scipy
     - TensorFlow
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


Build your Sparse Pipeline In Action
--------------------------------------

In this pipeline, we will make use of Jina's ``TFIDFTextEncoder`` together with ``PysparnnIndexer`` for encoding and indexing.

Step 1. Vectorize your data into sparse vector encoding
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

As was mentioned before, ``TFIDFTextEncoder`` was created based on Scikit-learn,
before using the Encoder, you need to ``fit`` the vectorizer with your training data.
In this example, we use a simple corpus containing four sentences of text.

.. highlight:: python
.. code-block:: python

    import pickle
    from sklearn.feature_extraction.text import TfidfVectorizer

    corpus = [
        'This is the first document.',
        'This document is the second document.',
        'And this is the third one.',
        'Is this the first document?'
    ]

    vectorizer = TfidfVectorizer()
    vectorizer.fit(corpus)
    # Dump the vectorizer fitted on your training data.
    pickle.dump(vectorizer, open('./tfidf_vectorizer.pickle', 'wb'))

Step 2. Setup Encoder & Indexer YAML configuration
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

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

Step 3. Create your index flow
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

.. highlight:: yaml
.. code-block:: yaml

    jtype: Flow
    pods:
      encoder:
        uses: encode.yml
        show_exc_info: true
        timeout_ready: 600000
        read_only: true
      doc_indexer:
        uses: indexer.yml
        shards: 1
        separated_workspace: true

Step 4. Create your query flow
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

.. highlight:: yaml
.. code-block:: yaml

    jtype: Flow
    with:
      read_only: true
    pods:
      encoder:
        uses: encode.yml
        timeout_ready: 600000
        read_only: true
      doc_indexer:
        uses: indexer.yml
        shards: 1
        separated_workspace: true
        timeout_ready: 100000

Step 5. Combine your flows and run Jina
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

.. highlight:: python
.. code-block:: python

    from jina import Flow

    def index_generator():
        """
        Data from which we create `Documents`.
        """
        import csv
        data_path = os.path.join(os.path.dirname(__file__), os.environ['JINA_DATA_PATH'])

        with open(data_path) as f:
            reader = csv.reader(f, delimiter='\t')
            for i, data in enumerate(reader):
                d = Document()
                d.tags['id'] = int(i)
                d.text = data[0]
                yield d

    # Load index flow configuration and run the index flow.
    f = Flow.load_config('index.yml')
    with f:
        f.index(input_fn=index_generator, request_size=16)

    # Load query flow configuration and run the query flow.
    f = Flow.load_config('flows/query.yml')
    with f:
        f.search_lines(lines=['my query', ], top_k=3)

Define your own Jina Sparse Encoder
-----------------------------------

If you want to create a customized `Encoder` with Jina,
for example,
encode your data with Scipy `COO` matrix format,
the code snippet blow shows how you could achieve it:

.. highlight:: python
.. code-block:: python

    from scipy.sparse import coo_matrix
    from jina.executors.encoders import BaseEncoder

    class SimpleScipyCOOEncoder(BaseEncoder):

        def encode(self, content: 'np.ndarray', *args, **kwargs) -> Any:
            """Encode document content into `coo` format."""
            return coo_matrix(content)

Then we're able to make use of the `SimpleScipyCOOEncoder` defined above,
inside the Jina Index and Search Flow.

Use a Jina Sparse Indexer
--------------------------

In Jina, we've created several Indexers to help you encode your Document content into sparse format.
For instance, `PysparnnIndexer <https://github.com/jina-ai/jina-hub/tree/master/indexers/vector/PysparnnIndexer>`_
is a library for fast similarity search of Sparse Scipy vectors.
In contains an algorithm that can be used to perform fast approximate search with sparse inputs.
Developed by Facebook AI Research.

Limitations
-------------
It should be noted that sparse indexers in the hub do not support ACID features.

What's Next
------------

If you still have questions, feel free to `submit an issue <https://github.com/jina-ai/jina/issues>`_ or post a message in our `community slack channel <https://docs.jina.ai/chapters/CONTRIBUTING.html#join-us-on-slack>`_ .

To gain a deeper knowledge on the implementation of Jina's primitive data types, you can find the source code `here <https://github.com/jina-ai/jina/tree/master/jina/types>`_.