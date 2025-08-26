# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Overview

Cooler is a Python library for handling genomic interaction data (Hi-C contact matrices) stored in a sparse, compressed, binary format using HDF5. The library provides both a Python API and command-line tools for creating, querying, and manipulating cooler files.

## Development Commands

### Testing
```bash
# Run all tests
pytest

# Run tests with coverage report
pytest --cov=cooler --cov-report=term-missing

# Run specific test file
pytest tests/test_api.py

# Run tests in verbose mode with detailed output
pytest -v
```

### Linting and Code Quality
```bash
# Check code style and potential issues
ruff check src tests

# Fix auto-fixable issues
ruff check --fix src tests

# Check for critical syntax errors (used in CI)
ruff check . --select=E9,F63,F7,F82
```

### Installation for Development
```bash
# Install in editable mode with all optional dependencies
pip install -e .[dev]

# Install with just test dependencies
pip install -e .[test]

# Install with all optional dependencies
pip install -e .[all]
```

### Documentation
```bash
# Build documentation (from docs/ directory)
cd docs
make html

# The built docs will be in docs/_build/html/index.html
```

## Architecture Overview

### Core Components

**Main API (`src/cooler/api.py`)**
- `Cooler` class: Primary interface for reading cooler files
- Provides methods for querying genomic regions, extracting contact matrices
- Handles both local files and remote URIs

**Core Modules (`src/cooler/core/`)**
- `_rangequery.py`: Efficient 2D range queries on contact matrices
- `_selectors.py`: Selection and indexing utilities for genomic regions
- `_tableops.py`: Low-level HDF5 table operations (get, put, delete)

**Creation Pipeline (`src/cooler/create/`)**
- `_create.py`: Main cooler file creation logic
- `_ingest.py`: Data ingestion, validation, and aggregation
- `_constants.py`: Data type definitions and format constants

**Command Line Interface (`src/cooler/cli/`)**
- Modular CLI with subcommands for different operations
- Key commands: `load`, `balance`, `coarsen`, `merge`, `zoomify`, `dump`
- Each subcommand in separate module (e.g., `load.py`, `balance.py`)

**Utility Functions**
- `fileops.py`: File operations and cooler file management
- `util.py`: General utilities (chromosome sizes, binning)
- `parallel.py`: Parallel processing utilities

### Data Flow Patterns

1. **File Creation**: Raw contact data → validation/aggregation → HDF5 cooler file
2. **Querying**: Cooler file → region selection → contact matrix extraction
3. **Processing**: Cooler file → transformation (balance/coarsen/merge) → new cooler file

### Testing Structure

Tests are organized by module functionality:
- `test_api.py`: Main API functionality
- `test_create.py`: File creation pipeline
- `test_cli_*.py`: Command-line interface tests
- `test_core.py`: Core utilities and operations

Test data is stored in `tests/data/` with various cooler files and input formats for comprehensive testing.

## Key Dependencies

- **HDF5/h5py**: Core storage format
- **NumPy/SciPy**: Numerical operations and sparse matrices
- **Pandas**: Data manipulation and genomic interval handling
- **Click**: Command-line interface framework

## Development Guidelines

- Follow PEP-8 style conventions
- Use Numpy-style docstrings for all public functions
- Maintain compatibility with Python 3.9+
- All new features should include tests
- Use pre-commit hooks for code quality (ruff + basic checks)