---
description: A historical overview.
---

# How to convert legacy extensions?

The architecture of Thunderbird extensions has changed over the years. The following table describes the different legacy extension types, and how they can be converted to modern WebExtensions.

{% hint style="danger" %}
**The current Thunderbird ESR no longer supports legacy extensions.**

If you are currently maintaining a legacy extension, please identify the type of your extension in the table below and check the provided guides on updating your extension to remain compatible with the latest versions of Thunderbird.
{% endhint %}

{% tabs %}
{% tab title="Wrapped WebExtension" %}
Status: **Unsupported in Thunderbird 128.**

Wrapped WebExtension are modern WebExtensions, but they use a large Wrapper Experiment (a.k.a. _WindowListener API_ or _BootstrapLoader API_), which re-implemented the framework available to legacy WebExtensions (unsupported since Thunderbird 78).

These Wrapper Experiments had been provided as an intermediate solution to make legacy WebExtensions compatible with Thunderbird 78. These APIs have become unmaintainable and developers should no longer use them.

Wrapped WebExtensions have a background script similar to the following:

```javascript
async function main() {
    await messenger.WindowListener.registerDefaultPrefs(
        "defaults/preferences/prefs.js"
    );
    await messenger.WindowListener.registerChromeUrl([
        ["content",  "prefix_switch",          "chrome/content/"],
        ["resource", "prefix_switch",          "chrome/skin/classic/"],
        ["locale",   "prefix_switch", "en-US", "chrome/locale/en-US/"],
        ["locale",   "prefix_switch", "de-DE", "chrome/locale/de-DE/"],
    ]);
    await messenger.WindowListener.registerOptionsPage(
        "chrome://prefix_switch/content/options.xhtml"
    );
    await messenger.WindowListener.registerWindow(
        "chrome://messenger/content/messengercompose/messengercompose.xhtml",
        "chrome://prefix_switch/content/messengercompose.js"
    );
    await messenger.WindowListener.startListening();
}
main();
```

#### Update Strategy

{% content-ref url="wrapped-to-modern.md" %}
[wrapped-to-modern.md](wrapped-to-modern.md)
{% endcontent-ref %}
{% endtab %}

{% tab title="Legacy WebExtension" %}
Status: **Deprecated in Thunderbird 78.**

Legacy WebExtensions are legacy overlay extensions or legacy bootstrap extensions wrapped inside a WebExtension.

They use a JSON manifest (`manifest.json`) with the `legacy` key (which has been deprecated in Thunderbird 78):

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

{% content-ref url="legacy-to-modern.md" %}
[legacy-to-modern.md](legacy-to-modern.md)
{% endcontent-ref %}

{% content-ref url="../tb78/changes.md" %}
[changes.md](../tb78/changes.md)
{% endcontent-ref %}
{% endtab %}

{% tab title="Legacy Bootstrap Extension" %}
Status: **Deprecated in Thunderbird 68.**

This type of extension uses a bootstrap file (`bootstrap.js`) as an entry point to the extension. The file defines four methods (`install`, `uninstall`, `startup`, and `shutdown`) from which all extension behaviour is controlled. These extensions can be installed/uninstalled and enabled/disabled without restarting Thunderbird, so they are sometimes called "restartless" extensions. They use an RDF manifest (`install.rdf`).

#### Update Strategy

It is recommended to update legacy bootstrap extensions to legacy WebExtensions first, before converting them to modern WebExtension. The update guide assumes, the extension is currently compatible with Thunderbird 60. If that is not the case, you can find further update instructions [here](https://wiki.mozilla.org/Thunderbird/Add-ons\_Guide\_57).

{% content-ref url="bootstrapped.md" %}
[bootstrapped.md](bootstrapped.md)
{% endcontent-ref %}

{% content-ref url="../tb68/changes.md" %}
[changes.md](../tb68/changes.md)
{% endcontent-ref %}
{% endtab %}

{% tab title="Legacy Overlay Extension" %}
Status: **Deprecated in Thunderbird 68.**

The original type of extension for Thunderbird and Firefox, using documents that overlay the Thunderbird UI, adding to and modifying it. These extensions use an RDF manifest (`install.rdf`) and require a restart of Thunderbird for installation/uninstallation, upgrading/downgrading and enabling/disabling.

#### Update Strategy

It is recommended to update legacy overlay extensions to legacy WebExtensions first, before converting them to modern WebExtensions. The update guide assumes, the extension is currently compatible with Thunderbird 60. If that is not the case, you can find further update instructions [here](https://wiki.mozilla.org/Thunderbird/Add-ons\_Guide\_57).

{% content-ref url="overlays.md" %}
[overlays.md](overlays.md)
{% endcontent-ref %}

{% content-ref url="../tb68/changes.md" %}
[changes.md](../tb68/changes.md)
{% endcontent-ref %}
{% endtab %}
{% endtabs %}
