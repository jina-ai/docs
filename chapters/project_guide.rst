#####################
Project setup guide
#####################

.. meta::
   :description: You will see the best practices on how to create a project on Jina.
   :keywords: Jina, setup

.. note:: This guide expects you have a basic understanding of Jina. If you do not have that yet, please read `Jina 101 <http://101.jina.ai>`_ first.

If you plan on developing applications with Jina, this guide explains best practices for managing your application working space. It explains the recommended folder structure you should use, and other tips to make your work reproducible for others. 


.. contents:: Table of Contents
    :depth: 2


Installing Jina
===============

To begin with, you must have Jina installed. We recommend you create a new python environment to allow for clean management of dependencies.   

There are several ways to manage python virtual environments. Once you have chosen one, set up and entered the new environment. You should begin by installing the latest version of Jina. 

.. highlight:: bash
.. code-block:: bash

    pip install jina

To learn about other ways to install Jina, we have an `installation guide <https://docs.jina.ai/chapters/install/os/index.html>`_. available for You.

Setup with Jina Hub
===================

The **jina hub new** command will automatically create the recommended project structure in your working directory.  

.. highlight:: bash
.. code-block:: bash

    jina hub new --type app

This saves you having to do a lot of typing and setup. So, you just need to run the previous command and follow the guide that you will see. But don't worry, you will still see how the basic folder structure is formed, and which result you will get in the end.


Manual Setup
============

You can also set up the project structure manually. The actual structure of your folder will vary depending on the needs of your project. But at the end you should have something similar to this:


| your-new-project
| ├── data
| │   └── data.txt
| ├── flows
| │   ├── index.yml
| │   └── query.yml
| ├── pods
| │   └── index.yml
| ├── .dockerignore
| ├── .gitignore
| ├── requirements.txt
| ├── app.py
| ├── Dockerfile
| ├── get_data.sh
| ├── README.md
| └── requirements.txt


Folder Structure
================

Now let us have a closer look at each element of the project structure to understand which parts of it are essential, and which ones are optional.

Project name
------------

``/your-new-project``

The first thing you need to do is to create the folder of your project. It contains all the components you need.

Requirements
------------

The next thing you should take care of is the list of requirements. Create a file **requirements.txt**. In this file you will specify the required dependencies your Jina project needs. State one module per line. You can then install all the packages using `pip -r`:

.. highlight:: bash
.. code-block:: bash

    pip install -r requirements.txt

Prepare and save data
---------------------

``/get_data.sh``

This step may be necessary and depends on whether you need additional data in your project or not. If you need to download data, the best practice is to use a suitable script. Store this script directly under the main folder.

Now you need to define a place where to store the data you just downloaded. Create a folder named **data** first. Inside this folder will live whatever data you downloaded with the previously created script. 

``/your-new-project/data/data.txt``

In this example the file is simply named **data.txt**, and it is a text file. You are free to do it and it can be whatever you need for your project.

Flows
-----

``/flows``

You will most likely need at least one :term:`Flow`, and it is good practice to have all your Flows in one dedicated folder. To be as explicit as possible, we call the folder this **flows**. 

In this example we have two flows -- the first one is for the index that is stored in **index.yml**, and a second one for search that is stored in **query.yml**. In your project you can define as many flows as you actually need.

Pods
----

``/pods``

Our Flows will need some :term:`Pods<Pod>`, so we also create a dedicated `pods` folder for them.

App.py
------

``/app.py``

Finally, the main app needs a dedicated place. This file will be living directly under the main directory.

Workspace
---------

``/workspace``

This :term:`workspace` is a special folder. You will **not** create this folder yourself. Design your **app.py** in such a way that when you run it for the first time, this folder is created by the app during the :term:`indexing` phase.

Dockerfile
----------

``/Dockerfile``

This is another optional element. It is recommended to store this file in the main directory of your Jina project.

.dockerignore
-------------

``/.dockerignore``

Your Jina project may also contain files that you do not want to be included in your initial build context. Use the file **/.dockerignore** to keep track of these files. The Docker daemon will skip those files for the :code:`docker build`.

.gitignore
----------

``/.gitignore``

It is quite common to keep your project in a revision control system,
for example Git, or Subversion. Similar to the file **/.dockerignore**,
the file **/.gitignore** allows you to list the files that you do not
want be tracked by Git. Add whatever files you do not want to be
committed.

README
------

``/README.md``

Finally, you have your README file. It is good practice to have this for you (and others) to show all the necessary steps that are needed to be done to run your application. Store this file directyl in main folder, too.
