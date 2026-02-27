Installation
============

This section covers how to install Clojure and set up your development
environment for agentic engineering.

Prerequisites
-------------

This manual assumes a Unix style operating system, MacOS or Linux.

You will need the JVM installed. We suggest that you use the `SDKMAN
<https://sdkman.io/>`_ project to manage your different JVM
instances. For this work, we are going to use the latest version of
`GraalVM <https://www.graalvm.org/>`_

.. code-block:: console

    # curl -s "https://get.sdkman.io" | bash


After we have installed SDKMAN, let's go ahead and install GraalVM 25.

.. code-block:: console

    sdk install java 25.0.2-graalce

Installing Clojure
------------------

Now let's install the Clojure command line tools. If you use `Homebrew
<https://brew.sh/>`_, you can install the Clojure command line tools
with the following command:

.. code-block:: console

    brew install clojure/tools/clojure


See the `Clojure documentation for more details
<https://clojure.org/guides/install_clojure>`_

Installing Babashka
-------------------

Installing bbin
---------------

`Bbin <https://github.com/babashka/bbin>`_ is a tool for installing
Babashka scripts

.. code-block:: console

   brew install babashka/brew/bbin

Install NodeJS
--------------

Now let's install `Node.js <https://nodejs.org/en/download>`_:

.. code-block:: console

   curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.40.4/install.sh | bash

   nvm install 24


Verifying Your Installation
----------------------------

Let's make sure everything is installed correctly.

Verify Java

.. code-block:: console

   java --version

   ## java 25.0.1 2025-10-21 LTS
   ## Java(TM) SE Runtime Environment Oracle GraalVM 25.0.1+8.1 (build 25.0.1+8-LTS-jvmci-b01)
   ## Java HotSpot(TM) 64-Bit Server VM Oracle GraalVM 25.0.1+8.1 (build 25.0.1+8-LTS-jvmci-b01, mixed mode, sharing)

Verify Clojure

.. code-block:: console

   clojure --version
   ## Clojure CLI version 1.12.4.1582

Verify Node.JS

.. code-block:: console

   node --version
   ## v25.4.0
