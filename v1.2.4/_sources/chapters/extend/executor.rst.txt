Adding New Executors
=====================================

.. meta::
   :description: Development Guide: Add new Executors
   :keywords: Jina, executor, model integration

.. note:: This guide assumes you have a basic understanding of Jina, if you haven't, please check out `Jina 101 <https://101.jina.ai>`_ first.

.. contents:: Table of Contents
    :depth: 2

Motivation
^^^^^^^^^^^

As a Jina user, you may have noticed that `Jina Hub <https://github.com/jina-ai/jina-hub>`_ is the open registry for hosting Jina Executors.
These `Executors <https://docs.jina.ai/chapters/all_exec.html>`_ have been categorised into categories by their type, such as `Encoder`, `Ranker`, `Crafter` etc.

However, when existing Executors do not fit your specific use case, you may want to **extend** Jina. For example, by integrating a new deep learning model, adding a new indexing algorithm, or creating your own evaluation metric.

In this tutorial, we'll guide you through the steps.
First of all, we will introduce the general steps on how to customize a Jina Executor.
At the end, we will give a concrete example of integrating the text encoder and image encoder on top of OpenAI's latest published `CLIP <https://github.com/openai/CLIP>`_ model.

Overview
^^^^^^^^^

To add a new Jina `Executor`:

1. Decide which `Executor` class to inherit from.
2. Override :meth:`__init__` and :meth:`post_init`.
3. Overwrite the **core** method of the `Executor`.
4. (Optional) Implement the save logic.

Implementation
^^^^^^^^^^^^^^^

Decide which :class:`Executor` class to inherit from
-----------------------------------------------------

When adding a custom Executor, first inherit the "correct" class based on the use case.
The built-in Executor types are:

1. `Encoder`: encode document as vector embeddings.
2. `Indexer`: save and retrieve vectors and key-value pairs from storage.
3. `Crafter`:  transform the content of documents.
4. `Segmenter`:  segment document into smaller pieces of documents.
5. `Ranker`: calculate scores of documents.
6. `Classifier`: enrich document with a model.
7. `Evaluator`: evaluate score based on output and GroundTruth.
8. `CompoundExecutor`: combine multiple Executors into one.

As a rule of thumb, you pick the Executor that shares the similar logic to inherit.

.. note:: If your algorithm is relatively unique and does not fit in any of the categories above, you may want to `submit an issue for discussion <https://github.com/jina-ai/jina/issues>`_ before you start.

.. list-table:: Built-in Executors to inherit from
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
     - Segment Document into smaller pieces.
   * - `BaseRanker`
     - `BaseExecutor`
     - Calculate scores of Documents.
   * - `Chunk2DocRanker`
     - `BaseRanker`
     - Translate chunk-wise score (distance) to doc-wise score.
   * - `Match2DocRanker`
     - `BaseRanker`
     - Re-score matches for a Document.
   * - `BaseClassifier`
     - `BaseExecutor`
     - Enrich documents with a classifier.
   * - `BaseEvaluator`
     - `BaseExecutor`
     - Evaluate score based on output and GroundTruth.
   * - `CompoundExecutor`
     - `BaseExecutor`
     - Combine multiple Executors into one.


Override :meth:`__init__` and :meth:`post_init`
------------------------------------------------

You can put simple type attributes that define the behavior of your ``Executor`` into :meth:`__init__`. Simple types represent all `pickle`-able types, including: integer, bool, string, tuple of simple types, list of simple types, and map of simple type. For example:

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

Remember to add ``super().__init__(*args, **kwargs)`` to your :meth:`__init__`. This is the only way you can enjoy many magic features like YAML support, persistence from the base class (and :class:`BaseExecutor`).


.. note::

    All attributes declared in :meth:`__init__` will be persisted during :meth:`save`  and :meth:`load`.



What if the data you need to load cannot be stored in a simple type?
For example, a deep learning graph, a big pretrained model, a gRPC stub, a TensorFlow session, a thread? The you can put them into :meth:`post_init`.

It is also interesting to override :meth:`post_init` when there is a better persistence method other than pickle.
For example, your hyperparameters matrix in numpy ``ndarray`` is certainly pickle-able. However, you can simply read and write it via standard file IO, and it is likely more efficient than ``pickle``. In this case, you load data in :meth:`post_init`.

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

    :meth:`post_init` is also a good place to introduce package dependencies, e.g. ``import x`` or ``from x import y``. Naively, you can always put all imports upfront at the top of the file. However, this will throw a ``ModuleNotFound`` exception when the package is not installed locally. Sometimes one missing dependency may break the whole system.

    As a rule of thumb, only import packages where you really need them. Often these dependencies are only required in :meth:`post_init` and the core method, which we shall see later.

Override the *core* method of the base class
--------------------------------------------

Each :class:`Executor` has a core method, which defines its algorithmic behavior. When making your own extension, you have to override this core method. The following table lists the core method you may want to override. Note some Executors may have multiple core methods.


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

Feel free to override other methods/properties as you need. But probably, most of the extension can be done by simply overriding the core methods listed above.


Implement the persistence logic
-------------------------------

If you don't override :meth:`post_init`, then you don't need to implement persistence logic. You get YAML and persistency support out-of-the-box because of :class:`BaseExecutor`. Simple crafters and rankers fall into this category.

If you override :meth:`post_init` but you don't care about persisting its state in the next run (when the Executor process is restarted); or the state is simply unchanged during the run, then you don't need to implement persistence logic. Loading from a fixed pretrained deep learning model falls into this category.

Persistence logic is only required **when you implement custom loading logic in :meth:`post_init` and the state is changed during the run**. Then you need to override :meth:`__getstate__`. Many of the indexers fall into this category.


In the example below, the ``tokenizer`` is loaded in :meth:`post_init` and saved in :meth:`__getstate__`, which completes the persistency cycle.

.. highlight:: python
.. code-block:: python

    class CustomizedEncoder(BaseEncoder):

        def post_init(self):
            self.tokenizer = tokenizer_dict[self.model_name].from_pretrained(self._tmp_model_path)
            self.tokenizer.padding_side = 'right'

        def __getstate__(self):
            self.tokenizer.save_pretrained(self.model_abspath)
            return super().__getstate__()


How can I use my extension?
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

    You can also assign a list of files to ``metas.py_modules`` if your Python logic is split over multiple files. This YAML file and all Python extension files should be put in the same directory.

Then simply use it in Jina CLI by specifying ``jina pod --uses=my.yml``, or ``Flow().add(uses='my.yml')`` in Flow API.


.. warning::

    If you use a customized Executor inside a :class:`jina.executors.CompoundExecutor`, then you only need to set ``metas.py_modules`` at the root level, not at the sub-component level.


Customize executor in action: CLIP encoder
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

`CLIP <https://github.com/openai/CLIP>`_ (Contrastive Language-Image Pre-Training) is a neural network trained on a variety of (image, text) pairs.
Given an image it can be instructed in natural language to predict the most relevant text snippet.

The pre-trained CLIP model is able to transform both images and text into the same latent space,
where image and text emebddings can be compared using a similarity measure.
We will use CLIP as an example to see how to create :term:`Encoder` powered by CLIP model,
for text-to-image search.
You can refer to our `cross model search <https://github.com/jina-ai/examples/tree/master/cross-modal-search>`_ to find the example.

Since CLIP maps image and text into a common latent space,
it's objective is to represent documents as vector embeddings.
So we need to inherit from `BaseEncoder` class.
To encode a piece of text using CLIP, we might create a `CLIPTextEncoder` and inherit from `BaseTextEncoder`.
To encode an image using CLIP, we might create a `CLIPImageEncoder` and inherit from `BaseNumericEncoder`.

The next step is to override :meth:`__init__` and :meth:`post_init`.
For :meth:`__init__`, we can specify a new parameter called `model_name` since CLIP has two pre-trained models,
i.e. ResNet50 and ViT-B/32.
As mentioned before, it is a good practice to load pre-trained model inside :meth:`post_init`. Now we have an Encoder like this:

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

At the end, we need to overwrite the *core* method of the Executor.
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

.. note:: The example above is a minimum working example of a `CLIPTextEncoder`, for full features such as GPU support, batching and dockerization, please check out `Jina-hub <https://github.com/jina-ai/jina-hub/tree/master/encoders>`_.

The same applies to `CLIPImageEncoder`, the only difference is to use :meth:`self.model.encode_image` in :meth:`encode`.
Last but not least, create the YAML configuration for the encoder and use it with Jina CLI or Flow API.

.. highlight:: yaml
.. code-block:: yaml

    !CLIPTextEncoder
    metas:
      py_modules:
        - __init__.py

Then use it in Jina CLI by specifying ``jina pod --uses=config.yml``,
or ``Flow().add(uses='config.yml')`` in Flow API.
And you have a good foundation to build your index/query Flow powered by CLIP.

Share your work!
^^^^^^^^^^^^^^^^^^^^^

If you would like to share your customized Executor with the community, you are more than welcome!
We use `cookiecutter <https://github.com/cookiecutter/cookiecutter>`_ to create Jina Executors from a template.

.. note:: Install Docker and run `pip install "jina[devel]"` before you start.

To make sure your work is in good shape, Jina provides a wizard to help you create a Executor. Start it with `jina hub new --type pod`.
It will generate a standard Executor project like this:

.. highlight:: text
.. code-block:: text

    CLIPTextEncoder/
    ├── Dockerfile
    ├── manifest.yml
    ├── README.md
    ├── config.yml
    ├── requirements.txt
    ├── __init__.py
    └── tests/
        ├── test_CLIPTextEncoder.py
        └── __init__.py

You can put your customized Encoder, such as `CLIPTextEncoder` inside `__init__.py`.
The YAML configuration should be placed in `config.yml`.

To ensure your custom Executor, like `CLIPTextEncoder`, performs exactly the same as the original CLIP model,
add tests inside `tests` folder.
For example, encode some text data with the raw CLIP model, and assert we get the same result with `CLIPTextEncoder`.

Build and test your Encoder locally with:

.. highlight:: shell
.. code-block:: shell

    jina hub build -t jinahub/type.kind.jina-image-name:image_version-jina_version <your_folder>

Once tested, you should login to Jina Hub with `jina hub login`, and copy/paste the token into GitHub to verify your account.
You are now able to push your work to Jina Hub:

.. highlight:: shell
.. code-block:: shell

    jina hub push jinahub/type.kind.jina-image-name:image-jina_version

In our example, the type is `pod`, kind is `encoder` and `jina-image-name` is `cliptextencoder` and `clipimageencoder`.


What's next?
^^^^^^^^^^^^
Thanks for your time and effort while reading this guide!

Please check out `Jina Hub <https://github.com/jina-ai/jina-hub>`_ to explore the Executors.
If you still have questions, feel free to `submit an issue <https://github.com/jina-ai/jina/issues>`_ or post a message in our `community slack channel <https://docs.jina.ai/chapters/CONTRIBUTING.html#join-us-on-slack>`_ .

To gain a deeper knowledge of the implementation of Jina Executors, you can find the source code `here <https://github.com/jina-ai/jina/tree/master/jina/executors>`_.
