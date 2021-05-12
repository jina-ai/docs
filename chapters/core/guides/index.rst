Developer Guides
======================================
Jina developer guides cover specific features and use cases.

Jina Core is the open-source framework of Jina AI. It is a new, state-of-the-art design pattern allowing you to create end-to-end search solutions. It leverages the latest AI and deep learning models out-of-the box and lets you deploy them anywhere you want.

Brand new users to Jina Core should first begin with the :doc:`../introduction/index` section which covers more fundamental concepts.

Further information on any specific method can be obtained in the :doc:`../api_references/index`.

.. toctree::
   :maxdepth: 1
   :caption: Getting started
   :titlesonly:
   :glob:

   ../setup/index
   ../../project-guide
   ../../my_first_jina_app
   ../../install/ide-speedup.md

.. toctree::
   :maxdepth: 1
   :caption: Documents
   :titlesonly:
   :glob:

   ../../primitive_data_type
   ../../mime-type/index
   ../../traversal

.. toctree::
   :maxdepth: 1
   :caption: Executors
   :titlesonly:
   :glob:

   Processing Documents with Executors <../../executors>
   ../../simple_exec
   ../../extend/executor
   ../../peas-and-pods
   ../../extend/driver

.. toctree::
   :maxdepth: 1
   :caption: Flows
   :titlesonly:
   :glob:

   ../../flow/index
   Inputs and Outputs <../../input_output>
   ../../crud
   ../../cross_multi_modality
   ../../sparse
   ../../logging
   ../../cli/exit

.. toctree::
   :maxdepth: 1
   :caption: Improving Speed and Results
   :titlesonly:
   :glob:

   ../../evaluation
   ../../optimization
   ../../ranker
   ../../prevent_duplicate_indexing
   ../../incremental_indexing
   ../../parallel
   ../../batching
   ../../request_size

.. toctree::
   :maxdepth: 1
   :caption: Deploying Jina
   :titlesonly:
   :glob:

   ../../remote/index
   ../../stress/index

.. toctree::
   :maxdepth: 1
   :caption: Reference
   :titlesonly:
   :glob:

   ../../envs
   ../../flow/pattern
   ../../autocomplete
