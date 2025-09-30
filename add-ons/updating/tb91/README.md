---
description: Required steps to update add-ons for Thunderbird 91.
---

# Update for Thunderbird 91

Support for legacy extensions was removed from Thunderbird Beta version 74, released in February 2020. Since Thunderbird 78 only modern [MailExtensions](https://developer.thunderbird.net/add-ons/about-add-ons) are supported. This section only covers the required update steps for add-ons which are already compatible with Thunderbird 78 and need to be made compatible with Thunderbird 91.

## Changes in Thunderbird Core

MailExtensions can still run legacy code inside [Experiments](../../mailextensions/#experiment-apis). Such legacy code has to be adjusted to changes made in Thunderbird Core. All known changes are listed in the following document:

* [Changes in Thunderbird 79-91](changes.md)

If you have encountered a change which is not yet listed there, please [contact us](../../community.md), so we can update the list.

## Changes in Shared Experiments

If you are using any of the shared Experiments, you probably do not have to update them on your own. Check if an updated version is already available:

* [Shared Experiments on DTN](../../mailextensions/#sharing-experiment-apis)
