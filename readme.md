# Agentic Engineering with Clojure

A comprehensive guide for using LLM coding agents with Clojure development.

## Overview

This manual covers best practices for combining Clojure's REPL-driven development 
with modern LLM coding agents. It includes detailed guides on:

- Installing and configuring Clojure development tools
- Setting up agentic toolchains (OpenCode, PI Agent, Clojure MCP)
- Browser automation with Etaoin for agentic testing
- REPL-driven development workflows
- Testing strategies with LLM agents

## Prerequisites

- Python 3.x with pipenv
- Java/JVM (via SDKMAN recommended)
- Clojure CLI tools
- Node.js (for AI agent tools)

See [Installation Guide](source/installation.rst) for detailed setup instructions.

## Building the Documentation

This project uses [Sphinx](https://www.sphinx-doc.org/) for documentation generation.

### Install Dependencies

```bash
# Install dependencies from Pipfile.lock
pipenv install

# Or if you need to update the lock file
pipenv install --dev
```

### Build

```bash
# Enter the pipenv shell
pipenv shell

# Then build the documentation
make html
```

Or run directly:

```bash
pipenv run make html
```

The generated documentation will be in `build/html/`.

### Watch for Changes

```bash
pipenv run make livehtml  # If using sphinx-autobuild
```

## Project Structure

```
source/              # Documentation source files (.rst)
├── index.rst        # Main documentation entry point
├── installation.rst # Setup instructions
├── agentic-toolchain.rst
├── agentic-browser.rst
├── repl-development.rst
└── testing.rst
build/               # Generated HTML output
deps.edn             # Clojure dependencies (for examples)
bb.edn               # Babashka tasks (nREPL)
```

## Contributing

This manual is built with reStructuredText. To contribute:

1. Edit files in `source/`
2. Build and verify: `pipenv run make html`
3. Submit changes via pull request

## License

[Add your license here]
