---
description: Required steps to convert legacy WebExtensions to modern WebExtensions.
---

# Update for Thunderbird 78

Support for [legacy WebExtensions](../../historical-overview.md#legacy-webextension) was removed from Thunderbird Beta version 74, released in February 2020. Only [modern WebExtensions](https://developer.thunderbird.net/add-ons/about-add-ons) are now compatible with Thunderbird 78. This guide is intended to help developers to port their legacy WebExtensions to modern WebExtensions.

{% hint style="warning" %}
We do not suggest to convert older legacy bootstrapped extensions or legacy overlay extensions (as used in Thunderbird 60) directly to modern WebExtensions. They should first be converted to legacy WebExtensions as described in the [update guide for Thunderbird 68](https://developer.thunderbird.net/add-ons/updating/tb68).
{% endhint %}

If you need any help, get in touch with the add-on developer community:

{% content-ref url="../../community.md" %}
[community.md](../../community.md)
{% endcontent-ref %}

Converting a legacy WebExtension into a modern WebExtension will be a complex task: almost all interactions with Thunderbird will need to be re-written to use the new APIs. If these APIs are not yet sufficient for your add-on, you may even need to implement additional Experiment APIs yourself. Don't worry though: you can find information on all aspects of the migration process below, including links to many advanced topics you may be interested in.

{% hint style="warning" %}
Before working on an update, it is adviced to read some information about the WebExtension technology first. Our [Extension guide](../../mailextensions/) and our ["Hello World" Extension Tutorial](../../hello-world-add-on/) are good starting points.
{% endhint %}

## Step 1: Dropping the legacy key

The technical conversion from a legacy WebExtension to a modern WebExtension is simple: drop the `legacy` key from the `manifest.json` file.

Your add-on should now install in current versions of Thunderbird without issues, but it will not yet do anything. The file `chrome.manifest` will no longer be read.

## Step 2: Converting locale files

One of the first steps should be to convert your locale files (DTD and property files) into the new JSON format used by WebExtensions. Our [`localeConverter.py`](https://github.com/thunderbird/addon-developer-support/tree/master/tools/locale-converter) python script will do the heavy lifting, it will merge the new entries into a potentially existing `messages.json` files.

The new locale data can be accessed through the [i18n](https://developer.mozilla.org/en-US/docs/Mozilla/Add-ons/WebExtensions/API/i18n) WebExtension API:

```javascript
browser.i18h.getMessage("a-locale-string");
```

## Step 3: Converting the XUL options page

Instead of a XUL dialog, WebExtensions use an HTML page for their options page, which will be accessible to the user through the add-on manager. The page is registered in `manifest.json`:

```javascript
"options_ui": {
  "page": "options.html"
}
```

From that document, all WebExtension APIs can be accessed in the same way as for example from the background script.

In this step the old XUL options page has to be re-created as an HTML page, using only HTML elements, JavaScript and CSS. It is no longer possible to use XUL elements. Some custom elements and 3rd party libraries to simplify this step can be found in the [webext-support](https://github.com/thunderbird/webext-support/tree/master/ui) repository.

Most legacy extensions stored their preferences in an `nsIPrefBranch`. Modern WebExtension should eventually use the WebExtension [`storage`](https://developer.mozilla.org/en-US/docs/Mozilla/Add-ons/WebExtensions/API/storage); however, to minimize code changes, it may be easier to keep accessing the legacy preferences for now. This can be achieved by including the [LegacyPrefs](https://github.com/thunderbird/webext-support/tree/master/experiments/LegacyPrefs) Experiment. The converted WebExtension options page is then able to access its existing preferences as follows:

```javascript
let color = await browser.LegacyPrefs.getPref("extensions.addon123.color");
```

The very last step of the conversion process will be the migration of the preferences from the `nsIPrefBranch` to the WebExtension [`storage`](https://developer.mozilla.org/en-US/docs/Mozilla/Add-ons/WebExtensions/API/storage). The [legacyPrefMigration example add-on](https://github.com/thunderbird/webext-examples/tree/master/manifest\_v2/experiment.prefMigration) performs such a migration API.

There is no automatic replacement of locale placeholder entities like `&myLocaleIdentifier;` in WebExtension HTML files any more. Instead, you can use placeholders like `__MSG_myLocaleIdentifier__` in your markup and include the `i18n.js` script provided by the [webext-support](https://github.com/thunderbird/webext-support/tree/master/scripts/i18n) repository. It will replace all `__MSG_*__` locale placeholders on page load using the `i18n` API.

## Step 4: Find matching WebExtension entry points and WebExtension APIs

Now it's time to find out how your add-on can leverage the existing WebExtension entry points. What UI elements did you use? Do any of the [supported WebExtension UI elements](../../mailextensions/supported-ui-elements.md) fit?

Even if they are not a perfect match, try to replace as many of your legacy UI entry points by WebExtension entry points:

* [menus and context menus](../../mailextensions/supported-ui-elements.md#menu-items)
* action buttons (normal or [menu-typed](https://github.com/thunderbird/webext-examples/tree/master/manifest\_v2/menuActionButton))
* action popus
* message display scripts ([manipulate/overlay the displayed message](https://github.com/thunderbird/webext-examples/tree/master/manifest\_v2/messageDisplayScript.pdfPreview))
* compose scripts ([interact with the editor, manipulate the DOM, the selection, the cursor position](https://github.com/jobisoft/quicktext/blob/WebExt/scripts/compose.js))
* content tabs
*   content popup windows\


    ```javascript
    let window = await messenger.windows.create({
        height: 400,
        width: 500,
        url: "/path/from/root/of/addon/to/dialog.html",
        type: "popup"
    });
    ```
* [native messaging](https://developer.mozilla.org/en-US/docs/Mozilla/Add-ons/WebExtensions/Native\_messaging)

The goal of this step is to re-create as much of the functionality of your add-on by using only WebExtension technology. Browse through the list of [supported WebExtension APIs](https://webextension-api.thunderbird.net/en/beta-mv2/) to see if any of them provide what is needed by your add-on. Check available [Web APIs](https://developer.mozilla.org/en-US/docs/Web/API), there is a high chance to find simple replacements for complicated XPCOM calls:

* [play sounds](https://developer.mozilla.org/en-US/docs/Web/API/Web\_Audio\_API)
* [localize plural rules](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global\_Objects/Intl/PluralRules)

Do not hesitate to ask in [our community channels](../../community.md) for help.

## Step 5: Creating missing UI entry points and APIs as Experiments

If certain crucial features of your add-on cannot be implemented using the available WebExtension APIs or Web APIs, you can create your own Experiment APIs.&#x20;

As Experiments usually run in the main process and have unrestricted access to any aspect of Thunderbird, they are expected to require updates for each new version of Thunderbird. To reduce the maintenance burden in the future, it is in your own interest to use Experiment APIs only to the extent necessary for the add-on.

Best practice: Try to write APIs that would be useful for a wide range of add-ons, not just the one you're porting. That way, you can later on propose the API you designed for inclusion in Thunderbird, with your add-on serving as the reference implementation. If your APIs become a part of Thunderbird, you no longer need to maintain them as part of the add-on.

A more thorough description of Experiment APIs can be found in a separate article:

{% content-ref url="../../mailextensions/experiments.md" %}
[experiments.md](../../mailextensions/experiments.md)
{% endcontent-ref %}

Examples Experiments:

* [open the search dialog](https://github.com/thunderbird/webext-examples/tree/master/manifest\_v2/experiment.openSearchDialog)
* [adding a click handler by overlaying the messenger window, and load code from a module](https://github.com/thunderbird/webext-examples/tree/master/manifest\_v2/experiment)
* [preference migration](https://github.com/thunderbird/webext-examples/tree/master/manifest\_v2/experiment.prefMigration)

## Replacing various discontinued features within Experiment code

Many parts of XUL are discontinued, and there are some other changes that prevent legacy code to run unchanged in an Experiment API. A separate article deals with these changes:

{% content-ref url="changes.md" %}
[changes.md](changes.md)
{% endcontent-ref %}

## Additional Tips

{% content-ref url="../../resources/tips-and-tricks.md" %}
[tips-and-tricks.md](../../resources/tips-and-tricks.md)
{% endcontent-ref %}
