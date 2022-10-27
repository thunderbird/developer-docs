---
description: >-
  This page has all the information you need to get your GNU/Linux development
  environment set up and ready to hack on Thunderbird.
---

# Linux Build Prerequisites

This guide assumes you already followed the [Getting Started](../getting-started.md) documentation and you already downloaded `mozilla-central` and `comm-central` source code.

## 64-bit version

You need a 64 bit version of Linux in order to build Thunderbird. You can check which version you're running by typing this command in your terminal:

```text
uname -m
```

if this command returns `x86_64` you can proceed.

## Python

You’ll need `Python 3.6` installed.

You can check with `python --version` to see if you have it already. If not, you can install it with your distribution’s package manager. Make sure your system is up to date!

## Bootstrap your system

Access the location where you downloaded the `mozilla-central` source code, most likely `source/` and trigger this command:

```text
./mach bootstrap
```

You will be asked to choose from the following list of options

```text
Please choose the version of Firefox you want to build:
  1. Firefox for Desktop Artifact Mode
  2. Firefox for Desktop
  3. GeckoView/Firefox for Android Artifact Mode
  4. GeckoView/Firefox for Android
```

Please choose option 2 to proceed with a successful build.

This action will install all the libraries and dependencies necessary to build Thunderbird locally.

### Missing libraries

It could happen that some libraries will not be installed by the `bootstrap` command, specifically those related to the `Rust` programming language. Check whether these packages are available in your system by running these commands in your terminal:

* `which rustc`
* `which cargo`

If one or both commands return an empty output, you need to install them manually:

* Install Rust and cargo \(the Rust package manager\): `curl https://sh.rustup.rs -sSf | sh`
* Install cbindgen \(tool that generates C bindings from Rust code\): `cargo install cbindgen`

{% hint style="info" %}
If you get a `command not found` error while running `cargo`, but the command `which cargo` returns the location of the that package, it means you need to update your `PATH` inside your `.bashrc` file to include the `cargo` location:

```text
export PATH=$HOME/.cargo/bin:$PATH
```
{% endhint %}

{% hint style="warning" %}
If you still are unable to find rustc and cargo via the ˋwhichˋ command after installing them, you may need to restart your session \(log out and back into your user account, or restart your computer\) to be able to see them.
{% endhint %}

## You're all set

Go back to the [Prerequisites for the build](./#build-configuration) page and continue following the guide.

{% page-ref page="./" %}

