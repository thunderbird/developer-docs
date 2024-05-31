---
description: >-
  This page has all the information you need to get your macOS development
  environment set up and ready to hack on Thunderbird.
---

# Hardware Requirements

## 30 GB of free space

The Thunderbird build can use 30-40GB of disk space to complete depending on your operating system.

{% hint style="warning" %}
Note that while it's not technically required to have an internet connection to build, the default when building from `mozilla/comm-central` is that `--enable-bootstrap` is set so that the toolchains download automatically. If you do not have an active internet connection then
{% endhint %}

# Build Environment

## Python

You will need `python` (version 3.8 or later) and `pipx` (used to install packages from `pypi`). Both of these can be installed from homebrew. If you have not yet setup homebrew, please see [the homebrew installation instructions](https://brew.sh/).

```
brew install python pipx
```

{% hint style="warning" %}
Note that once homebrew is installed, the macOS SDK headers are installed already and can be found under `/Library/Developer/CommandLineTools/SDKs`. There should be no additional action required to install these SDK headers.
{% endhint %}

### Use pipx to install mozphab

MozPhab is the tool needed to interface with Mozilla's instance of Phabricator. This step is needed before the bootstrap step. Pipx is the tool that we will use to install MozPhab and then we will make sure the relevant `~/.local/bin` has been added to the PATH envirnoment variable.

```
pipx install MozPhab
pipx ensurepath 
```
## Mercurial

As noted in the [Getting Started page](../getting-started#mercurial-version-control), both `mozilla-central` and `comm-central` are version controlled with Mercurial. This means you will need to install Mercurial.
Here is a quick command to install it with mercurial but for a more complete list of instructions, please see [Mercurial's download page on their wiki](https://www.mercurial-scm.org/wiki/Download).

```
brew install mercurial
```
# Get the Source

Once you have Mercurial and MozPhab installed, you are ready to grab the source code. There are a couple of different methods to do this.

{% hint style="warning" %}
Mozilla-central will build Firefox without the comm-central repo present and a few options set. Mozilla-central is the Firefox codebase and comm-central features the additions that turn Firefox into Thunderbird.
{% endhint %}

## Scripted

We have created and host a script that will grab the two source repos you need, run `./mach bootstrap` for you, and sets up a necessary `mozconfig` file. This script is called [`bootstrap.py`](https://hg.mozilla.org/comm-central/raw-file/tip/python/rocboot/bin/bootstrap.py). Download this file to the directory where you would like your source code folder to live, either by clicking the link and moving the file to the appropriate location or using `wget`. Then we will make it executable and run it.

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

### Mach Bootstrap
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

It could happen that some libraries will not be installed by the `bootstrap` command, specifically `Rust` and `Go`. Check if these packages are available in your system by running these commands in your terminal:

* `which rustc`
* `which cargo`

If one or both commands return an empty output, you need to install them manually. We recommend using [HomeBrew](https://brew.sh/) to download and install these packages in your system. After that, follow these steps:

* Install Rust: `brew install rust`
* Install C bindings: `cargo install cbindgen`

{% hint style="info" %}
If you get a `command not found` error while running `cargo`, but the command `which cargo` returns the location of the that package, it means you need to update your `PATH` inside your `.bashrc` file to include the `cargo` location:

```text
export PATH=$HOME/.cargo/bin:$PATH
```
{% endhint %}

# You're all set

Got back to the [Building Thunderbird](./#build-configuration) page and continue following the guide:

{% page-ref page="./" %}

