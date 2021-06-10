# Built-in Simple Executors and Reserved `uses`

- [Built-in Simple Executors and Reserved keyword `uses` in Jina](#built-in-simple-executors-and-reserved--uses--in-jina)
  * [What is Simple Executor?](#what-is-simple-executor)
  * [What are the reserved `uses`?](#what-are-the-reserved-uses)
  * [How to use built-in Simple Executors?](#how-to-use-built-in-simple-executors)

## What is Simple Executor?

In Jina we have several built-in *simple* executors, which are located in `jina/resources/`. They are called simple for two reasons:

- They only require a YAML configuration file, no Python code is required;
- They inherit from `BaseExecutor` directly, therefore the logic thus fully relies on the drivers.

For example, the built-in `_clear` executor is defined as:

```yaml
!BaseExecutor
with: {}
metas:
  name: clear
requests:
  on:
    SearchRequest:
      - !ExcludeReqQL
        with:
          fields:
            - search
    TrainRequest:
      - !ExcludeReqQL
        with:
          fields:
            - train
    IndexRequest:
      - !ExcludeReqQL
        with:
          fields:
            - index
    DeleteRequest:
      - !ExcludeReqQL
        with:
          fields:
            - delete
    UpdateRequest:
      - !ExcludeReqQL
        with:
          fields:
            - update
    ControlRequest:
      - !ControlReqDriver {}
```

It cleans up requests from the request-level protobuf message to reduce the total size of the message. This is often useful when the proceeding Pods require only a signal, not the full message.

## What are the reserved `uses`?

To help users quickly use these patterns, we reserved the following keywords for the `uses`. They all start with underscore.

| Reserved Name | Description |
| --- | --- |
| `_clear` | Clear request body from a message |
| `concat` | Concat all embeddings into one, grouped by ``doc.id`` |
| `eval_pr` | Evaluate ``precision`` and ``recall`` |
| `_index` | A simple compound indexer of ``NumpyIndexer`` and ``BinaryPbIndexer`` |
| `_logforward` | Like `_pass`, but print the message |
| `_pass` | Pass the message to the downstream |
| `_merge_chunks` | Merges chunks from requests |
| `_merge_matches` | Merges matches from requests |
| `_merge_matches_topk` | Merge the top-k search results at match level |
| `_merge_eval` | Merge all evaluations into one, grouped by ``doc.id`` |
| `_merge_root` | Merge results at root level |

## How to use built-in Simple Executors?

You can directly use this executor by specifying `--uses=_clear`, or use it via `--uses-after` after collecting results from replicas.

Where ever you need to use `uses` in Jina, you can take any one from the table to fill in.
