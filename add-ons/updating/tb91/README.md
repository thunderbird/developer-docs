---
description: Required steps to update add-ons for Thunderbird 91.
---

# Update for Thunderbird 91

Support for legacy extensions was removed from Thunderbird Beta version 74, released in February 2020. Since Thunderbird 78 only modern [MailExtensions](https://developer.thunderbird.net/add-ons/about-add-ons) are supported. This section only covers the required update steps for add-ons which are already compatible with Thunderbird 78 and need to be made compatible with Thunderbird 91.

## Changes in WebExtension APIs

Our WebExtension APIs are meant to be stable, but we are adding new features and since this is a very young technology this might also require backward incompatible changes. All WebExtension API changes are listed in our API documentation:

* [Thunderbird 81](https://webextension-api.thunderbird.net/en/latest/changes/beta81.html)
* [Thunderbird 82](https://webextension-api.thunderbird.net/en/latest/changes/beta82.html)
* [Thunderbird 83](https://webextension-api.thunderbird.net/en/latest/changes/beta83.html)
* [Thunderbird 84](https://webextension-api.thunderbird.net/en/latest/changes/beta84.html)
* [Thunderbird 85](https://webextension-api.thunderbird.net/en/latest/changes/beta85.html)
* [Thunderbird 86](https://webextension-api.thunderbird.net/en/latest/changes/beta86.html)
* [Thunderbird 87](https://webextension-api.thunderbird.net/en/latest/changes/beta87.html)
* [Thunderbird 88](https://webextension-api.thunderbird.net/en/latest/changes/beta88.html)
* [Thunderbird 89](https://webextension-api.thunderbird.net/en/latest/changes/beta89.html)

## Changes in Thunderbird Core

MailExtensions can still run legacy code inside [Experiments](../../mailextensions/#experiment-apis). Such legacy code has to be adjusted to changes made in Thunderbird Core. All known changes are listed in the following document:

* [Changes in Thunderbird 79-91](changes.md)

If you have encountered a change which is not yet listed there, please [contact us](../../community.md), so we can update the list.

## Changes in Shared Experiments

If you are using any of the shared Experiments, you probably do not have to update them on your own. Check if an updated version is already available:

* [Shared Experiments on DTN](../../mailextensions/#sharing-experiment-apis)
* [Wrapper Experiments available in the ADS repository](https://github.com/thundernest/addon-developer-support/tree/master/wrapper-apis)

