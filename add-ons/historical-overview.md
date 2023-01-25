# Historical Overview

The architecture of Thunderbird extensions has changed over the years. The following table describes the different legacy extension types.

{% hint style="danger" %}
**The current Thunderbird ESR no longer supports legacy extensions.**

If you are currently maintaining a legacy extension, please identify the type of your extension in the table below and check the provided guides on updating your extension to remain compatible with the latest versions of Thunderbird.
{% endhint %}

{% tabs %}
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

{% content-ref url="updating/tb78/" %}
[tb78](updating/tb78/)
{% endcontent-ref %}

{% content-ref url="updating/tb78/changes.md" %}
[changes.md](updating/tb78/changes.md)
{% endcontent-ref %}
{% endtab %}

{% tab title="Legacy Bootstrap Extension" %}
Status: **Deprecated in Thunderbird 68.**

This type of extension uses a bootstrap file (`bootstrap.js`) as an entry point to the extension. The file defines four methods (`install`, `uninstall`, `startup`, and `shutdown`) from which all extension behaviour is controlled. These extensions can be installed/uninstalled and enabled/disabled without restarting Thunderbird, so they are sometimes called "restartless" extensions. They use an RDF manifest (`install.rdf`).

#### Update Strategy

It is recommended to update legacy bootstrap extensions to legacy WebExtensions first, before converting them to MailExtension. The update guide assumes, the extension is currently compatible with Thunderbird 60. If that is not the case, you can find further update instructions [here](https://wiki.mozilla.org/Thunderbird/Add-ons\_Guide\_57).

{% content-ref url="updating/tb68/bootstrapped.md" %}
[bootstrapped.md](updating/tb68/bootstrapped.md)
{% endcontent-ref %}

{% content-ref url="updating/tb68/changes.md" %}
[changes.md](updating/tb68/changes.md)
{% endcontent-ref %}
{% endtab %}

{% tab title="Legacy Overlay Extension" %}
Status: **Deprecated in Thunderbird 68.**

The original type of extension for Thunderbird and Firefox, using documents that overlay the Thunderbird UI, adding to and modifying it. These extensions use an RDF manifest (`install.rdf`) and require a restart of Thunderbird for installation/uninstallation, upgrading/downgrading and enabling/disabling.

#### Update Strategy

It is recommended to update legacy overlay extensions to legacy WebExtensions first, before converting them to MailExtensions. The update guide assumes, the extension is currently compatible with Thunderbird 60. If that is not the case, you can find further update instructions [here](https://wiki.mozilla.org/Thunderbird/Add-ons\_Guide\_57).

{% content-ref url="updating/tb68/overlays.md" %}
[overlays.md](updating/tb68/overlays.md)
{% endcontent-ref %}

{% content-ref url="updating/tb68/changes.md" %}
[changes.md](updating/tb68/changes.md)
{% endcontent-ref %}
{% endtab %}
{% endtabs %}

