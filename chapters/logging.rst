==============================
Configuring Logging
==============================

.. meta::
   :description: A guide on logging configuration in Jina
   :keywords: Jina, logging

.. contents:: Table of Contents
    :depth: 2

Motivation
-------------------

In order to better monitor, and debug the running and lifetime of Peas, Pods, and Flows, Jina offers configurable logging.

Before you start
-------------------

You have installed the latest stable release of Jina Core according to the instructions found `here <https://docs.jina.ai/chapters/install/index.html>`_ .


Overview
-------------------

Jina logging messages at first glance
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Let's start from a logging example.
Jina logs messages with different formats in different situations.
The example below follows the most commonly used format:

.. highlight:: bash
.. code-block:: bash

    pod1@25908[I]:starting jina.peapods.runtimes.zmq.zed.ZEDRuntime...
    pod1@25908[I]:input tcp://0.0.0.0:63915 (PULL_BIND) output tcp://0.0.0.0:63916 (PUSH_BIND) control over tcp://0.0.0.0:63914 (PAIR_BIND)
    pod1@25903[S]:ready and listening

This is how the message is composed:

.. highlight:: text
.. code-block:: text

    pod_name@process_id[log_level]:message

Jina has six different log levels (DEBUG, INFO, WARNING, ERROR, CRITICAL, SUCCESS).
The default level can be controlled by the ``JINA_LOG_LEVEL`` environment variable or in YAML configuration.


Logging handler
^^^^^^^^^^^^^^^

You can control where the logs are sent/stored by using different logging handlers.
Jina supports four different handlers:

- The ``FileHandler`` puts the logs in local files either as simple text or as json format.
- The ``StreamHandler`` prints the logs in each Pea's local stdout, which you can view in console.
- The ``SysLogHandler`` outputs logs to system logs.
  When host and port are not given it records locally, /dev/log on linux /var/run/syslog on mac.
- The ``FluentHandler`` caters for the distributed nature of Jina's Peas and Pods.
  FluentD has its own configuration to forward the messages according to its own syntax.

Logging Configuration in Action
--------------------------------------

You can configure the logging either on ``Flow`` level

.. highlight:: python
.. code-block:: python

    f = Flow(log_config='logging_cfg.yml').add().add()

or ``Pod`` level

.. highlight:: python
.. code-block:: python

    f = Flow().add(log_config='logging_cfg.yml').add()

In a YAML file you can customize the logger, choose the handler you need to monitor and save logs.
``logging_cfg.yml`` can be configured as follows:

.. highlight:: yaml
.. code-block:: yaml

    handlers:  # enabled handlers, order does not matter
      - StreamHandler
      - SysLogHandler
      - FluentHandler
    level: INFO
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

If you want to hide the logs of a certain Pod, you can set ``quiet=True`` in a Flow like:

.. highlight:: python
.. code-block:: python

        f = Flow().add(quiet=True).add()
        with f:
            f.index(Document())

Or in YAML Flow configuration:

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

`Fluentd <https://github.com/fluent/fluentd>`_ is expected to be used as a daemon receiving messages from the Jina logger and forwarding them to specific outputs using its output plugins and configurations.

Although fluentd can be configured to forward logs to the user's preferred destinations, Jina offers a default configuration under `/resources` folder which expects a fluentd daemon to be running
inside every machine running a Jina instance or Pea.
Then the default configuration must be adapted to send the logs to the specific server where the Flow and the dashboard will be run.
(This default behavior will evolve)

See the default `fluent.conf` configuration provided by Jina.
It takes every input coming in the listening 24224 port and depending on the kind of message, sends it to a local temporary file, from where the Flow will read the incoming file.

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


This is the default configuration, that works well together with the configuration provided in ``logging.fluentd.yml``.
It controls the tags assigned to the different type of logs, as well as the host and port where the handler will send the logs.
By default it expects a fluentd daemon to run in every local and remote Pea (this is the most scalable configuration).

.. highlight:: yaml
.. code-block:: yaml

    tag: jina
    profile-tag: jina-profile
    host: 0.0.0.0
    port: 24224


To better understand fluentd configuration and to see how you can adapt to your needs, please see `Fluentd docs <https://docs.fluentd.org/configuration>`_.

Start fluentd daemon
^^^^^^^^^^^^^^^^^^^^^

For the logging using fluentd to work and therefore for the dashboard to properly have access to the logs, the user needs to start fluentd daemon.
It can be done in every remote and local machine or just in the host where the FluentDHandler will send the logs.

- Install `https://docs.fluentd.org/installation <https://docs.fluentd.org/installation>`_ .
- Run ``fluentd -c ${FLUENTD_CONF_FILE}`` (Default conf file ``${JINA_RESOURCES_PATH}/fluent.conf``)


Conclusion
-----------------

In this guide, we introduced the Jina Logger and how we can configure the logging in Jina.

What's Next
-----------------

If you still have questions, feel free to `submit an issue <https://github.com/jina-ai/jina/issues>`_ or post a message in our `community slack channel <https://slack.jina.ai>`_ .

To gain deeper knowledge on the implementation of Jina logging, you can find the source code `here <https://github.com/jina-ai/jina/tree/master/jina/logging>`_.
