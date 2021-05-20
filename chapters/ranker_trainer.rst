===============================
A guide on Jina Ranker Trainer
===============================

.. meta::
   :description: A guide on Jina Ranker Trainer
   :keywords: Jina, Ranker Trainer

.. note:: This guide assumes you have a basic understanding of Jina, if you haven't, please check out `Jina 101 <https://101.jina.ai>`_ first.

.. contents:: Table of Contents
    :depth: 2

Motivation
--------------------

In a search system, query accuracy is one of the most important aspects that will concern users. In most cases, we expect well-ordered query results to be presented.
For one search request, we may have multiple matches and we want to have the most relevant match or top-K matches presented to users. On the other hand, we may also want to aggregate the top-K chunks to top-K matches.


Before you start
-------------------

You have installed the latest stable release of Jina Core according to the instructions found `here <https://docs.jina.ai/chapters/core/setup/index.html>`_ and read `Understand Jina Recursive Document Representation guide. <https://docs.jina.ai/chapters/traversal.html?highlight=recursive>`_

Overview
-----------------





Conclusion
-----------------

In this guide, we introduced why we need :term:`Rankers` and how to use them. Apart from that, we provided some concrete examples of :term:`Rankers` in use.

What's next
-----------------

If you still have questions, feel free to `submit an issue <https://github.com/jina-ai/jina/issues>`_ or post a message in our `community slack channel <https://slack.jina.ai>`_ .

To gain a deeper knowledge of the implementation of Jina Ranker, you can find the source code `here <https://github.com/jina-ai/jina/tree/master/jina/executors/rankers>`_.
