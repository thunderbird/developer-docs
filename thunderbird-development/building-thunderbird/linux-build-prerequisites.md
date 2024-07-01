---
description: >-
  This page has all the information you need to get your GNU/Linux development
  environment set up and ready to hack on Thunderbird.
---

# Hardware Requirements

## 64-bit version

You will need to be running a 64-bit version of Linux in order to build Thunderbird. You can check which version you're running by typing this command in your terminal:

```
uname -m
```

if this command returns `x86_64` you can proceed.

## 30 GB of free space

The Thunderbird build can use 30-40GB of disk space to complete depending on your operating system.

{% hint style="warning" %}
Note that while it's not technically required to have an internet connection to build, the default setup has `--enable-bootstrap` so that the toolchains download automatically.
{% endhint %}

# Build Environment

## Python

You’ll need `Python 3.8` or later installed.

You can check with `python3 --version` to see if you have it already. If not, you can install it with your distribution’s package manager. Make sure your system is up to date!

You will also need `python3-distutils` and `python3-pip` installed from your distribution's package manager.

## Mercurial

As noted in the [Getting Started page](../getting-started#mercurial-version-control), both `mozilla-central` and `comm-central` are repositories using the Mercurial version control system. This means you will need to install Mercurial.
Here are the quick commands to use for common Linux based operating systems but for a more complete list of instructions (if neither of these works for your use case), please see [Mercurial's download page on their wiki](https://www.mercurial-scm.org/wiki/Download).

### Ubuntu/Debian
```
sudo apt install mercurial
```

### Fedora
```
sudo dnf install mercurial
```

# Getting the Code

Once you have Mercurial installed, you are ready to grab the source code. There are a couple of different methods to do this.

{% hint style="warning" %}
Mozilla-central will build Firefox without the comm-central repo present and a few options set. Mozilla-central is the Firefox codebase and comm-central features the additions that turn Firefox into Thunderbird.
{% endhint %}

## Scripted

The [`bootstrap.py`](https://hg.mozilla.org/comm-central/raw-file/tip/python/rocboot/bin/bootstrap.py) script will grab the two source repos you need, run `./mach bootstrap` for you, and sets up a necessary `mozconfig` file. Download this file to the directory where you would like your source code folder to live, either by clicking the link and moving the file to the appropriate location or using `wget`. Then make it executable and run it.

```
mkdir tb-build && cd tb-build
wget https://hg.mozilla.org/comm-central/raw-file/tip/python/rocboot/bin/bootstrap.py
chmod +x bootstrap.py
./bootstrap.py
```

This will create a `mozilla-unified` directory with both a `mozconfig` and a `comm/` folder inside. The `mozconfig` file is setup to build Thunderbird and you can verify this with `cat mozconfig`; the `--enable-project` parameter should be `comm/mail`:

```
ac_add_options --enable-project=comm/mail
```

## Manually

If you would rather manually gather the source code, perform the bootstrap, and create your `mozconfig` file, then follow these steps.

### Checkout the Source Code
Get the latest Mozilla source code from Mozilla's `mozilla-central` Mercurial code repository, and check it out into a local directory `source` (or however you want to call it). Then, get the latest Thunderbird source code from Mozilla's `comm-central` Mercurial code repository. It needs to be placed **inside** the Mozilla source code, in a directory named `comm/`:

```
hg clone https://hg.mozilla.org/mozilla-central source/
cd source/
hg clone https://hg.mozilla.org/comm-central comm/
```

### Create `mozconfig` file

This step will need to be performed if you manually checked out the code and performed the bootstrap, and it will covered in the next section you follow, [Building Thunderbird](./#build-configuration).

## Mach Bootstrap
In the `source` directory run the following command to get additional dependencies needed to install Thunderbird:

```
./mach bootstrap
```

You will be presented with the following options:

```
Please choose the version of Firefox you want to build:
  1. Firefox for Desktop Artifact Mode
  2. Firefox for Desktop
  3. GeckoView/Firefox for Android Artifact Mode
  4. GeckoView/Firefox for Android
```

Please choose option 2 to proceed with a successful build.

This action should install all the remaining libraries and dependencies necessary to build Thunderbird locally.

## Missing libraries

It could happen that some libraries will not be installed by the `bootstrap` command, specifically those related to the `Rust` programming language. Check whether these packages are available in your system by running these commands in your terminal:

* `which rustc`
* `which cargo`

If one or both commands return an empty output, you need to install them manually:

* Install Rust and cargo (the Rust package manager): `curl https://sh.rustup.rs -sSf | sh`
* Install cbindgen (tool that generates C bindings from Rust code): `cargo install cbindgen`

{% hint style="info" %}
If you get a `command not found` error while running `cargo`, but the command `which cargo` returns the location of the that package, it means you need to update your `PATH` inside your `.bashrc` file to include the `cargo` location:

```
export PATH=$HOME/.cargo/bin:$PATH
```
{% endhint %}

{% hint style="warning" %}
If you still are unable to find rustc and cargo via the ˋwhichˋ command after installing them, you may need to restart your session (log out and back into your user account, or restart your computer) to be able to see them.
{% endhint %}

# You're all set

Go back to the [Building Thunderbird](./#build-configuration) page and continue following the guide.

{% content-ref url="./" %}
[.](./)
{% endcontent-ref %}
