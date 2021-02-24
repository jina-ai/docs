=================
Flow Optimization
=================
-----------------------------------------------------------------------------------------------------------------------
The FlowOptimizer runs Flows with different parameter sets. It allows out of the box hyperparameter tuning inside Jina.
-----------------------------------------------------------------------------------------------------------------------

.. contents:: Table of Contents
  :depth: 2

Flow Optimization is hyperparameter tuning
------------------------------------------

A common pattern when building a Flow in Jina is:

1. Design the high level ``flow.yml``
2. Define several ``pods.yml`` files for all needed Executors
3. Repeat until the evaluation metric suits your use case:

  a. Change variable values in different Executors (e.g. used model, used model layer, details of the segmenter)
  b. Index some data
  c. Query some data and look at the results

The :class:`FlowOptimizer` automates step 3.
It can be done via python code or JAML definitions as commonly used around Jina.

Before you start
----------------

Make sure you install Jina via `Installation <https://docs.jina.ai/chapters/install/os/index.html>`_ with ``jina[optimizer]``.

Read the :class:`Evaluator` entry in the `glossary <https://docs.jina.ai/chapters/glossary.html>`_.

Using the FlowOptimizer
-----------------------

In this toy example, we try to find the optimal layer of an encoder for the final embedding.
This is a common practice in machine learning.
The best semantic representation for a given problem might not be the last layer of a given model.

A Flow Optimization requires the following components:

- At least one :class:`Flow` and the corresponding Pod definitions via JAML
- An Evaluator Executor in at least one of the Flows
- A datasource containing Documents, which are sent to each Flow
- A ``parameter.yml`` file describing the optimization scenario
- A ``FlowRunner`` object, which allows repeatedly running the same Flow with different configurations.

Let's define a ``flow.yml``:

.. code-block:: yaml

  !Flow
  version: '1'
  env:
    JINA_ENCODER_LAYER_VAR: ${{JINA_ENCODER_LAYER}}
  pods:
    - uses: encoder.yml
    - uses: EuclideanEvaluator


The :class:`FlowOptimizer` will change the value of ``JINA_ENCODER_LAYER`` later on.
The Flow passes it on to the ``encoder.yml`` via the ``JINA_ENCODER_LAYER_VAR``.

The :class:`EuclideanEvaluator` is used for calculating the distance between the calculated encoding and the expected one.
Furthermore, we need the corresponding ``encoder.yml``:

.. code-block:: yaml

  !SimpleEncoder
  with:
    layer: ${{JINA_ENCODER_LAYER_VAR}}

.. code-block:: python

  import numpy as np
  from jina.executors.encoders import BaseEncoder

  class SimpleEncoder(BaseEncoder):

      ENCODE_LOOKUP = {
          '🐲': [1, 3, 5],
          '🐦': [2, 4, 7],
          '🐢': [0, 2, 5],
      }

      def __init__(self, layer=0, *args, **kwargs):
          super().__init__(*args, **kwargs)
          self._column = layer

      def encode(self, data: Sequence[str], *args, **kwargs) -> 'np.ndarray':
          return np.array([[self.ENCODE_LOOKUP[data[0]][self._column]]])


The :class:`SimpleEncoder` is not doing any computation.
For illustration purposes, it just chooses precomputed values for the different queries.
Thus, the semantic switch from ``layer`` to ``_column``
So choosing one ``column`` here is comparable with choosing a layer in a real world encoder (the second layer for ``🐦`` would result in the encoding ``[4]``).

As the next step we need some ground truth data.

.. code-block:: python

  from jina import Document

  documents = [
      (Document(content='🐲'), Document(embedding=np.array([2]))),
      (Document(content='🐦'), Document(embedding=np.array([3]))),
      (Document(content='🐢'), Document(embedding=np.array([3])))
  ]

Documents will be sent in pairs ``(doc, groundtruth)`` to the Flow.
The ``doc`` represents a Document that should be encoded.
The ``groundtruth`` contains the ideal encoding.
The perfect semantic encoding for ``🐲`` would be ``2``.

*Note*: In a real world example the groundtruth would rather be documents, that should be retrieved after querying.
For the sake of simplicity we omitted the indexing step in this example.

The :class:``FlowRunner`` wraps the Flow and the Documents for rerunnability.
This ensures no side effects between different Flow runs during optimization.

.. code-block:: python

  from jina.optimizers.flow_runner import SingleFlowRunner

  runner = SingleFlowRunner('flow.yml', documents, 1, 'search', overwrite_workspace=True)


Now we need to tell the :class:`FlowOptimizer`, what it can optimize:
The ``JINA_ENCODER_LAYER`` variable.
This is done via a ``parameter.yml`` file:

.. code-block:: yaml

  - !IntegerParameter
    jaml_variable: JINA_ENCODER_LAYER
    high: 2
    low: 0
    step_size: 1

The variable ``JINA_ENCODER_LAYER`` can take ``int`` values in the range ``[0, 2]``.

Possible choices for variables are:

- `IntegerParameter <https://docs.jina.ai/api/jina.optimizers.parameters.html#jina.optimizers.parameters.IntegerParameter>`_ and `DiscreteUniformParameter <https://docs.jina.ai/api/jina.optimizers.parameters.html#jina.optimizers.parameters.DiscreteUniformParameter>`_ for ``int`` based python variables (e.g. layer of a model)
- `UniformParameter <https://docs.jina.ai/api/jina.optimizers.parameters.html#jina.optimizers.parameters.UniformParameter>`_ and `LogUniformParameter <https://docs.jina.ai/api/jina.optimizers.parameters.html#jina.optimizers.parameters.LogUniformParameter>`_ for ``float`` based python variables (e.g. confidence threshold in object detection)
- `CategoricalParameter <https://docs.jina.ai/api/jina.optimizers.parameters.html#jina.optimizers.parameters.CategoricalParameter>`_ for python variables which can be categorized (e.g. model names)

Under the hood, Jina leverages the `optuna <https://optuna.org/>`_ optimizer.

Finally, we can define the :class:``FlowOptimizer`` and run it:

.. code-block:: python

  from jina.optimizers import FlowOptimizer, MeanEvaluationCallback

  optimizer = FlowOptimizer(
      flow_runner=runner,
      parameter_yaml='parameter.yml',
      evaluation_callback=MeanEvaluationCallback(),
      n_trials=3,
      direction='minimize',
      seed=1
  )
  result = optimizer.optimize_flow()

The :class:`MeanEvaluationCallback` takes the results of the last Evaluator inside a Flow and averages the results.
In the above defined Flow it is the single :class:`EuclideanEvaluator`.

Finally, we can write the optimal parameters into a file:

.. code-block:: python

  result.save_parameters('result_file.yml')

If you are familiar with ``optuna``, you can access more information directly from the `optuna study object <https://optuna.readthedocs.io/en/stable/reference/generated/optuna.study.Study.html#optuna.study.Study>`_ via ``result.study``.
For example ``result.study.trials`` contains detailed information about all trials.

Limitations
------------

Currently it is not possible to optimize a Flow that is defined via the python interface.
