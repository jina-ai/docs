# Build Your First Jina App

## 👋 Introduction

The aim of this tutorial is to guide you through building your first simple neural text search app using the [Jina framework](https://github.com/jina-ai/jina/). 

![](/chapters/images/jinabox-wikipedia.gif)

This app will take a user's input and return a matching list of results from [your dataset](#set-up-your-dataset).

The end result will be a search app that is similar to [Wikipedia sentence search](https://github.com/jina-ai/examples/tree/master/wikipedia-sentences).

## 🗝️ Key concepts

You should be familiar with these before you start:

- **[What is Neural Search?](https://jina.ai/2020/07/06/What-is-Neural-Search-and-Why-Should-I-Care.html)** See how Jina is different from traditional search
- **[Jina 101](https://docs.jina.ai/chapters/101/)**: Learn about Jina's core components
- **[Jina 102](https://docs.jina.ai/chapters/102/)**: See how Jina's components are connected together
- **[Wikipedia sentence search](https://github.com/jina-ai/examples/tree/master/wikipedia-sentences)**: Test it out, see what you'll build in this tutorial

## 🐳 Try it in Docker

Before building your app, let's see what the finished product is like:

```sh
docker run -p 45678:45678 jinahub/app.example.wikipedia-sentences-30k:0.2.10-1.0.10
```

This runs a **pre-indexed** version of the example, and allows you to search using Jina's REST API.

ℹ️  Run the Docker image before trying the steps below

### Search with Jina Box

[Jina Box](https://github.com/jina-ai/jinabox.js/) is a simple web-based front-end for neural search. You can see it in the image at the top of this tutorial.

1. Go to [Jina Box](https://jina.ai/jinabox.js) in your browser
2. Set the server endpoint to `http://127.0.0.1:45678/api/search`
3. Type a word into the search bar and see which Wikipedia sentences come up

ℹ️  If the search times out the first time, that's because the query system is still warming up. Try again in a few seconds!

### Search with `curl`

```sh
curl --request POST -d '{"top_k":10,"mode":"search","data":["computer"]}' -H 'Content-Type: application/json' 'http://0.0.0.0:45678/api/search'
```

ℹ️  To make it easier to read the output, add `| jq | less` to the end of the command. This will add pretty JSON formatting and paging.

`curl` will output a *lot* of information in JSON format. This includes not just the lines you're searching for, but also metadata about the search and the Documents it returns. 

After looking through the JSON you should see lines that contain the text of the Document matches:

```json
"text": "It solves Aerospace problems with a data driven interface and automatic initial guesses.\n"
```

ℹ️  There's a LOT of other data too. This is all metadata and not so relevant to the output a user would want to see.
ℹ️  If you're not getting good results, check the [get better search results section](#get-better-search-results) below.

### Shut down Docker

To cleanly exit the Docker container, open a new terminal window and run:

```sh
docker stop wikipedia-search
```

## 🐍 Build the app

### Prerequisites

* You are comfortable using bash or another [terminal shell](https://en.wikipedia.org/wiki/Unix_shell)
* Linux or macOS (or see [instructions to install Jina on Windows](https://docs.jina.ai/chapters/install/os/via-pip.html#on-windows))
* 8 gigabytes or more of RAM
* Python 3.7 or higher, and `pip`
* Docker (optional, for running the Docker examples)

### Clone the repo

Setting up a Jina app from scratch requires many files. A simple template makes this easier. To download this template:

```sh
git clone https://github.com/jina-ai/examples.git
```

And then enter the template directory:

```sh
cd templates/nlp-simple
```

⚠️   This template is useful only for simple text search apps. For other examples, please see our [examples repo](https://github.com/jina-ai/examples).

### Create a virtualenv and install requirements

A [virtualenv](https://realpython.com/python-virtual-environments-a-primer/) (virtual environment) prevents your system libraries and project libraries conflicting with each other. Create one by running:

```sh
virtualenv env --python=python3.7
source /env/bin/activate
```

Now install the requirements:

```sh
pip install -r requirements.txt
```

### Set up your dataset

For this example, we recommend a relatively simple dataset. This is because we won't implement [segmenting](https://101.jina.ai/#segmenter) for this simple example. [Kaggle NLP Datasets](https://www.kaggle.com/datasets?tags=13204-NLP) are a good place to start.

Processing your data somehow is often a key part of any machine learning project. For this example, process your data into a single text file, with one item per line. This is similar to our [wikipedia-sentence data file](https://github.com/jina-ai/examples/blob/master/wikipedia-sentences/data/toy-input.txt). Since every dataset is different, we leave this as an exercise for the reader!

After processing:

1. Create a new folder: `mkdir data`
2. Move your file into the folder and rename to `input.txt`: `mv <filename> data/input.txt`
3. Define an environment variable for your data file: `export JINA_DATA_FILE=data/input.txt`

If you've got a large dataset and want to index just part of it, you can specify an environment variable:

```sh
export JINA_MAX_DOCS=3000
```

Replace `3000` with whatever number you like.

⚠️   You should set these environment variables in the same terminal that you will later use to run Jina itself. You can check if you have the environment variable set by running:

```shell
echo $JINA_DATA_FILE
```

or

```shell
echo $JINA_MAX_DOCS
```

It should return exactly what you set earlier when you used the `export` command.

### Set up your model and model settings

#### Set your model

Jina's default language model for text searches is [`distilbert-bert-cased`](https://huggingface.co/distilbert-base-cased). This is a good model for general purpose text, but your use case may require something more specific. You can find plenty of models at [huggingface](https://huggingface.co/models?pipeline_tag=fill-mask). Be sure to filter by "Fill-Mask" task.

You set your model in `pods/encode.yml` under `pretrained_model_name_or_path`:

```yaml
!TransformerTorchEncoder
with:
  ...
  pretrained_model_name_or_path: distilbert-base-cased
  ...
```

Depending on your use case, you may find the following models useful:

- [bert-base-chinese](https://huggingface.co/bert-base-chinese) - Chinese
- [distilbert-base-german-cased](https://huggingface.co/distilbert-base-german-cased) - German
- [camembert-base](https://huggingface.co/camembert-base) - French
- [legal-bert-base-uncased](https://huggingface.co/nlpaueb/legal-bert-base-uncased) - Legal text
- [financial_roberta](https://huggingface.co/abhilash1910/financial_roberta) - Financial text

Simply replace `distilbert-base-cased` with the other model name to use that model

⚠️   All the models that work with Jina are third-party and not something we develop in-house. We haven't tested all the models listed above, so your experience may vary.

#### Set your model settings

Now you've chosen your model, you can fine-tune its performance using `max_length`. Again, in `pods/encode.yml`:

```yaml
!TransformerTorchEncoder
with:
  ...
  max_length: 96
```

`max_length` refers to the length of text that will go into one vector embedding. A higher number (usually) means more accurate results but more time needed for indexing and querying.

With our [wikipedia sentences example]() we found a length of `192` was a lot better than the default of `96`. Generally, if you're indexing longer strings of text, you'll want a higher `max_length`.

## 🏃 Run Jina

In section you will index and search through your data.

### Index Flow

First we need to build an index of our dataset. Later we'll search this index with our [query Flow](#query-flow).

```sh
python app.py -t index
```

ℹ️  `-t` is short for `--task`

You'll see a lot of output scrolling on your terminal. Indexing is complete when you see:

```sh
Flow@58199[S]:flow is closed and all resources should be released already, current build level is EMPTY
```

This may take longer the first time, because Jina needs to download the language model and tokenizer to process the dataset. You can think of these as the brains that power the search.

### Query Flow

Run:

```bash
python app.py -t query_restful
```

After a while you should see the console stop scrolling and display output like:

```console
        🖥️ Local access         http://0.0.0.045678
        🔒 Private network:     http://192.168.1.68:45678
        🌐 Public address:      http://81.37.167.157:45678
```

Your search engine is now ready to run!

⚠️  Note down the port number. You'll need it for `curl` and the Jina Box front-end. In our case we can see it's `45678`.

ℹ️  `python app.py -t query_restful` doesn't pop up a search interface - for that you'll need to connect via `curl`, Jina Box, or another client. Alternatively run `python app.py -t query` to search from your terminal.

### Searching your data

See the instructions on [Searching the Docker example](#-try-it-in-docker) above. It's exactly the same commands to search your local app.

When you're finished, stop the Flow with Ctrl-C (or Command-C on a Mac), and run `deactivate` to exit your virtualenv. (If you wish to re-activate it in the future, you can return to the app directory and run `source env/bin/activate`).

## 🤔 How does it work?

### Flows

<img src="https://docs.jina.ai/_images/flow.png" width="30%" align="left">

Just as a plant manages nutrient flow and growth rate for its branches, Jina's Flow manages the states and context of a group of Pods, orchestrating them to accomplish one specific task. 

We define Flows in `app.py` to index and query our dataset:

```python
from jina.flow import Flow

<other code here>

def index():
    f = Flow.load_config('flows/index.yml')

    with f:
        data_path = os.path.join(os.path.dirname(__file__), os.environ.get('JINA_DATA_FILE', None)) # Set data path
        f.index_lines(filepath=data_path, batch_size=16, read_mode='r', size=num_docs) # Set mode (index_lines) and indexing settings
```

To start the Flow, we run `python app.py -t <flow_name>`, in this case:

```sh
python app.py -t index
```

ℹ️  You also can build Flows in `app.py` itself [without specifying them in YAML](https://docs.jina.ai/chapters/flow/index.html) or with [Jina Dashboard](http://dashboard.jina.ai)

#### Indexing

`input.txt` is just one big text file. Our indexing Flow will create an index of each line in the file, and later Jina will query this index. The indexing is performed by the Pods in the Flow. Each Pod performs a different task, with one Pod's output becoming another Pod's input. 

Every Flow is defined in its own YAML file. Let's look at `flows/index.yml`:

```yaml
!Flow
version: '1'
pods:
  - name: encoder
    uses: pods/encode.yml
    timeout_ready: 1200000
    read_only: true
  - name: indexer
    uses: pods/index.yml
```

Each Pod performs a different operation on the dataset:

| Pod       | Task                                                       |
| ---       | ---                                                        |
| `crafter` | Split Documents into sentences                             |
| `encoder` | Encode each input Document into a vector                   |
| `indexer` | Build an index of the vectors and metadata key-value pairs |

#### Searching

Like the index Flow, the search Flow is also defined in a YAML file, in this case at `flows/query.yml`:

```yaml
!Flow
version: '1'
with:
  read_only: true
  port_expose: $JINA_PORT
pods:
  - name: encoder
    uses: pods/encode.yml
    timeout_ready: 60000
    read_only: true
  - name: indexer
    uses: pods/index.yml
```

As in `flows/index.yml`, we use three Pods, but this time they behave differently:

| Pod       | Task                                                              |
| ---       | ---                                                               |
| `crafter` | Split input query into sentences                                  |
| `encoder` | Encode user's query into a vector                                 |
| `indexer` | Query vector index and key-value pairs; Return matching Documents |

### Pods

<img src="https://docs.jina.ai/_images/pod.png" width="20%" align="left">

- A Flow tells Jina *what* tasks (indexing, querying) to perform on the dataset.
- The [Pods]() comprise the Flow and tell Jina *how* to perform each task. They define the neural networks we use in neural search, namely the machine-learning models like `distilbert-base-cased`.

Like Flows, Jina defines Pods in their own YAML files. We can easily configure their behavior without touching our application code.

Let's look at [`pods/encode.yml`](pods/encode.yml) as an example:

```yaml
!TransformerTorchEncoder
with:
  pooling_strategy: auto
  pretrained_model_name_or_path: distilbert-base-cased
  max_length: 96
```

- The built-in `TransformerTorchEncoder` is the Pod's **[Executor](https://docs.jina.ai/chapters/101.html#executor)**. 
- The `with` field specifies the parameters we pass to `TransformerTorchEncoder`:

| Parameter                       | Effect                                                     |
| ---                             | ---                                                        |
| `pooling_strategy`              | Strategy to merge word embeddings into Document embeddings |
| `pretrained_model_name_or_path` | Name of the model we're using                              |
| `max_length`                    | Maximum length to truncate tokenized sequences to          |

All the other Pods follow a similar structure.

## ⏭️  Next steps

### Get better search results

Your results may not be very suitable when you query your dataset. This can be fixed in several ways:

#### Index more Documents

Use a larger dataset or increase your `JINA_MAX_DOCS` environment variable to index more sentences. This gives the language model more data to work with: 

```sh
export JINA_MAX_DOCS=30000
```

#### Increase `max_length`

In `pods/encode.yml` increase the length of your embeddings:

```yaml
with:
  ...
  max_length: 192 # Higher number means more accurate results but slower performance
```

#### Change language model

Language model performance depends on your task. If you're indexing Chinese sentences, you wouldn't use an English-language model! Jina default model for text search is [`distilbert-base-cased`](https://huggingface.co/distilbert-base-cased). [Other models](https://huggingface.co/models) may work better depending on your dataset and use case.

In `pods/encode.yml`:

```yaml
with:
  ...
  pretrained_model_name_or_path: <your model name>
```

### Enable [incremental indexing](https://docs.jina.ai/chapters/incremental_indexing/index.html)

In this example, if you wanted to index more data you would need to remove your `workspace` directory and then re-index **everything** from scratch. To avoid this [we can add incremental indexing](https://github.com/jina-ai/examples/tree/master/wikipedia-sentences-incremental)

## 🤕 Troubleshooting

### Module not found error

- Run `pip install -r requirements.txt`
- Ensure you have enough RAM/swap and space in your `tmp` partition (see below issues)

### My computer hangs

Machine learning requires a lot of resources. If your computer hangs this may be because it's run out of memory. To fix this, try [creating a swap file](https://linuxize.com/post/how-to-add-swap-space-on-ubuntu-20-04/) if you use Linux. This is less of an issue on macOS, since it allocates swap automatically.

## 🎁 Wrap up

In this tutorial you've learned:

* How to install Jina's neural search framework
* How to load and index text data from files
* How to query data with `curl` and Jina Box
* The details behind Jina Flows and Pods

Now you have a broad understanding of how things work. Next you can look at more [examples](https://github.com/jina-ai/examples) to build [image](https://github.com/jina-ai/examples/tree/master/pokedex-with-bit) or [video](https://github.com/jina-ai/examples/tree/master/tumblr-gif-search) search, or see a [more advanced text search example](https://github.com/jina-ai/examples/tree/master/multires-lyrics-search).
