# Code Quality Guide

⚠️ **IMPORTANT**: This guide is referenced in [python/BOOTSTRAP.md](../../BOOTSTRAP.md) and should be read when setting up code quality tools for new projects.

This guide covers code quality tools and best practices for Python projects, including:
- **Black** and **isort** for consistent code formatting
- **Ruff** for fast linting
- **MyPy** for static type checking
- Integration strategies to avoid tool conflicts

## Quick Start

⚠️ **Tool Order Matters!** Run formatters before linters to avoid conflicts:

```bash
# Run all quality checks IN THIS ORDER:
# 1. Organize imports (with Black profile)
uv run isort . --profile black

# 2. Format code
uv run black .

# 3. Run linting and additional formatting
uv run ruff check . --fix
uv run ruff format .

# 4. Type checking
uv run mypy src/

# 5. Tests with coverage
uv run pytest --cov=src
```

## Installing Code Quality Tools

### Getting Latest Versions (Recommended)

⚠️ **ALWAYS use latest versions when bootstrapping new projects:**

```bash
# Install all code quality tools with latest versions
uv add --dev pytest pytest-cov mypy ruff black isort pre-commit

# This automatically gets the latest stable versions
# No need to specify version numbers
```

### Manual Version Specification

If you need specific versions, add to `pyproject.toml`:

```toml
[tool.uv]
dev-dependencies = [
    "pytest>=8.0.0",
    "pytest-cov>=6.0.0",
    "mypy>=1.0.0",
    "ruff>=0.8.0",
    "black>=24.0.0",     # Code formatter
    "isort>=5.13.0",     # Import sorter
    "pre-commit>=3.5.0",
]
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

Black is an opinionated code formatter that ensures consistent style with minimal configuration.

#### Basic Usage

```bash
# Format all files
uv run black .

# Check without modifying (CI/CD mode)
uv run black . --check

# Show diff without modifying
uv run black . --diff

# Format specific file or directory
uv run black src/main.py
uv run black src/

# Format with verbose output
uv run black . --verbose

# Format Jupyter notebooks
uv run black . --include '\.ipynb$'
```

#### Configuration (pyproject.toml)

```toml
[tool.black]
line-length = 88  # Default is 88, some teams prefer 100 or 120
target-version = ['py311']  # Python versions to target
include = '\.pyi?$'
extend-exclude = '''
# Exclude patterns (regex)
(
  migrations
  | .+\.egg-info
  | build
  | dist
)
'''
# Optional: preview mode for upcoming style changes
preview = false
# Optional: skip string normalization (keep ' or " as-is)
skip-string-normalization = false
# Optional: skip magic trailing comma behavior
skip-magic-trailing-comma = false
```

#### Common Black Patterns

```python
# Black will format long function signatures
# Before:
def very_long_function_name(parameter_one, parameter_two, parameter_three, parameter_four, parameter_five):
    pass

# After:
def very_long_function_name(
    parameter_one,
    parameter_two,
    parameter_three,
    parameter_four,
    parameter_five,
):
    pass

# Magic trailing comma forces multi-line
# With trailing comma:
my_list = [
    "item1",
    "item2",
    "item3",  # <- This comma forces multi-line
]

# Without trailing comma (if fits):
my_list = ["item1", "item2", "item3"]
```

#### Integration with Other Tools

```bash
# Ensure compatibility with isort
uv run isort . --profile black

# Format Python code in Markdown files
pip install blacken-docs
blacken-docs README.md --line-length 88
```

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

isort automatically sorts and organizes imports according to PEP 8.

#### Basic Usage

```bash
# Sort imports in place
uv run isort .

# Check without modifying (CI/CD mode)
uv run isort . --check-only

# Show diff without modifying
uv run isort . --diff

# Profile for Black compatibility (RECOMMENDED)
uv run isort . --profile black

# Sort specific file
uv run isort src/main.py

# Show colorized diff
uv run isort . --diff --color

# Remove unused imports (use with caution)
uv run isort . --rm
```

#### Configuration (pyproject.toml)

```toml
[tool.isort]
# CRITICAL: Use black profile for compatibility
profile = "black"
line_length = 88  # Match Black's line length

# Import sections order
sections = [
    "FUTURE",
    "STDLIB",
    "THIRDPARTY",
    "FIRSTPARTY",
    "LOCALFOLDER"
]

# Additional settings
known_first_party = ["my_package"]  # Your package name
skip_gitignore = true  # Respect .gitignore
float_to_top = true  # Float imports to top of file
force_alphabetical_sort_within_sections = true
force_single_line = false  # Allow multi-line imports
lines_after_imports = 2  # Blank lines after imports
multi_line_output = 3  # Vertical hanging indent
include_trailing_comma = true  # Black compatibility
use_parentheses = true  # Black compatibility
ensure_newline_before_comments = true

# Files/directories to skip
skip = [
    ".git",
    ".venv",
    "venv",
    "build",
    "dist",
    "migrations",
]
extend_skip = [".md", ".json"]
extend_skip_glob = ["**/migrations/*.py"]
```

#### Import Organization Examples

```python
# Before isort:
import os
from my_package import utils
import sys
from typing import List, Dict
import requests
from pathlib import Path
from .models import User
import json

# After isort (with black profile):
# Standard library
import json
import os
import sys
from pathlib import Path
from typing import Dict, List

# Third party
import requests

# First party
from my_package import utils

# Local
from .models import User
```

#### Common isort Profiles

```bash
# Black (RECOMMENDED for Black users)
uv run isort . --profile black

# Django
uv run isort . --profile django

# Google (groups imports without blank lines)
uv run isort . --profile google

# PEP 8 (default)
uv run isort . --profile pep8

# Create custom profile in pyproject.toml
```

#### Handling Import Conflicts

```python
# Use isort:skip to skip specific imports
import sys
import os  # isort:skip

# Use isort:skip_file to skip entire file
# isort:skip_file
import z
import a

# Group imports with isort:split
import first_group
# isort:split
import second_group
```

## 🔴 CRITICAL: Black and isort Integration

### Recommended Setup for Black + isort

⚠️ **This section is CRITICAL for avoiding formatting conflicts in your project!**

1. **Always use isort's Black profile**:
   ```toml
   # pyproject.toml
   [tool.isort]
   profile = "black"
   ```

2. **Run tools in the correct order**:
   ```bash
   # 1. First organize imports
   uv run isort .
   
   # 2. Then format code
   uv run black .
   ```

3. **Pre-commit configuration order matters**:
   ```yaml
   # .pre-commit-config.yaml
   repos:
     # isort MUST come before black
     - repo: https://github.com/pycqa/isort
       rev: 5.13.2
       hooks:
         - id: isort
           args: ["--profile", "black"]
     
     - repo: https://github.com/psf/black
       rev: 24.10.0
       hooks:
         - id: black
   ```

4. **VS Code settings for compatibility**:
   ```json
   {
     "[python]": {
       "editor.defaultFormatter": "ms-python.black-formatter",
       "editor.formatOnSave": true,
       "editor.codeActionsOnSave": {
         "source.organizeImports": true
       }
     },
     "isort.args": ["--profile", "black"],
     "black-formatter.args": ["--line-length", "88"]
   }
   ```

5. **Makefile target for formatting**:
   ```makefile
   .PHONY: format
   format:
   	@echo "Organizing imports..."
   	uv run isort . --profile black
   	@echo "Formatting code with Black..."
   	uv run black .
   	@echo "✅ Code formatted successfully"
   ```

### Why Order Matters

⚠️ **Running tools in the wrong order causes infinite formatting loops!**

- **isort** organizes imports but might not format them exactly as Black prefers
- **Black** will reformat the organized imports to its style
- Running Black first, then isort, can create an endless loop of changes
- The `profile = "black"` setting makes isort format imports in a Black-compatible way

**Correct order**: isort → black → ruff
**Wrong order**: black → isort (causes conflicts!)

## Pre-commit Hooks

🔗 **See also**: [python/BOOTSTRAP.md](../../BOOTSTRAP.md) Step 7 for complete pre-commit setup in new projects.

Automate code quality checks before each commit:

```bash
# Install pre-commit (should already be in dev dependencies)
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

🔗 **See also**: [python/BOOTSTRAP.md](../../BOOTSTRAP.md) Step 3 for the standard Makefile template.

Create a Makefile for common tasks:

```makefile
.PHONY: quality
quality: lint format type-check test

.PHONY: lint
lint:
	uv run ruff check . --fix

.PHONY: format
format:
	# Order is critical: isort -> black -> ruff
	uv run isort . --profile black
	uv run black .
	uv run ruff format .

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
   # Install type stubs for specific packages only
   uv add --dev types-requests types-pyyaml
   
   # ⚠️ CRITICAL: NEVER use deprecated types-all package
   # It causes installation failures with pre-commit
   # Always add specific type stubs (types-*) for your dependencies
   ```
   
   **Referenced in**: [python/BOOTSTRAP.md](../../BOOTSTRAP.md) troubleshooting section

2. **Black and isort conflicts**:
   ```bash
   # ⚠️ CRITICAL: Use Black profile for isort
   uv run isort . --profile black
   ```
   
   **Referenced in**: [python/BOOTSTRAP.md](../../BOOTSTRAP.md) Steps 3, 8, and troubleshooting
   
   **Complete solution in pyproject.toml**:
   ```toml
   [tool.black]
   line-length = 88
   
   [tool.isort]
   profile = "black"  # This is the key setting
   line_length = 88  # Match Black's line length
   ```
   
   **If conflicts persist**:
   ```bash
   # Run in this exact order:
   uv run isort . --profile black
   uv run black .
   
   # Or use pre-commit to enforce order:
   # In .pre-commit-config.yaml, isort MUST come before black
   ```

3. **Ruff and Black disagreement**:
   ```toml
   # In pyproject.toml or ruff.toml
   [tool.ruff]
   line-length = 88  # Match Black's default
   
   # Disable rules that conflict with Black
   ignore = [
       "E501",  # Line too long (Black handles this)
       "W503",  # Line break before binary operator (Black's preference)
   ]
   
   # Format settings to match Black
   [tool.ruff.format]
   quote-style = "double"  # Black uses double quotes
   indent-style = "space"  # Black uses spaces
   skip-magic-trailing-comma = false  # Match Black's behavior
   line-ending = "auto"  # Let Black handle line endings
   ```
   
   **Recommended approach**: Use Ruff for linting, Black for formatting
   ```bash
   # Lint with Ruff
   uv run ruff check . --fix
   
   # Format with Black (run after Ruff)
   uv run black .
   ```

## Resources

- [Ruff Documentation](https://docs.astral.sh/ruff/)
- [Black Documentation](https://black.readthedocs.io/)
- [MyPy Documentation](https://mypy.readthedocs.io/)
- [Pre-commit Documentation](https://pre-commit.com/)
- [Python Type Hints](https://docs.python.org/3/library/typing.html)