---
description: >-
  This page has all the information you need to get your macOS development environment
  set up and ready to hack on Thunderbird.
---

# macOS Build Prerequisites

This guide assumes you already followed the [Getting Started](../untitled.md) documentation and you already downloaded `mozilla-central` and `comm-central` source code.

## Install Xcode

`Xcode` is a prerequisite to build Firefox an you will need administrator permissions on your machine to install it.  You can verify that you have these permission in `System Preferences -> Users & Groups`.)

{% hint style="info" %}
`Xcode` is pretty large and it could take up to 1 hour to complete the download and installation process
{% endhint %}

## Install macOS SDK headers

After installing `Xcode` you will find the SDK header at this location

```text
cd /Library/Developer/CommandLineTools/SDKs/
```

Launch the `MacOSX{your-version}.sdk` (eg. `MacOSX10.14.sdk`) by double clicking on it and follow the installation instructions.

## Bootstrap your system

Access the location where you downloaded the `mozilla-central` source code, most likely `source/` and trigger this command:

```text
./mach bootstrap
```

This action will install all the libraries and dependencies necessary to build Thunderbird locally.

### Missing libraries

It could happen that some libraries will not be installed by the `bootstrap` command, specifically `Rust` and `Go`. Check if these packages are available in your system by running these commands in your terminal:

* `which rustc`
* `which cargo`

If one or both commands return and empty output, you need to install them manually. We recommend using [HomeBrew](https://brew.sh/) to download and install these packages in your system. After that, follow these steps:

* Install Rust: `brew install rustc`
* Install C bindings: `cargo install cbindgen`

{% hint style="info" %}
If you get a `command not found` error while running `cargo`, but the command `which cargo` returns the location of the that package, it means you need to update your `PATH` inside your `.bashrc` file to include the `cargo` location:

```text
export PATH=$HOME/.cargo/bin:$PATH
```
{% endhint %}

## You're all set

Got back to the [Building Thunderbird](README.md) page and continue following the guide.