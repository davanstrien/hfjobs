# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

hfjobs is a CLI tool for managing Hugging Face Jobs, providing a Docker-like interface for running compute jobs on various hardware configurations (CPU, GPU, TPU).

## Development Setup

This project uses Poetry for dependency management, but following user preferences, UV should be used for Python project management:

```bash
# Install dependencies
uv pip install -e .

# Run the CLI
hfjobs --help
```

## Common Commands

### Development
```bash
# Run the CLI in development
python -m hfjobs.cli

# Format and lint code with ruff
ruff check hfjobs/ --fix
ruff format hfjobs/

# Check and format Python code blocks in documentation
uvx blacken-docs --check README.md  # Check if formatting needed
uvx blacken-docs README.md          # Apply formatting

# Install in editable mode for development
uv pip install -e .
```

### Building and Distribution
```bash
# Build the package
poetry build

# Or with uv
uv build
```

## Architecture

The codebase follows a command pattern architecture:

1. **Entry Point**: `hfjobs/cli.py` - Main CLI entry point that registers all commands and dispatches to handlers
   
2. **Command Structure**: Each command in `hfjobs/commands/` implements:
   - `register_subcommand(parser, parents)` - Registers command arguments
   - `run(args)` - Executes the command logic
   - Inherits from `BaseCommand` abstract class

3. **Commands**:
   - `run.py`: Execute jobs using Docker images or HF Spaces
   - `ps.py`: List jobs with filtering (--all, --user)
   - `logs.py`: Fetch job logs
   - `inspect.py`: Display detailed job information  
   - `cancel.py`: Cancel running jobs

4. **Shared Utilities**: `_cli_utils.py` provides common functions for API interactions and data formatting

## Key Implementation Details

- Authentication uses HF tokens from environment or `~/.huggingface/hub/token`
- API endpoint: `https://api.endpoints.huggingface.cloud/v2/job` with staging option
- Supports various hardware flavors: cpu-basic, gpu-nvidia-a10g, gpu-nvidia-h100, tpu-v5e-v4
- Docker images can be from any registry or `<username>/<space>:<version>` format for HF Spaces

## Testing

Currently no tests are implemented. When adding tests:
```bash
# Create test structure
mkdir tests
touch tests/__init__.py
touch tests/test_{command}.py

# Run tests (after implementation)
pytest tests/
```