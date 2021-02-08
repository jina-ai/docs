# Jina Cloud Benchmark & Deployment

[comment]: <> (TODO how do we sell this?)

## Purpose: Showcase how Jina scales and how you can deploy to AWS

### Preparations and AWS

Configuration of machines.

### Datasets

To run these tests for Jina, we have prepared 2 scenarios:

## Image

The first scenario simulates an image search application. Images are randomly generated and indexed using the flow with this
default structure.

![Image Index Flow](image_index_flow.png)

At query time, the Flow looks like this one.

![Image Query Flow](image_query_flow.png)

In the Image Search scenario, there is no `Segmentation` involved and no `Reranking` of the results is used.
The results are returned according to the `distance` in embedding space returned by the `VectorIndexers`. In these tests,
no quality is assessed and therefore only the amount of returned matches is validated.


### Configuration of Flows and Pods

Plots

Parallel, shards, 

Concurrency of client

#### Advanced Indexers

Annoy, Faiss

### Results

Table with params and results

Stats, QPS, 

### Test it yourself

Clone
Install reqs on AWS
Set up env
Run script

### Learnings

- encoder sharding issue
- 