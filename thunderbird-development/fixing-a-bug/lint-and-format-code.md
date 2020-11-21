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

You can run all the various linters in the tree using the `mach lint` command. Simply pass in the directory or file you wish to lint (defaults to current working directory):
```
./mach lint path/to/files
```
If not you're familiar with using `mach lint` on Firefox code, see the Firefox [Linting](https://firefox-source-docs.mozilla.org/code-quality/lint/index.html) documentation to get started.

## Configuring Mozlint for Thunderbird

By default, `mach lint` is configured to check Firefox code. Thunderbird has its own set of configurations. To use them, add the hidden option `--config-path`. **`--config-path` is always relative to the mozilla source directory, not your current directory.**

`$ ./mach lint --config-path=comm/tools/lint path/to/file.js`

Because `--config-path` is always relative to the source root, you can set up an alias in `~/.mozbuild/machrc` so you don't have to remember the extra option. On Windows, you should find `machrc` at `C:\Users\<you>\.mozbuild\machrc`.

Add these lines your`machrc`file. 
```text
[alias]
commlint = lint --config-path=comm/tools/lint
```

Now `mach commlint` is the same as running `mach --lint --config-path=comm/tools/lint`.  See [mach settings](https://firefox-source-docs.mozilla.org/mach/settings.html) for more details.

### Suite code

The Mozlint configuration files in `comm/tools/lint` are written so that they can be shared with the Seamonkey project. Thunderbird developers may want to set `MOZLINT_NO_SUITE=1` in their environment so `mach lint` will not check `comm/suite/` or `comm/editor`. Taskcluster will also set `MOZLINT_NO_SUITE` when running lint checks.

## Taskcluster

Several mozlint-based checks run automatically on Taskcluster, and more are being added. The Firefox [Linting](https://firefox-source-docs.mozilla.org/code-quality/lint/index.html) documentation is a valuable resource if you're seeing errors on your try builds. If you are not sure how to run a check locally, the mach commands run by Taskcluster are in `comm/taskcluster/ci/source-test/mozlint.yml`.

