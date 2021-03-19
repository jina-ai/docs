# CRUD Operations

##### In this section, you will get an overview of how Jina implements CRUD: Create, Read, Update, Delete  

### Feature description

#### Overview

CRUD abbreviates the four actions **C**reate, **R**ead, **U**pdate, and **D**elete. All together they form the basis of a database management system (DBMS). While Jina itself is *not* a DBMS, it does provide these methods of interacting with the data stored in its indexes. Jina uses the following terms for this:

CRUD Term | Jina Term 
--------- | ----------
Create    | Index
Read      | Query
Update    | Update
Delete    | Delete

We hope that the table above will help to avoid any possible confusion regarding the terms that are in use. We discuss the four actions in more detail below.

#### Availability in Jina

Before version `1.0`, Jina only supported indexing (creating) and querying (reading) Documents. In order to update or delete a Document, you had to edit your dataset first, and then rebuild both the Flow and the corresponding indexes. Needless to say, this could create problems if you had large datasets.

With the release of version `1.0` we are introducing **update** and **delete** operations. These are implemented across our Executors and Drivers, and allow you to update and delete Documents by their ids. With this, the full CRUD functionality is established and available for you in Jina.

#### CRUD actions in Jina

What do the four CRUD actions mean, and do in Jina in particular:

- Index: read a new Document, and add it to the search index.

- Query: retrieve indexed Documents based on given search criteria.

- Update: update an existing Document by re-indexing it. Requires the
  index id of the Document to be updated.

- Delete: remove an existing Document from the search index. Requires
  the index id of the Document to be deleted from the search index.

## Before you start 

Before you start indexing documents with Jina and searching through the database, we recommend you to study the basics as explained [here](https://docs.jina.ai/chapters/core/introduction/index.html).

### Implementation

A basic example of this can be found in the [`test_crud.py`](https://github.com/jina-ai/jina/blob/master/tests/integration/crud/simple/test_crud.py). Furthermore, the `Flow` class now supports `delete` and `update` methods, with a signature similar to `index`. The source code below demonstrates how to use these methods.

```python
    # create 10 random documents, and index them
    docs = random_docs(10)

    with f:
        f.index(input_fn=docs)

    # create 10 more random documents, and update the search index
    # new documents are added, and documents with the same id are updated
    new_docs = random_docs(10)
    
    with f:
        f.update(input_fn=new_docs)

    # extract the document ids from the docs list, and remove them from
    # the search index
    doc_ids = [d.id for d in docs]
    
    with f:
        delete_ids = [d.id for d in doc_ids]
        f.delete(delete_ids)
```

Note: As explained above, deletion and update of the search index will happen by `id` of the document.

## Limitations

Unfortunately there are some limitations to what Jina can do for the moment. These were trade-offs we needed to implement to keep Jina performant, robust, and easy to use. Some of these will be addressed in future versions, while some are intrinsic to Jina's architecture.

1. **Partial update**
   For the moment we do not support partial updates. So if you want to update a document, you need to send the entire document. This is due to Jina's architecture: the document is sent as one piece through the Flow.

2. **Update flows**
   In the context of Flows with segmenters and chunks, a Document may end up being split into chunks. Currently, the Update request will not work for these. You will need to manually remove the chunks by their `ids`. Then you can remove the parent document as well, by its `id`. Finally, you can index the new document, with its new contents (and thus new, different child chunks).

3. **Sharding**
  When sharding data in indexers, the data will be split across these. This is achieved due to the `polling: any` configuration. During a query, you will need to set `polling: all`. However, this will lead to some shards getting a query request with a key that does not exist. In this case, warnings will be emitted by the indexer. You can ignore these within this context. The warnings are there for the situations when missing keys are *not* expected.

4. **Indexing while querying**
   The index, update, and delete operations cannot be executed within the same context as the query operation. This is due to the way flushing to disk works within the Flow and Executor context lifecycle. This is applicable across all VectorIndexers. Thus, you need to exit the Flow context when you want to switch from one set of operations to the other.

   You can see this in the code listing at the beginning of this chapter.

5. **Expanding size**
   The update and delete operations use a masking underneath. This is done to maintain high performance overall. However, this means that old data will not be deleted, but will simply be masked as being deleted. Thus the size of the indexer on disk (and in memory) will grow over time the more update or delete operations you perform. We recommend setting the `delete_on_dump` parameter of the `NumpyIndexer` to `True`. When the Flow is shut down, the data that has been marked as deleted will be permanently deleted before being saved to disk. By default the parameter is set to `False`, as setting it to `True` will make the shut down process slower.  

