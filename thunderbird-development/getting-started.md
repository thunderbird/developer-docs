---
description: >-
  This page has all the information you need to get your development environment
  set up and ready to hack on Thunderbird.
---

# Getting Started

## What You Need to Know

### Mozilla Code Base

Thunderbird is built on the Mozilla platform, the same base that Firefox is built from. As such the two projects share a lot of code and much of the documentation for one will apply, in many ways, to the other. If at any point you are looking for answers that you can't find here, try looking at [Mozilla's Developer site called: **MDN**](https://developer.mozilla.org).

### Mercurial Version Control

Mozilla uses the [Mercurial version control](https://www.mercurial-scm.org) software to propose, review, incorporate, and log changes to its code. In order to contribute to Thunderbird, you will need to be able to use this software.

Information for how to install Mercurial is available [via the download page on their wiki](https://www.mercurial-scm.org/wiki/Download).

## Get the Source

{% hint style="warning" %}
The source code requires 3.6GB of free space or more and additionally 30GB or more for default build.
{% endhint %}

{% hint style="danger" %}
**For Windows Users: **If you are using Windows, you will want to follow instructions on the Windows Build Prerequisities page before getting the source and building Thunderbird.
{% endhint %}

{% content-ref url="prerequisites-build/windows-build-prerequisites.md" %}
[windows-build-prerequisites.md](prerequisites-build/windows-build-prerequisites.md)
{% endcontent-ref %}

Get the latest Mozilla source code from Mozilla's `mozilla-central` Mercurial code repository, and check it out into a local directory `source` (or however you want to call it). Then, get the latest Thunderbird source code from Mozilla's `comm-central` Mercurial code repository. It now needs to be placed **inside** the Mozilla source code, in a directory named `comm/`:

```
hg clone https://hg.mozilla.org/mozilla-central source/
cd source/
hg clone https://hg.mozilla.org/comm-central comm/
```

### mozilla-central vs. comm-central

Mozilla-central will build Firefox without the comm-central repo present and a few options set (detailed on the [Building Thunderbird](building-thunderbird/) page). Mozilla-central is the Firefox codebase and comm-central features the additions that turn Firefox into Thunderbird.

## What's Next

Let's build the latest Thunderbird:

{% content-ref url="building-thunderbird/" %}
[building-thunderbird](building-thunderbird/)
{% endcontent-ref %}
