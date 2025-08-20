# Test Coverage Guide

## Overview

Test coverage measures how much of your code is executed during testing. This guide covers coverage measurement, targets, and best practices for your project.

## Coverage Tools

### Installation
```bash
# Install coverage tools using uv (preferred)
uv add --dev pytest-cov

# Alternative: Using pip
pip install pytest-cov
```

### Running Coverage Reports

```bash
# Basic coverage report
uv run pytest --cov=src/<package_name>

# Detailed terminal report with missing lines
uv run pytest --cov=src/<package_name> --cov-report=term-missing

# Generate HTML coverage report
uv run pytest --cov=src/<package_name> --cov-report=html

# Coverage for specific module
uv run pytest --cov=src/<package_name>/module tests/module/

# Fail tests if coverage drops below threshold
uv run pytest --cov=src/<package_name> --cov-fail-under=80
```

## Coverage Targets

### Project Goals
- **Overall Coverage**: ≥ 80%
- **Core Modules**: ≥ 90%
- **New Code**: ≥ 95%
- **Critical Paths**: 100%

### Module-Specific Targets

| Module Category | Target Coverage | Priority |
|----------------|-----------------|----------|
| Core Operations | 95% | Critical |
| Business Logic | 90% | High |
| Utility Functions | 85% | Medium |
| Infrastructure | 80% | Medium |
| Example/Demo Code | 50% | Low |

## Understanding Coverage Reports

### Terminal Output
```
Name                                    Stmts   Miss  Cover   Missing
----------------------------------------------------------------------
src/<package_name>/core/module.py         43      0   100%
src/<package_name>/utils/helpers.py       40      5    88%   45-49
src/<package_name>/api/endpoints.py       98     98     0%   1-198
----------------------------------------------------------------------
TOTAL                                     181    103    43%
```

- **Stmts**: Total number of statements
- **Miss**: Number of statements not executed
- **Cover**: Percentage of statements covered
- **Missing**: Line numbers not covered

### HTML Reports
```bash
# Generate HTML report
uv run pytest --cov=src/<package_name> --cov-report=html

# Open report (macOS)
open htmlcov/index.html

# Open report (Linux)
xdg-open htmlcov/index.html

# Open report (Windows)
start htmlcov/index.html
```

HTML reports provide:
- Interactive line-by-line coverage visualization
- Sortable module list
- Coverage trends over time
- Branch coverage details

## Coverage Types

### Line Coverage
Basic metric showing which lines were executed:
```python
def calculate(x, y):
    result = x + y  # ✓ Covered
    if result > 100:
        return 100  # ✗ Not covered if result ≤ 100
    return result   # ✓ Covered
```

### Branch Coverage
Ensures all code paths are tested:
```python
def process(value):
    if value > 0:      # Need tests for both True and False
        return "positive"
    elif value < 0:    # Need tests for both True and False
        return "negative"
    else:
        return "zero"
```

### Statement Coverage vs Functional Coverage
```python
# High statement coverage but poor functional coverage
def divide(a, b):
    # Test might cover the line but miss edge cases
    return a / b  # ✓ Line covered, but did we test b=0?
```

## Best Practices

### 1. Focus on Meaningful Coverage
```python
# Good: Test actual functionality
def test_functionality():
    """Test business logic, not just lines."""
    result = process_data(valid_input)
    assert result.is_valid()
    assert result.meets_requirements()
    
    # Test edge cases and properties
    edge_result = process_data(edge_case_input)
    assert edge_result.handles_edge_case()
```

### 2. Don't Chase 100% Coverage Blindly
```python
# Not worth testing
if __name__ == "__main__":
    # Demo code - low priority for coverage
    demo()

# Platform-specific code
if sys.platform == "win32":
    # Only test on relevant platform
    windows_specific_function()
```

### 3. Prioritize Critical Paths
```python
# High priority - core business logic
def critical_calculation(data):
    """Critical function - aim for 100% coverage."""
    # Every line and branch should be tested
    
# Lower priority - convenience wrapper
def helper_wrapper(data):
    """Simple wrapper - basic test sufficient."""
    return critical_calculation(data)
```

### 4. Use Coverage to Find Gaps
```bash
# Identify untested modules
uv run pytest --cov=src/<package_name> --cov-report=term-missing | grep "0%"

# Find partially tested modules
uv run pytest --cov=src/<package_name> --cov-report=term-missing | grep -E "[0-9]{1,2}%"
```

## Improving Coverage

### Step-by-Step Approach

1. **Measure Baseline**
   ```bash
   uv run pytest --cov=src/<package_name> --cov-report=term > coverage_baseline.txt
   ```

2. **Identify Gaps**
   - Sort by coverage percentage
   - Focus on critical modules first
   - Look for easy wins (simple functions)

3. **Write Targeted Tests**
   ```python
   # Use coverage report to identify missing lines
   # Missing: lines 45-52 (error handling)
   def test_error_conditions():
       """Target uncovered error paths."""
       with pytest.raises(ValueError):
           function_that_needs_coverage(invalid_input)
   ```

4. **Verify Improvement**
   ```bash
   # Run coverage again and compare
   uv run pytest --cov=src/<package_name> --cov-report=term
   ```

## Coverage in CI/CD

### GitHub Actions
See [github-actions-coverage.yaml](https://github.com/chrishayuk/vibe-coding-templates/blob/main/python/templates/cicd/workflows/github-actions-coverage.yaml) for a complete GitHub Actions workflow template with coverage reporting and Codecov integration.

### Pre-commit Hooks
See [pre-commit-coverage-hook.yaml](https://github.com/chrishayuk/vibe-coding-templates/blob/main/python/templates/cicd/hooks/pre-commit-coverage-hook.yaml) for pre-commit hook configurations that enforce coverage thresholds.

Quick setup:
```bash
# Install pre-commit
uv add --dev pre-commit

# Add hooks to .pre-commit-config.yaml
# Copy content from the template above

# Install hooks
pre-commit install

# Run coverage check
pre-commit run test-coverage --all-files
```

## Common Coverage Patterns

### Async Function Coverage
```python
@pytest.mark.asyncio
async def test_async_function():
    """Ensure async functions are properly covered."""
    result = await async_function()
    assert result is not None
    
    # Test with various inputs
    large_input = generate_large_input()
    result = await async_function(large_input)
    assert validate_result(result)
```

### Error Path Coverage
```python
def test_error_paths():
    """Cover all error conditions."""
    # Invalid input type
    with pytest.raises(TypeError):
        function("not a valid type")
    
    # Invalid input value
    with pytest.raises(ValueError, match="must be positive"):
        function(-1)
    
    # Edge case errors
    with pytest.raises(ValueError, match="empty"):
        function([])
```

### Branch Coverage
```python
@pytest.mark.parametrize("input,expected", [
    (positive_value, "positive"),
    (negative_value, "negative"),
    (zero_value, "zero"),
    (edge_case, "edge")
])
def test_all_branches(input, expected):
    """Ensure all conditional branches are covered."""
    result = function_with_branches(input)
    assert result == expected
```

## Troubleshooting

### Coverage Not Detected
```bash
# Ensure test discovery is working
uv run pytest --collect-only

# Check source path is correct
uv run pytest --cov=src/<package_name> --cov-report=term

# Verify __init__.py files exist
find src -name "*.py" -type f | head
```

### Inconsistent Coverage
```bash
# Clear coverage cache
rm -rf .coverage .pytest_cache

# Run with fresh environment
uv run pytest --cov=src/<package_name> --no-cov-on-fail
```

### Missing Async Coverage
```python
# Ensure pytest-asyncio is installed
uv add --dev pytest-asyncio

# Use proper async test marking
@pytest.mark.asyncio  # Required for async tests
async def test_async():
    result = await async_function()
```

## Coverage Badges

Add coverage badges to README:
```markdown
![Coverage](https://img.shields.io/badge/coverage-80%25-green)
![Tests](https://img.shields.io/badge/tests-passing-green)
```

Or with dynamic coverage:
```markdown
[![codecov](https://codecov.io/gh/username/repo/branch/main/graph/badge.svg)](https://codecov.io/gh/username/repo)
```

## Related Documentation

- [Unit Testing Guide](./UNIT_TESTING.md) - General unit testing practices
- [Testing Overview](./TESTING.md) - Complete testing guide
- [CI/CD Setup](./CI_CD.md) - Continuous integration configuration
- [Project README](../README.md) - Project overview

## Template Information

- **Source**: [vibe-coding-templates](https://github.com/chrishayuk/vibe-coding-templates/blob/main/python/docs/testing/TEST_COVERAGE.md)
- **Version**: 1.0.0
- **Date**: 2025-08-19
- **Author**: chrishayuk
- **Template**: Generic Python Project

### Customization Notes

When adapting this template:
1. Replace `<package_name>` with your actual package name
2. Adjust coverage targets based on project requirements
3. Update CI/CD examples for your platform
4. Add project-specific testing patterns
5. Include relevant badges for your repository