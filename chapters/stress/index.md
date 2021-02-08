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

### Configuration

The configuration parameters of this experiment can be found in the `.env` file, whose values are set as environment variables and changed in the `flow` and `pod` yamls.

The file content looks like:

```
##Infrastructure parameters
JINA_ENCODER_HOST=encoder
JINA_RANKER_HOST=ranker
JINA_REDIS_INDEXER_HOST=ranker
JINA_VEC_INDEXER_HOST=vector

##Sharding/Performance parameters
JINA_SHARDS_ENCODER=2
JINA_SHARDS_INDEXERS=8
JINA_SHARDS_REDIS=2
OMP_NUM_THREADS=1
SCHEDULING=load_balance

##Functional/Indexers parameters
JINA_ENCODER_DRIVER_BATCHING=16
JINA_DISTANCE_REVERSE=False
JINA_FAISS_IMAGE=docker://jinahub/pod.indexer.faissindexer:0.0.15-0.9.29
JINA_ANNOY_IMAGE=docker://jinahub/pod.indexer.annoyindexer:0.0.16-0.9.29
JINA_FAISS_INDEX_KEY='IVF50,Flat'
JINA_ANNOY_NUM_TREES=100
JINA_ANNOY_SEARCH_K=-1

##Client/run parameters
TOP_K=50
#Number of documents a client will try to index at every connection
DOCS_INDEX=1000
#Number of documents a client will try to query at every connection
DOCS_QUERY=1000
PYTHON_EXEC=python3
DATASET=image
#Number of seconds for which clients will try to index documents. (The time is checked after each cycle of indexing `DOCS_INDEX`)
TIME_LOAD_INDEX=18000
#Number of seconds for which clients will try to query documents. (The time is checked after each cycle of indexing `DOCS_QUERY`)
TIME_LOAD_QUERY=3600
#Number of documents every request will contain
REQ_SIZE=50
#Number of concurrent clients indexing
CONCURRENCY_INDEX=5
#Number of concurrent clients querying
CONCURRENCY_QUERY=1
SLEEP_TIME=10

##Flow parameters
FLOW_HOST=flow
FLOW_PORT=8000
```

The parameters are organized in 4 groups-
- Infrastructure parameters: Set where the different Pods run
- Sharding Parameters: Set the parallelization and sharding of different components (Link to sharding explanation in docs)
- Functional/Indexers parameters: Set functional parameters that can affect the performance of the search
- Client parameters: Parameters related to how client sends data to Flow
- Flow parameters: Host and Port of the Flow

### Sharding parameters

### Functional/Indexers parameter

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