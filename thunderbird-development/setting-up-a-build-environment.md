---
description: >-
  This page has all the information you need to get your development environment
  set up and ready to hack on Thunderbird.
---

# Setting Up A Build Environment

## Build Prerequisites
Before you can build Thunderbird, please follow your platform's build prerequisites page:

* [Windows Build Prerequisites](building-thunderbird/windows-build-prerequisites.md)
* [Linux Build Prerequisites](building-thunderbird/linux-build-prerequisites.md)
* [macOS Build Prerequisites](building-thunderbird/macos-build-prerequisites.md)

## General Information

### Git Version Control

Mozilla uses the [Git version control](https://git-scm.com) software to propose, review, incorporate, and log changes to its code. In order to contribute to Thunderbird, you will need to be able to use this software.

Information for how to install Git is available [via the download page on their wiki](https://git-scm.com/install).

### Source Code

#### Firefox and Thunderbird

The latest Firefox and Thunderbird source code is available from the main branches of the [firefox](https://github.com/mozilla-firefox/firefox) and [thunderbird-desktop](https://github.com/thunderbird/thunderbird-desktop) Git repositories, respectively.

When building locally:
* The root of the Firefox source is often renamed to `source/` but it can be named anything you like.
* The root of the Thunderbird source needs to be placed **inside** the Firefox source code, in a directory that must be named `comm/`.

More detailed can be found on the [Building Thunderbird](building-thunderbird/) page.

#### Git and Mercurial

All Firefox and Thunderbird source code development today is performed with Git. However, Mercurial is still used behind the scenes for Continuous Integration.

Prior to moving to Git, the source code for Firefox and Thunderbird used Mercurial for version control. The Firefox and Thunderbird source code that now lives in the `main` Git branches was historically stored in repositories named mozilla-central and comm-central.

The following table shows the mapping of git branches to mercurial repositories. Note that all of the git branches exist in the [firefox](https://github.com/mozilla-firefox/firefox) and [thunderbird-desktop](https://github.com/thunderbird/thunderbird-desktop) repositories.

| Git Branch | Mercurial Repository |
|------------|----------------------|
| main       | mozilla-central      |
| beta       | mozilla-beta         |
| release    | mozilla-release      |
| esr140     | mozilla-esr140       |
| main       | comm-central         |
| beta       | comm-beta            |
| release    | comm-release         |
| esr140     | comm-esr140          |

For the time being, all Continuous Integration is run against Mercurial, until that tooling can be migrated to Git branches. You'll notice this on [Treeherder](https://treeherder.mozilla.org/jobs?repo=comm-central).

When code lands in the Firefox and Thunderbird Git repositories, it is mirrored to the corresponding Mercurial repository in order to run CI against it.

### Additional Documentation

Thunderbird is built on the Mozilla platform, the same base that Firefox is built from. As such the two projects share a lot of code and much of the documentation for one will apply, in many ways, to the other. If at any point you are looking for answers that you can't find here, here are some additional useful resources:

* [Thunderbird Source Tree Documentation](https://source-docs.thunderbird.net)
* [Firefox Source Tree Documentation](https://firefox-source-docs.mozilla.org/)
* [Mozilla's Developer site called: **MDN**](https://developer.mozilla.org)

## What's Next

If you have already gone through the relevant build prerequisite steps, then let's build the latest Thunderbird:

{% content-ref url="building-thunderbird/" %}
[building-thunderbird](building-thunderbird/)
{% endcontent-ref %}
