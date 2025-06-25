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

Let's run a simple Python command on Hugging Face's infrastructure:

```bash
hfjobs run python:3.12 python -c "print('Hello from the cloud!')"
```

This command:
- Uses the `python:3.12` Docker image
- Runs a Python one-liner that prints a message
- Executes on Hugging Face's infrastructure

You'll see output like:

```
Job submitted successfully!
Job ID: abc123xyz
View at: https://huggingface.co/jobs/username/abc123xyz

Waiting for job to start...
===== Job started =====
Hello from the cloud!
===== Job completed =====
```

### Understanding the Output

- **Job ID**: Unique identifier for your job
- **Web URL**: Monitor your job in the browser
- **Logs**: Streamed in real-time to your terminal

### Watch Logs Stream in Real-Time

Let's run a longer job to see how logs are streamed:

```bash
hfjobs run python:3.12 python -c "
import time
print('Starting job...')
for i in range(5):
    print(f'Processing step {i+1}/5')
    time.sleep(2)
print('Job complete!')
"
```

You'll see each print statement appear as the job runs, giving you real-time feedback on your job's progress.

### Run in Detached Mode

For long-running jobs, you might not want to wait for output:

```bash
hfjobs run -d python:3.12 python -c "import time; time.sleep(300); print('Done!')"
```

This returns immediately with just the job ID. You can check on it later with:

```bash
hfjobs logs <job_id>
```

## Working with Different Hardware

TODO: Add content

## Real-World Examples

TODO: Add content

## Next Steps

- Check out our [example scripts](./examples/) for complete working examples
- Read the [advanced guide](./advanced.md) for complex use cases
- See the [API reference](./api-reference.md) for detailed command documentation

---

## Draft Content

_This section contains content that will be integrated into the docs later_

### Inspect Job Details

Get detailed information about a job:

```bash
hfjobs inspect <job_id>
```

This shows:
- Current status (RUNNING, COMPLETED, FAILED)
- Hardware configuration (flavor, architecture)
- Docker image and command
- Timestamps and owner information
