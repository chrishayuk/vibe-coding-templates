# Makefile for Python Projects with uv
# 
# This Makefile provides common development tasks for Python projects
# using uv as the package manager. All commands use uv run to ensure
# proper environment isolation.
#
# Usage:
#   make help        Show this help message
#   make install     Install project dependencies
#   make test        Run tests with coverage
#   make lint        Run all code quality checks
#   make format      Format code automatically
#   make clean       Clean build artifacts and caches
#

# Variables
PACKAGE_NAME = {{package_name}}
SRC_DIR = src
TEST_DIR = tests
PYTHON_FILES = $(SRC_DIR) $(TEST_DIR)

# Default target
.DEFAULT_GOAL := help

# Help target
.PHONY: help
help:  ## Show this help message
	@echo "Available targets:"
	@grep -E '^[a-zA-Z_-]+:.*?## .*$$' $(MAKEFILE_LIST) | awk 'BEGIN {FS = ":.*?## "}; {printf "  %-20s %s\n", $$1, $$2}'

# Installation targets
.PHONY: install
install:  ## Install production dependencies
	uv sync

.PHONY: dev-install
dev-install:  ## Install all dependencies including dev
	uv sync --dev

.PHONY: upgrade
upgrade:  ## Upgrade all dependencies to latest versions
	uv sync --upgrade

# Testing targets
.PHONY: test
test:  ## Run all tests
	uv run pytest

.PHONY: test-cov
test-cov:  ## Run tests with coverage report
	uv run pytest --cov=$(SRC_DIR)/$(PACKAGE_NAME) --cov-report=term-missing --cov-report=html

.PHONY: test-watch
test-watch:  ## Run tests in watch mode (requires pytest-watch)
	uv run ptw -- --tb=short

.PHONY: test-verbose
test-verbose:  ## Run tests with verbose output
	uv run pytest -vv

.PHONY: test-failed
test-failed:  ## Re-run only failed tests
	uv run pytest --lf

# Code quality targets
.PHONY: lint
lint:  ## Run all linting checks
	uv run ruff check $(PYTHON_FILES)
	uv run mypy $(SRC_DIR)

.PHONY: lint-fix
lint-fix:  ## Fix auto-fixable linting issues
	uv run ruff check $(PYTHON_FILES) --fix

.PHONY: format
format:  ## Format code with ruff and black
	uv run ruff format $(PYTHON_FILES)
	uv run black $(PYTHON_FILES)

.PHONY: format-check
format-check:  ## Check code formatting without making changes
	uv run ruff format --check $(PYTHON_FILES)
	uv run black --check $(PYTHON_FILES)

.PHONY: typecheck
typecheck:  ## Run type checking with mypy
	uv run mypy $(SRC_DIR)

.PHONY: security
security:  ## Run security checks with bandit
	uv run bandit -r $(SRC_DIR)

# Pre-commit targets
.PHONY: pre-commit
pre-commit:  ## Run pre-commit on all files
	uv run pre-commit run --all-files

.PHONY: pre-commit-install
pre-commit-install:  ## Install pre-commit hooks
	uv run pre-commit install

.PHONY: pre-commit-update
pre-commit-update:  ## Update pre-commit hooks to latest versions
	uv run pre-commit autoupdate

# Build and distribution targets
.PHONY: build
build:  ## Build distribution packages
	uv build

.PHONY: publish-test
publish-test:  ## Publish to TestPyPI
	uv publish --publish-url https://test.pypi.org/legacy/

.PHONY: publish
publish:  ## Publish to PyPI (requires authentication)
	uv publish

# Documentation targets
.PHONY: docs
docs:  ## Build documentation (requires sphinx)
	uv run sphinx-build -b html docs docs/_build

.PHONY: docs-serve
docs-serve:  ## Serve documentation locally
	uv run python -m http.server -d docs/_build 8000

# Development targets
.PHONY: run
run:  ## Run the main application
	uv run python -m $(PACKAGE_NAME)

.PHONY: shell
shell:  ## Open an interactive Python shell
	uv run python

.PHONY: ipython
ipython:  ## Open IPython shell (requires ipython)
	uv run ipython

.PHONY: jupyter
jupyter:  ## Start Jupyter notebook (requires jupyter)
	uv run jupyter notebook

# Cleaning targets
.PHONY: clean
clean:  ## Clean build artifacts and caches
	rm -rf build/
	rm -rf dist/
	rm -rf *.egg-info
	rm -rf .pytest_cache/
	rm -rf .mypy_cache/
	rm -rf .ruff_cache/
	rm -rf htmlcov/
	rm -rf .coverage
	find . -type d -name __pycache__ -exec rm -rf {} +
	find . -type f -name "*.pyc" -delete
	find . -type f -name "*.pyo" -delete
	find . -type f -name "*~" -delete

.PHONY: clean-all
clean-all: clean  ## Clean everything including virtual environment
	rm -rf .venv/
	rm -rf uv.lock

# CI/CD targets
.PHONY: ci-test
ci-test:  ## Run CI test suite
	uv run pytest --cov=$(SRC_DIR)/$(PACKAGE_NAME) --cov-report=xml --cov-report=term

.PHONY: ci-lint
ci-lint:  ## Run CI linting checks
	uv run ruff check $(PYTHON_FILES) --format=github
	uv run mypy $(SRC_DIR) --junit-xml mypy-report.xml

# Dependency management targets
.PHONY: deps-add
deps-add:  ## Add a new dependency (use with PKG=package-name)
	@if [ -z "$(PKG)" ]; then echo "Usage: make deps-add PKG=package-name"; exit 1; fi
	uv add $(PKG)

.PHONY: deps-add-dev
deps-add-dev:  ## Add a new dev dependency (use with PKG=package-name)
	@if [ -z "$(PKG)" ]; then echo "Usage: make deps-add-dev PKG=package-name"; exit 1; fi
	uv add --dev $(PKG)

.PHONY: deps-remove
deps-remove:  ## Remove a dependency (use with PKG=package-name)
	@if [ -z "$(PKG)" ]; then echo "Usage: make deps-remove PKG=package-name"; exit 1; fi
	uv remove $(PKG)

.PHONY: deps-show
deps-show:  ## Show installed dependencies
	uv pip list

.PHONY: deps-outdated
deps-outdated:  ## Show outdated dependencies
	uv pip list --outdated

.PHONY: deps-tree
deps-tree:  ## Show dependency tree
	uv pip tree

# Lock file management
.PHONY: lock
lock:  ## Update lock file
	uv lock

.PHONY: lock-check
lock-check:  ## Verify lock file is up to date
	uv lock --check

# Environment targets
.PHONY: env-info
env-info:  ## Show environment information
	@echo "Python version:"
	@uv run python --version
	@echo "\nPython path:"
	@uv run which python
	@echo "\nuv version:"
	@uv --version
	@echo "\nProject dependencies:"
	@uv pip list

# Quality assurance targets
.PHONY: qa
qa: lint typecheck test-cov  ## Run all quality checks

.PHONY: qa-fix
qa-fix: format lint-fix test  ## Fix issues and run tests

# Watch targets for development
.PHONY: watch
watch:  ## Watch for changes and run tests
	uv run watchmedo shell-command \
		--patterns="*.py" \
		--recursive \
		--command='clear && make test' \
		$(PYTHON_FILES)

# Utility targets
.PHONY: check-all
check-all: format-check lint typecheck test-cov  ## Run all checks without modifications

.PHONY: ready
ready: format lint-fix typecheck test-cov  ## Prepare code for commit

.PHONY: release
release: clean check-all build  ## Prepare a release

# Template metadata
# Repository: vibe-coding-templates
# Path: python/templates/Makefile
# Version: 1.0.0
# Date: 2025-01-24