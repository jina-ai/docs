Docstring guide
==================================

    “Code is more often read than written.”

    — Guido van Rossum

In Jina, we are aware that documentation is an important part of Sofware, but we also think it is especially important for OpenSource. And for this reason, we try extra hard to have clear and extensive documentation for all of our source code. But, at the same time, we know this also takes time and effort, so we want to make things as easy as possible with this guide for you. Here we want to show you the best practices for writing docstrings in Jina.


What are docstrings?
----------------------------------------------------

First, we should define what are we talking about. A docstring is a string literal that we use to document elements of our code, such as functions, methods, modules, and classes. We do this to have a clear understanding about what are the details of each part of our code. For us in Jina we recommend the following:

* Write docstrings for **public** *functions* and *classes*
* Optionally you can write docstrings for **private** *functions* and *classes*, but it's not mandatory


Docstring format
----------------------------------------------------

In Jina we use **ReStructuredText** (reST), which is the default markup language used by `Sphinx <https://www.sphinx-doc.org/>`_. You can use *Markdown* too but we encourage you to use reST since *Markdown* doesn't contain rich markup.


One-line Docstrings
----------------------------------------------------

Use one-line docstrings when the description of the class/module/function fits in one line


*****************************************************
One-line Docstrings Guidelines
*****************************************************

We suggest the following guidelines:

* Define the Dosctrings with triple-double quotes (""")
* Don't leave blank lines before your Docstring
* Start your text right after the triple-double quotes
* Write the Docstring as a command, not as a description (*Start Flow* instead of *This will start a flow*)


*****************************************************
One-line Docstrings Example
*****************************************************

.. highlight:: python
.. code-block:: python

    def does_magic():
    """ Do magic """
        print('Magic happens here')


Multi-line Docstrings
----------------------------------------------------


*****************************************************
Multi-line Docstrings Guidelines
*****************************************************


*****************************************************
Multi-line Docstrings Example
*****************************************************


Commonly used python field directives
----------------------------------------------------

In our classes/functions we can have the following:

* Parameters: :code:`:param:[ParamName]:[ParamDescription]`
* Return: :code:`:return: [ReturnDescription]`
* Return types: :code:`:rtype: [ReturnType]`
* Raises: :code:`:raises: [ExceptionType]`






