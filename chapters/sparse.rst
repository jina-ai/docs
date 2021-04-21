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

.. Note::
    This feature is not yet production-ready.

Behind Jina Sparse Matrix
-------------------------

In Jina, we support three `backends` to create your sparse matrix/Tensor:
`Scipy`, `Tenroflow` and `Pytorch`.
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
You can create your own Encoder to encode your Document content into your expected format.

Use a Jina Sparse Indexer
-----------------------------------

In Jina, we've created several Indexers to help you encode your Document content into sparse format.
For instance, `PysparnnIndexer <https://github.com/jina-ai/jina-hub/tree/master/indexers/vector/PysparnnIndexer>`_
is a library for fast similarity search of Sparse Scipy vectors.
In contains an algorithm that can be used to perform fast approximate search with sparse inputs.
Developed by Facebook AI Research.

Build your Sparse Index & Search FLow
-----------------------------------

to be added

Limitations [optional]:
------------------------
 If there are known feature limitations that a user would expect to see mention them here.