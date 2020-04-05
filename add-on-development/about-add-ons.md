---
description: What you need to know about making add-ons for Thunderbird.
---

# Overview

## What are Add-ons?

Add-ons include:

1. **extensions** that add functionality or change the way Thunderbird works
2. **themes** that change the way Thunderbird looks

You can check out many of the add-ons available for Thunderbird at [addons.thunderbird.net](https://addons.thunderbird.net).

## Extensions

### MailExtensions

"MailExtensions" are WebExtensions with additional features that are specific to Thunderbird. WebExtensions are the current type of extension used for Firefox and Chrome. MailExtensions access functionality through stable APIs, which makes them less likely to break or require updating when Thunderbird changes. \(Unlike older legacy extensions that had more direct access to Thunderbird's internal components and UI.\)

{% hint style="success" %}
**From Thunderbird 68 onward, new extensions should be built as MailExtensions to ensure future compatibility. To get you started, we have put together a tutorial:**
{% endhint %}

{% page-ref page="examples/hello-world-add-on.md" %}

You can find detailed information about the currently available APIs in the [MailExtensions API documentation](https://thunderbird-webextensions.readthedocs.io/) \([↗](https://thunderbird-webextensions.readthedocs.io/)\).

If you need an API that does not exist yet, please [tell us about it](https://bugzilla.mozilla.org/enter_bug.cgi?product=Thunderbird&component=General).

#### WebExtension Experiments

WebExtension Experiments are designed to allow for experimentation with new extension APIs, as a step towards adding those APIs to Thunderbird. They can be used in MailExtensions. Learn more about them in the [WebExtension Experiments documentation](https://thunderbird-webextensions.readthedocs.io/en/68/how-to/experiments.html).

Currently and for the foreseeable future \(years\) WebExtension Experiments can be used in extensions released to the general public. This is to help with the transition to MailExtensions and the development of additional APIs. However, as this transition progresses, the use of WebExtension Experiments in publicly released extensions will eventually be deprecated and then unsupported, as they currently are for Firefox.

### Legacy Extensions \(deprecated\)

The extensions that were used before MailExtensions are now referred to as "Legacy Extensions".

{% hint style="danger" %}
The current Thunderbird Beta does not support legacy extensions \(starting with version 74, released in February 2020\). Similarly, the next major \(non-beta\) release, Thunderbird 78, will not support legacy extensions. It is scheduled for release around June 2020.

Since Thunderbird 60 was released, there have been many major changes to Thunderbird’s core. **Legacy extensions require modifications to remain compatible with newer versions of Thunderbird**.
{% endhint %}

If you are currently maintaining a legacy extension please check out our guides on updating extensions for Thunderbird 78 and 68:

{% page-ref page="updating/tb78/" %}

{% page-ref page="updating/tb68/" %}

Legacy extensions fall into two sub-categories:

#### **Overlay Legacy Extensions \(deprecated\)**

The original type of extension for Thunderbird and Firefox. This type uses documents that overlay Thunderbird UI, adding to and modifying it. They use an RDF manifest \(`install.rdf`\) and require a restart of Thunderbird for installation/uninstallation, upgrading/downgrading and enabling/disabling.

#### **Bootstrapped Legacy Extensions \(deprecated\)**

Use a bootstrap file \(`bootstrap.js`\) as an entry point to the extension. The file defines four methods \(`install`, `uninstall`, `startup`, and `shutdown`\) from which all extension behaviour is controlled. This type of extension can be installed or shut down without restarting Thunderbird, so it’s sometimes called a “restartless” extension. Also uses an RDF manifest \(`install.rdf`\).

## Themes

Themes change the way that Thunderbird looks, for instance - here is a screenshot of the side panel using the built-in dark theme:

![It&apos;s Dark](../.gitbook/assets/screenshot-from-2019-03-23-13-47-57.png)

#### Static Themes

Static themes, like the name implies - are static and do not change. They have a set color or images that make up the theme and this does not change.

#### Dynamic Themes

Dynamic themes use the theme API to do more interesting things with the look of Thunderbird. For instance, [one example](https://developer.mozilla.org/en-US/docs/Mozilla/Add-ons/Themes/Theme_concepts#Dynamic_themes) used in Firefox documentation is a dynamic theme that changes the theme colors based on the time of day.

## Tips and tricks for add-on development

We've begun to assemble a collection of tips and tricks for making add-on development easier:

{% page-ref page="updating/tips-and-tricks.md" %}

