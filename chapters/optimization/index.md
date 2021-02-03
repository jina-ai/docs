## Flow Optmization

##### Flow Optimization eases the process of rerunning Flows with different parameter sets. It allows out of the box hyperparameter tuning inside Jina.

### Flow Optimization is hyperparameter tuning on steroids

A common principle when building a Flow in Jina is:

1. Design the high level `flow.yml`
2. Define several `pods.yml` files for all needed Executors
3. Repeat until happy with results:
  a. Change variable values in different Executors (e.g. used model, used model layer, details of the segmenter)
  b. Index some data
  c. Query some data and look at the results

Step 3 is time consuming and often the user has no idea, which values to test.
Flow Optimization automates exactly these steps.
It can be done via python code or `.yml` definitions as commonly used around Jina.

### Before you start

Make sure you install Jina via `Installation <https://docs.jina.ai/chapters/install/os/index.html>`_ with `jina[optimizer]`.

### Using FlowOptimization

In this toy example, we choose the optimal layer of an encoder for the final embedding.
This is a common practice in ML.
The best semantic representation for a given problem might not be the last layer of a given model.

Flow Optimization requires the following components:

- At least one Flow and the corresponding Pod definitions via JAML
- An Evaluator Executor in at least one of the Flows
- Documents, which are send to each Flow
- A `parameter.yml` file describing the optimization scenario
- A `FlowRunner` object, which allows repeatedly running the same Flow with different configurations.

Let's define a `flow.yml`:

```yaml
!Flow
version: '1'
env:
  JINA_ENCODER_LAYER_VAR: ${{JINA_ENCODER_LAYER}}
pods:
  - uses: encoder.yml
  - uses: EuclideanEvaluator
```

The `EuclideanEvaluator` is used for calculating the distance between the encoded and an expected vector.
Furthermore, we need the corresponding `encoder.yml`:

```yaml
!SimpleEncoder
with:
  index: ${{JINA_ENCODER_LAYER_VAR}}
```

```python
class SimpleEncoder(BaseEncoder):

    ENCODE_LOOKUP = {
        'cat': [1, 3, 5],
        'pokemon': [2, 4, 7],
        'dog': [0, 2, 5],
    }

    def __init__(self, layer=0, *args, **kwargs):
        super().__init__(*args, **kwargs)
        self._layer = layer

    def encode(self, data: 'np.ndarray', *args, **kwargs) -> 'np.ndarray':
        return np.array([[self.ENCODE_LOOKUP[data[0]][self._layer]]])
```

The `SimpleEncoder` is not doing any computation.
It rather illustrates the output a real encoder would have on different layers (the second layer for 'pokemon' would result in the encoding `[4]`).

As the next step we need some ground truth data.

```python
documents = [
    (Document(content='cat'), Document(embedding=np.array([2]))),
    (Document(content='pokemon'), Document(embedding=np.array([3]))),
    (Document(content='dog'), Document(embedding=np.array([3])))
]
```

Documents will be send in pairs `(doc, groundtruth)` to the Flow.
The _doc_ represents a Document that should be encoded.
The _groundtruth_ contains the ideal encoding.
The perfect semantic encoding for `cat` would be `2`.

*Note*: In a real world example the groundtruth would rather be documents, that should be retrieved after querying.
For the sake of simplicity we omitted the indexing step in this example.

The _FlowRunner_ wraps the Flow and the Documents for rerunnability.
This ensures no side effects between different Flow runs during optimization.

```python
runner = SingleFlowRunner('flow.yml', documents, 1, 'search', overwrite_workspace=True)
```

Now we need to tell the optimizer, what it can optimize:
The `JINA_ENCODER_LAYER` variable.
This is done via a `parameter.yml` file:

```yaml
- !IntegerParameter
  jaml_variable: JINA_ENCODER_LAYER
  high: 2
  low: 0
  step_size: 1
```

Here we say, the variable can take `int` values in the range `[0, 2]`.
Jina leverages the optuna optimizer under the hood.
For a detailed description of all possible choices see (TODO: INSERT LINK TO PARAMETER DOCSTRING).

Finally, we can define the FlowOptimizer and run it:

```python
optimizer = FlowOptimizer(
    flow_runner=runner,
    parameter_yaml='parameter.yml',
    evaluation_callback=MeanEvaluationCallback(),
    n_trials=3,
    direction='minimize',
    seed=1
)
result = optimizer.optimize_flow()
```

The `MeanEvaluationCallback` takes the results of the last Evaluator inside a Flow and averages the results.
In the above defined Flow it is the solemn `EuclideanEvaluator`.

Finally, we can write the optimal parameters into a file:

```python
result.save_parameters('result_file.yml')
```

### Limitations:

Currently it is not possible to optimize a Flow that is defined via the python interface.
