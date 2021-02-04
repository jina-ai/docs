==================
Classifier in Jina
==================
------------------------------------------------------------------------------------------------------------------------------------------------------
Summary: Classifier allows you to perform classification and regression based on given input and have the predicted hard/soft labels as output.
------------------------------------------------------------------------------------------------------------------------------------------------------

What is Classifier in Jina?
------------------------------------------
:class:`Classifier` is one of the members in Jina executors family. Which can be used to generated Machine Learning/Deep Learning-based tags.

Why introduce Classifier in Jina?
------------------------------------------
:class:`Classifier` can be used for document meta data enrichment. :class:`Classifier` may help to generate and add tags to Documents automatically
to serve as a smart annotation method. It can enable future features such as smart sub-flow routing.


Before you start:
-----------------
You may want to check `here <https://github.com/jina-ai/jina/pull/1194>`_ to learn more details about the implementation of :class:`Classifier`.

BaseClassifier
---------------------------------
:class:`BaseClassifier` is the interface that we have in Jina Core, subclasses of :class:`BaseClassifier` should be implemented and used.

Details of :class:`BaseClassifier` can be found `here <https://docs.jina.ai/api/jina.executors.classifiers.html>`_.

Corresponding Drivers
----------------------------------
Jina provide corresponding :class:`drivers` to support :class:`Classifier`. The :class:`Classifier` will be equipped with :class:`drivers` to generate tags.
For examples, you may want to choose :class:`BinaryPredictDriver` to receive data from a binary :class:`Classifier` and interpret the data into binary labels.

Details of :class:`PredictDriver` can be found `here <https://docs.jina.ai/api/jina.drivers.predict.html>`_.


