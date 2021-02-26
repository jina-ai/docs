==================
Classifier in Jina
==================
Classifier allows you to perform classification and regression based on given input and have the predicted hard/soft labels as output.

.. contents:: Table of Contents
    :depth: 3

Motivation
------------------------------------------
:class:`Classifier` can be used for document meta data enrichment. :class:`Classifier` may help to generate and add tags to Documents automatically
to serve as a smart annotation method. It can enable future features such as smart sub-flow routing.

Before you start
-----------------
You may want to check `here <https://github.com/jina-ai/jina/pull/1194>`_ to learn more details about the implementation of :class:`Classifier`.

Overview
------------------------------------------
:class:`Classifier` is one of the members in Jina executors family. Just like Machine Learning/Deep Learning classification
models, :class:`Classifier` can be used to generated tags for data in Jina.

BaseClassifier
---------------------------------
:class:`BaseClassifier` is the interface that we have in Jina Core, subclasses of :class:`BaseClassifier` should be implemented and used.
More implementations of :class:`BaseClassifier` will be added into `Jina Hub <https://github.com/jina-ai/jina-hub>`_ in the future.
Details of :class:`BaseClassifier` can be found `here <https://docs.jina.ai/api/jina.executors.classifiers.html>`_.


Corresponding Drivers
----------------------------------
Jina provide corresponding :class:`Drivers` to support :class:`Classifier`. The :class:`Classifier` will be equipped with :class:`Drivers` to generate tags.
For examples, you may want to choose :class:`BinaryPredictDriver` to receive data from a binary :class:`Classifier` and interpret the data into binary labels.

Details of :class:`PredictDriver` can be found `here <https://docs.jina.ai/api/jina.drivers.predict.html>`_.

What's Next
====================

Thanks for your time & effort while reading this guide!

We don't have implementations of :class:`Classifier` in Jina hub, and we are looking forward your contribution!

If you still have questions, feel free to `submit an issue <https://github.com/jina-ai/jina/issues>`_ or post a message in our `community slack channel <https://docs.jina.ai/chapters/CONTRIBUTING.html#join-us-on-slack>`_ .

To gain a deeper knowledge on the implementation of Jina :class:`Classifier`, you can find the source code `here <https://github.com/jina-ai/jina/tree/master/jina/executors/classifiers>`_.

