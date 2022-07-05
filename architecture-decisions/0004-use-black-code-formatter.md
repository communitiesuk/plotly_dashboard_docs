# 4. Use Black as a code formatter

Date: 2022-07-05

## Status

Accepted

## Context

- Black is an opinionated, automated code formatter for Python.
- Black produces a consistent code style, making code appear as though it's been written by one person.
- Consistent code is easier to read.
- By being opinionated, the team has fewer discussions related to personal code style preferences.
- Black is fast to run and automatically makes any changes required.
- It is [supported by VS Code][vs_code_formatting].
- We've been using Black on our projects since November 2021 and found it useful. 

[vs_code_formatting]: https://code.visualstudio.com/docs/python/editing#_formatting 

## Decision

- We will use Black code formatter on all Python code projects.

## Consequences and mitigations

None