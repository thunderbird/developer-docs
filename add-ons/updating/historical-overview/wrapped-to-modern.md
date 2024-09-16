# Convert wrapped WebExtensions to modern WebExtensions

After legacy WebExtensions had been deprecated in Thunderbird 78, the Thunderbird team provided two so called wrapper Experiments (the `WindowListener` Experiment and the `BootstrapLoader` Experiment), which re-implemented the loading framework of legacy extensions and required only little changes for add-ons to be usable in Thunderbird 78. This mechanism was intended as a temporary step, after 4 years the Thunderbird team is no longer able to maintain the two mentioned Experiments.&#x20;

This document describes how to remove the wrapper Experiments and how to properly convert extensions to modern WebExtensions.

If you need any help, get in touch with the add-on developer community:

{% content-ref url="../../community.md" %}
[community.md](../../community.md)
{% endcontent-ref %}

Converting a wrapped WebExtension into a modern WebExtension will be a complex task: almost all interactions with Thunderbird will need to be re-written to use the new APIs. If these APIs are not yet sufficient for your add-on, you may even need to implement additional Experiment APIs yourself. Don't worry though: you can find information on all aspects of the migration process below, including links to many advanced topics.

{% hint style="warning" %}
Before working on an update, it is adviced to read some information about the WebExtension technology first. Our [Extension guide](../../mailextensions/) and our ["Hello World" Extension Tutorial](../../hello-world-add-on/) are good starting points.
{% endhint %}

## Step 1: Replace `registerDefaultPrefs()`

Most legacy extensions stored their preferences in an `nsIPrefBranch`, and the `registerDefaultPrefs()` function loaded a JavaScript file with default preference values. An example default preference file could look like this:

```javascript
pref("extensions.myaddon.enableDebug", false);
pref("extensions.myaddon.retries", 5);
pref("extensions.myaddon.greeting", "Hello");
```

This file and the associated call to `registerDefaultPrefs()` can be removed, and the default values must be set in the background script by using the [LegacyPrefs](https://github.com/thunderbird/webext-support/tree/master/experiments/LegacyPrefs) Experiment:

```javascript
await browser.LegacyPrefs.setDefaultPref("extensions.myaddon.enableDebug", false);
await browser.LegacyPrefs.setDefaultPref("extensions.myaddon.retries", 5);
await browser.LegacyPrefs.setDefaultPref("extensions.myaddon.greeting", "Hello");
```

We can now use the [LegacyPrefs](https://github.com/thunderbird/webext-support/tree/master/experiments/LegacyPrefs) Experiment to access existing preferences, for example the preference entry at `extensions.myaddon.enableDebug` can be read from any WebExtension script via:

```javascript
let enableDebug = await browser.LegacyPrefs.getPref("extensions.myaddon.enableDebug");
```

Modern WebExtension should eventually use the WebExtension [`storage`](https://developer.mozilla.org/en-US/docs/Mozilla/Add-ons/WebExtensions/API/storage) for their preferences, but to simplify the conversion process, we will keep using the `nsIPrefBranch` for now. The very last conversion step will migrate the preferences.

## Step 2: Replace `registerChromeUrl()`

We will keep registering global legacy `chrome://` or `resource://` urls, but we will use the [LegacyHelper](https://github.com/thunderbird/webext-support/tree/master/experiments/LegacyHelper) Experiment instead. Replace the call to `registerChromeUrl()` of the wrapper API by the `registerGlobalUrls()` function of the LegacyHelper Experiment. For example:

```javascript
browser.LegacyHelper.registerGlobalUrls([
    ["content",  "myaddon",  "content/"],
    ["resource", "myaddon",  "assets/"],
    ["locale",   "myaddon", "en-US", "locale/en-US/"],
    ["locale",   "myaddon", "de-DE", "locale/de-DE/"],
]);
```

## Step 3: Replace `registerOptionsPage()`

Modern WebExtensions show their options in an HTML page in a tab or in a frame inside the Add-on Manger. The wrapper APIs instead allowed to register a legacy XUL dialog to be opened when the wrench icon in the add-on card of the Add-on Manger was clicked. This has to be removed to allow that wrench icon to show the standard WebExtension HTML option page.

In this step we will create a menu entry on the `tools` menu to open the XUL option dialog via the LegacyHelper Experiment:

```javascript
browser.menus.create({
    id: "oldOptions",
    contexts: ["tools_menu"],
    title: "Old XUL options dialog",
    onclick: () => browser.LegacyHelper.openDialog(
        "XulAddonOptions",
        "chrome://myaddon/content/options.xhtml"
    )
})
```

This will be removed after the XUL options dialog has been converted to a standard WebExtension HTML option page.

## Step 4: Remove the wrapper API

This step will interrupt the main functionality of your add-on. Remove the registration for the wrapper API from `manifest.json`, remove its implementation and schema files and any usage from your background script. The only remaining working part of your add-on should now be your XUL options dialog.

**Please continue the conversion at step 5 of the guide, which describes the standard conversion from legacy WebExtensions to modern WebExtensions.**

{% content-ref url="legacy-to-modern.md" %}
[legacy-to-modern.md](legacy-to-modern.md)
{% endcontent-ref %}
