Development Guide: Add new Drivers
====================================

.. meta::
   :description: Development Guide: Add new Drivers
   :keywords: Jina, driver

.. note:: This guide assumes you have a basic understanding of Jina, if you haven't, please check out `Jina 101 <https://101.jina.ai>`_ first.
.. note:: Before reading this guide, you might want to read `Development Guide: Add new Executors <../executor.rst>`_ first.

.. contents:: Table of Contents
    :depth: 2

Motivation
^^^^^^^^^^^

As you might have already learned from `Jina 101 <https://101.jina.ai>`_,
a :term:`Driver` interprets incoming messages into :term:`Document` and extracts required fields for an :term:`Executor`.

Jina already created `several drivers <https://docs.jina.ai/chapters/all_driver/>`_ drivers inside Core,
and these `Drivers` should already fulfill most of the scenarios.
However, when the existing solutions do not fit your specific use case,
you might be curious on how to **extend** Jina.
For instance, if you have created a customised `Executor`,
and figure out the existent `Drivers` can not fit for your customised `Executor`,
you might want to create a customised `Driver`.

Overview
^^^^^^^^^

To make an extension of a Jina `Driver`, please follow the steps listed below:

1. Decide which `Driver` class to inherit from.
2. Overwrite the **Core** method of the `Driver`.

Implementation
^^^^^^^^^^^^^^^

.. list-table:: Built-in Driver to Inherit
   :widths: 25 25 50 25
   :header-rows: 1

   * - Name
     - Corresponded Executor
     - Description
     - Core method
   * - `BaseEncodeDriver`
     - `BaseEncoder`
     - Driver bind with :meth:`encode` in the Executor.
     - `_apply_all`
   * - `BaseIndexDriver`
     - `BaseIndexer`
     - Driver bind with :meth:`add` in the Executor.
     - `_apply_all`
   * - `BaseSearchDriver`
     - `BaseIndexer`
     - Driver bind with :meth:`query` in the Executor.
     - `_apply_all`
   * - `BasePredictDriver`
     - `BaseClassifier`
     - Driver bind with :meth:`predict` in the Executor.
     - `_apply_all`
   * - `BaseLabelPredictDriver`
     - `BaseClassifier`
     - Driver bind with :meth:`predict` for label prediction.
     - `prediction2label`
   * - `BaseEvaluateDriver`
     - `BaseEvaluator`
     - Driver bind with :meth:`evaluate` in the Executor.
     - `extract`

This is to say, if you want to create a customized `Executor` and it's associated `Driver`,
Follow the table above to decide which `BaseDriver` class to inherit from.
After creating your customised `Driver` class, you need to implement your own Core method based on your specific need.


Customize Driver in Action: `MultimodalDriver`
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

We created `MultimodalDriver <https://github.com/jina-ai/jina/blob/master/jina/drivers/multimodal.py>`_ to better support Cross/Multi-modal search.
Assume a `Document` has 2 child `Documents` with different modalities, e.g. text and image.
The objective of `MultimodalDriver` is to extract embeddings from both modalities.
Our expected input and output can be represented as:

.. highlight:: shell
.. code-block:: shell

    Input:
    document:
            |- child document: {modality: mode1}
            |
            |- child document: {modality: mode2}
    Output:
    document: (embedding: multimodal encoding)
            |- child document: {modality: mode1}
            |
            |- child document: {modality: mode2}




.. highlight:: python
.. code-block:: python

    class MultiModalDriver(FlatRecursiveMixin, BaseEncodeDriver):
    """Extract multimodal embeddings from different modalities.

    ...

    def _apply_all(self, docs: 'DocumentSet', *args, **kwargs) -> None:
        """Apply the driver to each of the Documents in docs.

        :param docs: the docs for which a ``multimodal embedding`` will be computed, whose chunks are of different
        :param args: unused
        :param kwargs: unused
        """
        content_by_modality = defaultdict(
            list
        )  # array of num_rows equal to num_docs and num_columns equal to

        valid_docs = []
        for doc in docs:
            # convert to MultimodalDocument
            doc = MultimodalDocument(doc)
            if doc.modality_content_map:
                valid_docs.append(doc)
                for modality in self.positional_modality:
                    content_by_modality[modality].append(doc[modality])
            else:
                self.logger.warning(
                    f'Invalid doc {doc.id}. Only one chunk per modality is accepted'
                )

        if len(valid_docs) > 0:
            # Pass a variable length argument (one argument per array)
            for modality in self.positional_modality:
                content_by_modality[modality] = np.stack(content_by_modality[modality])

            # Guarantee that the arguments are provided to the executor in its desired order
            input_args = self._get_executor_input_arguments(content_by_modality)
            embeds = self.exec_fn(*input_args)
            if len(valid_docs) != embeds.shape[0]:
                self.logger.error(
                    f'mismatched {len(valid_docs)} docs from level {valid_docs[0].granularity} '
                    f'and a {embeds.shape} shape embedding, the first dimension must be the same'
                )
            for doc, embedding in zip(valid_docs, embeds):
                doc.embedding = embedding


What's next
^^^^^^^^^^^

Thanks for your time and effort while reading this guide!

Please checkout `Jina Core <https://github.com/jina-ai/jina/drivers>`_ to explore the source code of built-in `Drivers`.
If you still have questions, feel free to `submit an issue <https://github.com/jina-ai/jina/issues>`_ or post a message in our `community slack channel <https://docs.jina.ai/chapters/CONTRIBUTING.html#join-us-on-slack>`_ .
