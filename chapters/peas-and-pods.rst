Development Guide: Peas and Pods in Jina
=========================================

.. meta::
   :description: Development Guide: Peas and Pods in Jina
   :keywords: Jina, pea, pod

.. note:: This guide assumes you have a basic understanding of Jina, if you haven't, please check out `Jina 101 <https://101.jina.ai>`_ first.
.. note:: This guide assumes you have a basic understanding of Parallelization inside Jina, if you haven't, please check out `Parallelization in Jina <../parallel>`_ first.

You might already learned from `Jina 101 <../101.rst>`_.
Jina :term:`Pea` wraps an Executor and lets it exchange data with other Peas.
Jina :term:`Pod` is a container and interface for one or multiple Peas that have the same properties.
It coordinates Peas to improve efficiency and scaling.
Beyond that, a Pod adds further control, scheduling, and context management to its Peas.

In this tutorial, we dive deeper into Peas and Pods,
in order to help you gain more knowledge on the architecture and design principle of Jina.

.. contents:: Table of Contents
    :depth: 3

Parallelization for Peas and Pods
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Jina offers parallelization at different levels, such as

1. (Local) Threads/Process with `BasePea`.
2. Inside container with `ContainerPea`.
3. Remotely with `RemotePeas` and `RemotePods`.

Jina Daemon (JinaD) enable Jina to spin up and distribute Peas, Pods, Flows in any system.

Pea and Pod in Action
^^^^^^^^^^^^^^^^^^^^^^^

Example Setup
--------------

The Jobs of a Pea
------------------

Stateless and Stateful Pea
---------------------------

How Local Pea and Remote Pea interact with Pod
-----------------------------------------------

Conclusion
^^^^^^^^^^^

