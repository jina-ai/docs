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
    This is an experimental feature, more notes to be added.

Behind Jina Sparse Matrix
-------------------------

In Jina, we support three `backends` to create your sparse matrix/Tensor:
`Scipy`, `Tenroflow` and `Pytorch`.
You might noticed that `Scipy.sparse` supports different sparse formats:

.. list-table:: Sparse Matrix Formats
   :widths: 25 25 50
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

When creating your own sparse matrix,
we suggest you use `coo` as matrix type.

Make use of Jina Primitive Types
---------------------------------

Lead-in sentence for an ordered list:

1. Sub-step A
2. Sub-step B
3. Sub-step C

Define your Jina Sparse Encoder
-----------------------------------

Lead-in sentence explaining the code snippet. For example:

Run the ``apt`` command to install the Asciidoctor package and check the
version.

.. code:: bash

    $ sudo apt install asciidoctor

    $ asciidoctor --version
    Asciidoctor 1.5.6.2 [https://asciidoctor.org]

Use a Jina Sparse Indexer
-----------------------------------

Provide a summary of the steps completed and explain what the user has
achieved by following them. You can also include links to related
articles that may help the reader reinforce concepts discussed in this
How To article.

Build your Sparse Index & Search FLow
-----------------------------------

to be added

Limitations [optional]:
------------------------
 If there are known feature limitations that a user would expect to see mention them here.