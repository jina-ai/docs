====================
Gracefully Exit Jina
====================

In this section, you will learn best practices shutting down a Flow and exiting Jina correctly.

.. contents:: Table of Contents
    :depth: 4

Feature description and expected outcome
----------------------------------------
Jina provides several methods to exit this service gracefully. In this way, the Flow is terminated and all occupied resources are released.

Before you start
-----------------
Make sure that Jina is installed properly as explained in `Installation <https://docs.jina.ai/chapters/install/os/index.html>`_. Define a Flow as explained in `101: Basic components <https://docs.jina.ai/chapters/101/>` , and `102: How basic components work together <https://docs.jina.ai/chapters/102/>`_.

Implementation
---------------

In Python
^^^^^^^^^

A common way to start a Flow in your Python code is using a ``with`` statement. Moving out from the ``with`` scope, all resources (including Pods of all kinds) of the Flow will be released immediately. 

The source snippet below demonstrates this by defining a Flow named ``f`` containing the two names ``p1`` and ``p2``, only. Next, the ``with`` statement simply contains a ``pass`` statement. The execution of the ``pass`` statement is followed by leaving the scope of the ``with`` statement, and releasing the resources for ``f``.

.. highlight:: python
.. code:: python

    from jina.flow import Flow

    f = (Flow.add(name='p1')
             .add(name='p2'))
    with f:
        pass

Starting a Flow using the :meth:`start` method requires you to also call :meth:`close` method in order to properly shut down the Flow when you do not use it anymore. The source code below demonstrates this in a ``try``-``finally`` block. 

.. highlight:: python
.. code:: python

    from jina.flow import Flow

    f = (Flow.add(name='p1')
             .add(name='p2'))
    try:
        f.start()
    finally:
        f.close()


In the console
^^^^^^^^^^^^^^

If you are running Jina locally, e.g. using the command :command:`jina flow`, you can use the key combinations :kbd:`Control-c` or :kbd:`Command-c` to terminate the running Jina process at any time. All Pods created with :class:`BasePod` will receive this signal and react upon it by shutting down the process accordingly.

Please note container Pods and remote Pods sometimes take longer to shut down. When you open many replicas or many Pods, it may also take some time to release all resources.

Rule of thumb, for an individual Pod/Pea, when you see the output below on the console, then it is already shut down successfully.

.. highlight:: bash
.. code-block:: bash

    BaseExecutor@7317[I]:no update since 2020-04-23 20:31:10, will not save. If you really want to save it, call "touch()" before "save()" to force saving
    BasePea@7317[I]:executor says there is nothing to save
    BasePea@7317[I]:msg_sent: 0 bytes_sent: 0 KB msg_recv: 0 bytes_recv:0 KB
    BasePea@7317[I]:msg_sent: 0 bytes_sent: 0 KB msg_recv: 0 bytes_recv:0 KB
    BasePea@7317[S]:terminated


For Flow, when you see the output below from the console, then it is already shut down.

.. highlight:: bash
.. code-block:: bash

    chunk_idx-3@6376[S]:terminated
    chunk_idx-7@6383[I]:msg_sent: 653 bytes_sent: 590 KB msg_recv: 326 bytes_recv:956 KB
    chunk_idx-7@6383[S]:terminated
    chunk_idx-5@6378[I]:msg_sent: 653 bytes_sent: 587 KB msg_recv: 326 bytes_recv:948 KB
    chunk_idx-5@6378[S]:terminated
    chunk_idx-2@6375[I]:msg_sent: 651 bytes_sent: 583 KB msg_recv: 325 bytes_recv:939 KB
    chunk_idx-2@6375[S]:terminated
    chunk_idx-6@6381[I]:msg_sent: 653 bytes_sent: 589 KB msg_recv: 326 bytes_recv:953 KB
    chunk_idx-6@6381[S]:terminated
    Flow@6331[S]:flow is closed and all resources should be released already, current build level is EMPTY
