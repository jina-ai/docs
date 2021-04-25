Glossary
============

The following terminology is commonly used within the Jina framework. Jina is an active development, meaning these terms may be updated and refined regularly as new and improved features are added.

.. glossary::

    Chunk
        Chunks are semantic units from a larger parent Document. A Chunk is a Document itself. Subdividing parent Documents into Chunks is performed by the Segmenter class of Executors. Examples of individual units would be sentences from large documents or pixel patches from an image.  `For further information see the Understand Jina Recursive Document Representation guide. <https://docs.jina.ai/chapters/traversal.html?highlight=recursive>`_

    Client
        A Python client that connects to Jina gateway.

    Container
        A container is a standard unit of software that packages up code and all its dependencies so the application runs quickly and reliably from one computing environment to another. Jina Peas and Pods can be deployed within containers to avail of these features.

    Crafter
        Crafters are a class of Executors. They transform a Document. This includes tasks such as resizing images or reducing all text in a document to lowercase.

    Document
        Document is the fundamental data type within Jina. Anything you want to search within Jina is considered a Document. This could include images, sounds clips or text documents.

    Driver
        Drivers wrap an Executor. Drivers interpret network traffic into a format the Executor can understand. All Executors must have at least one driver. A Driver is further wrapped by a Pea.

    Embedding space
        Embedding space is the vector space in which the data is embedded after encoding. Depending on how the space is created, semantically similar items are closer. Position (distance and direction) in the vector space potentially encodes semantics. `[reference_1] <https://link.springer.com/referenceworkentry/10.1007%2F978-0-387-73003-5_573#:~:text=Embedding%20space%20is%20the%20space,than%20of%20the%20ambient%20space.>`_

    Encoder
        Encoders are a class of Executors. Their purpose is to generate *meaningful* vector representations from high dimensional data. This is achieved by passing the input to a pretrained model which returns a fixed length vector.

    Evaluator
        Evaluators are a class of Executors. Evaluators provide advanced evaluation metrics on the performance of a search system. Therefore, they compare a Document against a ground truth Document. Evaluators provide several kinds of metrics:

        - Distance metrics, such as cosine and euclidean distance.

        - Evaluation metrics, such as precision and recall.

    Executor
        Executors represent an algorithmic class within the Jina framework. Examples include Ranker classes, Evaluator classes etc.

    Flow
        A Flow is created for each high-level task within Jina, such as querying or indexing. It manages the state and context of the Pods or Peas who work together to complete this high-level task.

    gRPC
        `gRPC <https://en.wikipedia.org/wiki/GRPC>`_ is a modern open source high performance RPC (add wikipedia link) framework that can run in any environment. It can efficiently connect services in and across data centers with pluggable support for load balancing, tracing, health checking and authentication.

    HeadPea
        There are two possible reasons for a HeadPea to exist:

        - `uses_before` is defined in a Pod or

        - `parallel > 1` for a given Pod.

        In both cases, traffic directed to a Pod arrives at the HeadPea and it distributes it afterwards to the Peas in the Pod. Its counterpart it the TailPea

    Indexer
        Indexers are a stateful class of Executors. Indexers save/retrieve vectors and key-value information to/from storage.

    Indexing
        Indexing is the process of collecting, parsing, and storing of the data to facilitate fast and accurate information retrieval. This includes adding, updating, deleting, and reading of Jina Documents.

    JAML
        JAML is a Jina YAML parser that supports loading, dumping and substituting variables.

    Jina Box
        Jina Box is a light-weight, highly customizable JavaScript-based front-end component. Jina Box enables devs to easily create front-end applications and GUIs for their end-users.

    Jina Dashboard
        Jina Dashboard is a low code environment to create, deploy, manage and monitor Jina flows. It is also tightly integrated with our Hub to create a seamless end-to-end experience with Jina.

    Jina Hub
        Jina Hub is a centralized repository that hosts key Jina executors and integrations contributed by the community or the Jina Dev team. The components (pods) or full flows (apps) are offered on an accessible, easy to use platform.

    JinaD
        JinaD stands for Jina Daemon. It is a daemon enabling Jina on remote and distributed machines. `[reference_2] <https://www.google.com/url?q=https://en.wikipedia.org/wiki/Daemon_(computing)&sa=D&source=editors&ust=1612348052031000&usg=AOvVaw0bLJC-Qxk62HJKqZ-Px7mJ>`_

    Match
        A result retrieved from the Flow. This is attached to the Document in the response, in the `matches` attribute.

    Neural Search
        Neural Search is Semantic search created using deep learning neural networks to create vector embeddings. The search itself is typically performed by measuring distances between these vector embeddings.

    Optimizer
        Optimizer is a Jina feature that runs the given flows on multiple parameter configurations in order to find the best performing parameters. In order to run them, an Evaluator needs to be defined.

    Parallelization
        Parallelization means data is processed simultaneously on several machines (or virtual machines). Data could be split in equal parts across those machines, or it could be duplicated.

    Pea
        A Pea wraps an Executor and driver and lets it exchange data with other Peas. This can occur over a remote network or locally within the same system. Peas can also run in standalone Docker containers, which manages all dependencies and context in one place. Peas are stored within Pods.

    Pod
        A Pod is a wrapper around a group of Peas with the same executor. Since Peas hold the same type of Executor, the Pod unifies the network interfaces of those Peas. The Pod makes them look like one single Pea from the rest of the components of a Flow. After a Flow is started, the Pod itself does not receive any traffic anymore.

    Primitive data types
        Jina offers a Pythonic interface to allow users access to and manipulation of Protobuf objects without working with Protobuf itself through its defined primitive data types.

    Protobuf
        Protocol buffers are Google's language-neutral, platform-neutral, extensible mechanism for serializing structured data – think XML, but smaller, faster, and simpler. You define how you want your data to be structured once, then you can use special generated source code to easily write and read your structured data to and from a variety of data streams and using a variety of languages.

    QueryLanguage
        Within the context of Jina, QueryLang  is a specific set of commands. QueryLang adds logical statements to search queries, such as filter, select, sort, reverse. To see the full list see `here <https://hanxiao.io/2020/08/28/What-s-New-in-Jina-v0-5/#new-query-language-driver>`_

    REST
        `REST <https://en.wikipedia.org/wiki/Representational_state_transfer>`_ is an application programming interface (API or web API) that conforms to the constraints of REST architectural style and allows for interaction with RESTful web services.

    Ranker
        Rankers are a class of Executors. They provide ranking functionality, based on users’ business logic needs.

    Runtime
        A Jina Runtime is a procedure that blocks the main process once running. It begins when a program is opened (or executed) and ends when the program ends or is closed.

    Searching
        Searching is the process of retrieving previously indexed Documents for a given query. A query in Jina can be text, an image, a video or even more complex objects, like a pdf.

    Segmenter
        Segmenters are a class of Executors. Segmenters divide large Documents into smaller parts. For example, they divide a text document into paragraphs. A user can determine the granularity or method by which data should be divided. `For further information see the Understand Jina Recursive Document Representation guide. <https://docs.jina.ai/chapters/traversal.html?highlight=recursive>`_

    Semantic Search
        Semantic search is search with meaning, as distinguished from lexical search, where the search engine looks for literal matches of the query words or variants of them, without understanding the overall meaning of the query `[reference_3] <https://en.wikipedia.org/wiki/Semantic_search>`_

    Sharding
        Sharding is splitting data across multiple Peas, which are all stored inside a single Pod.

    Shards
        A section of the data stored or processed in separate Peas inside a single Pod.

    TailPea
        There are two possible reasons for a TailPea to exist:

        - `uses_after` is defined in a Pod or

        - `parallel > 1` for a given Pod.

        In both cases, the TailPea collects the calculated results from the Peas in the Pod and forwards it to the next Pod. For example, when using sharding with indexers, the TailPea merges the retrieval results. This is achieved by adding `uses_after`.

    Vector embedding
        Vector embedding is a vector representation of the semantic meaning of a single document.

    Workspace
        A workspace is a directory that stores the indexed files (embeddings and documents) plus the serialization of executors if needed. A workspace is automatically created after the first indexing.

    YAML
        YAML (a recursive acronym for "**YAML** Ain't Markup Language") is a human-readable data-serialization language. It is commonly used for configuration files and in applications where data is being stored or transmitted.
