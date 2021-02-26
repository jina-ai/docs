==============================
Logging configuration in Jina
==============================

.. meta::
   :description: A guide on logging configuration in Jina
   :keywords: Jina, logging

.. contents:: Table of Contents
    :depth: 2

Motivation
-------------------

In order to better understand, monitor, and debug the running and lifetime of Jina's peas, pods, and flow, Jina offers logging to help log the messages.

Before you start
-------------------
You have installed the latest stable release of Jina according to the instructions found `here <https://docs.jina.ai/chapters/core/setup/index.html>`_ .


Overview
-------------------

Jina logging message at first glance
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Let's take a look at a logging example first. This will help you quickly understand the content of Jina's logging message.

Jina logs messages with different formats in different situations. The example below follows the most commonly used format:

.. highlight:: bash
.. code-block:: bash

   pod1@25908[I]:starting jina.peapods.runtimes.zmq.zed.ZEDRuntime...
   pod1@25908[I]:input tcp://0.0.0.0:63915 (PULL_BIND) output tcp://0.0.0.0:63916 (PUSH_BIND) control over tcp://0.0.0.0:63914 (PAIR_BIND)
   pod1@25903[S]:ready and listening

It follows the following format:

.. highlight:: text
.. code-block:: text

    name@process[levelname]:message

Where ``name`` is the name of the Pod, ``process`` is the process ID, ``levelname`` is the level.
Jina logs messages in 6 different levels (DEBUG, INFO, WARNING, ERROR, CRITICAL, SUCCESS).
The default level can be controlled by ``JINA_LOG_LEVEL`` environment variable or in YAML configuration.


Logging handler
^^^^^^^^^^^^^^^

You can choose the way to monitor and save logs by using different logging handlers. Jina logger supports different ``Handlers`` to control where the messages are sent/stored:

.. list-table:: Logging handler
   :widths: 25 50 25 30
   :header-rows: 1

   * - Name
     - Description
     - Format
     - Notes
   * - FileHandler
     - Jina offers the possibility to put the logs in local files either as simple text or as json format.
     - asctime:name@process[levelname]:message
     - output: jina-uptime.log
   * - StreamHandler
     - Jina logger uses a `StreamHandler` to print the logs in each Pea's local stdout, which you can view in console.
     - name@process[levelname]:message
     -
   * - SysLogHandler
     - Jina uses SysLogHandler to output logs to system logs.
     - name@process[levelname]:message
     - When host and port not given then record it locally, /dev/log on linux /var/run/syslog on mac.
   * - FluentHandler
     - Given the distributed nature of Jina's Peas and Pods, Jina offers a flexible solution that lets the user configure how and where the logs are forwarded.
     - Need to set ``hostname``, ``process`` ``levelname``
     - FluentD then will have its own configuration to forward the messages according to its own syntax


Logging configuration in Action
--------------------------------------

Jina logging can be configured in YAML file.

.. highlight:: python
.. code-block:: python

        from jina.flow import Flow
        from jina import Document

        f = Flow(log_config='logging_cfg.yml').add().add()
        # If you want to set the configuration for a certain Pod
        #f = Flow().add(log_config='logging_cfg.yml').add()

In YAML file you can customized the logger, choose the handler you need to monitor and save logs. ``logging_cfg.yml`` can be configured as follows:

.. highlight:: yaml
.. code-block:: yaml

    handlers:  # enabled handlers, order does not matter
      - StreamHandler
      - SysLogHandler
      - FluentHandler
    level: INFO  # set verbose level
    configs:
      FileHandler:
        format: '%(asctime)s:{name:>15}@%(process)2d[%(levelname).1s]:%(message)s'
        output: 'jina-{uptime}.log'
        formatter: JsonFormatter
      StreamHandler:
        format: '{name:>15}@%(process)2d[%(levelname).1s]:%(message)s'
        formatter: ColorFormatter
      SysLogHandler:
        ident: # this will be prepend to all messages
        format: '{name:>15}@%(process)2d[%(levelname).1s]:%(message)s'
        host: # when not given then record it locally, /dev/log on linux /var/run/syslog on mac
        port: # when not given then record it locally,  /dev/log on linux /var/run/syslog on mac
        formatter: PlainFormatter
      FluentHandler:
        # this configuration describes where is the fluentD daemon running and waiting for logs to be emitted.
        # FluentD then will have its own configuration to forward the messages according to its own syntax
        # prefix will help fluentD filter data. This will be prepended for FluentD to easily filter incoming messages
        tag: jina
        host: 0.0.0.0
        port: 24224
        format:
          host: '%(hostname)s'
          process: '%(process)s'
          type: '%(levelname)s'


If you want to hide the logs of a certain Pod, you can set ``quiet=True`` in flow like:

.. highlight:: python
.. code-block:: python

        f = Flow().add(quiet=True).add()
        with f:
            f.index(Document())

Or in YAML flow configuration:

.. highlight:: yaml
.. code-block:: yaml

    !Flow
    pods:
      - uses: Pod1.yml
        quiet: true
      - uses: Pod2.yml


What's more
-------------

FluentD
^^^^^^^^^
`Fluentd <https://github.com/fluent/fluentd>`_ is an open source data collector for unified logging layer.

`Fluentd <https://github.com/fluent/fluentd>`_ is expected to be used as a daemon receiving messages from the Jina logger and forwarding them to specific outputs using its
output plugins and configurations. 
 
Although fluentd can be configured to forward logs to the user's preferred destinations, Jina offers a default configuration under `/resources` folder which expects a fluentd daemon to be running
inside every machine running a Jina instance or Pea. Then the default configuration must be adapted to send the logs to the specific server
where the Flow and the dashboard will be run. (This default behavior will evolve)

See default `fluent.conf` configuration provided by Jina. It takes every input coming in the listening 24224 port and
depending on the kind of message, sends it to a local temporary file, from where the Flow will read the incoming file.

.. highlight:: xml
.. code-block:: xml

    <source>
      @type forward
      @id http_input

      port 24224
    </source>

    ## match tag=myapp.** and forward and write to file in local
    <match jina.**>
      @type file
      path /tmp/jina-log
      append true
      <buffer>
          @type file
          flush_mode interval
          flush_interval 1s
      </buffer>
    </match>

    <match jina-profile.**>
      @type file
      path /tmp/jina-profile
      append true
      <buffer>
          @type file
          flush_mode interval
          flush_interval 1s
      </buffer>
    </match>


This is the default configuration, that works well together with the configuration provided in ``logging.fluentd.yml``,
which controls the tags assigned to the different type of logs, as well as the host and port where the handler will send the 
logs. By default it expects a fluentd daemon to run in every local and remote Pea (this is the most scalable configuration)

.. highlight:: yaml
.. code-block:: yaml

    # this configuration describes where is the fluentD daemon running and waiting for logs to be emitted.
    # FluentD then will have its own configuration to forward the messages according to its own syntax
    # prefix will help fluentD filter data. This will be prepended for FluentD to easily filter incoming messages
    tag: jina
    profile-tag: jina-profile
    host: 0.0.0.0
    port: 24224


To better understand fluentd configuration and to see how you can adapt to your needs, please see `Fluentd docs <https://docs.fluentd.org/configuration>`_.

Start fluentd daemon
^^^^^^^^^^^^^^^

For the logging using fluentd to work and therefore for the dashboard to properly have access to the logs, the user needs to
start fluentd daemon. It can be done in every remote and local machine or just in the host where the FluentDHandler will send the logs.

- Install `https://docs.fluentd.org/installation <https://docs.fluentd.org/installation>`_ .
- Run ``fluentd -c ${FLUENTD_CONF_FILE}`` (Default conf file ``${JINA_RESOURCES_PATH}/fluent.conf``)


Conclusion
-----------------

In this guide, we introduced what is Jina Logger and how we can configure the logging in Jina.

What's next
-----------------

If you still have questions, feel free to `submit an issue <https://github.com/jina-ai/jina/issues>`_ or post a message in our `community slack channel <https://slack.jina.ai>`_ .

To gain a deeper knowledge on the implementation of Jina logging, you can find the source code `here <https://github.com/jina-ai/jina/tree/master/jina/logging>`_.
