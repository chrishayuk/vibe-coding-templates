# Example Usage by AI Agents

This document demonstrates how an AI agent would use these templates to bootstrap a project.

## Example Scenario: User Requests a Data Processing Tool

**User**: "Create a Python project called data-processor that can read CSV files and generate reports"

**AI Agent Response & Actions**:

### Step 1: Acknowledge and Plan

"I'll create a Python project called 'data-processor' with CSV processing capabilities. Let me bootstrap this using the vibe-coding-templates structure with:
- Package management using uv
- Testing with pytest  
- CI/CD with GitHub Actions
- Pre-commit hooks for code quality"

### Step 2: Create Project Structure

```bash
# Create directories
mkdir -p data-processor
cd data-processor
mkdir -p src/data_processor tests docs .github/workflows scripts manifests
```

### Step 3: Create pyproject.toml

```toml
[build-system]
requires = ["hatchling"]
build-backend = "hatchling.build"

[project]
name = "data-processor"
version = "0.1.0"
description = "A tool for reading CSV files and generating reports"
readme = "README.md"
requires-python = ">=3.11"
authors = [
    { name = "Your Name", email = "your.email@example.com" }
]
dependencies = [
    "pandas>=2.0.0",
    "click>=8.1.0",
    "rich>=13.0.0",
    "pydantic>=2.0.0",
]

[project.scripts]
data-processor = "data_processor.cli:main"

[tool.uv]
dev-dependencies = [
    "pytest>=8.0.0",
    "pytest-cov>=6.0.0",
    "mypy>=1.0.0",
    "ruff>=0.8.0",
    "pre-commit>=3.5.0",
]

[tool.ruff]
line-length = 88
target-version = "py311"

[tool.pytest.ini_options]
testpaths = ["tests"]
addopts = ["-v", "--tb=short"]

[tool.coverage.run]
source = ["src/data_processor"]
branch = true
```

### Step 4: Create Source Files

**src/data_processor/__init__.py**:
```python
"""Data processor package for CSV analysis and reporting."""

__version__ = "0.1.0"
```

**src/data_processor/processor.py**:
```python
"""Core data processing functionality."""

import pandas as pd
from pathlib import Path
from typing import Dict, Any


class DataProcessor:
    """Process CSV files and generate reports."""
    
    def __init__(self, file_path: Path):
        """Initialize with CSV file path."""
        self.file_path = file_path
        self.data = None
    
    def load_data(self) -> pd.DataFrame:
        """Load CSV data into DataFrame."""
        self.data = pd.read_csv(self.file_path)
        return self.data
    
    def generate_summary(self) -> Dict[str, Any]:
        """Generate summary statistics."""
        if self.data is None:
            raise ValueError("No data loaded. Call load_data() first.")
        
        return {
            "rows": len(self.data),
            "columns": len(self.data.columns),
            "column_names": list(self.data.columns),
            "missing_values": self.data.isnull().sum().to_dict(),
            "numeric_summary": self.data.describe().to_dict(),
        }
```

**src/data_processor/cli.py**:
```python
"""Command-line interface for data processor."""

import click
from pathlib import Path
from rich.console import Console
from rich.table import Table
from .processor import DataProcessor

console = Console()


@click.command()
@click.argument('csv_file', type=click.Path(exists=True))
@click.option('--output', '-o', help='Output file for report')
def main(csv_file: str, output: str):
    """Process CSV file and generate report."""
    try:
        processor = DataProcessor(Path(csv_file))
        processor.load_data()
        summary = processor.generate_summary()
        
        # Display summary
        table = Table(title="Data Summary")
        table.add_column("Metric", style="cyan")
        table.add_column("Value", style="green")
        
        table.add_row("Total Rows", str(summary["rows"]))
        table.add_row("Total Columns", str(summary["columns"]))
        
        console.print(table)
        
        if output:
            # Save report to file
            with open(output, 'w') as f:
                f.write(f"Data Summary Report\n")
                f.write(f"==================\n")
                f.write(f"Rows: {summary['rows']}\n")
                f.write(f"Columns: {summary['columns']}\n")
            console.print(f"[green]Report saved to {output}[/green]")
            
    except Exception as e:
        console.print(f"[red]Error: {e}[/red]")
        raise click.ClickException(str(e))


if __name__ == "__main__":
    main()
```

### Step 5: Create Tests

**tests/test_processor.py**:
```python
"""Tests for data processor."""

import pytest
import pandas as pd
from pathlib import Path
from src.data_processor.processor import DataProcessor


@pytest.fixture
def sample_csv(tmp_path):
    """Create a sample CSV file for testing."""
    csv_path = tmp_path / "test_data.csv"
    df = pd.DataFrame({
        'name': ['Alice', 'Bob', 'Charlie'],
        'age': [25, 30, 35],
        'city': ['NYC', 'LA', 'Chicago']
    })
    df.to_csv(csv_path, index=False)
    return csv_path


def test_load_data(sample_csv):
    """Test loading CSV data."""
    processor = DataProcessor(sample_csv)
    data = processor.load_data()
    
    assert data is not None
    assert len(data) == 3
    assert list(data.columns) == ['name', 'age', 'city']


def test_generate_summary(sample_csv):
    """Test generating summary statistics."""
    processor = DataProcessor(sample_csv)
    processor.load_data()
    summary = processor.generate_summary()
    
    assert summary['rows'] == 3
    assert summary['columns'] == 3
    assert 'name' in summary['column_names']


def test_summary_without_loading_raises():
    """Test that summary raises error if data not loaded."""
    processor = DataProcessor(Path("dummy.csv"))
    
    with pytest.raises(ValueError, match="No data loaded"):
        processor.generate_summary()
```

### Step 6: Create GitHub Actions Workflow

**.github/workflows/test.yml**:
```yaml
name: Test Suite

on:
  push:
    branches: [ main ]
  pull_request:
    branches: [ main ]

jobs:
  test:
    runs-on: ubuntu-latest
    strategy:
      matrix:
        python-version: ['3.9', '3.10', '3.11']
    
    steps:
    - uses: actions/checkout@v4
    
    - name: Install uv
      uses: astral-sh/setup-uv@v3
      
    - name: Set up Python ${{ matrix.python-version }}
      uses: actions/setup-python@v5
      with:
        python-version: ${{ matrix.python-version }}
        
    - name: Install dependencies
      run: uv sync --dev
        
    - name: Run tests
      run: uv run pytest tests/ --cov=src/data_processor
```

### Step 7: Create Pre-commit Configuration

**.pre-commit-config.yaml**:
```yaml
repos:
  - repo: https://github.com/astral-sh/ruff-pre-commit
    rev: v0.8.0
    hooks:
      - id: ruff
        args: [--fix]
      - id: ruff-format

  - repo: https://github.com/pre-commit/mirrors-mypy
    rev: v1.11.2
    hooks:
      - id: mypy
        additional_dependencies: [types-all, pandas-stubs]

  - repo: https://github.com/pre-commit/pre-commit-hooks
    rev: v4.5.0
    hooks:
      - id: trailing-whitespace
      - id: end-of-file-fixer
      - id: check-yaml
```

### Step 8: Create README.md

```markdown
# data-processor

A tool for reading CSV files and generating reports.

## Features

- Load and analyze CSV files
- Generate summary statistics
- Export reports in multiple formats
- Command-line interface with rich output

## Installation

```bash
# Install uv package manager
curl -LsSf https://astral.sh/uv/install.sh | sh

# Install dependencies
uv sync --dev
```

## Usage

```bash
# Process a CSV file
uv run data-processor data.csv

# Save report to file
uv run data-processor data.csv --output report.txt
```

## Development

```bash
# Run tests
uv run pytest

# Run with coverage
uv run pytest --cov=src/data_processor

# Format code
uv run ruff format .

# Lint code
uv run ruff check .
```

## Project Structure

```
data-processor/
├── src/data_processor/
│   ├── __init__.py
│   ├── processor.py
│   └── cli.py
├── tests/
│   └── test_processor.py
├── pyproject.toml
├── README.md
└── .github/workflows/
    └── test.yml
```
```

### Step 9: Initialize and Test

```bash
# Initialize git
git init
git add .
git commit -m "Initial commit: Data processor project structure"

# Install dependencies
uv sync --dev

# Install pre-commit hooks
uv run pre-commit install

# Run tests
uv run pytest

# Verify linting
uv run ruff check .

# Format code
uv run ruff format .
```

### Step 10: Verification Output

```
✅ Project structure created
✅ Dependencies installed with uv
✅ Tests passing (3 tests)
✅ Linting configured with ruff
✅ Pre-commit hooks installed
✅ GitHub Actions CI/CD configured
✅ Git repository initialized

Your data-processor project is ready! You can now:
1. Add more CSV processing features to src/data_processor/
2. Write additional tests in tests/
3. Push to GitHub to trigger CI/CD
4. Run 'uv run data-processor --help' to see CLI options
```

## Key Takeaways for AI Agents

1. **Always follow BOOTSTRAP.md** - It provides the exact steps
2. **Customize for user needs** - Added pandas, click, rich for CSV processing
3. **Include domain-specific code** - Created processor.py with actual functionality
4. **Write meaningful tests** - Tests cover the actual features
5. **Verify everything works** - Run tests and linting before declaring success

## Template Adaptation Points

When using these templates, AI agents should:

- **Identify project type** (CLI tool, web service, library, etc.)
- **Select appropriate dependencies** (pandas for data, fastapi for web, etc.)
- **Create relevant source files** (not just hello_world)
- **Write domain-specific tests** (test actual functionality)
- **Customize GitHub Actions** (add specific checks if needed)
- **Update documentation** (README should describe actual project)