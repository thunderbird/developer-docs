---
description: Prepare build of Thunderbird.
---

# Prerequisites for the build

## Hardware Requirements

* At least **4 GB of RAM**. 8 GB or more is recommended. While you can build Thunderbird on older hardware it can take quite a bit of time to compile on slower machines with less RAM.
* Good internet connection for the initial source download.

## 30 GB of free space

The Thunderbird build will take up to 30 GB of disk space in order to complete. Be sure to have enough free space and a fast internet connection to avoid interruptions.

## Build Prerequisites

Depending on your Operating System you will need to carry out a different process to prepare your machine. So firstly complete the instructions for your OS and then continue following the setup with the [Build Configuration](./#build-configuration).

* [Windows Build Prerequisites](windows-build-prerequisites.md)
* [Linux Build Prerequisites](linux-build-prerequisites.md)
* [macOS Build Prerequisites](macos-build-prerequisites.md)

## Build Configuration

To build Thunderbird, you need to create a file named `mozconfig` to the root directory of the mozilla-central checkout that contains the option `comm/mail` enabled. You can create a file with this line by doing this in the `source/` directory:

```
echo 'ac_add_options --enable-application=comm/mail' > mozconfig
```

**If you omit this line, the build system will build Firefox instead**. Other build configuration options can be added to this file, although it's **strongly recommended** that you only use options that you fully understand. For example, to create a debug build instead of a release build, that file would also contain the line:

```
echo 'ac_add_options --enable-debug' >> mozconfig
```

_Each of these ac\_add\_options entries needs to be on its own line._

For more on configuration options, see the page [Configuring build options](https://firefox-source-docs.mozilla.org/setup/configuring_build_options.html). Note that if you use an MOZ\_OBJDIR it cannot be a sibling folder to your source directory. Use an absolute path to be sure!

## You're all set

Go ahead to the [Building Thunderbird](thunderbird-development/building-thunderbird/README.md) page and continue following the guide.

{% page-ref page="./" %}