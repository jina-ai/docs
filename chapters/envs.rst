OS Environment Variables Used in Jina
=====================================




Here is the list of environment variables that ``jina`` respects during runtime.

.. note::
    These enviroment variables must be set **before** starting ``jina`` or before any ``import jina`` in Python. Changing the variables while ``jina`` is running may result in unexpected result and exceptions.

    For example:

        .. highlight:: python
        .. code-block:: python

            os.environ['JINA_LOG_PROFILING'] = 'true'

            from jina.flow import Flow


.. confval:: JINA_ARRAY_QUANT

    Quantization scheme when storing ndarray into protobuf message, useful for reducing the network latency and saving bandwidth. Possible values: ``fp16`` (almost lossless), ``uint8``.

    :default: unset

.. confval:: JINA_BINARY_DELIMITER

    The delimiter used in :class:`BasePbIndexer`. Often use a delimiter phrase that you can determine does not occur. (Think of the mime message boundaries)

.. confval:: JINA_CONTRIB_MODULE

    Paths of the third party components.

    :default: unset

.. confval:: JINA_CONTRIB_MODULE_IS_LOADING

    Set to True when loading the third party components.

    :default: unset

.. confval:: JINA_CONTROL_PORT

    Control port of all pods.

    :default: unset. A random port will be used for each :func:`BasePod`.

.. confval:: JINA_DEFAULT_HOST

    The default host address of Jina.

    :default: `0.0.0.0`

.. confval:: JINA_DISABLE_UVLOOP

    ``uvloop`` is ultra fast implementation of the asyncio event loop on top of libuv. Since Jina 0.3.6, Jina relies on ``uvloop`` to manage the sockets and streams.

    :default: unset

.. confval:: JINA_EXECUTOR_WORKDIR

    The default executor working directory, where dumping and IO output happens.

    :default: unset

.. confval:: JINA_FULL_CLI

    When set, show all Jina subcommands.

    :default: unset

.. confval:: JINA_IPC_SOCK_TMP

    Temp directory when using IPC sockets for the control port, not used on Windows system or when the control port is over TCP sockets.

.. confval:: JINA_LOG_CONFIG

    The configuration file for the logger. If unset then will load from logging.default.yml.

    :default: unset

.. confval:: JINA_LOG_ID

    The identifier of a logger so that it can be used as group identifier by ``fluentd``. It is set when Pea starts a new process
    to allow grouping by pod identifier for pea and executor logger.

    :default: unset

.. confval:: JINA_LOG_LEVEL

    The log verbosity of the Jina logger. Possible values are ``DEBUG``, ``INFO``, ``SUCCESS``, ``WARNING``, ``ERROR``, ``CRITICAL``.

    :default: ``INFO``

.. confval:: JINA_LOG_NO_COLOR

    Show colored logs in stdout, set to any non-empty value to disable the color log, e.g. if you want to pipe the log into other apps.

    :default: unset

.. confval:: JINA_LOG_WORKSPACE

    A temporary work space during context.

    :default: unset

.. confval:: JINA_POD_NAME

    The Pod name set when a Pod started when ``--log-with-pod-name`` is on, this should not given manually by users.

    :default: unset

.. confval:: JINA_RAISE_ERROR_EARLY

    Raise exception immedidately instead of passing it forward in the flow. Useful in debugging.

    :default: unset

.. confval:: JINA_RANDOM_PORTS

    Set to any non-empty will trigger the random search for available port.

    :default: unset

.. confval:: JINA_RANDOM_PORT_MAX

    Set to '65535' as the max value when searching random port.

    :default: unset

.. confval:: JINA_RANDOM_PORT_MIN

    Set to '49153' as the min value when searching random port.

    :default: unset

.. confval:: JINA_SOCKET_HWM

    High-watermarks of ZMQ send & receive sockets. Reference: http://api.zeromq.org/master:zmq-setsockopt

    :default: 4

.. confval:: JINA_TEST_PRETRAINED

    If set, then all pretrained model-related tests will be conducted in the unit test.

    :default: unset

.. confval:: JINA_TEST_GPU

    If set, then all gpu-related tests will be conducted in the unit test.

    :default: unset

.. confval:: JINA_VCS_VERSION

    Git version of ``jina``. This is used when ``--check-version`` is turned on. For official docker image of ``jina``, ``JINA_VCS_VERSION`` is automatically set to the git version during the building procedure.

    :default: the git head version for ``jina`` image. If you are using ``jina`` locally outside docker container then this is unset.

.. confval:: JINA_WARN_UNNAMED

    Set to any non-empty value to turn on the warning for unnamed executors.

    :default: unset

.. confval:: JINA_WORKSPACE

    The work space to store indexed data.

    :default: unset








