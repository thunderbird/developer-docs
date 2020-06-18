# Converting Legacy WebExtensions

This guide is intended to help developers to port their Legacy WebExtensions to MailExtensions to be compatible with Thunderbird 78. While the intention of the guide is to simplify the porting process, porting Legacy WebExtensions is not a straightforward process and requires a lot of time and some degree of development experience.

{% hint style="info" %} We do not suggest to convert older Legacy Bootstrapped Extensions or Legacy Overlay Extensions (as used in Thunderbird 60) directly to MailExtensions. They should first be converted to Legacy WebExtensions as described in the [update guide for Thunderbird 68](https://developer.thunderbird.net/add-ons/updating/tb68). {% endhint %}

## Dropping the legacy key and adding new entry points

The technical conversion of a Legacy WebExtension is ridiculously easy: just drop the `legacy` key from the `manifest.json` file.

Now your add-on should install in current versions of Thunderbird without issues, but do nothing. After all, Thunderbird no longer calls anything within your add-on. To fix that, you need one or more entry points.

There are multiple ways to get an add-on to load \(documented [in the WebExtensions course on MDN](https://developer.mozilla.org/en-US/docs/Mozilla/Add-ons/WebExtensions/Anatomy_of_a_WebExtension)\), but for a MailExtension the most common option will be adding a *background script* to `manifest.json`:
```json
"background": {
  "scripts": ["background-script.js"]
}
```
Adding this secton to `manifest.json` will cause the file `background-script.js` to be loaded and evaluated by Thunderbird. For bootstrap typed Legacy WebExtensions, the existing `bootstrap.js` script itself is a good starting point for a background script – for overlay typed Legacy WebExtensions it may be reasonable to start with an empty script and convert overlays using the guidelines below, gradually building up the background script.

Contrary to the bootstrap script in legacy add-ons, the background scripts will *not* get evaluated in a browser context. Instead it is added to an HTML document (a.k.a the "background page") living in a content process, which only has access to [MailExtension APIs](https://thunderbird-webextensions.readthedocs.io/en/latest/index.html) and [some WebExtension APIs](new linkable header in API doc being added by geoff soon) inherited from the underlying Firefox code base. Any interaction with Thunderbird must occur through these APIs. Whenever code needs to be added to the background script, you need to make sure to migrate calls to XPCOM or other native Thunderbird features to these APIs.

{% hint style="info" %} Since the WebExtension technology originates in Browser like Google Chrome and Firefox, the namespace for this new kind of API is `chrome.*` or `browser.*`which work in Thunderbird as well. For Thunderbird however the additional namespace `messenger.*` has been added. We think that is a better fit and should be used with MailExtensions, as they do not run in Browsers anyhow, if they use any of the MailExtension APIs, which only exist in Thunderbird. {% endhint %}

[//remove? //Some APIs may require specific permissions or settings to be specified within `manifest.json`, and some WebExtension APIs may be disabled or missing if they are not relevant to Thunderbird.]


## Experiment-ing with new APIs

While the Thunderbird team plans to add more APIs with upcoming releases, the current set of APIs will not be sufficient to port most add-ons. To work around this limitation, add-ons can introduce their own, additional APIs as so-called [*experiments*](https://thunderbird-webextensions.readthedocs.io/en/latest/how-to/experiments.html). **Any feature that was available in previous versions of Thunderbird remains available in Thunderbird 78 when using experiments.**

Experiments consist of three parts:
1. a [*schema*](https://firefox-source-docs.mozilla.org/toolkit/components/extensions/webextensions/schema.html) describes the API that can be accessed by the WebExtension part of the add-on. The schema may define functions that can be called by the add-in, events the add-in can register listeners for.
2. a *parent* implementation implements the API in Thunderbird's main process. All features that were available to a bootstrapped legacy extension can be used here.
3. a *child* implementation implements the API in the content process(es). In most cases, it will get generated automatically to call the parent implementation – but manual adjustments may be necessary if function parameters cannot be transferred to a different process (for example, if an API function accepts a function as argument). If the API does not need to interact with anything running in the main thread, it is also possible to implement an API completely within the child implementation.

   To return values not supported by the [structured clone algorithm](https://developer.mozilla.org/en-US/docs/Web/API/Web_Workers_API/Structured_clone_algorithm), you may need to clone them into the unprivileged scope of the WebExtension. Usually, that boils down to invoking [`Components.utils.cloneInto()`](https://developer.mozilla.org/en-US/docs/Mozilla/Tech/XPCOM/Language_Bindings/Components.utils.cloneInto) or a related function with `context.cloneScope` as target scope. Alternatively, you can directly return unprivileged objects created using constructors within `context.cloneScope`.

Full examples for [a simple function with parent and child implementations](https://firefox-source-docs.mozilla.org/toolkit/components/extensions/webextensions/functions.html) and [events add-ons can listen for](https://firefox-source-docs.mozilla.org/toolkit/components/extensions/webextensions/events.html) are available in the Firefox source documentation.

If your experiment is so complex that it does not reasonably fit into a single source file, you can use JavaScript modules just like in legacy extensions with some additional boilerplate:
```javascript
const { ExtensionParent } = ChromeUtils.import(
    "resource://gre/modules/ExtensionParent.jsm");
const extension = ExtensionParent.GlobalManager.getExtension(
    "insert-your-extension-id-here@example.com");
const { /* ... exported symbols ... */ } =
    ChromeUtils.import(extension.rootURI.resolve("path/to/module.jsm"));

// when unloading: (safe to call even if the import is conditional / elsewhere!)
Components.utils.unload(extension.rootURI.resolve("path/to/module.jsm"));
```

As experiments usually run in the main process and have unrestricted access to any aspect of Thunderbird, they are expected to require updates for each new version of Thunderbird. To reduce the maintainance burden in the future, it is in your own interest to use experiments only to the extent necessary for the add-on.

Best practice: try to write APIs that would be useful for a wide range of add-ons, not just the one you're porting. That way, you can later on propose the API you designed for inclusion in Thunderbird, with your add-on serving as reference implementation. If your APIs become a part of Thunderbird, you no longer need to maintain them as part of the add-on.


## Replacing options

Option windows are replaced by [settings pages](https://developer.mozilla.org/en-US/docs/Mozilla/Add-ons/WebExtensions/Implement_a_settings_page) defined in `manifest.json`:
```json
"options_ui": {
  "page": "options.html"
}
```
Instead of an XUL dialog, use the specified HTML document will be accessible to the user through the Add-on manager. From that document, all WebExtension and MailExtension APIs can be accessed in the same way as form a background script. The settings themselves should be stored using one of the new APIs to store data, such as [`storage`](https://developer.mozilla.org/en-US/docs/Mozilla/Add-ons/WebExtensions/API/storage), so it may be necessary to add an experiment to migrate existing settings from nsIPrefBranch or other mechanisms not accessible through modern APIs.


## Replacing chrome.manifest

The `chrome.manifest` file is no longer supported. Many mechanisms have a more or less direct equivalent in the WebExtension world:
* **interfaces** it should no longer be necessary to use binary interfaces, as binary components are deprecated for a long time. To access custom methods on JS-implemented classes from an experiment, use `.wrappedJSObject` to get access to the raw JS implementation.
* **component**, **contract** [see section 'Replacing XPCOM registration' below](#replacing-xpcom-registration).
* **category** use [`nsICategoryManager`](https://developer.mozilla.org/en/XPCOM_Interface_Reference/nsICategoryManager) through an experiment
* **content**, **skin**, **resource** it is no longer easily possible to assign chrome://- and resource://-URLs from add-ons. However, all files of an add-on are now available through moz-extension://-URLs by default. To generate such URLs, use [`browser.runtime.getURL`](https://developer.mozilla.org/en-US/docs/Mozilla/Add-ons/WebExtensions/API/runtime/getURL) or `extension.getURL` from an experiment. Some WebExtension APIs also accept relative paths and generate suitable URLs automatically. Not all native components support moz-extension://-URLs, though – experiments can use `extension.rootURI.resolve` to get a raw file://-URL instead.
* **locale** localization for WebExtensions is handled using the [`i18n` API](https://developer.mozilla.org/en-US/docs/Mozilla/Add-ons/WebExtensions/API/i18n), which uses a single `messages.json` file to store translations.
* **style** use an experiment to monitor open windows, and inject the style through that experiment
* **overlay** [see section 'Replacing Overlays' below](#replacing-overlays).

There are no direct equivalents to manifest flags, so add-ons now need to provide their own mechanisms to switch code or resources depending on the runtime environment. Relevant information is accessible through the [`runtime` API](https://developer.mozilla.org/en-US/docs/Mozilla/Add-ons/WebExtensions/API/runtime/).


## Replacing Overlays

If you used XUL overlays, they are now finally gone for good and you must find an alternative. While it is technically possible to write an XUL overlay loader experiment, it is a better idea to re-evaluate the purpose of the overlay:
* For overlays extending the user interface in a way that can be replaced using calls to built-in APIs: do those calls in a background script (Example: adding context menu items)
* For overlays extending the user interface in a generalizeable way: add an experiment with an API for the generic customization (Example: adding toolbar buttons to a toolbar without an existing API; use existing toolbar APIs as reference)
* For overlays loading a script without user interface relationship: move the script's content to a background script (Example: startup script in a main window overlay)

If a part of an overlay cannot be replaced with one of the suggestions above, it may be reasonable to replace the overlay with a specific function in an experiment.


## Replacing XUL windows and dialogs

While it would be possible to attempt to re-use existing XUL code in an experiment, it is probably a better idea to use the more future-proof [`windows` MailExtension API](https://thunderbird-webextensions.readthedocs.io/en/latest/windows.html) to create a window displaying an HTML dialog:
```javascript
messenger.windows.create({url: 'relative/path/to/window.html', type: 'popup'});
```
From these dialogs, all WebExtension and MailExtension APIs can be accessed in the same way as from a background script.


## Replacing XPCOM registration

Components and contract IDs can get registered by calling [`Components.manager.registerFactory()`](https://developer.mozilla.org/en-US/docs/Mozilla/Tech/XPCOM/Reference/Interface/nsIComponentRegistrar#registerFactory()) from an experiment. Remember to also call [`Components.manager.unregisterFactory()`](https://developer.mozilla.org/en-US/docs/Mozilla/Tech/XPCOM/Reference/Interface/nsIComponentRegistrar#unregisterFactory()) when the experiment shuts down.

To get a factory implementation, copy the component's existing implementation into an experiment's parent script and use its NSGetFactory method to build a factory to register:
```javascript
// original chrome.manifest:
component {00000000-0000-0000-0000-000000000000} implementation.js
contract  @example.com/contract;1 {00000000-0000-0000-0000-000000000000}

// original implementation.js:
var { XPCOMUtils } = ChromeUtils.import("resource://gre/modules/XPCOMUtils.jsm");
let classID = Components.ID("{00000000-0000-0000-0000-000000000000}");
let contractID = "@example.com/contract;1";
function exampleComponent() { /* ... implementation ... */ }
exampleComponent.prototype = { /* ... implementation ... */ };
var NSGetFactory = XPCOMUtils.generateNSGetFactory([exampleComponent]);

// new experiment.js:
/* ... */ getAPI(context) { /* ... */
  var { XPCOMUtils } = ChromeUtils.import("resource://gre/modules/XPCOMUtils.jsm");
  let classID = Components.ID("{00000000-0000-0000-0000-000000000000}");
  let contractID = "@example.com/contract;1";
  function exampleComponent() { /* ... implementation ... */ }
  exampleComponent.prototype = { /* ... implementation ... */ };
  let factory = XPCOMUtils.generateNSGetFactory([exampleComponent])(classId);
  Components.manager.registerFactory(classID, "exampleComponent", contractID,
    factory);
  context.callOnClose({close(){
    Components.manager.unregisterFactory(classID, factory);
  }});
/* ... */ } /* ... */
```
For complex cases, it might be reasonable to put the implementation and optionally its registration in a separate JavaScript module.


## Replacing various discontinued features within experiment code

Many parts of XUL are discontinued, and there are some other changes that prevent legacy code to run unchanged in an experiment. [A separate article deals with these changes.](changes.md)


## Additional Tips

Some general tips to speed up your porting workflow:
* To debug code running in the backgroud page or to interactively use Web-/MailExtension APIs, you can access debugging tools using the gear icon in the add-on tab.
* To debug code running in the browser context (usually: your experiments) you can still use the browser console (Ctrl+Shift+J) or developer toolbox (Ctrl+Shift+I) – just like with legacy add-ons.
* The add-on debugging tools accessible through the add-on page's gear icon permit to directly install add-ons without packaging them, similar to linking a legacy add-on in the profile folder. Using that option permits to reload the add-on without restarting Thunderbird.
* When testing a change in an experiment, always restart Thudnerbird *and delete Thunderbird's cache folder*. Experiment code may get cached, and these caches are not always cleared when uninstalling or replacing an add-on.

