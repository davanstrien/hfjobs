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
    - [Understanding the Output](#understanding-the-output)
    - [Watch Logs Stream in Real-Time](#watch-logs-stream-in-real-time)
    - [Run in Detached Mode](#run-in-detached-mode)
  - [Working with Different Hardware](#working-with-different-hardware)
    - [Run on GPU](#run-on-gpu)
    - [Check GPU Memory](#check-gpu-memory)
    - [Available Hardware Options](#available-hardware-options)
  - [Real-World Examples](#real-world-examples)
  - [Next Steps](#next-steps)
  - [Draft Content](#draft-content)
    - [Inspect Job Details](#inspect-job-details)

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

So far we've been using the default CPU hardware. The real power of hfjobs comes from accessing GPUs and TPUs with a simple flag.

### Run on GPU

Let's verify CUDA is available on a GPU instance:

```bash
hfjobs run --flavor t4-small pytorch/pytorch:2.6.0-cuda12.4-cudnn9-devel \
  python -c "import torch; print(f'CUDA available: {torch.cuda.is_available()}'); print(f'GPU: {torch.cuda.get_device_name()}')"
```

Output:

```
CUDA available: True
GPU: NVIDIA T4
```

### Check GPU Memory

Let's see how much memory is available on a T4:

```bash
hfjobs run --flavor t4-small pytorch/pytorch:2.6.0-cuda12.4-cudnn9-devel \
  python -c "
import torch
print(f'GPU: {torch.cuda.get_device_name()}')
print(f'Memory: {torch.cuda.get_device_properties(0).total_memory / 1e9:.0f} GB')
"
```

### Available Hardware Options

| Flavor        | Hardware        | GPU Memory | Best For                             |
| ------------- | --------------- | ---------- | ------------------------------------ |
| `cpu-basic`   | CPU only        | N/A        | Light processing, debugging          |
| `cpu-upgrade` | High-memory CPU | N/A        | Data processing, CPU-intensive tasks |
| `t4-small`    | NVIDIA T4       | 16 GB      | Inference, small models              |
| `a10g-small`  | NVIDIA A10G     | 24 GB      | Medium training jobs                 |
| `a10g-large`  | NVIDIA A10G     | 24 GB      | Larger batch sizes                   |
| `a100-large`  | NVIDIA A100     | 80 GB      | Large model training                 |

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
