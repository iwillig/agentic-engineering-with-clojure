Common User Prompts
====================

LLM agents are very sensitive to the user prompts you give them. A lot
of the functionality that we see in the most advanced LLM agent
harnesses like planning can be achieved through careful user prompts.

Here are some examples of common User Prompts that we have found to be
useful.

Overview
--------

These prompts act as reusable guardrails for LLM agents, preventing common
failure modes like uncontrolled iteration or unauthorized changes while
enforcing proven workflows (TDD, review-before-code) that improve quality
and save time on repetitive engineering tasks.

Ask for Help
------------

Prevent the agent from spiraling on errors by forcing a stop and help request
after two failed attempts.

.. code-block:: text

    Stop after TWO failed attempts to fix an error. On the second failure,
    stop and ask for help. Include: the exact error message, the code that
    triggered it, what you tried, and why it failed.


Create a Suggestion
-------------------

Analyze a story and codebase to produce a detailed implementation plan before
writing any code.

.. code-block:: text

    Review story ID <STORY_ID>. Review the codebase at <FILE_OR_DIRECTORY>.
    Analyze the requirements and current implementation. Create a specific
    engineering suggestion: outline the approach, files to modify, and any
    risks. WAIT for my approval before making any code changes.


Generate Git Commit Message
---------------------------

This user prompt is helpful for generating Git commit messages. It
creates the commit message and saves it to a specific file for you to review.

.. code-block:: text

    Create a git commit message for the UNSTAGED/STAGED changes in the
    working directory. Explain WHY this change is needed (business reason,
    bug fix, feature). DO NOT describe what code changed - that is visible
    in the diff. First, review 5-10 recent commits to identify the team's
    commit message style (tense, format, length). Write the commit message
    to GIT-COMMIT.msg. DO NOT run git commit.

Review Story and Bug, Ask for Next Steps
----------------------------------------

Fetch a story from your product management tool, analyze the issue, and
present findings before taking action.

.. code-block:: text

    1. Fetch and read story <STORY_ID> completely
    2. Explore the relevant codebase to understand current implementation
    3. Analyze: Is this a bug fix or new feature? What is the expected
       behavior vs. current behavior?
    4. Create an implementation plan: specific files to change, approach,
       and estimated effort
    5. PRESENT this analysis to me and STOP. Wait for my direction on
       next steps before writing any code.

Red/Green Test Behavior
-----------------------

Follow strict test-driven development: write a failing test first, then
implement the minimal code to pass.

Following Simon Willison's `RED Green TDD
<https://simonwillison.net/guides/agentic-engineering-patterns/red-green-tdd/>`_

.. code-block:: text

    TDD Approach for <DESCRIBE_BEHAVIOR_HERE>:
    1. Review existing tests in <TEST_FOLDER> to understand patterns for
       naming, structure, and assertions
    2. Write a NEW test that defines the expected behavior. The test MUST
       fail initially (RED phase)
    3. Show me the failing test output
    4. Implement the minimal code change to make the test pass (GREEN phase)
    5. Run tests again to confirm they pass
    6. Briefly review for refactoring opportunities (REFACTOR phase)
