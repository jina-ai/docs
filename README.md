# Jina Documentation

![CD](https://github.com/jina-ai/docs/workflows/CD/badge.svg?branch=master)
![Release](https://github.com/jina-ai/docs/workflows/Release/badge.svg?branch=master)
![Link Check](https://github.com/jina-ai/docs/workflows/Linkcheck/badge.svg?branch=master)

> “Open source is this magical thing right? You release code, and the code gnomes come and make it better for you.
>
> Not quite.
> There are lots of ways that open source is amazing, but it doesn’t exist outside the laws of physics. You have to put work in, to get work out.
>
>You only get contributions after you have put in a lot of work.
>You only get contributions after you have users.
>You only get contributions after you have documentation.”
>
From @ericholscher [guide to writing docs](https://www.writethedocs.org/guide/writing/beginners-guide-to-docs/)

## Table of contents
* [Hierarchical structure](#hierarchical-structure)
* [Missing content](#missing-content)
* [How to add pages](#how-to-add-pages)
* [Updating Docstrings](#updating-docstrings)
* [Jina style guide](#jina-style-guide)
* [Technical aspects of the the doc site](#technical-aspects-of-the-the-doc-site)

## Hierarchical structure

Jina documentation adheres to the following hierarchical structure. Each Jina product has its own section, containing three subsections below.

| <!-- -->    | <!-- -->    |
|-------------|-------------|
| **Overview** |A high level conceptual overview of the product. Introducing terms and broad architectural concepts. Content here should apply to all Jina users. For example, all users should understand what a Pod is, but only some users need to understand deployment on a GPU.|
 |**Developer Guides**  | Are technical how-to guides/tutorials which describe product features or implementations. Assumes basic knowledge of the product and related terms. |
| **API References**| Are detailed descriptions of the product API. Possibility auto-generated from docstrings or open API references. Describes how the methods work and which parameters can be used.  |


## Missing content

If you find a gap in our documentation, please submit a GitHub issue [here](https://github.com/jina-ai/docs).

## How to add pages

For getting started pages and developer guides:

1.  A page can be written in Markdown or reStructuredText format.

2.  Use a template from the [page_templates](https://github.com/jina-ai/docs/tree/master/page_templates) folder.

3.  Using Git, clone the repo: `git clone https://github.com/jina-ai/docs` .

4.  Create a git new branch: `git checkout -b fix_pods` .

5. Your commit messages should following the standard Jina format seen [here](https://github.com/jina-ai/jina#contributing).

5.  Add your file to the chapters folder.

6.  Add your file to a table of contents.

8.  Push your branch and create a pull request.

9.  Add at least two people to your pull request review. One product manager and one developer.

10.  You can preview how the docs website will look with your changes. Inside your pull request, navigate to checks and click 'preview with netlify'.

11.  After the pull request is merged, the website will automatically update.

* A guide to RST can be found [here](https://bashtage.github.io/sphinx-material/rst-cheatsheet/rst-cheatsheet.html)
*  A guide to MD formatting can be found [here](https://github.com/adam-p/markdown-here/wiki/Markdown-Cheatsheet) [*Note that MD is more limited in functionality then RST*]

## Updating Docstrings

See details [here](https://docs.jina.ai/chapters/docstring.html).

## Jina style guide

All documentation should follow this [style guide](https://github.com/jina-ai/docs/blob/master/page_templates/style_guide.md).

## Build docs locally


```bash
#Clone the code.
git clone https://github.com/jina-ai/docs.git

#Install dependencies.
pip install -r requirements.txt

# Clean & build docs locally
make dirhtml

# Serve the docs website with Python 3
python -m http.server 8080 -d _build/html
```
