Docstring guide
==================================

    “Code is more often read than written.”

    — Guido van Rossum

In Jina, we are aware that documentation is an important part of Sofware, but we also think it is especially important for OpenSource. And for this reason, we try extra hard to have clear and extensive documentation for all of our source code. But, at the same time, we know this also takes time and effort, so we want to make things as easy as possible with this guide for you. Here we want to show you the best practices for writing docstrings in Jina.


What are docstrings?
----------------------------------------------------

First, we should define what are we talking about. A docstring is a string literal that we use to document elements of our code, such as functions, methods, modules, and classes. We do this to have a clear understanding about what are the details of each part of our code. We can see it more in detail in `PEP 257 <https://www.python.org/dev/peps/pep-0257/>`_. Jina recommends the following:

* Write docstrings for **public** *functions* and *classes*
* Optionally you can write docstrings for **private** *functions* and *classes*, but it's not mandatory

In Jina, we use **ReStructuredText** (reST), which is the default markup language used by `Sphinx <https://www.sphinx-doc.org/>`_. You can use *Markdown* too but we encourage you to use reST since *Markdown* doesn't contain rich markup.


One-line Docstrings
----------------------------------------------------

Use one-line docstrings when the description of the class/module/function fits in one line

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

We use multi-line docstring for more complex functions or classes. And we suggest the following:

* Define the Dosctrings with triple-double quotes (""")
* Don't leave blank lines before your Docstring
* Start your text one line after the triple-double quotes
* Write the Docstring as a command, not as a description (*Start Flow* instead of *This will start a flow*). We should have a more detailed description here as compared to the one-line docstrings
* Use the same indentation line as with the triple-double quotes
* Leave a blank line after the docstring and before the rest of the function/class/method


Commonly used directives
----------------------------------------------------

You can use all the `Sphinx directives <https://www.sphinx-doc.org/en/master/usage/restructuredtext/directives.html>`_. And here is an example of the most used ones:

* *.. note::* [description]
* *.. warning::* [description]
* *.. deprecated::* [version]
* *.. seealso::* [description]
* *.. highlight::* [language]
* *.. code-block::* [langage] [description]
* *.. math::* [latex markup]


Commonly used python field directives
----------------------------------------------------

In our classes/functions we can have the following:

* Parameters: *:param[ParamName]:* [ParamDescription]
* Return: *:return:* [ReturnDescription]
* Return types: *:rtype:* [ReturnType]
* Raises: *:raises:* [ExceptionType]


Commonly used directives for cross-referencing
----------------------------------------------------

You can use the following for cross-referencing

* For classes: *:class:* [ClassName]
* For methods: *:meth:* [MethodName]
* For attributes: *:attr:* [AttributeName]
* For exceptions: *:exc:* [ExceptionName]
* For data: *:data:* [ModuleLevelVariable]


Use terms from a glossary
----------------------------------------------------

You can reference a term that is defined in the Glossary. You can do it like this:

*:term:* ` Magic `

You need to match exactly the term as in the Glossary. If you want to show different text in the topic, you can do it by including the term in angle brackets. You can do it like this:

*:term:* ` Another type of Magic <Magic> `


Deprecation warning
----------------------------------------------------

You should warn the user if an object (class, function, method) is deprecated.

* Specify in which version the object has been deprecated.
* Specify when this will be removed
* Recommend a proposed way to do it

To show this warning you can do it with the  *.. deprecated::* directive


*****************************************************
Multi-line Docstrings Example
*****************************************************

.. highlight:: python
.. code-block:: python

    def does_complex_magic(param1: Document, param2: str):
    """
    Do complex magic

    .. note::
        This is an example note
    .. warning::
        This is a warning example
    .. highlight:: python
    .. code-block:: python
        print('This is a print example')

    :param param1: This is an example of a param1 of type :class:`Document`
    :param param2: This is an example of a param2
    :returns: This is an example of what will be returned
    :raises KeyError: raises an exception
    """



Documenting classes
----------------------------------------------------

