# Jina Cloud Benchmark & Deployment

[comment]: <> (TODO how do we sell this?)

## Purpose: Showcase how Jina scales and how you can deploy to AWS

### Preparations and AWS

Configuration of machines.

### Datasets

To run these tests for Jina, we have prepared 2 scenarios:

#### Image

The first scenario simulates an image search application. Images are randomly generated and indexed using the flow with this
default structure.

![Image Index Flow](image_index_flow.png)

At query time, the Flow looks like this one.

![Image Query Flow](image_query_flow.png)

In the Image Search scenario, there is no `Segmentation` involved and no `Reranking` of the results is used.
The results are returned according to the `distance` in embedding space returned by the `VectorIndexers`. In these tests,
no quality is assessed and therefore only the amount of returned matches is validated.

###Configuration

The configuration parameters of this experiment can be found in the `.env` file, whose values are set as environment variables
and changed in the `flow` and `pod` yamls.

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

#### Sharding parameters


Parallel, shards, 

#### Functional/Indexers parameter

Annoy, Faiss

#### Client parameters

Concurrency of client

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