# 🚀 hfjobs

Run compute jobs on Hugging Face infrastructure with a Docker-like experience!

`hfjobs` is a command-line tool that lets you run anything on Hugging Face's infrastructure (including GPUs and TPUs!) with simple commands. Think `docker run`, but for running code on A100s.

```bash
# Directly run Python code
hfjobs run python:3.12 python -c "print('Hello from the cloud!')"

# Use GPUs without any setup
hfjobs run --flavor a10g-small pytorch/pytorch:2.6.0-cuda12.4-cudnn9-devel \
  python -c "import torch; print(torch.cuda.get_device_name())"

# Run from Hugging Face Spaces
hfjobs run hf.co/spaces/lhoestq/duckdb duckdb -c "select 'hello world'"
```

## ✨ Key Features

- 🐳 **Docker-like CLI**: Familiar commands (`run`, `ps`, `logs`, `inspect`) to run and manage jobs
- 🔥 **Any Hardware**: From CPUs to A100 GPUs and TPU pods - switch with a simple flag
- 📦 **Run Anything**: Use Docker images, HF Spaces, or your custom containers
- 🔐 **Simple Auth**: Just use your HF token
- 📊 **Live Monitoring**: Stream logs in real-time, just like running locally
- 💰 **Pay-as-you-go**: Only pay for the seconds you use

## Installation

```
pip install hfjobs
```

## Available commands

```
usage: hfjobs <command> [<args>]

positional arguments:
  {inspect,logs,ps,run,cancel}
                        hfjobs command helpers
    inspect             Display detailed information on one or more Jobs
    logs                Fetch the logs of a Job
    ps                  List Jobs
    run                 Run a Job
    cancel              Cancel a Job

options:
  -h, --help            show this help message and exit
```

## Run jobs

### Usage

```
usage: hfjobs <command> [<args>] run [-h] [-e ENV] [-s SECRET] [--env-file ENV_FILE] [--secret-env-file SECRET_ENV_FILE] [--flavor FLAVOR] [--timeout TIMEOUT] [-d] [--token TOKEN] dockerImage ...

positional arguments:
  dockerImage           The Docker image to use.
  command               The command to run.

options:
  -h, --help            show this help message and exit
  -e ENV, --env ENV     Set environment variables.
  -s SECRET, --secret SECRET
                        Set secret environment variables.
  --env-file ENV_FILE   Read in a file of environment variables.
  --secret-env-file SECRET_ENV_FILE
                        Read in a file of secret environment variables.
  --flavor FLAVOR       Flavor for the hardware, as in HF Spaces.
  --timeout TIMEOUT     Max duration: int/float with s (seconds, default), m (minutes), h (hours) or d (days).
  -d, --detach          Run the Job in the background and print the Job ID.
  --token TOKEN         A User Access Token generated from https://huggingface.co/settings/tokens
```

### Examples

```
$ hfjobs run ubuntu echo hello world
hello world
```

```
$ hfjobs run python:3.12 python -c "print(2+2)"
4
```

```
$ hfjobs run python:3.12 /bin/bash -c "cd /tmp && wget https://gist.githubusercontent.com/sergeyprokudin/e8e1eeb9263766cc43a05ab9190442e4/raw/3c34504fd646517aeb15903700f8e9c1f4d6d2e5/fibonacci.py && python fibonacci.py"
0
1
...
218922995834555169026
```

```
$ hfjobs run hf.co/spaces/lhoestq/duckdb duckdb -c "select 'hello world'"
┌───────────────┐
│ 'hello world' │
│    varchar    │
├───────────────┤
│ hello world   │
└───────────────┘
```

```
$ hfjobs run --flavor t4-small pytorch/pytorch:2.6.0-cuda12.4-cudnn9-devel python -c "import torch; print(torch.tensor([42]).to('cuda'))"
tensor([42], device='cuda:0')
```

## Hardware

Available `--flavor` options:

- CPU: `cpu-basic`, `cpu-upgrade`
- GPU: `t4-small`, `t4-medium`, `l4x1`, `l4x4`, `a10g-small`, `a10g-large`, `a10g-largex2`, `a10g-largex4`,`a100-large`
- TPU: `v5e-1x1`, `v5e-2x2`, `v5e-2x4`

(updated in 03/25 from Hugging Face [suggested_hardware docs](https://huggingface.co/docs/hub/en/spaces-config-reference))
