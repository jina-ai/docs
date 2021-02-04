##################
Project set up guide
##################

.. meta::
   :description: You will see the best practices on how to create a project on Jina.
   :keywords: Jina, set-up

.. note:: This guide expects you have a basic understanding of Jina, if you haven't, please check out `Jina 101 <https://docs.jina.ai/chapters/101/index.html>`_ first.

Here you will see the suggested structure for a project structure in Jina.


* What is the standard project structure?
* What are the requirements for Jina set-up?

*********
Implementation
*********


Folder structure
====================

Let's start by seeing a folder structure example. The actual structure of your folder will vary depending on the needs of your project. But at the end you should have something similar to this:


| your-cool-project
| ├── data
| │   ├── super-important-data.txt
| ├── flows
| │   ├── index.yml
| │   ├── query.yml
| ├── pods
| │   ├── craft.yml
| │   ├── doc.yml
| │   ├── encoder.yml
| │   ├── vec.yml
| ├── workspace
| ├── app.py
| ├── DOCKERFILE
| ├── get_data.sh
| ├── README.md
| ├── requirements.txt
|
|


Folder structure elements
====================

Now let's take a closer look at each element to see what is optional

Project name
---------

``/your-cool-project``

The first thing you need to do is create the folder of your project. Here is where everything will live.

Requirements
---------

The first thing you should take care of is the requirements. Create a **requirements.txt**. In this file you will specify the required dependencies you'll need. Write a module per line. You can then install all the packages with pip like this:

.. highlight:: bash
.. code-block:: bash

    pip install -r requirements.txt


Get and save data
---------

This can be optional depending on if you need extra data on your project or not. If you need to download data the best practice is to have a script that will download the data. This script should live directly under the main folder.

``/get_data.sh``

Now you need someplace where to store the data you just downloaded. For this, you'll create a folder named **data** and inside this folder will live whatever data you downloaded with the previous script. In this example, we have a **super-important-data.tx** text file. But this can be whatever you need.

``/your-cool-project/data/super-important-data.txt``


Flows
---------

``/flows``

You will most likely need at least one :term:`Flow`, and it's good practice to have all your Flows in one dedicated folder. To try to be the most explicit as possible, we call this folder also **flows**. In this example, we have two flows, one for index **index.yml** and one for search **query.yml**, but you can have more or less.


Pods
---------

``/flows``

Our Flows will need some :term:`Pods<Pod>`, so we also create a dedicated folder for them. And you guessed right, the folder is called **pods**. Here we have 4 pods, but in your project, you might have a different number.


App.py
---------

``/app.py``

And of course, we need our main app, we have this file living directly under the main directory.


Workspace
---------

``/workspace``

This :term:`workspace is a special folder. You will **not** create this folder yourself. You should design your **app.py** in a way that when you run it for the first, this folder is created during the :term:`indexing`. And this workspace folder should be created under the main directory.



Dockerfile
---------

``/app.py``


This is another optional element, but if you want to have a Docker image you can have one under the main directory


README
---------

``/README.md``

Finally, we have our README. It is good practice to have this for you to show all the necessary steps you'll need to do to run your app. And we have this living under the main folder too.







