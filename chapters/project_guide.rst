#####################
Project setup guide
#####################

.. meta::
   :description: You will see the best practices on how to create a project on Jina.
   :keywords: Jina, setup

.. note:: This guide expects you have a basic understanding of Jina. If you do not have that yet, please read `Jina 101 <http://101.jina.ai>`_ first.

Here you will see the suggested structure for a project structure in Jina.

.. contents:: Table of Contents
    :depth: 2


Installing Jina
===============

The first thing you need is to have Jina installed and ready to run. There are different ways you can do this, but here you will just see the easiest one. With Linux/Mac, you just need to install Jina with pip using the following command:

.. highlight:: bash
.. code-block:: bash

    pip install jina

To learn about other ways to install Jina, we have an `installation guide <https://docs.jina.ai/chapters/install/os/index.html>`_. available for You.

Setup with Jina Hub
===================

Use our **jina hub new** command to spin up a basic Jina app:

.. highlight:: bash
.. code-block:: bash

    jina hub new --type app

This saves you having to do a lot of typing and setup. So, you just need to run the previous command and follow the guide that you will see. But don't worry, you will still see how the basic folder structure becomes, and which result you will get in the end.


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


Folder structure elements
==========================

Now let's take a closer look at each element to see what is optional

Project name
-------------

``/your-new-project``

The first thing you need to do is create the folder of your project. Here is where everything will live.

Requirements
-------------

The first thing you should take care of is the requirements. Create a **requirements.txt**. In this file you will specify the required dependencies you'll need. Write a module per line. You can then install all the packages with pip:

.. highlight:: bash
.. code-block:: bash

    pip install -r requirements.txt

Prepare and save data
----------------------

This can be optional depending on if you need extra data on your project or not. If you need to download data the best practice is to use a script. This script should live directly under the main folder.

``/get_data.sh``

Now you need someplace where to store the data you just downloaded. For this, you'll create a folder named **data** and inside this folder will live whatever data you downloaded with the previous script. In this example, we have a **data.txt** text file. But this can be whatever you need.

``/your-new-project/data/data.txt``

Flows
---------

``/flows``

You will most likely need at least one :term:`Flow`, and it's good practice to have all your Flows in one dedicated folder. To try to be the most explicit as possible, we call this folder also **flows**. In this example, we have two flows, one for index **index.yml** and one for search **query.yml**, but you can have more or less.

Pods
---------

``/pods``

Our Flows will need some :term:`Pods<Pod>`, so we also create a dedicated `pods` folder for them.

App.py
---------

``/app.py``

And of course, we need our main app, we have this file living directly under the main directory.

Workspace
----------

``/workspace``

This :term:`workspace` is a special folder. You will **not** create this folder yourself. You should design your **app.py** in a way that when you run it for the first time, this folder is created during the :term:`indexing`.

Dockerfile
-----------

``/Dockerfile``


This is another optional element. It should be stored in the main directory.

.dockerignore
--------------

``/.dockerignore``

Don't forget to add here all the files that you don't want to include in your initial build context. The Docker daemon will skip those files for the :code:`docker build`


.gitignore
-----------

``/.gitignore``

Add here whatever files you don't want to commit.

README
---------

``/README.md``

Finally, we have our README. It is good practice to have this for you to show all the necessary steps you'll need to do to run your app. And we have this living under the main folder too.
