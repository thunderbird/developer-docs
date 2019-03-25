---
description: >-
  This page has all the information you need to get your GNU/Linux development
  environment set up and ready to hack on Thunderbird.
---

# Linux Build Prerequisites

This guide assumes you already followed the [Getting Started](https://github.com/thundernest/developer-docs/tree/cf723ef593ec303339590f2096f17c8cfc64514d/the-basics/untitled.md) documentation and you already downloaded `mozilla-central` and `comm-central` source code.

## 64-bit version

You need a 64 bit version of Linux in order to build Thunderbird. You can check which version you're running by typing this command in your terminal:

```shell
uname -m
```

if this command returns `x86_64` you can proceed.

## Python 2.7

You’ll need `Python 2.7` installed, `Python 3.x` is currently not supported.

You can check with `python --version` to see if you have it already. If not, you can install it with your distribution’s package manager. Make sure your system is up to date!

## 30 GB of free space

The Thunderbird build will take up to 30 GB of disk space in order to complete. Be sure to have enough free space and a fast internet connection to avoid interruptions.

## Bootstrap your system

Access the location where you downloaded the `mozilla-central` source code, most likely `source/` and trigger this command:

```shell
./mach bootstrap
```

This action will install all the libraries and dependencies necessary to build Thunderbird locally.

### Missing libraries

It could happen that some libraries will not be installed by the `bootstrap` command, specifically `Rust` and `Go`. Check if these packages are available in your system by running these commands in your terminal:

* `which rustc`
* `which cargo`

If one or both commands return an empty output, you need to install them manually:

* Install Rust: `curl https://sh.rustup.rs -sSf | sh`
* Install C bindings: `cargo install cbindgen`

{% hint style="info" %}
If you get a `command not found` error while running `cargo`, but the command `which cargo` returns the location of the that package, it means you need to update your `PATH` inside your `.bashrc` file to include the `cargo` location:

```shell
export PATH=$HOME/.cargo/bin:$PATH
```
{% endhint %}

## You're all set

Got back to the [Building Thunderbird](./#build-configuration) page and continue following the guide.

{% page-ref page="./" %}

