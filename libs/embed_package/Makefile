.PHONY: all clean docs_build docs_clean docs_linkcheck api_docs_build api_docs_clean api_docs_linkcheck format lint test tests test_watch integration_tests docker_tests help extended_tests

# Default target executed when no arguments are given to make.
all: help

precommit : | format spell_fix spell_check lint poetry_check test

######################
# TESTING AND COVERAGE
######################

# Define a variable for the test file path.
TEST_FILE ?= 

# Run unit tests and generate a coverage report.
coverage:
	poetry run coverage run --source ./embed -m pytest --doctest-modules 
	poetry run coverage report -m
	poetry run coverage xml

test tests:
	poetry run pytest --doctest-modules 


######################
# LINTING AND FORMATTING
######################

# Define a variable for Python and notebook files.
PYTHON_FILES=.
lint format: PYTHON_FILES=.
lint_diff format_diff: PYTHON_FILES=$(shell git diff --relative=libs/infinity_emb --name-only --diff-filter=d master | grep -E '\.py$$|\.ipynb$$')

lint lint_diff:
	poetry run ruff check .
	
	[ "$(PYTHON_FILES)" = "" ] || poetry run mypy $(PYTHON_FILES)

format format_diff:
	[ "$(PYTHON_FILES)" = "" ] || poetry run ruff format $(PYTHON_FILES)

poetry_check:
	poetry check

spell_check:
	poetry run codespell --toml pyproject.toml

spell_fix:
	poetry run codespell --toml pyproject.toml -w

benchmark_embed: tests/data/benchmark/benchmark_embed.json
	ab -n 10 -c 10 -l -s 480 \
	-T 'application/json' \
	-p $< \
	http://127.0.0.1:7997/embeddings
	# sudo apt-get apache2-utils

######################
# HELP
######################

help:
	@echo '===================='
	@echo 'clean                        - run docs_clean and api_docs_clean'
	@echo 'docs_build                   - build the documentation'
	@echo 'docs_clean                   - clean the documentation build artifacts'
	@echo 'docs_linkcheck               - run linkchecker on the documentation'
	@echo 'api_docs_build               - build the API Reference documentation'
	@echo 'api_docs_clean               - clean the API Reference documentation build artifacts'
	@echo 'api_docs_linkcheck           - run linkchecker on the API Reference documentation'
	@echo '-- LINTING --'
	@echo 'format                       - run code formatters'
	@echo 'lint                         - run linters'
	@echo 'spell_check               	- run codespell on the project'
	@echo 'spell_fix               		- run codespell on the project and fix the errors'
	@echo 'poetry_check                 - run poetry check'
	@echo '-- TESTS --'
	@echo 'coverage                     - run unit tests and generate coverage report'
	@echo 'test                         - run unit tests'
	@echo 'tests                        - run unit tests (alias for "make test")'
	@echo 'test TEST_FILE=<test_file>   - run all tests in file'
	@echo 'extended_tests               - run only extended unit tests'
	@echo 'test_watch                   - run unit tests in watch mode'
	@echo 'integration_tests            - run integration tests'
	@echo 'docker_tests                 - run unit tests in docker'
	@echo '-- DOCUMENTATION tasks are from the top-level Makefile --'