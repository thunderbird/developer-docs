---
description: What you need to know about making add-ons for Thunderbird.
---

# Introduction

## What are Add-ons?

Add-ons include:

* **extensions** that add functionality or change the way Thunderbird works
* **themes** that change the way Thunderbird looks

You can check out many of the add-ons available for Thunderbird at [addons.thunderbird.net](https://addons.thunderbird.net).

### Extensions

{% hint style="danger" %}
**The current Thunderbird Beta and the next major release \(Thunderbird 78, scheduled for around June 2020\) no longer support legacy extensions.**

If you are currently maintaining a legacy extension, please identify the type of your extension in the table below and check the provided guides on updating your extension to remain compatible with the latest versions of Thunderbird.
{% endhint %}

The architecture of Thunderbird extensions has changed over the last years. The following table describes the different extension types.

{% tabs %}

Status: **The only valid extension type in Thunderbird 74 and later.**

MailExtensions are based on the WebExtension technology, which is also used by many web browsers. Thunderbird can use some of the WebExtension APIs provided by Firefox \(as they are both based on the Mozilla platform\), but Thunderbird of course requires additional interfaces to messenger specific functions. These additional APIs are called MailExtension APIs.

MailExtensions use a JSON manifest \(`manifest.json`\) but do not use the `legacy` key \(which was deprecated in Thunderbird 74\).

Unlike older legacy extensions, which had direct access to Thunderbird's internal components and UI elements, MailExtensions access functionality through the stable WebExtension/MailExtension APIs. They are less likely to break and do not need frequent and complex updating when Thunderbird changes.

You can find detailed information about MailExtensions here:

* [Hello World MailExtension example](https://developer.thunderbird.net/add-ons/examples/hello-world-add-on)
* [Our sample-extensions repository](https://github.com/thundernest/sample-extensions/tree/master/experiment)
* [MailExtensions API documentation](https://thunderbird-webextensions.readthedocs.io/)

If you need an API that does not exist yet, please [tell us about it](https://bugzilla.mozilla.org/enter_bug.cgi?product=Thunderbird&component=Add-Ons%3A+Extensions+API).

#### Experimental APIs \(a.k.a. WebExtension Experiments\)

Alternatively, if you need an API that does not exist yet, you can write your own and bundle it with your add-on. These so called experimental APIs have access to the same functions, components and UI elements as Thunderbird itself \(like the deprecated legacy extensions\). They allow to experiment with new MailExtension APIs, as a step towards adding those APIs to Thunderbird. Learn more about them here:

* [WebExtension Experiments documentation](https://thunderbird-webextensions.readthedocs.io/en/68/how-to/experiments.html)
* [Example in our sample-extensions repository](https://github.com/thundernest/sample-extensions/tree/master/experiment)

Currently and for the foreseeable future \(years\) WebExtension Experiments can be used in extensions released to the general public. This is to help with the transition to MailExtensions and the development of additional APIs. However, as this transition progresses, the use of WebExtension Experiments in publicly released extensions will eventually be deprecated and then unsupported, as they currently are for Firefox.



Status: **Deprecated in Thunderbird 74.**

Legacy WebExtensions are legacy overlay extensions or legacy bootstrap extensions wrapped inside a WebExtension.

They use a JSON manifest \(`manifest.json`\) with the `legacy` key \(which has been deprecated in Thunderbird 74\):

```javascript
{
  "manifest_version": 2,
  "applications": {
    "gecko": {
      "id": "myextension@sample.extensions.thunderbird.net",
      "strict_min_version": "68.0"
    }
  },
  "name": "Extension",
  "description": "Does a thing",
  "version": "2.0",
  "icons": {
    "32": "content/icon32x32.png"
  },
  "legacy": {
    "type" : "bootstrap",
    "options": {
      "page": "chrome://myextension/content/options.xul",
      "open_in_tab": true
    }
  }
}
```

#### Update Strategy

{% page-ref page="updating/tb78/" %}

{% page-ref page="updating/tb78/changes.md" %}



Status: **Deprecated in Thunderbird 68.**

This type of extension uses a bootstrap file \(`bootstrap.js`\) as an entry point to the extension. The file defines four methods \(`install`, `uninstall`, `startup`, and `shutdown`\) from which all extension behaviour is controlled. These extensions can be installed/uninstalled and enabled/disabled without restarting Thunderbird, so they are sometimes called “restartless” extensions. They use an RDF manifest \(`install.rdf`\).

#### Update Strategy

It is recommended to update legacy bootstrap extensions to legacy WebExtensions first, before converting them to MailExtension. The update guide assumes, the extension is currently compatible with Thunderbird 60. If that is not the case, you can find further update instructions [here](https://wiki.mozilla.org/Thunderbird/Add-ons_Guide_57).

{% page-ref page="updating/tb68/bootstrapped.md" %}

{% page-ref page="updating/tb68/changes.md" %}

Status: **Deprecated in Thunderbird 68.**

The original type of extension for Thunderbird and Firefox, using documents that overlay the Thunderbird UI, adding to and modifying it. These extensions use an RDF manifest \(`install.rdf`\) and require a restart of Thunderbird for installation/uninstallation, upgrading/downgrading and enabling/disabling.

#### Update Strategy

It is recommended to update legacy overlay extensions to legacy WebExtensions first, before converting them to MailExtensions. The update guide assumes, the extension is currently compatible with Thunderbird 60. If that is not the case, you can find further update instructions [here](https://wiki.mozilla.org/Thunderbird/Add-ons_Guide_57).

{% page-ref page="updating/tb68/overlays.md" %}

{% page-ref page="updating/tb68/changes.md" %}

### Themes

Themes change the way that Thunderbird looks, for instance - here is a screenshot of the side panel using the built-in dark theme:

![It&apos;s Dark](../.gitbook/assets/screenshot-from-2019-03-23-13-47-57.png)

#### Static Themes

Static themes, like the name implies - are static and do not change. They have a set color or images that make up the theme and this does not change.

#### Dynamic Themes

Dynamic themes use the theme API to do more interesting things with the look of Thunderbird. For instance, [one example](https://developer.mozilla.org/en-US/docs/Mozilla/Add-ons/Themes/Theme_concepts#Dynamic_themes) used in Firefox documentation is a dynamic theme that changes the theme colors based on the time of day.

