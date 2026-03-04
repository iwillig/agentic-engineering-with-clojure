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

.. code-block:: bash

   brew install anomalyco/tap/opencode

Open Code Configuration
^^^^^^^^^^^^^^^^^^^^^^^

OpenCode configured via a `opencode.json
<https://opencode.ai/docs/config/>`_ file in your projects root folder
of the project. You can also define a global configuration file.

A common development pattern with OpenCode is to configure a custom
System Prompt with the standard editing tools. In the below
configuration, we are using the standard editing tools with the
`clojure-lsp` tool for validating edits.

.. code-block:: json

    {
      "$schema": "https://opencode.ai/config.json",
      "autoupdate": true,
      "model": "anthropic/claude-sonnet-4-5",
      "agent": {
        "clojure_build": {
          "mode": "primary",
          "model": "anthropic/claude-sonnet-4-5",
          "prompt": "{file:./clojure_build.md}",
          "tools": {
            "write": true,
            "edit": true,
            "read": true,
            "bash": true
          }
        }
      },
      "mcp": {},
      "lsp": {
        "clojure-lsp": {
          "command": ["/opt/homebrew/bin/clojure-lsp"],
          "extensions": [".clj", ".cljs", ".cljc", ".edn", ".bb"],
          "env": {
            "CLOJURE_LSP_LOG_LEVEL": "info"
          },
          "initialization": {
            "initializationOptions": {
              "project-root-uri-as-project-root?": true,
              "text-document-sync-kind": 2
            }
          }
        }
      }
    }


Install PI Agent
----------------

PI Agent is a TypeScript based coding agent that is designed for
simplicity. PI Agent is only an TUI.

.. code-block:: bash

   npm install -g @mariozechner/pi-coding-agent


PI Agent Configuration
^^^^^^^^^^^^^^^^^^^^^^

PI Agent allows you to configure what models and providers via two
configuration files, ``settings.json`` and ``models.json``.

In your ``~/.pi/agent/settings.json`` you can define "enabledModels"
and the defaultModel as follows.

.. code-block:: json

    {
      "defaultModel": "claude-sonnet-4-5",
      "defaultProvider": "anthropic",
      "defaultThinkingLevel": "off",
      "enabledModels": [
        "ollama/qwen3.5:0.8b",
        "ollama/qwen3.5:2b",
        "huggingface/moonshotai/Kimi-K2.5",
        "anthropic/claude-opus-4-5",
        "anthropic/claude-sonnet-4-5",
        "anthropic/claude-sonnet-4-6",
        "anthropic/claude-opus-4-6"
      ],
      "hideThinkingBlock": false,
      "lastChangelogVersion": "0.55.4",
      "packages": [
        "npm:@tmustier/pi-usage-extension",
        "npm:pi-mcp-adapter",
        "npm:pi-interactive-shell",
        "npm:pi-ask-tool-extension"
      ]
    }

Here are two examples, for running PI Agent against Hugging Face and
via Ollama via in ``~/.pi/agent/models.json``

.. code-block:: json

    {
      "providers": {
        "huggingface": {
          "api": "openai-completions",
          "apiKey": "HF_TOKEN",
          "baseUrl": "https://router.huggingface.co/v1",
          "models": [
            {
              "compat": {
                "supportsDeveloperRole": false
              },
              "contextWindow": 128000,
              "cost": {
                "cacheRead": 0,
                "cacheWrite": 0,
                "input": 0,
                "output": 0
              },
              "id": "moonshotai/Kimi-K2",
              "input": [
                "text"
              ],
              "maxTokens": 16384,
              "name": "Kimi K2",
              "reasoning": true
            }
          ]
        },
        "ollama": {
          "api": "openai-completions",
          "apiKey": "ollama",
          "baseUrl": "http://127.0.0.1:11434/v1",
          "models": [
            {
              "id": "qwen3.5:0.8b"
            },
            {
              "id": "qwen3.5:2b"
            }
          ]
        }
      }
    }



PI Agent System Prompt
^^^^^^^^^^^^^^^^^^^^^^

We want to override the default system prompt for Clojure
development. In your application project, you can create a new folder.

.. code-block:: bash

    mkdir .pi
    cd .pi
    touch SYSTEM.md

Please review the PI Agent configuration `documentation
<https://github.com/badlogic/pi-mono/tree/main/packages/coding-agent#customization>`_
for more details.


Install Clojure MCP
-------------------

`Clojure MCP <https://github.com/bhauman/clojure-mcp>`_ is an advance
MCP server for working with Clojure. It includes toosl for running
Clojure Code via the REPL, and advance tools for editing Clojure code.

.. code-block:: bash

   clojure -Ttools install-latest :lib io.github.bhauman/clojure-mcp :as mcp


Install Clojure MCP Light
-------------------------

`Clojure MCP Light <https://github.com/bhauman/clojure-mcp-light>`_ is
a command line tool for evaluating Clojure code. It also includes tools for fixing common

.. code-block:: bash

   bbin install \
     https://github.com/bhauman/clojure-mcp-light.git \
     --tag v0.2.1

clj-nrepl-eval
^^^^^^^^^^^^^^

Clojure MCP Light includes a tool for evaluating Clojure code. This is
a standard bash command that can be used in any Coding harness.

.. code-block:: bash

   bbin install \
     https://github.com/bhauman/clojure-mcp-light.git \
     --tag v0.2.1 \
     --as clj-nrepl-eval \
     --main-opts '["-m" "clojure-mcp-light.nrepl-eval"]'

Now lets verify the install

.. code-block:: bash

   clj-nrepl-eval --discover-ports

clj-paren-repair
^^^^^^^^^^^^^^^^^

.. code-block:: bash

   bbin install \
     https://github.com/bhauman/clojure-mcp-light.git \
     --tag v0.2.1 \
     --as clj-paren-repair \
     --main-opts '["-m" "clojure-mcp-light.paren-repair"]'

And now lets verify the clj-paren-repair

.. code-block:: bash

    clj-paren-repair --help
