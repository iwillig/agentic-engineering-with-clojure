Installation
------------

This section covers how to install Clojure and set up your development
environment for agentic engineering. We are going to use tools from
Node.js and Python as well.

Prerequisites
^^^^^^^^^^^^^

All of the below dependencies can be installed via this homebrew Brewfile.

.. code-block:: text

    brew "pandoc"
    brew "qemu"
    brew "clojure/tools/clojure"
    brew "borkdude/brew/babashka"
    brew "babashka/brew/bbin"
    brew "clojure-lsp/brew/clojure-lsp-native"
    brew "node"
    brew "pipx"
    brew "anomalyco/tap/opencode"

.. code-block:: bash

    brew bundle

Each tool is defined with specific install instructions.

SDKMAN
^^^^^^

This manual assumes a Unix style operating system, MacOS or Linux.

You will need the JVM installed. We suggest that you use the `SDKMAN
<https://sdkman.io/>`_ project to manage your different JVM
instances. For this work, we are going to use the latest version of
`GraalVM <https://www.graalvm.org/>`_

.. code-block:: bash

    curl -s "https://get.sdkman.io" | bash


After we have installed SDKMAN, let's go ahead and install GraalVM 25.

.. code-block:: bash

    sdk install java 25.0.2-graalce

Pandoc
^^^^^^

Pandoc is a power tool for document transformation and validation. We
are going to use pandoc in our manual.

.. code-block:: bash

    brew install pandoc

Qemu
^^^^^

We are going to use Qemu with Gondolin. Its a native dependency
installed via brew.

.. code-block:: bash

    brew install qemu


Clojure
-------

Installing Clojure
^^^^^^^^^^^^^^^^^^

Now let's install the Clojure command line tools. If you use `Homebrew
<https://brew.sh/>`_, you can install the Clojure command line tools
with the following command:

.. code-block:: bash

    brew install clojure/tools/clojure


See the `Clojure documentation for more details
<https://clojure.org/guides/install_clojure>`_

Install Babashka
^^^^^^^^^^^^^^^^

`Babashka <https://github.com/babashka/babashka>`_ is a native Clojure
interpreter for scripting with fast startup.

.. code-block:: bash

   brew install borkdude/brew/babashka


Install bbin
^^^^^^^^^^^^

`Bbin <https://github.com/babashka/bbin>`_ is a tool for installing
Babashka scripts

.. code-block:: bash

   brew install babashka/brew/bbin

Install clojure-lsp
^^^^^^^^^^^^^^^^^^^

.. code-block:: bash

    brew install clojure-lsp/brew/clojure-lsp-native

NodeJS
------

Install NodeJS
^^^^^^^^^^^^^^

There are a lot of AI tools written in NodeJS. Now let's install
`Node.js <https://nodejs.org/en/download>`_:

.. code-block:: bash

   curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.40.4/install.sh | bash

.. code-block:: bash

   nvm install 24

Gondolin
^^^^^^^^

.. code-block:: bash

    npm install -g @earendil-works/gondolin


Python
------

Install Python
^^^^^^^^^^^^^^

Python is one of the default languages for working with LLM
Agents. There are serveral important libraries and tools that we are
going to be reviewing for this manual. There are lot of Python build
tools, but we are going to use `pipenv
<https://pipenv.pypa.io/en/latest/>`_.

Lets install `pipx <https://github.com/pypa/pipx>`_ for install global
python tools.

.. code-block:: bash

   brew install pipx

.. code-block:: bash

   pipx install pipenv

.. code-block:: bash

   pipx install llm


Verifying Your Installation
^^^^^^^^^^^^^^^^^^^^^^^^^^^

Let's make sure everything is installed correctly.

Verify Java

.. code-block:: bash

   java --version

Verify Clojure

.. code-block:: bash

   clojure --version

Verify Node.JS

.. code-block:: bash

   node --version
