---
description: >-
  This page has all the information you need to get your development environment
  set up and ready to hack on Thunderbird.
---

# Getting Started

## Build Prerequisites
Before you can build Thunderbird, please follow your platform's build prerequisites page:

* [Windows Build Prerequisites](building-thunderbird/windows-build-prerequisites.md)
* [Linux Build Prerequisites](building-thunderbird/linux-build-prerequisites.md)
* [macOS Build Prerequisites](building-thunderbird/macos-build-prerequisites.md)

## General Information

### Mercurial Version Control

Mozilla uses the [Mercurial version control](https://www.mercurial-scm.org/) software to propose, review, incorporate, and log changes to its code. In order to contribute to Thunderbird, you will need to be able to use this software.

Information for how to install Mercurial is available [via the download page on their wiki](https://www.mercurial-scm.org/wiki/Download).

### Mozilla Code Base

#### mozilla-central vs. comm-central

The latest Mozilla source code comes from Mozilla's `mozilla-central` Mercurial code repository, often called `source/` but it can be named anything you like. The latest Thunderbird source code comes from Mozilla's `comm-central` Mercurial code repository and needs to be placed **inside** the Mozilla source code, in a directory that must be named `comm/`.

Mozilla-central will build Firefox without the comm-central repo present and a few options set (detailed on the [Building Thunderbird](building-thunderbird/) page).

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
