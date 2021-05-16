
# How To Install Jina Core on Raspberry Pi OS and other Linux Systems

## Overview

This article explains how to successfully set up Jina Core on a Raspberry Pi using Raspberry Pi OS or Linux system in just a few steps.

## Before you start

Please make sure that the following requirements are met:

* Python >= 3.7 has already been installed on your system.

* `pip` or `pip3` has already been installed on your system.

* Setting up Jina Core requires administrative user rights.

* The Jina Core software package itself requires about 12M of disk space. Furthermore, it depends on other software packages to be installed in order to function properly (see list below). Make sure that enough disk space is available on your system.

Fortunately, Jina Core has minimal software dependencies. The table below lists the corresponding software packages for [PyPi](https://pypi.org/), deb-based Linux distributions such as [Debian GNU/Linux](https://www.debian.org/), [Ubuntu](https://ubuntu.com/) and [Raspberry Pi OS](https://www.raspberrypi.org/software/) and [Alpine Linux](https://alpinelinux.org/).

| PyPi Name | Linux Package Name | Alpine Linux Package Name |
|---|---|---|
|`numpy`| `python3-numpy` | `py3-numpy` |
|`pyzmq>=17.1.0`| `python3-zmq` | `py3-pyzmq`|
|`protobuf`| `python3-protobuf`| `py3-protobuf`|
|`grpcio`| `python3-grpcio`| `py3-grpcio` |
|`ruamel.yaml>=0.15.89`| `python3-ruamel.yaml`| `py3-ruamel.yaml`|

## Step-by-step guide

### Step 1: Install software dependencies

On some Linux systems, PyPi may not provide the wheels on that OS. In this case, you may want to pre-install some dependencies via `apt` not via `pip`. These packages are pre-compiled, and require much less time to install. 

For deb-based Linux distributions the most common way is by using your favourite package manager such as `apt`, `apt-get`, or `aptitude`. The command for `apt` is as follows:

```bash
# apt install python3-numpy python3-zmq python3-protobuf python3-grpcio python3-ruamel.yaml
```

For Alpine Linux, the corresponding `apk` command is as follows:

```bash
# apk add py3-numpy py3-pyzmq py3-protobuf py3-grpcio py3-ruamel.yaml
```

### Step 2: Install Jina Core via `pip`

As of February 2021, Jina Core has not yet been packaged for any Linux distribution. We therefore resort to `pip`, and simply install Jina Core as follows:

```bash
# pip install jina
```

As of February 2021, Jina Core requires version 1.33 of the Python-bindings for gRPC. Please note that the currently provided packages `python3-grpcio` (Debian GNU/Linux 10 and 11) and `py3-grpcio` (Alpine Linux) are too old. Automatically, they will be replaced by a newer version from PyPi as an unmet dependency.

### Step 3: Check the installation

The last step is an installation check to make sure that Jina Core is set up correctly. See [Check the installation](jina-check.md) for more information.

## Alternatives

If you can have Docker installed on your Linux, then an easier way is probably [run Jina with Docker container](via-docker.md).
