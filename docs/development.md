---
icon: lucide/code-xml
---

# Development

## Prerequisites

- [uv](https://github.com/astral-sh/uv) for Python package management

## Setup

Clone the repository and install dependencies:

```sh
uv sync
```

## Running Locally

Use `uv run` to run from your local source (this ensures your local code is used, not the PyPI-published package):

```sh
uv run oai2ollama --help
```
