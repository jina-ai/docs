Glossary
============

The following terminology is commonly used within the Jina framework. Jina is an active development, meaning these terms may be updated and refined regularly as new and improved features are added.

.. glossary::

    Chunk
        Chunks are individual semantic units of a Document. This occurs within the Segmenter class of Executors. Examples of individual units would be sentences from large documents or pixel patches from an image. `For further information see the Understand Jina Recursive Document Representation guide. <https://docs.jina.ai/chapters/traversal/index.html?highlight=recursive>`_
    Client
        A Python client that connects to a remote Jina gateway.
    Container
        A Docker container image is a lightweight, standalone, executable package of software that includes everything needed to run an application: code, runtime, system tools, system libraries and settings.
    Crafter
        Crafters are a class of Executor whose purpose is to alter a Document to match a certain requirement. This could include tasks such as resizing images or reducing all text in a document to lowercase.
    Document
        Documents are the most basic data types available within Jina. Anything you want to search for within Jina is considered a document, this could include images, sounds clips or text documents.
    Driver
            Drivers wrap an Executor and interprets network traffic into a format the Executor can understand. All Executors must have at least one driver. A Driver is further wrapped by a Pea.
    Embedding space
        Embedding space is the vector space in which the data is embedded after dimensionality reduction. Depending on how the space is created, semantically similar items are put together and dissimilar items are kept far apart. Position (distance and direction) in the vector space can encode semantics in a good embedding. `reference_1 <https://link.springer.com/referenceworkentry/10.1007%2F978-0-387-73003-5_573#:~:text=Embedding%20space%20is%20the%20space,than%20of%20the%20ambient%20space.>`_
    Encoder
        Encoders are a class of Executor whose purpose is to take a variable-length input and output a fixed-length semantic embedding vector. This is achieved by passing the input to a pre-created language model which returns a fixed length context specific vector.
    Evaluator
        Evaluators are a class of Executor whose purpose is to provide advanced evaluation metrics on the performance of a search system. Distance metrics such as cosine distance and euclidean distance. As well evaluation metrics such as precision, and recall between the results and the ground truth (provided by user) can be computed.
    Executor
        Executors represent an algorithmic class within the Jina framework. Examples include Ranker classes, Evaluator classes etc.
    Flow
        A Flow is created for each high-level task in Jina, such as querying or indexing. It manages the state and context of the Pods or Peas who work together to complete this high-level task.
    Index Mode
        During Index mode, Jina performs collecting, parsing, and storing of the data to facilitate fast and accurate information retrieval.
    Indexer
        Encoders are a class of Executor whose purpose is to save and retrieve vectors and key-value information from storage.
    JAML
        JAML is a Jina YAML parser that supports loading, dumping and substituting variables.
    Jina Box
        Jina Box is a light-weight, highly customizable JavaScript-based front-end tool that enables devs to easily create front-end applications and GUIs for their end-users.
    Jina Hub
        Jina Hub is a centralized repository that hosts key Jina executors and integrations contributed by the community or the Jina Dev team, the components (pods) or full flows (apps) are offered on an accessible, easy to use and navigate platform.
    JinaD
        JinaD stands for Jina Daemon. It is a daemon tool for running Jina on remote and distributed machines.
    Optimizer
        Optimizer is a Jina feature that runs the given flows on multiple parameter configurations in order to find the best performing parameters.
    Pea
        A Pea wraps an Executor and driver and lets it exchange data with other Peas. This can occur over a remote network or locally within the same system. Peas can also run in standalone Docker containers, which manages all dependencies and context in one place. Peas are stored within Pods.
    Pod
        A Pod is a microservice managing one or more Peas which have the same Executor class. They are run in parallel on a localhost or over a network
    Primitive types
        Jina offers a Pythonic interface to allow users access and manipulate Protobuf object without working with Protobuf itself through its defined primitive data types.
    Protobuf
        Protocol buffers are Google's language-neutral, platform-neutral, extensible mechanism for serializing structured data – think XML, but smaller, faster, and simpler. You define how you want your data to be structured once, then you can use special generated source code to easily write and read your structured data to and from a variety of data streams and using a variety of languages.
    QueryLanguage
        QueryLang is within the context of Jina, is a specific set of commands that can be used in Jina search to add logical statements to search queries.  Such as filter, select, sort, reverse. To see the full list see `here <https://hanxiao.io/2020/08/28/What-s-New-in-Jina-v0-5/#new-query-language-driver>`_
    REST
        REST is an application programming interface (API or web API) that conforms to the constraints of REST architectural style and allows for interaction with RESTful web services.
    Ranker
        Rankers are a class of Executor whose purpose is to provide advanced ranking functionality, based on users’ business logic needs.
    Runtime
        A Jina Runtime is a procedure that blocks the main process once running. It begins when a program is opened (or executed) and ends when the program is quit or closed.
    Search Mode
        TBD
    Segmenter
        Segmenters are a class of Executor whose purpose is to divide a large Document into smaller parts. For example, dividing a text document into paragraphs. A user can determine the granularity or method in which data should be converted. `For further information see the Understand Jina Recursive Document Representation guide. <https://docs.jina.ai/chapters/traversal/index.html?highlight=recursive>`_.
    Sharding
        Sharding is splitting data across multiple Peas which are all stored inside a single Pod.
    Shards
        A section of the data stored or processed in multiple Peas inside a single Pod.
    Vector embedding
        Vector embedding is a vector representation of the semantic meaning of a single document.
    Workspace
        Within the Jina framework a workspace is a directory that stores the indexed files (embeddings and documents), plus the serialization of executors if needed. A workspace is automatically created after the first indexing.
    YAML
        YAML is a human-readable data-serialization language. It is commonly used for configuration files and in applications where data is being stored or transmitted.
    gRPC
        gRPC is a modern open source high performance RPC framework that can run in any environment. It can efficiently connect services in and across data centers with pluggable support for load balancing, tracing, health checking and authentication.
