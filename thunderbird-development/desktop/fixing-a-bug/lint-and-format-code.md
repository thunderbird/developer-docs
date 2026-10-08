---
description: How to lint and format code.
---

# Lint and Format Code

Thunderbird's source code is linted and formatted using automated tools, which provides several benefits that include:

* ensuring a consistent formatting style across the code base
* preventing formatting issues from taking up developer time in code review
* catching problems that might otherwise go unnoticed
* making it easier for developers to write well-formatted code

Mozlint is a library that standardizes linter configuration and provides an interface for running all linters at once. It is run via mach and is run automatically by Taskcluster.

## Running Linters Locally

You can run all the various linters in the tree using the `mach commlint` command. Simply pass in the directory or file you wish to lint (defaults to current working directory):

```
../mach commlint path/to/files
```

It works the same as regular `mach lint`, however it has Thunderbird configs pre-applied. If you're not familiar with using `mach lint` on Firefox code, see the Firefox [Linting](https://firefox-source-docs.mozilla.org/code-quality/lint/index.html) documentation to get started.

## Configuring Mozlint for Thunderbird

### Suite code

The Mozlint configuration files in `comm/tools/lint` are written so that they can be shared with the Seamonkey project. Thunderbird developers may want to set `MOZLINT_NO_SUITE=1` in their environment so `mach commlint` will not check `comm/suite/`. Taskcluster will also set `MOZLINT_NO_SUITE` when running lint checks.

## Taskcluster

Several mozlint-based checks run automatically on Taskcluster, and more are being added. The Firefox [Linting](https://firefox-source-docs.mozilla.org/code-quality/lint/index.html) documentation is a valuable resource if you're seeing errors on your try builds. If you are not sure how to run a check locally, the mach commands run by Taskcluster are in `comm/taskcluster/ci/source-test/mozlint.yml`.
