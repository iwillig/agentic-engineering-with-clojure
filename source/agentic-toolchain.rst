Agentic Toolchain
=================

This section covers how to install and configure the agentic toolchain
for Clojure development.

We are going to focus on using open source tools and open weight
models for this manual. Open Source based tool chains offer a lot of
attractive charactists. You have a lot of control


In order to do so, we are going to use `OpenCode
<https://opencode.ai/>`_, or `PI Agent
<https://github.com/badlogic/pi-mono/tree/main/packages/coding-agent>`_.

You are welcome to pick and choose any of these tools. This manual will
focus on using PI Agent with the Clojure MCP server.

Install OpenCode
----------------

Opencode is a complete coding editor designed to complete with `Claude
Code <https://code.claude.com/docs/en/overview>`_ or `Codex
<https://openai.com/codex/>`_.

OpenCode has both an Native UI and TUI version.

.. code-block:: console

   brew install anomalyco/tap/opencode

Install PI Agent
----------------

PI Agent is a TypeScript based coding agent that is designed for
simplicity. PI Agent is only an TUI.

.. code-block:: console

   npm install -g @mariozechner/pi-coding-agent


Install Clojure MCP
-------------------

`Clojure MCP <https://github.com/bhauman/clojure-mcp>`_ is an advance
MCP server for working with Clojure. It includes toosl for running
Clojure Code via the REPL, and advance tools for editing Clojure code.

.. code-block:: console

   clojure -Ttools install-latest :lib io.github.bhauman/clojure-mcp :as mcp


Install Clojure MCP Light
-------------------------

`Clojure MCP Light <https://github.com/bhauman/clojure-mcp-light>`_ is
a command line tool for evaluating Clojure code.

.. code-block:: console

   bbin install https://github.com/bhauman/clojure-mcp-light.git --tag v0.2.1
