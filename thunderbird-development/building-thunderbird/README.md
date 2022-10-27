---
description: How to build and run Thunderbird.
---

# Building Thunderbird

## Prerequisites

Ensure that your [Prerequisites for the build](thunderbird-development/prerequisites-build/README.md) are met.

You should have done the following:

* Installed tools for ./mach bootstrap depending on your OS
* Finished ./mach bootstrap in `Firefox for Desktop` mode
* The [Build Configuration](thunderbird-development/prerequisites-build/README.md) in a seperate `source/mozconfig` file so that Thunderbird is build and not Firefox

## Building

{% hint style="warning" %}
Before you start, make sure that the version you checked out is not busted. For `hg` tip, you should see green Bs on [https://treeherder.mozilla.org/#/jobs?repo=comm-central](https://treeherder.mozilla.org/#/jobs?repo=comm-central)
{% endhint %}

The build is started in the `source` directory with:

```
./mach build
```

mach is our command-line tool to streamline common developer tasks. See the [mach](https://firefox-source-docs.mozilla.org/mach/index.html) article for more.

Building can take a significant amount of time, depending on your system, OS, and chosen build options. Linux builds on a fast box may take under _15 minutes_, but Windows builds on a slow box may take _several hours_.

### Make Your Build Faster

Follow this guide to rely on [sccache](https://firefox-source-docs.mozilla.org/setup/configuring_build_options.html#sccache) and other [Tips for making builds faster](https://firefox-source-docs.mozilla.org/build/buildsystem/slow.html).

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
