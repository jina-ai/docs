# Jina Cloud Benchmark & Deployment

[comment]: <> (TODO how do we sell this?)

## Purpose: Showcase how Jina scales and how you can deploy to AWS

## Preparations and AWS

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

[comment]: <> (TODO)

The full search Flow can be seen below:

[comment]: <> (TODO)

## Configuration of Flows and Pods

Jina allows for parallelization of data processing. You can read an overview of these options [here](link)

[comment]: <> (TODO link)

We use these to parallelize the various `Pods`. We also use the `load_balance` scheduling strategy. This prioritizes the idle `Peas` inside the `Pods`.    

## Client 

In order to simulate a real-world scenarios, we also test using multiple concurrent clients. This is achieved using the Python `multiprocessing` library.

In order to stress-test the system, we also issue index and query requests for several hours in a row. Each request contains a specific number of `Documents`, split across requests of the same `request_size`.

### Advanced Indexers

As part of the benchmark, we also compare the performance of the `BaseNumpyIndexer` with the advanced indexers, based on `Annoy` and `Faiss`. We also provide specific arguments to these: `num_tress` for `Annoy`; `index_key` for `Faiss`.

## Results

Table with params and results

Stats, QPS, 

## Test it yourself

Clone
Install reqs on AWS
Set up env
Run script

## Learnings

- encoder sharding issue
- 