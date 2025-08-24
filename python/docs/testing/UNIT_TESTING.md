# Unit Testing Guide

Comprehensive guide for writing and running unit tests in Python projects using pytest.

## Quick Start

```bash
# Run all tests
uv run pytest

# Run with coverage
uv run pytest --cov=src

# Run specific test file
uv run pytest tests/test_main.py

# Run tests matching pattern
uv run pytest -k "test_authentication"

# Run with verbose output
uv run pytest -v

# Run and stop on first failure
uv run pytest -x
```

## Project Structure

```
project/
├── src/
│   └── my_package/
│       ├── __init__.py
│       ├── core.py
│       └── utils.py
├── tests/
│   ├── __init__.py
│   ├── conftest.py          # Shared fixtures
│   ├── test_core.py
│   ├── test_utils.py
│   ├── unit/                # Unit tests
│   ├── integration/          # Integration tests
│   └── fixtures/            # Test data
└── pyproject.toml
```

## Writing Tests

### Basic Test Structure

```python
"""Test module for core functionality."""

import pytest
from my_package.core import Calculator


class TestCalculator:
    """Test cases for Calculator class."""
    
    def test_addition(self):
        """Test addition operation."""
        calc = Calculator()
        assert calc.add(2, 3) == 5
    
    def test_division(self):
        """Test division operation."""
        calc = Calculator()
        assert calc.divide(10, 2) == 5
    
    def test_division_by_zero(self):
        """Test division by zero raises exception."""
        calc = Calculator()
        with pytest.raises(ZeroDivisionError):
            calc.divide(10, 0)
```

### Using Fixtures

```python
# conftest.py
import pytest
from pathlib import Path
from my_package.database import Database


@pytest.fixture
def temp_dir(tmp_path):
    """Create a temporary directory for tests."""
    test_dir = tmp_path / "test_data"
    test_dir.mkdir()
    yield test_dir
    # Cleanup happens automatically


@pytest.fixture
def sample_data():
    """Provide sample data for tests."""
    return {
        "users": ["alice", "bob", "charlie"],
        "scores": [100, 85, 92],
    }


@pytest.fixture
def database():
    """Create a test database connection."""
    db = Database(":memory:")
    db.initialize()
    yield db
    db.close()


# test_features.py
def test_with_fixtures(temp_dir, sample_data, database):
    """Test using multiple fixtures."""
    # temp_dir is a Path object
    file_path = temp_dir / "test.txt"
    file_path.write_text("test content")
    
    # sample_data is available
    assert len(sample_data["users"]) == 3
    
    # database is ready to use
    database.insert_user("test_user")
    assert database.get_user("test_user") is not None
```

### Parametrized Tests

```python
import pytest


@pytest.mark.parametrize("input,expected", [
    (2, 4),
    (3, 9),
    (4, 16),
    (-2, 4),
    (0, 0),
])
def test_square(input, expected):
    """Test square function with multiple inputs."""
    assert input ** 2 == expected


@pytest.mark.parametrize("a,b,expected", [
    (2, 3, 5),
    (-1, 1, 0),
    (0, 0, 0),
    (100, 200, 300),
])
def test_addition(a, b, expected):
    """Test addition with various inputs."""
    assert a + b == expected
```

### Mocking and Patching

```python
from unittest.mock import Mock, patch, MagicMock
import pytest


def test_with_mock():
    """Test using Mock objects."""
    # Create a mock object
    mock_service = Mock()
    mock_service.get_data.return_value = {"status": "ok"}
    
    # Use the mock
    result = mock_service.get_data()
    assert result["status"] == "ok"
    mock_service.get_data.assert_called_once()


@patch("my_package.external.requests.get")
def test_api_call(mock_get):
    """Test API call with patched requests."""
    # Configure the mock
    mock_response = Mock()
    mock_response.json.return_value = {"data": "test"}
    mock_response.status_code = 200
    mock_get.return_value = mock_response
    
    # Test the function that uses requests.get
    from my_package.api import fetch_data
    result = fetch_data("https://api.example.com")
    
    assert result["data"] == "test"
    mock_get.assert_called_with("https://api.example.com")
```

### Async Tests

```python
import pytest
import asyncio


@pytest.mark.asyncio
async def test_async_function():
    """Test async function."""
    async def async_add(a, b):
        await asyncio.sleep(0.1)
        return a + b
    
    result = await async_add(2, 3)
    assert result == 5


@pytest.fixture
async def async_client():
    """Async fixture for client."""
    client = AsyncClient()
    await client.connect()
    yield client
    await client.disconnect()


@pytest.mark.asyncio
async def test_with_async_fixture(async_client):
    """Test using async fixture."""
    response = await async_client.get("/api/data")
    assert response.status_code == 200
```

## Test Organization

### Test Markers

```python
# Mark tests with custom markers
@pytest.mark.slow
def test_complex_operation():
    """Test that takes a long time."""
    pass


@pytest.mark.integration
def test_database_integration():
    """Test requiring database."""
    pass


@pytest.mark.skip(reason="Not implemented yet")
def test_future_feature():
    """Test for upcoming feature."""
    pass


@pytest.mark.skipif(sys.platform == "win32", reason="Unix only")
def test_unix_specific():
    """Test that only runs on Unix."""
    pass
```

Run specific markers:

```bash
# Run only slow tests
uv run pytest -m slow

# Run everything except slow tests
uv run pytest -m "not slow"

# Run integration tests
uv run pytest -m integration
```

### Test Classes

```python
class TestUserManagement:
    """Group related tests in a class."""
    
    @pytest.fixture(autouse=True)
    def setup(self, database):
        """Setup for each test method."""
        self.db = database
        self.db.clear()
    
    def test_create_user(self):
        """Test user creation."""
        user = self.db.create_user("alice")
        assert user.name == "alice"
    
    def test_delete_user(self):
        """Test user deletion."""
        user = self.db.create_user("bob")
        self.db.delete_user(user.id)
        assert self.db.get_user(user.id) is None
```

## Configuration

### pyproject.toml

```toml
[tool.pytest.ini_options]
minversion = "7.0"
testpaths = ["tests"]
python_files = ["test_*.py", "*_test.py"]
python_classes = ["Test*"]
python_functions = ["test_*"]
addopts = [
    "-ra",
    "--strict-markers",
    "--strict-config",
    "--cov=src",
    "--cov-branch",
    "--cov-report=term-missing",
    "--cov-report=html",
    "--cov-report=xml",
]
markers = [
    "slow: marks tests as slow",
    "integration: marks tests as integration tests",
    "unit: marks tests as unit tests",
]
```

### Coverage Configuration

```toml
[tool.coverage.run]
source = ["src"]
branch = true
parallel = true
omit = [
    "*/tests/*",
    "*/test_*.py",
    "*/__init__.py",
]

[tool.coverage.report]
exclude_lines = [
    "pragma: no cover",
    "def __repr__",
    "if self.debug:",
    "if __name__ == .__main__.:",
    "raise AssertionError",
    "raise NotImplementedError",
    "if TYPE_CHECKING:",
]
precision = 2
show_missing = true
skip_covered = false

[tool.coverage.html]
directory = "htmlcov"
```

## Best Practices

### 1. Test Naming

```python
# Good test names
def test_user_creation_with_valid_email():
    pass

def test_authentication_fails_with_invalid_password():
    pass

def test_calculate_discount_applies_percentage_correctly():
    pass

# Bad test names
def test1():
    pass

def test_user():
    pass

def test_function():
    pass
```

### 2. Arrange-Act-Assert Pattern

```python
def test_shopping_cart_total():
    """Test shopping cart calculates total correctly."""
    # Arrange
    cart = ShoppingCart()
    cart.add_item("Book", price=10.00, quantity=2)
    cart.add_item("Pen", price=1.50, quantity=3)
    
    # Act
    total = cart.calculate_total()
    
    # Assert
    assert total == 24.50
```

### 3. Test Isolation

```python
@pytest.fixture
def clean_database():
    """Provide a clean database for each test."""
    db = Database(":memory:")
    db.initialize()
    yield db
    db.close()


def test_user_creation(clean_database):
    """Each test gets its own database."""
    user = clean_database.create_user("alice")
    assert clean_database.count_users() == 1
```

### 4. Testing Exceptions

```python
def test_invalid_input_raises_exception():
    """Test that invalid input raises appropriate exception."""
    with pytest.raises(ValueError, match="Invalid email format"):
        validate_email("not-an-email")


def test_exception_details():
    """Test exception contains expected information."""
    with pytest.raises(CustomError) as exc_info:
        risky_operation()
    
    assert exc_info.value.error_code == "ERR_001"
    assert "failed" in str(exc_info.value).lower()
```

## Continuous Integration

### GitHub Actions

```yaml
name: Tests

on: [push, pull_request]

jobs:
  test:
    runs-on: ubuntu-latest
    strategy:
      matrix:
        python-version: ["3.10", "3.11", "3.12"]
    
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-python@v5
        with:
          python-version: ${{ matrix.python-version }}
      - uses: astral-sh/setup-uv@v3
      
      - name: Install dependencies
        run: uv sync --dev
      
      - name: Run tests
        run: |
          uv run pytest \
            --cov=src \
            --cov-report=xml \
            --cov-report=term-missing \
            --junit-xml=test-results.xml
      
      - name: Upload coverage
        uses: codecov/codecov-action@v4
        with:
          file: ./coverage.xml
```

## Advanced Topics

### Property-Based Testing

```python
from hypothesis import given, strategies as st


@given(st.integers(), st.integers())
def test_addition_commutative(a, b):
    """Test that addition is commutative."""
    assert a + b == b + a


@given(st.lists(st.integers()))
def test_sort_idempotent(lst):
    """Test that sorting twice gives same result."""
    sorted_once = sorted(lst)
    sorted_twice = sorted(sorted_once)
    assert sorted_once == sorted_twice
```

### Benchmark Tests

```python
def test_performance(benchmark):
    """Test function performance."""
    def fibonacci(n):
        if n < 2:
            return n
        return fibonacci(n-1) + fibonacci(n-2)
    
    result = benchmark(fibonacci, 10)
    assert result == 55
```

## Debugging Tests

```bash
# Run with print statements visible
uv run pytest -s

# Drop into debugger on failure
uv run pytest --pdb

# Show local variables on failure
uv run pytest -l

# Verbose output with full diff
uv run pytest -vv

# Run last failed tests
uv run pytest --lf

# Run failed tests first, then others
uv run pytest --ff
```

## Resources

- [Pytest Documentation](https://docs.pytest.org/)
- [Python Testing 101](https://realpython.com/pytest-python-testing/)
- [Effective Python Testing](https://testdriven.io/)
- [Coverage.py Documentation](https://coverage.readthedocs.io/)
- [Hypothesis Documentation](https://hypothesis.readthedocs.io/)