# Code Quality Guide

This guide covers code quality tools and best practices for Python projects.

## Quick Start

```bash
# Run all quality checks
uv run ruff check . --fix
uv run ruff format .
uv run black .
uv run mypy src/
uv run pytest --cov=src
```

## Tools Overview

### 1. Ruff - Fast Python Linter

Ruff is an extremely fast Python linter and formatter written in Rust.

```bash
# Check for issues
uv run ruff check .

# Auto-fix issues
uv run ruff check . --fix

# Format code
uv run ruff format .

# Check specific rules
uv run ruff check . --select E,W,F
```

**Configuration**: `ruff.toml` or `pyproject.toml`

### 2. Black - Code Formatter

Black is an opinionated code formatter that ensures consistent style.

```bash
# Format all files
uv run black .

# Check without modifying
uv run black . --check

# Show diff
uv run black . --diff

# Format specific file
uv run black src/main.py
```

**Configuration**: `pyproject.toml` under `[tool.black]`

### 3. MyPy - Static Type Checker

MyPy performs static type checking using Python type hints.

```bash
# Type check entire project
uv run mypy src/

# Type check with strict mode
uv run mypy src/ --strict

# Generate HTML report
uv run mypy src/ --html-report mypy-report

# Check specific file
uv run mypy src/main.py
```

**Configuration**: `mypy.ini` or `pyproject.toml`

### 4. isort - Import Sorter

isort automatically sorts and organizes imports.

```bash
# Sort imports
uv run isort .

# Check without modifying
uv run isort . --check-only

# Show diff
uv run isort . --diff

# Profile for Black compatibility
uv run isort . --profile black
```

## Pre-commit Hooks

Automate code quality checks before each commit:

```bash
# Install pre-commit
uv add --dev pre-commit

# Install hooks
uv run pre-commit install

# Run manually on all files
uv run pre-commit run --all-files

# Run specific hook
uv run pre-commit run ruff --all-files

# Update hooks
uv run pre-commit autoupdate
```

## CI/CD Integration

### GitHub Actions Example

```yaml
name: Code Quality

on: [push, pull_request]

jobs:
  quality:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-python@v5
        with:
          python-version: '3.11'
      - uses: astral-sh/setup-uv@v3
      
      - name: Install dependencies
        run: uv sync --dev
      
      - name: Run Ruff
        run: uv run ruff check . --output-format=github
      
      - name: Run Black
        run: uv run black . --check
      
      - name: Run MyPy
        run: uv run mypy src/
```

## Code Quality Metrics

### 1. Code Coverage

Aim for minimum 80% coverage:

```bash
# Run tests with coverage
uv run pytest --cov=src --cov-report=term-missing

# Generate HTML report
uv run pytest --cov=src --cov-report=html

# Fail if below threshold
uv run pytest --cov=src --cov-fail-under=80
```

### 2. Complexity Metrics

Monitor code complexity:

```bash
# Check cyclomatic complexity
uv run ruff check . --select C90

# Use radon for detailed metrics
uv add --dev radon
uv run radon cc src/ -s
```

### 3. Security Scanning

```bash
# Install security tools
uv add --dev bandit safety

# Run Bandit
uv run bandit -r src/

# Check dependencies
uv run safety check
```

## Best Practices

### 1. Type Hints

Always use type hints:

```python
from typing import List, Optional, Dict

def process_data(
    items: List[str],
    config: Optional[Dict[str, Any]] = None
) -> Dict[str, int]:
    """Process data items according to config."""
    config = config or {}
    results: Dict[str, int] = {}
    
    for item in items:
        results[item] = len(item)
    
    return results
```

### 2. Docstrings

Use Google-style docstrings:

```python
def calculate_average(numbers: List[float]) -> float:
    """Calculate the average of a list of numbers.
    
    Args:
        numbers: List of numbers to average.
        
    Returns:
        The arithmetic mean of the numbers.
        
    Raises:
        ValueError: If the list is empty.
        
    Examples:
        >>> calculate_average([1, 2, 3, 4, 5])
        3.0
    """
    if not numbers:
        raise ValueError("Cannot calculate average of empty list")
    return sum(numbers) / len(numbers)
```

### 3. Error Handling

Use specific exceptions:

```python
class ConfigurationError(Exception):
    """Raised when configuration is invalid."""
    pass

def load_config(path: Path) -> Dict[str, Any]:
    """Load configuration from file."""
    if not path.exists():
        raise ConfigurationError(f"Config file not found: {path}")
    
    try:
        with open(path) as f:
            return json.load(f)
    except json.JSONDecodeError as e:
        raise ConfigurationError(f"Invalid JSON in {path}: {e}")
```

## Makefile Integration

Create a Makefile for common tasks:

```makefile
.PHONY: quality
quality: lint format type-check test

.PHONY: lint
lint:
	uv run ruff check . --fix

.PHONY: format
format:
	uv run ruff format .
	uv run black .
	uv run isort .

.PHONY: type-check
type-check:
	uv run mypy src/

.PHONY: test
test:
	uv run pytest --cov=src --cov-report=term-missing

.PHONY: security
security:
	uv run bandit -r src/
	uv run safety check

.PHONY: clean
clean:
	find . -type d -name __pycache__ -exec rm -rf {} +
	find . -type f -name "*.pyc" -delete
	rm -rf .mypy_cache .pytest_cache .ruff_cache
	rm -rf htmlcov coverage.xml .coverage
```

## VS Code Integration

`.vscode/settings.json`:

```json
{
  "python.linting.enabled": true,
  "python.linting.ruffEnabled": true,
  "python.formatting.provider": "black",
  "python.formatting.blackArgs": ["--line-length=88"],
  "editor.formatOnSave": true,
  "editor.codeActionsOnSave": {
    "source.organizeImports": true
  },
  "[python]": {
    "editor.defaultFormatter": "ms-python.black-formatter"
  }
}
```

## Quality Gates

Set up quality gates in your CI/CD:

1. **Coverage**: Minimum 80%
2. **Type Coverage**: 100% of public APIs
3. **Linting**: Zero errors, warnings acceptable
4. **Security**: No high/critical vulnerabilities
5. **Complexity**: Max cyclomatic complexity of 10

## Continuous Improvement

1. **Regular Updates**: Update tools monthly
2. **Team Standards**: Document team conventions
3. **Metrics Tracking**: Monitor trends over time
4. **Code Reviews**: Enforce standards in reviews
5. **Automation**: Automate everything possible

## Troubleshooting

### Common Issues

1. **Import errors in MyPy**:
   ```bash
   # Install type stubs
   uv add --dev types-requests types-pyyaml
   ```

2. **Black and isort conflicts**:
   ```bash
   # Use Black profile for isort
   uv run isort . --profile black
   ```

3. **Ruff and Black disagreement**:
   ```toml
   # In ruff.toml
   line-length = 88  # Match Black's default
   ```

## Resources

- [Ruff Documentation](https://docs.astral.sh/ruff/)
- [Black Documentation](https://black.readthedocs.io/)
- [MyPy Documentation](https://mypy.readthedocs.io/)
- [Pre-commit Documentation](https://pre-commit.com/)
- [Python Type Hints](https://docs.python.org/3/library/typing.html)