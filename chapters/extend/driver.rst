Developer Guide: Add new Drivers
====================================

.. meta::
   :description: Developer Guide: Add new Drivers
   :keywords: Jina, driver

.. note:: This guide assumes you have a basic understanding of Jina, if you haven't, please check out `Jina 101 <https://101.jina.ai>`_ first.
.. note:: Before reading this guide, you might want to read `Development Guide: Add new Executors <../executor.rst>`_ first.

.. contents:: Table of Contents
    :depth: 2

Motivation
^^^^^^^^^^^

As you might have already learned from `Jina 101 <https://101.jina.ai>`_,
a :term:`Driver` interprets incoming messages into :term:`Document` and extracts required fields for an :term:`Executor`.

Jina already created `several Drivers <https://docs.jina.ai/chapters/all_driver/>`_ Drivers inside Core,
and these `Drivers` should already fulfill most of the scenarios.
However, when the existing solutions do not fit your specific use case,
you might be curious how to **extend** Jina.
For instance, if you have created a customized `Executor`,
and figure out the existent `Drivers` can not fit for your customized `Executor`,
you might want to create a customized `Driver`.

Overview
^^^^^^^^^

There are two steps to adding an extension of a Jina `Driver`:

1. Decide which `Driver` class to inherit from.
2. Overwrite the **Core** method of the `Driver`.

Implementation
^^^^^^^^^^^^^^^

If you want to create a customized `Executor` and it's associated `Driver`,
follow the table below to decide which `BaseDriver` class to inherit from.
After creating your customized `Driver` class, you need to implement your own Core method based on your specific need.

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


In the code snippet below, you should be able to see the logic of how we implemented the Driver.
In :meth:`_apply_all`,
We firstly convert each Document in the `DocumentSet` into a `MultimediaDocument`.
For each instance of the `MultimediaDocument`,
we check if the instance has :meth:`modality_content_map` (a python `dict`, where key is the name of the modality, while value is the content of the modality).
If so, we consider it has a valid `MultimediaDocument`.
And we save the value of the specific Modality into `content_by_modality`.
The result will be feed into :meth:`exec_fn` as the input.

Since class `MultiModalDriver` is based on `BaseEncodeDriver`, the :meth:`exec_fn` will be bind to the :meth:`encode` method,
to encode data from different modalities into embeddings.
Last but not least, we assign the `embeddings` property of each Document as the encoded vector representation.
And the task of the Driver finished.

.. highlight:: python
.. code-block:: python


    class MultiModalDriver(FlatRecursiveMixin, BaseEncodeDriver):
    """Extract multimodal embeddings from different modalities."""

        ...

        def _apply_all(self, docs: 'DocumentSet', *args, **kwargs) -> None:
            """Apply the driver to each of the Documents in docs."""
            content_by_modality = defaultdict(
                list
            )

            valid_docs = []
            for doc in docs:
                doc = MultimodalDocument(doc)
                if doc.modality_content_map:
                    valid_docs.append(doc)
                    for modality in self.positional_modality:
                        content_by_modality[modality].append(doc[modality])

            if valid_docs:
                for modality in self.positional_modality:
                    content_by_modality[modality] = np.stack(content_by_modality[modality])

                input_args = self._get_executor_input_arguments(content_by_modality)
                embeds = self.exec_fn(*input_args)
                for doc, embedding in zip(valid_docs, embeds):
                    doc.embedding = embedding


What's next
^^^^^^^^^^^

Thanks for your time and effort while reading this guide!

Please checkout `Jina Core <https://github.com/jina-ai/jina/drivers>`_ to explore the source code of built-in `Drivers`.
If you still have questions, feel free to `submit an issue <https://github.com/jina-ai/jina/issues>`_ or post a message in our `community slack channel <https://docs.jina.ai/chapters/CONTRIBUTING.html#join-us-on-slack>`_ .
