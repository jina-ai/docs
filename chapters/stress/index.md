# Jina Cloud Benchmark & Deployment

[comment]: <> (TODO how do we sell this?)

#### Purpose: Showcase how Jina scales and how you can deploy to AWS

---

#### Table of Contents

  * [Infrastructure](#infrastructure)
  * [Datasets](#datasets)
    + [Image search](#image-search)
    + [Text search](#text-search)
  * [Configuration](#configuration)
    + [Sharding parameters](#sharding-parameters)
    + [Functional/Indexers parameter](#functional-indexers-parameter)
    + [Client](#client)
  * [Results](#results)
  * [Test it yourself](#test-it-yourself)
  * [Learnings](#learnings)

## Infrastructure

This example benchmark prioritizes a real-world scenario, where a user hosts Jina in the cloud. We use six machines in AWS, of instace size `c5.2xlarge` (8 vCPUs, 16GB RAM). We also add 100GB gp2 ssd.

The instances are split as follows:

- **Client**: from this machine we create the Flows and issue the client requests (indexing and querying)
[comment]: <> (- Crafter: this machine TODO is this used?)
- **Flow**: this machine hosts the `Gateway` to the `Flow`
- **Encoder**: this machine processes the raw data (text or image) with the respective `Encoders`
- **Indexer**: this machine hosts the `Indexers`
- **Ranker**: this machine hosts the `Redis` servers and the `Rankers`

## Datasets

To run these tests, we prepared two scenarios: text and image search. These are the most common use cases for neural search. 

Note that in these tests, the quality of the results is not assessed, and therefore only the amount of returned matches is validated.

### Image search

The first scenario simulates an image search application. Images are randomly generated and indexed using a Flow with this topology:

![Image Index Flow](image_index_flow.png)

We use the `mobilenet_v2` `Encoder` with the `ImageTorchEncoder`.

At query time, the Flow looks like this:

![Image Query Flow](image_query_flow.png)

In the image search scenario, there is no `Segmentation` and ranking. The results are returned according to the `distance` in embedding space returned by the `VectorIndexers`. 

### Text search

The text search topology is similar to the one above, but with a `Segmenter` (`Sentencizer`) and a `Ranker` (`SimpleAggregateRanker`). This means we are indexing on the chunk level of a `Document`. 

The full indexing Flow can be seen below:

![Text Index Flow](text_index_flow.png)

The full search Flow can be seen below:

![Text Query Flow](text_query_flow.png)

## Configuration

The configuration parameters of this experiment can be found in the `.env` file, whose values are set as environment variables and changed in the `flow` and `pod` yamls.

The parameters are organized in 4 groups:

- Infrastructure parameters: Configure what the different machines are.
- Sharding Parameters: Configure the parallelization and sharding of different components
- Functional/Indexers parameters: Configure the functional parameters that can affect the performance of the search
- Client parameters: Configure how the client connects to the Flow

### Sharding parameters

Jina allows for parallelization of data processing. You can read an overview of these options [here](https://docs.jina.ai/chapters/parallel/index.html)

We use these to parallelize the various `Pods`. We also configure the scheduling strategy to `load_balance`. This prioritizes the idle `Peas` inside the `Pods`.

### Functional/Indexers parameter

As part of the benchmark, we also compare the performance of the `NumpyIndexer` with the advanced indexers, based on `Annoy` and `Faiss`. We also provide specific arguments to these: `num_tress` for `Annoy`; `index_key` for `Faiss`.

### Client  

In order to simulate a real-world scenarios, we also test using multiple concurrent clients. This is achieved using the Python `multiprocessing` library.

In order to stress-test the system, we also issue index and query requests for several hours in a row. Each request contains a specific number of `Documents`, split across requests of the same `request_size`.

The client also sets the `TOP_K` parameter. This limits the number of matches returned by the Flow. This also affects performance.

## Results

Table with params and results

Stats, QPS, 

## Test it yourself

You can reproduce this benchmark yourself. You have to do the following:

- configure and start the instances in AWS

    You can try using another provider. Additionally, you can try running it in Docker Compose, on your local machine. However, the Annoy & Faiss indexers use their own Docker images and running a Docker within Docker is currently not supported.

- clone the repository [here](https://github.com/jina-ai/cloud-ops/tree/master/stress-example)
- install Jina, JinaD and the requirements on each of the machines
    
    These can be found in the folders `image` or `text`.

Do the following in the client machine:

- clone the same repository
- `cd` into the `stress-example` directory
- set up the configuration options in `.env`
- run the automatic script: `nohup bash run_test.sh > output.txt &`

    `nohup` allows you to disconnect from the machine without interrupting the process. The output will be piped into `output.txt`. 

When the process is done, you will find the results in `output.txt`. Look for the lines starting with `TOTAL: Ran operation`

## Learnings

- encoder sharding issue
- 