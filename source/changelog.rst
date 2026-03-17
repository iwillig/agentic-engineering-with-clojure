Changelog
=========

This document tracks the evolution of the *Agentic Engineering with Clojure* manual.

[0.5.0] - 2026-03-17
--------------------

Changed
~~~~~~~

- Expanded ``repl-development.rst`` from stub placeholders into a full chapter:

  - **What is the REPL?** -- Introduced the REPL concept and its role in
    preventing LLM hallucinations
  - **Starting the REPL** -- Added ``deps.edn`` setup for ``nrepl`` and
    ``cider-nrepl``, startup commands, and ``clj-nrepl-eval`` connection example
  - **Reload** -- Added ``clj-reload`` integration and the passive-prompting
    technique of printing docstrings on namespace load
  - **Interactive Development** -- Explained REPL-grounded workflow for LLM
    agents: evaluation gives facts from the running system instead of guesses
    from training data
  - **The REPL as Hallucination Prevention** -- New section covering:

    - Validation-before-saving discipline with a ``sum-evens`` worked example
    - Exploration-before-use pattern using ``clojure.repl/doc``, ``dir``,
      ``apropos``, ``find-doc``, and ``source``
    - Codebase awareness via ``require`` + ``clojure.repl/dir``

  - **The Paren Edit Death Loop** -- Documented the delimiter-repair cycle that
    LLM agents fall into and how ``clj-paren-repair`` breaks it
  - **Passive Prompting Through the REPL** -- Technique for printing docstrings
    on dev-namespace reload to keep agents informed without repetition
  - **Best Practices** -- New sub-sections on keeping the nREPL process running,
    test fixtures and ``clojure.test/run-test-var``, and pipeline debugging with
    intermediate ``def`` bindings

[0.4.0] - 2026-03-05
--------------------

Added
~~~~~

- Added reusable LLM agent prompt templates (``common-user-prompts.rst``)
- Added this changelog

[0.3.0] - 2026-03-04
--------------------

Added
~~~~~

- Git Hygiene workflow for automated commit message validation
- GitHub Actions integration for pull request checks
- Local LLM model support for commit linting via ``iwillig/git-hygiene`` action

[0.2.0] - 2026-03-04
--------------------

Added
~~~~~

- PI Agent configuration guide and setup instructions
- Enhanced agentic toolchain documentation
- Integration patterns for local LLM models

[0.1.0] - 2026-03-01 to 2026-03-04
----------------------------------

Added
~~~~~

- Project README with build instructions and prerequisites
- Comprehensive installation guide covering:
  - Homebrew setup for macOS
  - SDKMAN for JVM/Clojure management
  - OpenCode AI agent installation
  - Gonodlin browser automation tool
  - PI Agent configuration
- Agentic browser automation chapter (``agentic-browser.rst``) with Etaoin examples
- REPL-driven development best practices
- Testing strategies for agentic workflows
- Sphinx documentation configuration and build system
- Basic project structure with reStructuredText source files
- Makefile and Windows batch build scripts
- Python dependencies via Pipfile (Sphinx, Etaoin)
- Clojure dependencies for code examples (``deps.edn``)
- Babashka tasks for nREPL management (``bb.edn``)

Changed
~~~~~~~

- Improved documentation layout and sidebar navigation
- Fixed width constraints for better readability

Initial Commit - 2026-02-26
---------------------------

- Created Sphinx documentation project structure
- Added introduction chapter explaining the manual's purpose
- Established foundational vision: Clojure's relevance in the age of LLM agents
