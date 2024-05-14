---
description: How to build and run Thunderbird.
---

# Building Thunderbird

## Hardware Requirements

* At least **4 GB of RAM**. 8 GB or more is recommended. While you can build Thunderbird on older hardware it can take quite a bit of time to compile on slower machines with less RAM.
* 30 GB of free space. The Thunderbird build can use up to 30-40GB of disk space to complete depending on your operating system.
* Good internet connection for the initial source download.

## Build Prerequisites

Depending on your Operating System you will need to carry out a different process to prepare your machine. So first complete the instructions for your OS and then continue following these build instructions.

* [Windows Build Prerequisites](windows-build-prerequisites.md)
* [Linux Build Prerequisites](linux-build-prerequisites.md)
* [macOS Build Prerequisites](macos-build-prerequisites.md)

## Build Configuration

To build Thunderbird, you need a file named `mozconfig` in the root directory of the mozilla-central checkout that contains the option `comm/mail` enabled. If you do not already have this file, then you can create it with this line by doing this in the `source/` directory:

```
echo 'ac_add_options --enable-project=comm/mail' > mozconfig
```

**If you omit this line, the build system will build Firefox instead**. Other build configuration options can be added to this file, although it's **strongly recommended** that you only use options that you fully understand. For example, to create a debug build instead of a release build, that file would also contain the line:

```
echo 'ac_add_options --enable-debug' >> mozconfig
```

_Each of these ac\_add\_options entries needs to be on its own line._

For more info on configuration options, see the page [Configuring build options](https://firefox-source-docs.mozilla.org/setup/configuring\_build\_options.html#configuring-build-options). Note that if you use a MOZ\_OBJDIR it cannot be a sibling folder to your source directory. Use an absolute path to be sure!

## Building

{% hint style="warning" %}
Before you start, make sure that the version you checked out is not busted. For `hg` tip, you should see green Bs on [https://treeherder.mozilla.org/#/jobs?repo=comm-central](https://treeherder.mozilla.org/#/jobs?repo=comm-central)
{% endhint %}

After you have met the [build prerequisites](./#build-prerequisites) for your OS, the build is started in the `source` directory with:

```
./mach build
```

mach is our command-line tool to streamline common developer tasks. See the [mach](https://firefox-source-docs.mozilla.org/mach/) article for more.

Building can take a significant amount of time, depending on your system, OS, and chosen build options. Linux builds on a fast box may take under _15 minutes_, but Windows builds on a slow box may take _several hours_.

### Make Your Build Faster

Follow this guide to rely on [sccache](https://firefox-source-docs.mozilla.org/setup/configuring\_build\_options.html?highlight=sccache#sccache) and other [Tips for making builds faster](https://firefox-source-docs.mozilla.org/build/buildsystem/slow.html#why-the-build-system-is-slow).

## Running Thunderbird

To run your build, you can use:

```
./mach run
```

There are various command line parameters you can add, e.g. to specify a profile.

Various temporary files, libraries, and the Thunderbird executable will be found in your object directory (under `comm-central/`), which is prefixed with `obj-`. The exact name depends on your system and OS. For example, a Mac user may get an object directory name of `obj-x86_64-apple-darwin10.7.3/`.

The Thunderbird executable in particular, and its dependencies are located under the `dist/bin` folder under the object directory. To run the executable from your `comm-central` working directory:

* Windows: `obj-.../dist/bin/thunderbird.exe`
* Linux: `obj-.../dist/bin/thunderbird`
* macOS: `obj-.../dist/Daily.app/Contents/MacOS/thunderbird`

## Update and Build Again

To pull down the latest changes, in the mozilla directory run the following commands:

```
hg pull -u
cd comm
hg pull -u
cd ..
```

or to do it via one command:

```
hg pull -u; (cd comm; hg pull -u)
```

Then just run the `./mach build` command detailed in the [Building](./#building) instructions above. This will only recompile files that changed, but it may still take a long time.

## Rebuilding

To build after changes you can simply run:

```
./mach build
```

### Rebuilding Specific Parts

If you have made many changes, but only want to rebuild specific parts, you may run the following commands.

#### C or C++ Files:

```
./mach build binaries
```

#### JavaScript or XUL Files (Windows Only):

```
./mach build path/to/dir
```

{% hint style="info" %}
Replace `path/to/dir` with the directory with the files changed.

This is the tricky bit since you need to specify the directory that installs the files, which may be a parent directory of the changed file's directory. For example, to just rebuild the Lightning calendar extension:

```
./mach build comm/calendar/lightning
```
{% endhint %}
