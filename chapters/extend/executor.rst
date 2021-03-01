Development Guide: Add new Executors
=====================================

.. meta::
   :description: Development Guide: Add new Executors
   :keywords: Jina, executor, model integration

.. note:: This guide assumes you have a basic understanding of Jina, if you haven't, please check out `Jina 101 <https://101.jina.ai>`_ first.

.. contents:: Table of Contents
    :depth: 2

Motivation
^^^^^^^^^^^

As a Jina user, you might already noticed that `Jina-hub <https://github.com/jina-ai/jina-hub>`_ is the open open-registry for hosting Jina executors.
These `Executors <https://docs.jina.ai/chapters/all_exec.html>`_ has been categorised into folders by their types, such as `Encoder`, `Ranker`, `Crafter` etc.

However, when the existing Executors does not fit your specific use case,
you might be curious on how to **extent** Jina.
For example, integrate a new deep learning model,
add a new indexing algorithm,
or create your own evaluation metric.

In this tutorial, we'll guide you through the steps.
First of all, we will introduce the general steps on how to customize a Jina Executor.
At the end, we will give a concrete example of integrating the text encoder & image encoder on top of OpenAI's latest published `CLIP <https://github.com/openai/CLIP>`_ model.

Overview
^^^^^^^^^

To make an extension of a Jina `Executor`, please follow the steps listed below:

1. Decide which `Executor` class to inherit from.
2. Override :meth:`__init__` and :meth:`post_init`.
3. Overwrite the **Core** method of the `Executor`.
4. (Optional) Implement the save logic.

Implementation
^^^^^^^^^^^^^^^

Decide which :class:`Executor` class to inherit from
-----------------------------------------------------

When adding a customised Executor, the first thing is to inherit the "correct" class based on the use case.
The built-in Executor types are:

1. `Encoder`: encode document as vector embeddings.
2. `Indexer`: save and retrieve vectors and key-value pairs from storage.
3. `Crafter`:  transform the content of documents.
4. `Segmenter`:  segment document into smaller pieces of documents.
5. `Ranker`: calculate scores of documents.
6. `Classifier`: enrich document with a model.
7. `Evaluator`: evaluate score based on output and GroundTruth.
8. `CompoundExecutor`: combine multiple executors into one.

Rule of thumb, you always pick the executor that shares the similar logic to inherit.

.. note:: If your algorithm is so unique and does not fit any any of the category above, you may want to `submit an issue for discussion <https://github.com/jina-ai/jina/issues>`_ before you start.

.. list-table:: Built-in Executors to Inherit
   :widths: 25 25 50
   :header-rows: 1

   * - Name
     - Base Class
     - Description
   * - `BaseEncoder`
     - `BaseExecutor`
     - Represent the documents as vector embeddings.
   * - `BaseNumericEncoder`
     - `BaseEncoder`
     - Represent numpy array object (e.g. image, video, audio) as vector embeddings.
   * - `BaseTextEncoder`
     - `BaseEncoder`
     - Represent string object as vector embeddings.
   * - `BaseMultimodalEncoder`
     - `BaseExecutor`
     - Encode input from different modalities.
   * - `BaseIndexer`
     - `BaseExecutor`
     - Save and retrieve vectors and key-value pairs from storage.
   * - `BaseVectorIndexer`
     - `BaseIndexer`
     - Save and retrieve vectors from storage.
   * - `NumpyIndexer`
     - `BaseVectorIndexer`
     - Use numpy array for storage.
   * - `BaseKVIndexer`
     - `BaseIndexer`
     - Save and retrieve key-value pairs from storage.
   * - `BaseCrafter`
     - `BaseExecutor`
     - Transform the content of Documents.
   * - `BaseSegmenter`
     - `BaseExecutor`
     - Segment Document into small pieces of Document.
   * - `BaseRanker`
     - `BaseExecutor`
     - Calculate scores of Documents.
   * - `Chunk2DocRanker`
     - `BaseRanker`
     - Translates the chunk-wise score (distance) to the doc-wise score.
   * - `Match2DocRanker`
     - `BaseRanker`
     - Re-scores the matches for a document.
   * - `BaseClassifier`
     - `BaseExecutor`
     - Enrich the documents and chunks with a classifier.
   * - `BaseEvaluator`
     - `BaseExecutor`
     - Evaluate score based on output and GroundTruth.
   * - `CompoundExecutor`
     - `BaseExecutor`
     - Combine multiple executors in one.


Override :meth:`__init__` and :meth:`post_init`
------------------------------------------------

You can put simple type attributes that define the behavior of your ``Executor`` into :meth:`__init__`. Simple types represent all `pickle`-able types, including: integer, bool, string, tuple of simple types, list of simple types, map of simple type. For example,

.. highlight:: python
.. code-block:: python

  from jina.executors.crafters import BaseSegmenter

  class GifPreprocessor(BaseSegmenter):
    def __init__(self, img_shape: int = 96, every_k_frame: int = 1, max_frame: int = None, from_bytes: bool = False, *args, **kwargs):
        super().__init__(*args, **kwargs)
        self.img_shape = img_shape
        self.every_k_frame = every_k_frame
        self.max_frame = max_frame
        self.from_bytes = from_bytes

Remember to add ``super().__init__(*args, **kwargs)`` to your :meth:`__init__`. Only in this way you can enjoy many magic features, e.g. YAML support, persistence from the base class (and :class:`BaseExecutor`).


.. note::

    All attributes declared in :meth:`__init__` will be persisted during :meth:`save`  and :meth:`load`.



So what if the data you need to load is not in simple type. For example, a deep learning graph, a big pretrained model, a gRPC stub, a tensorflow session, a thread? The you can put them into :meth:`post_init`.

Another scenario is when you know there is a better persistence method other than ``pickle``. For example, your hyperparameters matrix in numpy ``ndarray`` is certainly pickable. However, you can simply read and write it via standard file IO, and it is likely more efficient than ``pickle``. In this case, you do the data loading in :meth:`post_init`.

Please check the example below:


.. highlight:: python
.. code-block:: python

    from jina.executors.encoders import BaseTextEncoder

    class TextPaddlehubEncoder(BaseTextEncoder):

        def __init__(self,
                     model_name: str = 'ernie_tiny',
                     max_length: int = 128,
                     *args,
                     **kwargs):
            super().__init__(*args, **kwargs)
            self.model_name = model_name
            self.max_length = max_length


        def post_init(self):
            import paddlehub as hub
            self.model = hub.Module(name=self.model_name)
            self.model.MAX_SEQ_LEN = self.max_length


.. note::

    :meth:`post_init` is also a good place to introduce package dependency, e.g. ``import x`` or ``from x import y``. Naively, you can always put all imports upfront at the top of the file. However, this will throw an ``ModuleNotFound`` exception when this package is not installed locally. Sometimes it may break the whole system because of this one missing dependency.

    Rule of thumb, only import packages where you really need them. Often these dependencies are only required in :meth:`post_init` and the core method, which we shall see later.

Override the *core* method of the base class
--------------------------------------------

Each :class:`Executor` has a core method, which defines the algorithmic behavior of the :class:`Executor`. For making your own extension, you have to override the core method. The following table lists the core method you may want to override. Note some executors may have multiple core methods.


+-------------------------+-----------------------------+
|      Base class         |        Core method(s)       |
+-------------------------+-----------------------------+
| :class:`BaseEncoder`    |        :meth:`encode`       |
+-------------------------+-----------------------------+
| :class:`BaseCrafter`    |  :meth:`craft`              |
+-------------------------+-----------------------------+
| :class:`BaseSegmenter`  |   :meth:`segment`           |
+-------------------------+-----------------------------+
| :class:`BaseIndexer`    |  :meth:`add`, :meth:`query` |
+-------------------------+-----------------------------+
| :class:`BaseRanker`     |  :meth:`score`              |
+-------------------------+-----------------------------+
| :class:`BaseClassifier` |    :meth:`predict`          |
+-------------------------+-----------------------------+
| :class:`BaseEvaluator`  |   :meth:`evaluate`          |
+-------------------------+-----------------------------+

Feel free to override other methods/properties as you need. But frankly, most of the extension can be done by simply overriding the core methods listed above.


Implement the persistence logic
-------------------------------

If you don't override :meth:`post_init`, then you don't need to implement persistence logic. You get YAML and persistency support off-the-shelf because of :class:`BaseExecutor`. Simple crafters and rankers fall into this category.

If you override :meth:`post_init` but you don't care about persisting its state in the next run (when the executor process is restarted); or the state is simply unchanged during the run, then you don't need to implement persistence logic. Loading from a fixed pretrained deep learning model falls into this category.

Persistence logic is only required **when you implement customized loading logic in :meth:`post_init` and the state is changed during the run**. Then you need to override :meth:`__getstate__`. Many of the indexers fall into this category.


In the example below, the ``tokenizer`` is loaded in :meth:`post_init` and saved in :meth:`__getstate__`, whcih completes the persistency cycle.

.. highlight:: python
.. code-block:: python

    class CustomizedEncoder(BaseEncoder):

        def post_init(self):
            self.tokenizer = tokenizer_dict[self.model_name].from_pretrained(self._tmp_model_path)
            self.tokenizer.padding_side = 'right'

        def __getstate__(self):
            self.tokenizer.save_pretrained(self.model_abspath)
            return super().__getstate__()


How Can I Use My Extension
^^^^^^^^^^^^^^^^^^^^^^^^^^^

You can use the extension by specifying ``py_modules`` in the YAML file. For example, your extension Python file is called ``my_encoder.py``, which describes :class:`MyEncoder`. Then you can define a YAML file (say ``my.yml``) as follows:

.. highlight:: yaml
.. code-block:: yaml

    !MyEncoder
    with:
      greetings: hello im external encoder
    metas:
      py_modules: my_encoder.py

.. note::

    You can also assign a list of files to ``metas.py_modules`` if your Python logic is splitted over multiple files. This YAML file and all Python extension files should be put under the same directory.

Then simply use it in Jina CLI by specifying ``jina pod --uses=my.yml``, or ``Flow().add(uses='my.yml')`` in Flow API.


.. warning::

    If you use customized executor inside a :class:`jina.executors.CompoundExecutor`, then you only need to set ``metas.py_modules`` at the root level, not at the sub-component level.


Customize Executor in Action: CLIP Encoder
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

`CLIP <https://github.com/openai/CLIP>`_ (Contrastive Language-Image Pre-Training) is a neural network trained on a variety of (image, text) pairs.
It can be instructed in natural language to predict the most relevant text snippet given an image.

The pre-trained CLIP model is able to transform both images and text into the same latent space,
where they can be compared using a similarity measure.
We will use CLIP as an example to see how to create :term:`Encoder` powered by CLIP model,
for text-to-image search.
You can refer to our `cross model search <https://github.com/jina-ai/examples/tree/master/cross-modal-search>`_ to find the example.

Since CLIP maps image and text into a common latent space,
it's objective is to represent documents as vector embeddings.
So we need to inherit from `BaseEncoder` class.
To encode a piece of text using CLIP, we might create a `CLIPTextEncoder` and inherit from `BaseTextEncoder`.
To encoder an image using CLIP, we might create a `CLIPImageEncoder` and inherit from `BaseNumericEncoder`.

The next step is to override :meth:`__init__` and :meth:`post_init`.
For :meth:`__init__`, we could specify a new parameter called `model_name` since CLIP has 2 pre-trained models,
i.e. ResNet50 and ViT-B/32.
As was mentioned before, it is a good practice to load pre-trained model inside :meth:`post_init`, now we have an Encoder like this:

.. highlight:: python
.. code-block:: python

    class CLIPTextEncoder(BaseTextEncoder):
        """Encode text into vector embeddings powered by OpenAI's CLIP model."""

        def __init__(
            self,
            model_name: str ='ViT-B/32',
            *args, **kwargs
        ):
            super().__init__(*args, **kwargs)
            self.model_name = model_name

        def post_init(self):
            """Load pre-trained CLIP model."""
            import clip
            model, _ = clip.load(self.model_name, self.device)
            self.model = model

        # the rest of the code

In the end, we need to overwrite the *core* method of the Executor.
Since it is an Encoder, we need to overwrite the :meth:`encode`.

.. highlight:: python
.. code-block:: python

    class CLIPTextEncoder(BaseTextEncoder):
        """Encode text into vector embeddings powered by OpenAI's CLIP model."""

        def __init__(
            self,
            model_name: str ='ViT-B/32',
            *args, **kwargs
        ):
            super().__init__(*args, **kwargs)
            self.model_name = model_name

        def post_init(self):
            """Load pre-trained CLIP model."""
            import clip
            model, _ = clip.load(self.model_name, self.device)
            self.model = model

        def encode(self, data: 'np.ndarray', *args, **kwargs) -> 'np.ndarray':
            tensor = clip.tokenize(data)
            with torch.no_grad():
                encoded_data = self.model.encode_text(tensor)
            return encoded_data.cpu().numpy()

In the code sample above, we called CLIP's :meth:`encode_text` to use the pre-trained CLIP model and encode input data into vector embeddings.

.. note:: The example above is a minimum working example of a `CLIPTextEncoder`, for full features such as GPU support, batching and dockerization, please checkout `Jina-hub <https://github.com/jina-ai/jina-hub/tree/master/encoders>`_.

The same applies to `CLIPImageEncoder`, the only difference is to use `self.model.encode_image` in :meth:`encode`.
Last but not least, create the YAML configuration for the encoder and use it with Jina CLI or Flow API.

.. highlight:: yaml
.. code-block:: yaml

    !CLIPTextEncoder
    metas:
      py_modules:
        - __init__.py


What's Next
^^^^^^^^^^^^^^^^^^^^^^^^^^

Thanks for your time and effort while reading this guide!

Please checkout `Jina-Hub <https://github.com/jina-ai/jina-hub>`_ to explore the executors.
If you still have questions, feel free to `submit an issue <https://github.com/jina-ai/jina/issues>`_ or post a message in our `community slack channel <https://docs.jina.ai/chapters/CONTRIBUTING.html#join-us-on-slack>`_ .

To gain a deeper knowledge on the implementation of Jina Executors, you can find the source code `here <https://github.com/jina-ai/jina/tree/master/jina/executors>`_.
