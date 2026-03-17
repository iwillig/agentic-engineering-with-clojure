REPL-Based Development
======================

This section explores REPL-based development in Clojure and how it
enhances agentic engineering workflows.

What is the REPL?
-----------------

`REPL <https://clojure.org/guides/repl/introduction>`_ stands for
read, eval, print, and loop. It is a interactive shell that allows you
to evaluate Clojure expressions and have those Clojure expressions be
printed.

It is the defacto method for exploring and interacting with Clojure
code. It turns out that a full featured repl is excellent tool for
working with large language models and preventing AI hallucinations.

Starting the REPL
-----------------

In this manual we're going to use the `clj-mcp-light
<https://github.com/bhauman/clojure-mcp-light/tree/main>`_ tool. First
first we're going to need a nREPL process in order to interact with
closure code. Here is a very basic setup that you can use in any
Clojure project.

.. code-block:: clojure

    {:deps {org.clojure/clojure {:mvn/version "1.12.4"}
            io.github.tonsky/clj-reload {:mvn/version "1.0.0"}}
     :aliases {:nrepl
               {:extra-deps
                {nrepl/nrepl {:mvn/version "1.3.0"}
                 cider/cider-nrepl {:mvn/version "0.56.0"}}
                :jvm-opts ["-Djdk.attach.allowAttachSelf"]
                :main-opts ["-m" "nrepl.cmdline"
                            "--middleware" "[cider.nrepl/cider-middleware]"
                            "--port" "7889"]}}}

This pull the Clojure nRepl dependency and sets up the nRepl to always
start on the `7889` port.

Lets now start the repl process and try to connect to it.

.. code-block:: bash

    clojure -M:nrepl

.. code-block:: bash

    clj-nrepl-eval -p 7889 "*ns*"

Reload
------

In order to be effective with the REPL, we need a mechanism for
loading and reloading Clojure code without having to restart the
Clojure process. The `clj-reload
<https://github.com/tonsky/clj-reload>`_ library is an excellent tool
that does this. The `clojure.tools.namespace
<https://github.com/clojure/tools.namespace>`_ project also has
similar functionaity.

.. code-block:: clojure

    (ns dev
      (:require [clj-reload.core :as reload]
                [clojure.repl :as repl))

     (reload/init
      {:dirs ["src" "dev" "test"]})

      (println (repl/doc reload/reload))

Note the that print the doc string for the reload function. This
prints the docs for that function every time the namespace is
loaded. This is helpful to remind the LM agent about these tools.

Interactive Development
-----------------------

With the nREPL running and ``clj-nrepl-eval`` connected, the same
REPL workflow that Clojure developers already use becomes a grounding
mechanism for LLM agents. The agent can evaluate expressions, call
``clojure.repl/doc``, inspect data, and test functions against a live
runtime -- all through standard bash commands.

The key insight is that every REPL evaluation gives the agent a fact
from the running system instead of a guess from training data. An
agent that calls ``(clojure.repl/doc clojure.set/intersection)``
before using it will not hallucinate the argument order. An agent that
evaluates ``(my-function nil)`` before saving will discover the nil
handling bug immediately.

The sections below describe how to structure this workflow to get the
most out of it.


The REPL as Hallucination Prevention
-------------------------------------

Without a REPL, an LLM agent writes code based entirely on its
training data and the current context window. It has no way to check
whether a function exists, what arguments it takes, or how it behaves
on edge cases. The result is code that looks plausible but may be
wrong in subtle ways -- a function called with its arguments in the
wrong order, a missing initial value to ``reduce``, a method that
does not exist in the version of the library you are using.

The REPL solves this by giving the agent access to the actual running
system. When the agent can evaluate ``(clojure.repl/doc some-fn)``
and get back real documentation, or call ``(some-fn test-input)`` and
see the actual result, it is working with facts instead of
statistical predictions.

Validation Before Saving
^^^^^^^^^^^^^^^^^^^^^^^^^

The most impactful discipline is requiring the agent to validate
functions in the REPL before writing them to files. Consider a simple
example:

.. code-block:: bash

    clj-nrepl-eval -p 7889 "(defn sum-evens [nums] (->> nums (filter even?) (reduce +)))"
    clj-nrepl-eval -p 7889 "(sum-evens [1 2 3 4 5 6])"
    # => 12

The happy path works. But what about edge cases?

.. code-block:: bash

    clj-nrepl-eval -p 7889 "(sum-evens [])"
    # => ArityException (reduce needs an init value)

The agent discovers that ``reduce`` without an initial value fails on
an empty collection. Without the REPL, this bug would have been
written to a file and discovered much later. With the REPL, the agent
fixes it immediately:

.. code-block:: bash

    clj-nrepl-eval -p 7889 "(defn sum-evens [nums] (->> nums (filter even?) (reduce + 0)))"
    clj-nrepl-eval -p 7889 "(sum-evens [])"
    # => 0

This define-test-fix-save loop is the core discipline. When you see
your agent skipping REPL validation and writing code directly to
files, that is a sign something needs to change in your workflow.

Exploration Before Use
^^^^^^^^^^^^^^^^^^^^^^^

Agents frequently hallucinate function signatures, argument orders,
or behavior -- especially for less common libraries. Instructing the
agent to explore unfamiliar functions before using them eliminates
this class of error cheaply. A call to ``clojure.repl/doc`` or
``clojure.repl/source`` takes milliseconds and replaces a guess with
a fact.

The ``clojure.repl`` namespace provides the tools for this:
``doc``, ``dir``, ``apropos``, ``find-doc``, and ``source``. An
agent that uses these freely before writing code will produce
significantly fewer errors than one that relies on what it remembers
from training.

Codebase Awareness
^^^^^^^^^^^^^^^^^^

A second category of hallucination comes from the agent ignoring the
conventions of your codebase. It might use ``camelCase`` in a
``kebab-case`` project, or duplicate a utility function that already
exists in another namespace.

The REPL helps here too. The agent can ``require`` a namespace, call
``clojure.repl/dir`` on it, and immediately see what functions
already exist. It can call existing functions to understand their
behavior before deciding whether to modify or extend them.


The Paren Edit Death Loop
--------------------------

LLM agents frequently produce mismatched delimiters when editing
Clojure code. A missing closing parenthesis triggers a parse error,
the agent attempts to fix it, the fix introduces a new error, and the
cycle repeats. Bruce Hauman calls this the "Paren Edit Death Loop."

The ``clj-paren-repair`` tool breaks this cycle. It uses `parinfer
<https://shaunlebron.github.io/parinfer/>`_ to automatically repair
delimiters based on indentation:

.. code-block:: bash

    clj-paren-repair path/to/file.clj

Having the agent run ``clj-paren-repair`` on delimiter errors instead
of attempting manual fixes is one of the places where tooling makes a
dramatic difference in agent effectiveness.


Passive Prompting Through the REPL
------------------------------------

A useful technique for keeping the agent informed is printing
docstrings when the dev namespace loads. As shown in the Reload
section above:

.. code-block:: clojure

    (println (repl/doc reload/reload))

Every time the agent reloads the dev namespace, it sees the
documentation in the REPL output. This is a form of passive prompting
-- the agent is reminded of available tools without you having to
repeat yourself. You can extend this pattern to any function or
library the agent should be aware of.


Best Practices
--------------

Keep the REPL Process Running
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Restarting the Clojure process is expensive. It requires reloading
all dependencies and re-initializing state. More importantly, when
the nREPL is down, the agent loses its connection to the running
system and falls back to writing code without validation. Treat the
nREPL process as infrastructure. Keep it running for the duration of
your development session and use ``clj-reload`` or
``clojure.tools.namespace`` to reload changed code.

Test Fixtures
^^^^^^^^^^^^^^

Test namespaces in Clojure often use fixtures to bind dynamic
variables like ``*db*`` or ``*config*``. Agents that do not know
about fixtures will call test functions directly, bypassing fixture
setup and producing confusing unbound variable errors.

The agent needs to use ``clojure.test/run-test-var`` or
``clojure.test/run-tests`` rather than calling test functions
directly:

.. code-block:: bash

    # Correct -- runs with fixtures
    clj-nrepl-eval -p 7889 "(clojure.test/run-test-var #'myapp.test/my-test)"

    # Incorrect -- bypasses fixtures
    clj-nrepl-eval -p 7889 "(myapp.test/my-test)"

This is a common source of confusion for agents and worth documenting
explicitly in whatever instructions you provide to the agent.

Pipeline Debugging
^^^^^^^^^^^^^^^^^^^

When an agent produces a threading pipeline that gives wrong results,
having it evaluate each step individually is an effective debugging
technique. Defining intermediate values with ``def`` makes it easy to
inspect the data at each stage:

.. code-block:: bash

    clj-nrepl-eval -p 7889 "(def raw-data (fetch-data))"
    clj-nrepl-eval -p 7889 "(def filtered (filter valid? raw-data))"
    clj-nrepl-eval -p 7889 "(count filtered)"
    clj-nrepl-eval -p 7889 "(first filtered)"
