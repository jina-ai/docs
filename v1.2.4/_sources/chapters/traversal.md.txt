# Jina's Recursive Document Representation

This guide explains how Documents are represented within the Jina framework. It is especially relevant for understanding how segmenters, crafters and rankers work.

## General overview

In Jina, the term Document refers to more than just a simple representation of datasets. A Document is used for both indexing and querying. A search query is also a Document itself.

When you index a Document in Jina, it can be represented as a rooted recursive representation (tree). The representation consists of a *root node* and several *child nodes*. In Jina, the root node is the original Document itself, while the *left* and *right* children are referred to as *chunks* and *matches* respectively. The figure below illustrates a basic Document structure that consists of a Document (root node) as well as two child nodes (chunks and matches).

![rooted-binary-tree](./images/overview.png)

The two terms chunks and sequence require further explanation. In short, chunks are a sequence of Documents which can be attached to any parent Document with a higher `granularity` degree. In the figure above this is the root node. 

Similarly, this applies to `matches`. `matches` is a sequence of Documents which are semantically related to the higer-level Document (the root node in the figure above). We will dive into these concepts in more detail below.

- [Chunks](#chunks)
- [Matches](#matches)
- [Let's go deeper: Recursive Document Representation](#lets-go-deeper-recursive-document-representation)
- [Document Traversal with traversal paths](#document-traversal-with-traversal-paths)

## Chunks

### Understanding chunks

Each Jina Document (potentially) consists of a list of *chunks*. A single chunk is a small semantic unit of a Document, like a sentence, a 64x64 pixel image patch or a video sequence. You identify each chunk uniquely via an id called `chunk_id`.

In practice, all chunks for text data will not neccesarily have the same size -- some sentences are short, and others rather long. For regularly divided images it can be different -- a 256x256 pixel image can be split into a patches of 64x64 pixel that all have the same size. 

A chunk also allows you to search for a section of the Document, for example an image followed by a paragraph of text. These are use cases that are more common than you might think.

[//]: # (The original link below does not make sense any more)
[//]: # (https://hanxiao.io/2020/11/22/Primitive-Data-Types-in-Neural-Search-System/)

Following the definition of [primitive data types in Jina](primitive_data_type.rst), a chunk is a property of a Document:


```python
from jina import Document

with Document() as root:
    root.text = 'What is love? Oh baby do not hurt me.'

# Initialised a Document as root with zero chunks.
print(len(root.chunks))                # outputs 0

# Initialise two Documents and add them as chunks to root.
with Document() as chunk1:
    chunk1.text = 'What is love?'
    root.chunks.add(chunk1)
with Document() as chunk2:
    chunk1.text = 'Oh baby do not hurt me.'
    root.chunks.add(chunk2)

# Now the Document has two chunks
print(len(root.chunks))                # outputs 2
```

### Internals: Adding a chunk to a root node

Now let us have a look at what happens when you added a chunk to a root node. The code example below shows the granularity level of a specific node using the attribute `granularity` -- as a root node (level 0), and as a chunk (level 1).

```python
print(root.granularity)                # outputs 0
print(root.chunks[0].granularity)      # outputs 1
```
This is illustrated in the image below:

![granularity](./images/granularity.png)

Furthermore, with the help of the attribute `parent_id` , the relation between the root node and the chunk can be validated.

```python
print(root.id == root.chunks[0].parent_id)  # outputs True
```

The code sample and graph above demonstrates the basic idea of a `chunk` in a Document. At the beginning, by default a Document is initialised with `granularity = 0`. Next, two chunks have been initialised with `granularity = 0`, and have been added to the root node. On addition of a chunk to a node, two things happen:

1. The granularity of the chunk will be increased by 1.
2. The chunk will be referenced to its parent which is the root node.

This procedure allows Jina (and you) to query chunks and reference them back to its root Document at any `granularity` level.

## Matches

In a neural search system (and traditional retrieval system), *matches* are the expected Documents returned from the search index for the user query. In Jina, matches could happen at any level of the representation tree -- the root level or any chunk level.

To fully understand the concept of matches, we introduce a new term, named *adjacency* (short for *a* in the diagram below). The adjacency reflects the level of the Document it is connected to.

**NOTE: granularity and adjacency apply to both chunks and matches.**

```python
from jina import Document

with Document() as root:
    root.text = 'What is love? Oh baby do not hurt me.'

print(root.adjacency)                  # outputs 0

# Initialise two Documents and add as chunks to root.
with Document() as chunk1:
    chunk1.text = 'What is love?'
    root.chunks.add(chunk1)
with Document() as chunk2:
    chunk1.text = 'Oh baby do not hurt me.'
    root.chunks.add(chunk2)

# Add a match Document.
with Document() as match:
    # a match Document semantically related to our root
    match.text = 'What is love? Oh please do not hurt me.'
    root.matches.add(match)

print(len(root.matches))               # outputs 1
print(root.matches[0].granularity)     # outputs 0
print(root.matches[0].adjacency)       # outputs 1
```

![adjacency](./images/adjacency.png)

In the code snippet and diagram above, we initialized a Document as `root` with the text: *What is love? Oh, baby do not hurt me.*. Next, a Document with the text *What is love? Oh please do not hurt me* was added as a match to the `root` node. The matched Document `match` is a Document without any parents, so it stays at the same level as the `root` node with a granularity value of 0. Meanwhile, since `match` is the retrieved result from the `root` node, so the `adjacency` increased to 1.

By default, the `root` node has an `adjacency` of 0. The value increases by 1 when it hits a `match`.

## Let's go deeper: Recursive Document Representation

So far we have only discussed `chunks` and `matches` **with a depth of 1**. While in a real-world scenario, things could be much more complicated than this. For instance, a `chunk` could be further divided into smaller chunks, and a chunk at any level might have its own `matches` at that level.

![go-deeper](https://hanxiao.io/2020/08/28/What-s-New-in-Jina-v0-5/blog-post-v050-protobuf-documents.jpg)

Jina has defined a recursive structure with **arbitrary width and depth** instead of a trivial bi-level structure. Roughly speaking, chunks can have the next level chunks and the same level matches; and so do matches. This could go on and on. The following figure illustrates this structure [Ref: New Features in Jina v0.5 You Should Know About](https://hanxiao.io/2020/08/28/What-s-New-in-Jina-v0-5/).

![recursive](./images/recursive.png)

This recursive structure provides Jina the flexibility to cover any complex use case that may require search at different semantic units. Besides that, the recursive structure enables Jina rankers to accumulate scores from lower granularities to upper granularities, such as `Chunk2DocRankers`. For example, in NLP a long Document is composed of semantic chapters; each chapter consists of multiple paragraphs, which can be further segmented into sentences. In CV, a video is composed of one or more scenes, including one or more shots (i.e. a sequence of frames taken by a single camera over a continuous period of time). Each shot includes one or more frames. 

Such hierarchical structures can be very well represented with the recursive representation. The image below shows the tree view (with a depth of 3):

![tree-view](./images/tree.png)

## Document traversal with traversal paths

As you have already learned from [Jina 101](https://101.jina.ai), you need to apply transformation (i.e. a `callback`) on a different level of Documents. Given the tree structure, how could we achieve that? The answer is `traversal`.

Jina has defined a method called `traversal()` within the class of a Document. The method looks like this:

```python
def traverse(self, traversal_path: str, callback_fn: Callable, *args, **kwargs) -> None
    """Traversal apply `:meth:callback_fn` on the recursive tree representation."""
    ...
```

This allows you to apply a callback function named `callback_fn()` based on `traversal_path`. The `traversal_path` is defined as shown below:

![nodes](./images/nodes.png)

With these pre-defined node names, you are able to apply any callbacks (defined as `_apply_all` in the `driver`) to a specific node. In the YAML configuration below, the `VectorSearchDriver` was applied to node `c`, `KVSearchDriver` was applied to node `cm` (matches of chunks).

```yaml
!CompoundIndexer
...
requests:
  on:
    SearchRequest:
      - !VectorSearchDriver
        with:
          traversal_path: ['c']
      - !KVSearchDriver
        with:
          traversal_path: ['cm']
```

The two drivers will be applied sequentially, so the first one applies `VectorSearchDriver` for the indexer. This step helps to get matches for chunks (cm), and add them. The matches for the chunks will contain the scores and the id of the Document. Then, `KVSearchDriver` will be applied at matches of chunks (cm) in order to get the embeddings for `cm` via the id of the analysed Document.
