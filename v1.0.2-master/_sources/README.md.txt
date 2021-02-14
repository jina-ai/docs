# Jina Documentations

![CD](https://github.com/jina-ai/docs/workflows/CD/badge.svg?branch=docs-migration)

Please read the documentations at https://docs.jina.ai 

This repository hosts the documentation of Jina. It gets updated when [jina-ai/jina](https://github.com/jina-ai/jina/) is modified.

All doc-related issues should be [submitted to here](https://github.com/jina-ai/jina/issues/new).

## Read the Docs Locally

You can read the documentations locally via:

```bash
# Clone the code.
git clone https://github.com/jina-ai/docs.git

# Install dependencies.
pip install -r requirements.txt

# Clean & build docs locally
make clean
make html

# Serve the docs website with Python 3
python -m http.server 8080 -d _build/html
```
