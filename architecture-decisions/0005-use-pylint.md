# 4. Use Pylint

Date: 2022-07-05

## Status

Accepted

## Context

- `pylint` is a static code analyser which identifies code quality issues
- It includes a [number of checkers][pylint-checkers], each of which identifies a specific issue
- Examples of code quality checks performed include
  - Missing docstring on public method
  - Line too long
  - Unused variable
- Automatic identification of quality issues results in quicker feedback to developers, and more time can be spent by code reviewers on issues which are harder for an automated tool to identify.
- Individual checkers can be configured or disabled if they aren't helping.
- We've been using `pylint` on our projects since November 2021 and found it useful.

[pylint-checkers]: https://pylint.pycqa.org/en/latest/user_guide/checkers/features.html

## Decision

- We will use `pylint` on all Python code projects.

## Consequences and mitigations

- `pylint` has been slow to run within the DAP
  - As of writing it takes 1-2 minutes to check all our code.
  - We've performed some investigations, but have not identified any fixes.
  - We will accept the slowness for now.