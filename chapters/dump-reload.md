# Rolling Updates and Query while Indexing in Jina

##### In this section, you will learn how to configure Jina to allow query while indexing

#### Overview

Query while indexing refers to the capacity of still being able to search your system while data is being written to it. 
Within Jina this is possible using the rolling updates paradigm. 
This means you can have two Flows running at the same time, one for DBMS-type operations (adding, updating, deleting data) and one for searching (querying). 
The two systems communicate with each other using a system of rolling updates calls to the respective Flows. 


#### DBMS and Query Indexers

As part of this paradigm, we provide two types of indexers:

The **DBMS** (Database Management System) indexers provide a CRUD-like interface and store both the embedding and the Document's metadata as one entity. 
These provide `index`, `update`, and `delete` methods. 
They should be part of your Indexing Flow, and function as normal Indexers.

At the moment we provide two such indexers, with more to come:

- `BinaryPbDBMSIndexer`, which uses a file system-based storage (part of `Jina core`)
- `PostgreSQLDBMSINdexer`, which uses the PostgreSQL database system as its storage system. 
Can be found in [Jina Hub](https://github.com/jina-ai/jina-hub/tree/master/indexers/dbms/PostgreSQLIndexer)

The **Query** indexers are **write-once** indexers that are used only for *querying* data.
**Write-once** means that the data is only written to the indexer once, at initialization time. 
After that, you can only issue `search` requests to it. 
In order to re-build it with new data, you need to use the `.rolling_update` method.

#### Dump-Reload

The Flow object provides two methods, `.dump` and `.rolling_update`, for configuring the rolling updates. 

`.rolling_update` should be calle on the Query Flow, with the name of the Pod where the Indexer resides, and the path from which to read.
The method then shuts down the indexers in the Flow sequentially, and instructs them to start again, but with the new data from the location provided.

`.dump` should be called on the DBMS Flow, with the name of the Pod where the Indexer resides, which path to dump to, and how many shards you have in your Query Flow.
The method then extracts the data from the DBMS Indexer and stores it in the specified location, prepared to be read by Query Indexers.

#### Replicas and Rolling Updates

In order to maintain availability during the `.rolling_update` call you need to make sure that you have at least 2 replicas configured for your Query Indexer. 
This way, when one is taken offline for reloading, the other one is still alive and serving results.

This can be configured via the Python API, or in the YAML of the Pod:

```yml
...
pods:
  - name: indexer_query
    uses: indexer_query.yml
    replicas: 2
...
```

##### Jinad

When running remote Flows, you can access these methods via `PUT /flows/{flow_id}`. You can check the endpoint docs [here](https://api.jina.ai/daemon/#operation/_update_flows__id__put)


