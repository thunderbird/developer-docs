---
description: What you need to know about making add-ons for Thunderbird.
---

# About Add-ons

## What are Add-ons?

Add-ons are themes that change the way Thunderbird looks, or extensions that add functionality or change the way Thunderbird works. You can check out many of the add-ons available for Thunderbird at addons.thunderbird.net:

## Types of Extensions

### MailExtensions

"MailExtensions" are WebExtensions with some added features specific to Thunderbird. WebExtensions is the current type of Extension used for Firefox and Chrome. Unlike how extensions once were, MailExtensions don't have complete access to Thunderbird's internal components and UI, but instead are able to access functionality through the use of APIs. 

{% hint style="success" %}
**From Thunderbird 68 onward, new extensions should be built as MailExtensions to ensure future compatibility.**
{% endhint %}

You can learn to make a Hello World MailExtension via this tutorial:

{% page-ref page="hello-world-add-on.md" %}

You can check out the MailExtensions APIs via its documentation here:

### Legacy Extensions

The extensions that were used before MailExtensions are now referred to as "Legacy Extensions" and fall into two categories:

#### Overlay Extension

The original type of extension for Thunderbird and Firefox. This type uses documents that overlay Thunderbird UI, adding and modifying it. Uses an RDF manifest \(`install.rdf`\) and requires a restart of Thunderbird for installation/uninstallation, upgrading/downgrading and enabling/disabling.

#### Bootstrapped Extension

Uses a bootstrap file \(`bootstrap.js`\) as an entry point to the extension. The file defines four methods \(`install`, `uninstall`, `startup`, and `shutdown`\) from which all extension behaviour is controlled. This type of extension can be installed or shut down without restarting Thunderbird, so it’s sometimes called a “restartless” extension. Also uses an RDF manifest \(`install.rdf`\).

{% hint style="danger" %}
Since Thunderbird 60 was released, there have been many major changes to Thunderbird’s core. **Almost every extension will require modifications for compatibility with the next Thunderbird, 68**. Some changes are trivial, others not.
{% endhint %}

If you are currently maintaining a legacy extension please consider checking out our guide on updating extensions for Thunderbird 68:

{% page-ref page="upgrading-add-ons-for-tb68/" %}

## Themes

Themes change the way that Thunderbird looks, for instance - here is a screenshot of the side panel using the built-in dark theme:

![It&apos;s Dark](../.gitbook/assets/screenshot-from-2019-03-23-13-47-57.png)

### Static Themes

Static themes, like the name implies - are static and do not change. They have a set color or images that make up the theme and this does not change.

### Dynamic Themes

Dynamic themes use the theme API to do more interesting things with the look of Thunderbird. For instance, [one example](https://developer.mozilla.org/en-US/docs/Mozilla/Add-ons/Themes/Theme_concepts#Dynamic_themes) used in Firefox documentation is a dynamic theme that changes the theme colors based on the time of day.

# Tips and tricks for add-on development

We've begun to assemble a collection of tips and tricks for making add-on development easier:

{% page-ref page="tips-and-tricks.md" %}
