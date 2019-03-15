---
description: >-
  This page has all the information you need to get your development environment
  set up and ready to hack on Thunderbird.
---

# Getting Started

## Hardware requirements

* At least **4 GB of RAM**. 8 GB or more is recommended. While you can build Thunderbird on older hardware it can take quite a bit of time to compile on slower machines with less RAM.
* Good internet connection for the initial source download.

## Build prerequisites

Depending on your Operating System you will need to carry out a different process to prepare your machine. So firstly complete the instructions for your OS and then continue following these build instructions.

* ["Windows Build Prerequisites"](the-basics/windows-build.md)
* ["GNU/Linux Build Prerequisites"](the-basics/linux-build.md)
* ["macOS Build Prerequisites"](the-basics/macos-build.md)

## Build configuration

To build Thunderbird, you need to create a file named `mozconfig` to the root directory of the mozilla-central checkout that contains the option `comm\mail` enabled.
You can create a file with this line by doing this in the `source/` directory:

```
echo 'ac_add_options --enable-application=comm/mail' > mozconfig
```

**If you omit this line, the build system will build Firefox instead**. Other build configuration options can be added to this file, although it's **strongly recommended** that you only use options that you fully understand. For example, to create a debug build instead of a release build, that file would also contain the line:

```
ac_add_options --enable-debug
```
_Each of these ac_add_options entries needs to be on its own line._

For more on configuration options, see the page ["Configuring build options"](https://developer.mozilla.org/en/Configuring_Build_Options). Note that if you use an MOZ_OBJDIR it cannot be a sibling folder to your source directory. Use an absolute path to be sure!

### Build the Lightning Calendar when building Thunderbird

Add the following line to your `mozconfig` file:

```
ac_add_options --enable-calendar
```

## Building

Before you start, make sure that the version you checked out is not busted. For hg tip, you should see green Bs on https://treeherder.mozilla.org/#/jobs?repo=comm-central

To start the build, cd into the `source` directory, and run:

```
./mach build
```

mach is our command-line tool to streamline common developer tasks. See the ["mach"](https://developer.mozilla.org/en-US/docs/Mozilla/Developer_guide/mach) article for more.

Building can take a significant amount of time, depending on your system, OS, and chosen build options. Linux builds on a fast box may take under _15 minutes_, but Windows builds on a slow box may take _several hours_.

### Make your build faster

Follow this guide to rely on cache and other ["Tips for making builds faster"](#).

## Running

To run your build, you can use

```
./mach run
```

There are various command line parameters you can add, e.g. to specify a profile.

Various temporary files, libraries, and the Thunderbird executable will be found in your object directory (under `comm-central/`), which is prefixed with **`obj-`**. The exact name depends on your system and OS. For example, a Mac user may get an object directory name of **`obj-x86_64-apple-darwin10.7.3/`**.

The Thunderbird executable in particular, and its dependencies are located under the `dist/bin` folder under the object directory. To run the executable from your `comm-central` working directory:

* Windows: `obj-.../dist/bin/thunderbird.exe`
* Linux: `obj-.../dist/bin/thunderbird`
* macOS: `obj-.../dist/Daily.app/Contents/MacOS/thunderbird`
