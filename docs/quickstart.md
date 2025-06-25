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
  - [Running Commands and Code](#running-commands-and-code)
    - [Understanding the Execution Model](#understanding-the-execution-model)
    - [Execution Approaches](#execution-approaches)
      - [1. Direct Commands](#1-direct-commands)
      - [2. Download and Run](#2-download-and-run)
      - [3. UV Scripts](#3-uv-scripts)
      - [4. Hugging Face Spaces](#4-hugging-face-spaces)
    - [Which Approach Should You Use?](#which-approach-should-you-use)
  - [Choosing Your Container Image](#choosing-your-container-image)
    - [Quick Guide](#quick-guide)
    - [Common Images](#common-images)
    - [GPU Considerations](#gpu-considerations)
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

## Running Commands and Code

In the previous example, we passed Python code directly as a string. But hfjobs can run any command or program available in your container. Let's explore the different approaches.

### Understanding the Execution Model

When you run a job with hfjobs, your commands execute inside a container on Hugging Face's infrastructure. Since your local files aren't directly accessible in the container, you need strategies for running your programs.

### Execution Approaches

#### 1. Direct Commands

Run any command available in the container:

```bash
# Python code
hfjobs run python:3.12 python -c "print('Hello')"

# Shell commands
hfjobs run ubuntu:22.04 echo "Hello from Ubuntu"

# Data tools
hfjobs run hf.co/spaces/lhoestq/duckdb duckdb -c "SELECT 'Hello SQL'"
```

**When to use**: Quick tests, simple commands, one-liners

**Limitations**: Complex commands get unwieldy

#### 2. Download and Run

Fetch programs or scripts from URLs and execute them:

```bash
# Python script
hfjobs run python:3.12 /bin/bash -c \
  "wget https://example.com/script.py && python script.py"
```

**When to use**: Running existing code hosted online

**Limitations**: Dependencies must be handled separately

#### 3. UV Scripts

UV scripts include dependencies inline, making them perfect for hfjobs:

```bash
# Run our hello_world_uv.py example that uses cowsay
hfjobs run ghcr.io/astral-sh/uv:latest  /bin/bash -c \"
   uv run https://raw.githubusercontent.com/davanstrien/hfjobs/main/docs/examples/hello_world_uv.py 'Hello from the cloud!'"
```

The script includes its dependencies at the top:

```python
# /// script
# dependencies = [
#     "cowsay",
# ]
# ///
```

**When to use**: Scripts with dependencies, reproducible environments
**Benefits**: Dependencies handled automatically, no complex Docker builds

> See [`examples/hello_world_uv.py`](./examples/hello_world_uv.py) for the full script.

TODO add link to full doc page on using UV with hfjobs

#### 4. Hugging Face Spaces

Use a Space as a container for complex projects with multiple files:

```bash
# Run a training script from a Space containing multiple modules
hfjobs run hf.co/spaces/username/my-training-space python train.py \
  --model bert-base --epochs 10

# The Space can contain:
# - train.py (main script)
# - model.py, data.py (supporting modules)
# - config.yaml (configuration files)
# - requirements.txt (dependencies)
```

**When to use**: Complex projects with multiple files, team collaboration
**Benefits**: Full project structure, version control, easy sharing

> We'll cover creating Spaces for hfjobs in the Real-World Examples section.

### Which Approach Should You Use?

- **Quick test or one-liner?** → Direct commands
- **Single script with dependencies?** → UV scripts
- **Complex project with multiple files?** → HF Space
- **Existing script online?** → Download and run

Each approach has its place. Start simple with direct commands, then move to UV scripts or Spaces as your needs grow.

## Choosing Your Container Image

The container image you choose determines what software is available to your code. Images are pre-built environments tailored for different workloads and frameworks.

### Quick Guide

Match your image to your task:

```bash
# Basic Python work → Python image
hfjobs run python:3.12 python -c "print('Hello')"

# PyTorch code → PyTorch image
hfjobs run pytorch/pytorch:latest python -c "import torch; print(torch.__version__)"

# Using UV scripts without GPU → UV image
hfjobs run ghcr.io/astral-sh/uv:latest uv run script.py
```

### Common Images

- **python:3.12** - Clean Python environment
- **ubuntu:22.04** - Commonly used linux image
- **pytorch/pytorch** - PyTorch pre-installed
- **tensorflow/tensorflow** - TensorFlow pre-installed
- **ghcr.io/astral-sh/uv** - An image with uv set up for running UV scripts
- **transformers:latest** - Hugging Face libraries ready to go

### GPU Considerations

For GPU workloads, use CUDA-enabled images:

```bash
# CPU version
hfjobs run pytorch/pytorch:latest python -c "..."

# GPU version (note the cuda tag)
hfjobs run --flavor t4-small pytorch/pytorch:2.6.0-cuda12.4-cudnn9-devel python -c "..."
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
