# hfjobs Quickstart Guide

This quickstart will walk you through using `hfjobs` to run compute jobs on Hugging Face's infrastructure. By the end, you'll be running GPU workloads with simple commands.

## Table of Contents

- [hfjobs Quickstart Guide](#hfjobs-quickstart-guide)
  - [Table of Contents](#table-of-contents)
  - [Installation \& Setup](#installation--setup)
    - [Requirements](#requirements)
    - [Install hfjobs](#install-hfjobs)
    - [Verify Installation](#verify-installation)
  - [Authentication](#authentication)
  - [Your First Job](#your-first-job)
  - [Working with Different Hardware](#working-with-different-hardware)
  - [Real-World Examples](#real-world-examples)
  - [Next Steps](#next-steps)

## Installation & Setup

### Requirements

- Python 3.9 or higher
- A Hugging Face account (currently `hfjobs` is only available for HF staff)

### Install hfjobs

Install using pip:

```bash
pip install hfjobs
```

Or with [uv](https://docs.astral.sh/uv/):

```bash
uv pip install hfjobs
```

You can also run hfjobs directly without installation using `uv run`:

```bash
uv run hfjobs --help
```

### Verify Installation

Check that hfjobs is installed correctly:

```bash
hfjobs --help
```

You should see the help output with available commands and options.

## Authentication

hfjobs needs your Hugging Face token to submit jobs. The easiest way is to use the Hugging Face CLI:

```bash
huggingface-cli login
```

Follow the prompts to enter your token. hfjobs will automatically use your saved credentials.

To verify authentication is working:

```bash
hfjobs ps
```

This should show your jobs (or an empty list if you haven't run any yet).

## Your First Job

TODO: Add content

## Working with Different Hardware

TODO: Add content

## Real-World Examples

TODO: Add content

## Next Steps

- Check out our [example scripts](./examples/) for complete working examples
- Read the [advanced guide](./advanced.md) for complex use cases
- See the [API reference](./api-reference.md) for detailed command documentation
