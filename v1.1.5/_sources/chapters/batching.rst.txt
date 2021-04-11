=============================================
 Understanding Batching in Jina
=============================================


Some :term:`Executors` in Jina may profit from the capacity to process multiple instances of their input at the same time.
This can lead to a speed up and higher throughput.

In order to provide this capability, the :term:`Driver` extracts all the needed information from all the :term:`Document` in a :term:`Request`. [see `guide on request size <https://docs.jina.ai/chapters/request_size/>`_ ].

Then all these instances (one per :term:`Document`) is passed to the :term:`Executor`. Then the :term:`Executor` decides how it wants to
consume these instances (in batches or one by one).

.. meta::
   :description: Development Guide: Batching in Jina
   :keywords: Jina, batching

.. contents:: Table of Contents
    :depth: 2

Batching decorators
--------------------
To control how an :term:`Executor` consumes its incoming inputs, Jina provides a set of decorator functions.

single
------------------------------
The single decorator ensures that even when the function is called with a set of instances, they will be processed one by one by the executor.
This decorator is useful when the executor won't have benefits of processing inputs in batches, and allows the executor to keep a clean interface.

In the case where this decorator is used, the method needs to be implemented considering a single input.

When the decorated method requires multiple arguments, `slice_nargs` parameter must be used to select how many arguments need to be batched.

.. highlight:: python
.. code-block:: python

    from jina.executors.crafters import BaseCrafter
    from jina.executors.decorators import single

    class CapitalizerCrafter(BaseCrafter):

        @single
        def craft(self, text: str, *args, **kwargs):
            return {'text': text.capitalize()}

    crafter = CapitalizerCrafter()

    batch_of_docs = ['first', 'second', 'third']
    crafted_results = crafter.craft(batch_of_docs)
    assert crafted_results[0]['text'] == 'First'
    assert crafted_results[1]['text'] == 'Second'
    assert crafted_results[2]['text'] == 'Third'


batching
----------------------------------
The batching decorator ensures that data is processed in batches of a given size. This is useful when an :term:`Executor` can benefit
from processing multiple inputs at a time.

In the case where this decorator is used, the method needs to be implemented considering input comes in batches.

.. highlight:: python
.. code-block:: python

    from jina.executors.crafters import BaseCrafter
    from jina.executors.decorators import batching

    class CapitalizerCrafter(BaseCrafter):

        @batching
        def craft(self, text: Iterable[str], *args, **kwargs):
            return [{'text': t.capitalize()} for t in text]

    crafter = CapitalizerCrafter()

    batch_of_docs = ['first', 'second', 'third']
    crafted_results = crafter.craft(batch_of_docs)
    assert crafted_results[0]['text'] == 'First'
    assert crafted_results[1]['text'] == 'Second'
    assert crafted_results[2]['text'] == 'Third'


Batching vs Single
------------------
The usage of `batching` or `single` does not affect the correct functionality, from the :term:`Driver` point of view it is the same, it can provide
call them with a set of inputs, and gets a set of output in return. The only changes are encapsulated in the :term:`Executor` itself.

Batch Size
------------
When using `batching` decorator, one may wonder how an :term:`Executor` can control the batch_size. There are 3 ways an Executor can define this.

- Provide it directly in the batching decorator as a hardcoded value:
    .. highlight:: python
    .. code-block:: python

        class CapitalizerCrafter(BaseCrafter):

            @batching(batch_size=64)
            def craft(self, text: Iterable[str], *args, **kwargs):
                pass

- Make it an attribute of the :term:`Executor` explicitly:
    .. highlight:: python
    .. code-block:: python

        class CapitalizerCrafter(BaseCrafter):
            def __init__(*args, **kwargs):
                super.__init__(*args, **kwargs)
                self.batch_size = 64

            @batching
            def craft(self, text: Iterable[str], *args, **kwargs):
                pass

- Pass it as a metadata in the :term:`Executor` yaml.
    .. highlight:: yaml
    .. code-block:: yaml

        !CapitalizerCrafter
        with:
            {}
        metas:
            batch_size: 64


When do I need to set these decorators?
----------------------------------------

Currently, there are 5 classes of our :term:`Executor` that receive input from :term:`Driver` in batches, and therefore, all the
classes of these families need to make sure that their core methods are decorated with either `single` or `batching`.

These :term:`Executor` are:

- Encoder
- Classifier
- Crafter
- Segmenter
- Match2DocRanker
