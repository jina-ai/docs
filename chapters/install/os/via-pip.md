# Install Jina via `pip`

If you prefer to run Jina natively on your host, please make sure you have Python >= 3.7 installed.

## Install from PyPi

On Linux/Mac, simply run:
 
```bash
pip install jina
```

## Install from the Master Branch

If you want to keep track of the master branch of our development repository:

```bash
pip install git+https://github.com/jina-ai/jina.git
```

Be aware that the master branch may not be stable. We only recommend this branch for testing new features.

## Install from Your Local Fork/Clone

If you are a developer and want to test your changes on-the-fly: 

```bash
git clone https://github.com/jina-ai/jina
cd jina && pip install -e .
``` 

In the dev mode, if you later switch to a different method of Jina installation, remember to first uninstall the version you edited:

```bash
pip uninstall $(basename $(find . -name '*.egg-info') .egg-info)
```

## Cherry-Pick Extra Dependencies

Jina requires only five dependencies `numpy`, `pyzmq`, `protobuf`, `grpcio` and `pyyaml`. No third-party pre-trained models, deep learning/NLP/CV packages will be installed. 

However, some Executors may require extra dependencies. The full table of these extra dependencies can be found in `extra-requirements.txt`. You can cherry-pick what you want to install, e.g.

```bash
pip install "jina[nlp+cv]"
``` 

This will install all dependencies tagged with `nlp` or `cv`.

Though not recommended, you can install Jina with full dependencies via:

```bash
pip install "jina[all]"
``` 

### To Install Cherry-Picked Dependencies From the Master Branch

```bash
pip install "git+https://github.com/jina-ai/jina.git#egg=jina[http]" 
```

### Extra Dependencies Explained

These are the extra dependencies used by Jina:

| PyPi Name | Required by | Description | Compatibility
|---|---|---|---|
| `scipy>=1.4.1` | `index`, `numeric`, `cicd` | `Scientific Library for Python. Required for similarity measure computation and required for many other extra packages (tensorflow, paddlehub ...)` | `tensorflow>=2.0.0 requires scipy>=1.4.1, while paddlepaddle<1.8.1 require scipy<=1.3.1.`
| `fastapi` | `devel`, `cicd`, `http`, `test`, `daemon` | `FastAPI is a modern, fast (high-performance), web framework for building APIs with Python 3.6+ based on standard Python type hints.`
| `uvicorn>=0.12.1` | `devel`, `cicd`, `http`, `test`, `daemon` | `Uvicorn is a lightning-fast ASGI server implementation, using uvloop and httptools.`
| `fluent-logger` | `logging`, `http`, `sse`, `dashboard`, `devel`, `cicd`, `test`, `daemon` | `fluent-logger-python is a Python library, to record the events from Python application.` |
| `nmslib>=1.6.3` | `index` | `Non-Metric Space Library (NMSLIB) is an efficient cross-platform similarity search library.` | 
| `docker` | `devel`, `cicd`, `network`, `hub`, `test`, `daemon` | `A Python library for the Docker Engine API` | `See https://docs.docker.com/engine/api/ for compatibility with docker engine versions.`
| `torch>=1.1.0` | `framework`, `cicd` | `Tensors and Dynamic neural networks in Python with strong GPU acceleration. Enables several image encoders, object detection crafters and transformers models` | `It imposes compatibility restrictions with torchvision (https://pypi.org/project/torchvision/).`
| `transformers>=2.6.0` | `nlp` | `Repository of pre-trained NLP Transformer models` | `Some flair versions impose some requirements on the transformer version required. For proper padding to work, version 2.6.0 is required as minimmum version.`
| `flair` | `nlp` | `A very simple framework for state-of-the-art NLP` | `It imposes restrictions on torch and transformers version compatibility.`
| `paddlepaddle` | `framework`, `py37` | `Parallel Distributed Deep Learning` | `It imposes restrictions on scipy version and is required for paddlehub models.`
| `paddlehub` | `framework`, `py37` | `A toolkit for managing pretrained models of PaddlePaddle` | `Requires paddlepaddle.`
| `tensorflow>=2.0` | `framework`, `cicd` | `TensorFlow is an open source machine learning framework for everyone.`
| `tensorflow-hub` | `framework`, `py37` | `TensorFlow Hub is a library to foster the publication, discovery, and consumption of reusable parts of machine learning models.`
| `torchvision>=0.3.0` | `framework`, `cv` | `Image and video datasets and models for torch deep learning` | `Make sure that the models you want to use ara available at your installed torchvision version.`
| `onnx` | `framework`, `py37` | `Open Neural Network Exchange.` 
| `onnxruntime` | `framework`, `py37` | `ONNX Runtime Python bindings.` 
| `Pillow` | `cv`, `cicd`, `test` | `Python Imaging Library.`
| `annoy>=1.9.5` | `index` | `Approximate Nearest Neighbors in C++/Python optimized for memory usage and loading/saving to disk.`
| `sklearn` | `numeric` | `A set of python modules for machine learning and data mining. Used for a variety of numeric encoders.`
| `plyvel` | `index` | `Fast and feature-rich Python interface to LevelDB. Enables the use of LevelDB as a Key-Value indexer.`
| `jieba` | `nlp` | `Chinese Words Segmentation Utilities.`
| `lz4<3.1.2` | `devel`, `cicd`, `perf`, `network` | `LZ4 Bindings for Python. Enables compression to send large messages.`
| `gevent` | `http`, `devel`, `cicd` | `Coroutine-based network library`
| `python-magic` | `http`, `devel`, `cicd` | `File type identification using libmagic. Used to identify document request type.`
| `pymilvus` | `index` | `Python Sdk for Milvus. Enables the usage of Milvus DB as vector indexer as a client.`
| `deepsegment` | `nlp` | `Sentence Segmentation with sequence tagging.`
| `ngt` | `index`, `py37` | `Neighborhood Graph and Tree for Indexing High-dimensional Data.`
| `librosa>=0.7.2` | `audio` | `Python module for audio and music processing.`
| `uvloop` | `devel`, `cicd`, `perf` | `Fast implementation of asyncio event loop on top of libuv.`
| `numpy` | `core` | `Provides an array object of arbitrary homogeneous items, fast mathematical operations over arrays, Linear Algebra, Fourier Transforms, Random Number Generation.`
| `pyzmq>=17.1.0` | `core` | `PyZMQ is an asynchronous messaging library, aimed at use in distributed or concurrent applications.`
| `protobuf>=3.13.0` | `core` | `Protocol Buffers (Protobuf) is a method of serializing structured data.`
| `grpcio>=1.33.1` | `core` | `HTTP/2-based RPC framework.`
| `pyyaml>=5.3.1` | `core` | `YAML is a data serialization format designed for human readability and interaction with scripting languages. `
| `tornado>=5.1.0` | `core` | `Tornado is a Python web framework and asynchronous networking library, originally developed at FriendFeed.`
| `cookiecutter` | `hub`, `devel`, `cicd` | `A command-line utility that creates projects from project templates, e.g. creating a Python package project from a Python package project template.`
| `pytest` | `test` | `The pytest framework makes it easy to write small tests, yet scales to support complex functional testing for applications and libraries.`
| `pytest-xdist==1.34.0` | `test` | `pytest xdist plugin for distributed testing and loop-on-failing modes.`
| `pytest-timeout` | `test` | `py.test plugin to abort hanging tests.`
| `pytest-mock` | `test` | `Thin-wrapper around the mock package for easier use with pytest.`
| `pytest-cov` | `test` | `Pytest plugin for measuring coverage.`
| `pytest-repeat` | `test` | `pytest plugin for repeating tests.`
| `pytest-asyncio` | `test` | `pytest-asyncio is an Apache2 licensed library, written in Python, for testing asyncio code with pytest.`
| `flaky` | `test` | `Flaky is a plugin for nose or pytest that automatically reruns flaky tests.`
| `mock` | `test` | `Mock is a library for testing in Python. It allows you to replace parts of your system under test with mock objects and make assertions about how they have been used.`
| `requests` | `http`, `devel`, `test`, `daemon` | `Requests is a simple, yet elegant HTTP library.`
| `prettytable` | `devel`, `test` | `A simple Python library for easily displaying tabular data in a visually appealing ASCII table format`
| `sseclient-py` | `test` | `A Python client for SSE event sources that seamlessly integrates with urllib3 and requests.`
| `optuna` | `test`, `optimizer` | `Optuna is an automatic hyperparameter optimization software framework, particularly designed for machine learning.`
| `websockets` | `http`, `devel`, `test`, `ws`, `daemon` | `Websockets is a library for building WebSocket servers and clients in Python with a focus on correctness and simplicity.`
| `wsproto` | `http`, `devel`, `test`, `ws`, `daemon` | `WebSockets state-machine based protocol implementation.`
| `pydantic` | `http`, `devel`, `test`, `daemon` | `Data validation and settings management using Python type hinting.`
| `python-multipart` | `http`, `devel`, `test`, `daemon` | `A streaming multipart parser for Python.`
| `aiofiles` | `devel`, `cicd`, `http`, `test`, `daemon` | `Aiofiles is an Apache2 licensed library, written in Python, for handling local disk files in asyncio applications.`
| `pytest-custom_exit_code` | `cicd`, `test` | `Exit pytest test session with custom exit code in different scenarios.`
| `bs4` | `test` | `Dummy package for Beautiful Soup.`
| `aiostream` | `devel`, `cicd` | `Generator-based operators for asynchronous iteration.`

## On Windows

Currently we do not support native python on Windows.

If you are a Windows user, one workaround is to [run Jina on Windows Subsystem for Linux](on-wsl.md) or [run Jina in a Docker container](via-docker.md). If you manage to run Jina on Windows after some tweaks, please submit your changes [here](https://github.com/jina-ai/jina/issues/new).

## Other OSes
Please refer to [run Jina in a Docker container](via-docker.md). If you manage to run Jina on other OSes after some tweaks, please submit your changes [here](https://github.com/jina-ai/jina/issues/new).

# Upgrade Jina

If you have a previously installed version of Jina, you can upgrade it by running:

```bash
pip install -U jina
```

For Docker users: 

```bash
docker pull jinaai/jina
```
