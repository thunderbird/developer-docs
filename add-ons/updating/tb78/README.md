---
description: Required steps to convert deprecated Legacy WebExtensions to MailExtensions.
---

# Update for Thunderbird 78

Support for legacy extensions was removed from Thunderbird Beta version 74, released in February 2020. Only modern [MailExtensions](https://developer.thunderbird.net/add-ons/about-add-ons) are now compatible with Thunderbird 78. This guide is intended to help developers to port their Legacy WebExtensions to MailExtensions.

{% hint style="warning" %}
We do not suggest to convert older Legacy Bootstrapped Extensions or Legacy Overlay Extensions \(as used in Thunderbird 60\) directly to MailExtensions. They should first be converted to Legacy WebExtensions as described in the [update guide for Thunderbird 68](https://developer.thunderbird.net/add-ons/updating/tb68).
{% endhint %}

If you need any help, get in touch with the add-on developer community:

{% page-ref page="../../community.md" %}

## **Update Strategies**

This guide covers the 'proper' migration strategy to convert a legacy add-on into a MailExtension. If you follow this strategy, you will end up with a future-proof MailExtension that will require substantially less maintenance work for future versions of Thunderbird.

This will be a complex task: almost all interactions with Thunderbird will need to be re-written to use the new APIs. If these APIs are not yet sufficient for your add-on, you may even need to implement additional APIs yourself. Don't worry though: you can find information on all aspects of the migration process below, including links to many advanced topics you may be interested in.

{% hint style="info" %}
Knowing that following the proper migration strategy is not easy, we created [two wrapper APIs which do not require all of these changes](https://github.com/thundernest/addon-developer-support/wiki). Using these APIs, you can quickly get your add-on running in Thunderbird 78 again, but you will not get the benefits of a MailExtension. The idea behind this is to make add-ons compatible with the current ESR as quickly and easily as possible, so users can continue to work with their beloved add-ons. The actual conversion to a pure MailExtension can then take place in smaller steps. To assist you, we will release small tutorials to remove the legacy parts one after another.
{% endhint %}

## Dropping the legacy key and adding new entry points

The technical conversion from a Legacy WebExtension to a MailExtension is simple: drop the `legacy` key from the `manifest.json` file.

Now your add-on should install in current versions of Thunderbird without issues, but it will do nothing. You need to define one or more entry points as documented in the [WebExtensions course on MDN](https://developer.mozilla.org/en-US/docs/Mozilla/Add-ons/WebExtensions/Anatomy_of_a_WebExtension), but for a MailExtension the most common option will be adding a background script to `manifest.json`:

```javascript
"background": {
  "scripts": ["background-script.js"]
}
```

Adding this section to your `manifest.json` will cause the file `background-script.js` to be loaded and evaluated by Thunderbird. For bootstrap typed Legacy WebExtensions, the existing `bootstrap.js` script itself is a good starting point for a background script – for overlay typed Legacy WebExtensions it may be reasonable to start with an empty script and convert overlays using the guidelines below, gradually building up the background script.

Contrary to the bootstrap script in legacy add-ons, the background scripts will _not_ get evaluated in a privileged browser context. Instead it is added to an HTML document \(a.k.a the "background page"\) living in a content process, which only has access to [MailExtension APIs](https://thunderbird-webextensions.readthedocs.io/en/78/index.html) and some WebExtension APIs inherited from the underlying Firefox code base \(they are listed further down on [this page](https://thunderbird-webextensions.readthedocs.io/en/78/)\). Any interaction with Thunderbird must occur through these APIs. Whenever code needs to be added to the background script, you need to make sure to migrate calls to XPCOM or other native Thunderbird features to these APIs.

{% hint style="info" %}
The document "[Comparison with XUL/XPCOM extensions](https://extensionworkshop.com/documentation/develop/comparison-with-xul-xpcom-extensions/)" from the Extensions Workshop provides a comprehensive overview about which legacy API can be replaced by which WebExtension API.
{% endhint %}

{% hint style="info" %}
Since the WebExtension technology originates in Browsers like Google Chrome and Firefox, the namespace for this new kind of API is `chrome.*` or `browser.*` which work in Thunderbird as well. Thunderbird also supports `messenger.*`, which is a better fit for MailExtensions.
{% endhint %}

## Experiment-ing with new APIs

While the Thunderbird team plans to add more APIs with upcoming releases, the current set of APIs will not be sufficient to port most add-ons. To work around this limitation, add-ons can introduce their own, additional APIs as so-called [_Experiments_](https://thunderbird-webextensions.readthedocs.io/en/78/how-to/experiments.html).

{% hint style="info" %}
Any feature that was available in previous versions of Thunderbird remains available in Thunderbird 78 inside of Experiment APIs.
{% endhint %}

As Experiments usually run in the main process and have unrestricted access to any aspect of Thunderbird, they are expected to require updates for each new version of Thunderbird. To reduce the maintenance burden in the future, it is in your own interest to use Experiment APIs only to the extent necessary for the add-on.

Best practice: Try to write APIs that would be useful for a wide range of add-ons, not just the one you're porting. That way, you can later on propose the API you designed for inclusion in Thunderbird, with your add-on serving as reference implementation. If your APIs become a part of Thunderbird, you no longer need to maintain them as part of the add-on.

A more thorough description of Experiment APIs can be found in a separate article:

{% page-ref page="../../mailextensions/experiments.md" %}

## Replacing options

Option windows are replaced by [settings pages](https://developer.mozilla.org/en-US/docs/Mozilla/Add-ons/WebExtensions/Implement_a_settings_page) defined in `manifest.json`:

```javascript
"options_ui": {
  "page": "options.html"
}
```

Instead of a XUL dialog, the specified HTML document is used, which will be accessible to the user through the add-on manager. From that document, all WebExtension and MailExtension APIs can be accessed in the same way as from the background script.

{% hint style="warning" %}
[Under unidentified circumstances, WebExtension APIs invoked from an options page may throw the error "Error: Unknown sender or wrong actor for recvAPICall".](https://bugzilla.mozilla.org/show_bug.cgi?id=1607859) Until this issue is fixed, you can call any API through the background page: `(await messenger.runtime.getBackgroundPage()).messenger./* ... */`
{% endhint %}

The settings themselves should be stored using one of the new APIs to store data, such as [`storage`](https://developer.mozilla.org/en-US/docs/Mozilla/Add-ons/WebExtensions/API/storage), so it may be necessary to add an Experiment to migrate existing settings from nsIPrefBranch or other mechanisms not accessible through modern APIs.

The [legacyPrefMigration example add-on](https://github.com/thundernest/sample-extensions/tree/master/legacyPrefMigration) includes such a migration API.

## Replacing chrome.manifest

The `chrome.manifest` file is no longer supported. Many mechanisms have a more or less direct equivalent in the WebExtension world:

* **interfaces**   It should no longer be necessary to use binary interfaces, as binary components are deprecated for a long time. To access custom methods on JS-implemented classes from an Experiment, use `.wrappedJSObject` to get access to the raw JS implementation.   
* **component**, **contract**   See section '[Replacing XPCOM registration](https://developer.thunderbird.net/add-ons/updating/tb78#replacing-xpcom-registration)' below.  
* **category**   Use [`Services.catMan.addCategory()`](https://developer.mozilla.org/en-US/docs/Mozilla/Tech/XPCOM/Reference/Interface/nsICategoryManager) \(`nsICategoryManager`\) through an Experiment.  
* **content**, **skin**, **resource**  
  WebExtensions mostly access files by specifying a relative path inside the extensions directory structure and not by global `chrome://*`, `resource://*` or `skin://*` URLs. The WebExtension technology itself does not provide a way to register such URLs anymore. If you need global URLs in a WebExtension, use `file://*` URLs generated by `extension.rootURI.resolve()`.  


  Inside Experiment APIs some calls do not work with `file://*` URLs \(e.g. `new ChromeWorker()`\) , here one needs to manually register a `chrome://*` URL, as done in the [enigmail add-on](https://github.com/cleidigh/ThunderKdB/blob/fa91a81ba77f71358b34533095381f03b0a3b3ed/xall/x68/71-enigmail/src/webextension.js#L15-L54).  

* **locale**   Localization for WebExtensions is handled using the [`i18n` API](https://developer.mozilla.org/en-US/docs/Mozilla/Add-ons/WebExtensions/API/i18n), which uses a single `messages.json` file to store translations. We have created a tool to convert the legacy DTD and property files. See section '[Converting locale files](https://developer.thunderbird.net/add-ons/updating/tb78#converting-locale-files)' below.  
* **style** Use an [Experiment to monitor open windows](https://github.com/thundernest/sample-extensions/blob/f44d61ad796fa86c04da6add0dda162084aaea44/restart/implementation.js#L19), and inject the style through that Experiment. 
* **overlay**  See section ['Replacing Overlays' ](./#replacing-overlays)below.

There are no direct equivalents to manifest flags, so add-ons now need to provide their own mechanisms to switch code or resources depending on the runtime environment. Relevant information is accessible through the [`runtime` API](https://developer.mozilla.org/en-US/docs/Mozilla/Add-ons/WebExtensions/API/runtime/).

## Converting locale files

You may use our python script [`localeConverter.py`](https://github.com/thundernest/addon-developer-support/tree/master/tools/locale-converter) to convert the legacy DTD and property files into the new JSON format. That script will merge the new entries into a potentially existing `messages.json` file.

To access the new locales use [`messenger.i18n.getMessage()`](https://developer.mozilla.org/en-US/docs/Mozilla/Add-ons/WebExtensions/API/i18n/getMessage), from within an Experiment API use `context.extension.localeData.localizeMessage()`.

There is no automatic replacement of locale placeholder entities like `&myLocaleIdentifier;` in HTML or XHTML files anymore. Instead you can use placeholders like `__MSG_myLocaleIdentifier__` and include the `i18n.js` script provided by the [addon-developer-support repository](https://github.com/thundernest/addon-developer-support/tree/master/scripts/i18n).

## Replacing Overlays

XUL overlays are no longer supported and you need to find an alternative:

* Overlays just loading a script without a user interface relationship: 
  * Move the script's content to a background script.   
* Overlays extending the user interface in a way that can be replaced by calls in the background script to built-in APIs:
  * [Example](https://github.com/cleidigh/EditEmailSubject-MX/blob/30c8dd9bf6a7326873a1ad37541384ec8c4bfb36/src/background.js#L11-L16) for adding context menu items using the [`menus` API](https://thunderbird-webextensions.readthedocs.io/en/78/menus.html)    
* Overlays extending the user interface beyond the built-in APIs:
  * Use an [Experiment API with a window listener](https://github.com/thundernest/sample-extensions/blob/master/restart/implementation.js) to manually add UI elements.

## Replacing XUL windows and dialogs

While it would be possible to attempt to re-use existing XUL code in an Experiment, it is probably a better idea to use the more future-proof [`windows` API](https://thunderbird-webextensions.readthedocs.io/en/78/windows.html) to create a window displaying an HTML dialog:

```javascript
messenger.windows.create({
    height: 400,
    width: 500,
    url: "/path/from/root/of/addon/to/dialog.html",
    type: "popup"
});
```

From these dialogs, all WebExtension and MailExtension APIs can be accessed in the same way as from a background script. To send data to and from the opened window, you can use the message passing concept. An example can be found in [this TopicBox thread](https://thunderbird.topicbox.com/groups/addons/Ta8337b5f8c8012d5/webextension-how-to-send-data-to-created-window-and-back-as-with-legacy-window-open).  
  
**We do not suggest to keep working with XUL dialogs, because we want to protect authors from spending conversion time on a dead technology, which is being removed step by step and requires constant updates to the add-on.** However, there might be cases where it is currently reasonable to keep loading a XUL document, which could be done like so:

1. Manually inject the button, menuitem or whatever is opening the dialog via a window listener Experiment \(see the [restart example extension](https://github.com/thundernest/sample-extensions/blob/f44d61ad796fa86c04da6add0dda162084aaea44/restart/implementation.js#L19)\).
2.  An event handler attached to the injected element runs in privileged chrome context and can call functions like `window.open()` or `window.openDialog()`. 
3. You need a global path to specify the location of your XUL file, either use a`file://*` or `chrome://*` path as described in the section ["Replacing chrome.manifest"](https://developer.thunderbird.net/add-ons/updating/tb78#replacing-chrome-manifest) above.
4. In TB78 you need to rename your `*.xul` file to `*.xhtml` .

Any JavaScript file/module loaded by your XUL dialog also runs in privileged chrome context and does not have direct access to MailExtension or WebExtension APIs.

## Replacing XPCOM registration

Components and contract IDs can get registered by calling [`Components.manager.registerFactory()`](https://developer.mozilla.org/en-US/docs/Mozilla/Tech/XPCOM/Reference/Interface/nsIComponentRegistrar#registerFactory%28%29) from an Experiment. Remember to also call [`Components.manager.unregisterFactory()`](https://developer.mozilla.org/en-US/docs/Mozilla/Tech/XPCOM/Reference/Interface/nsIComponentRegistrar#unregisterFactory%28%29) when the Experiment shuts down.

To get a factory implementation, copy the component's existing implementation into an Experiment's implementation script and use its NSGetFactory method to build a factory to register:

```javascript
// original chrome.manifest:
component {00000000-0000-0000-0000-000000000000} implementation.js
contract  @example.com/contract;1 {00000000-0000-0000-0000-000000000000}

// original implementation.js:
var { XPCOMUtils } = ChromeUtils.import("resource://gre/modules/XPCOMUtils.jsm");
let classID = Components.ID("{00000000-0000-0000-0000-000000000000}");
let contractID = "@example.com/contract;1";
function exampleComponent() {
  /* ... implementation ... */ 
}
exampleComponent.prototype = {
  /* ... implementation ... */
};
var NSGetFactory = XPCOMUtils.generateNSGetFactory([exampleComponent]);

// new experiment.js:
/* ... */ 
getAPI(context) { 
  /* ... do the following in an init() method only called once from your
     background page ... */
  var { XPCOMUtils } = ChromeUtils.import("resource://gre/modules/XPCOMUtils.jsm");
  let classID = Components.ID("{00000000-0000-0000-0000-000000000000}");
  let contractID = "@example.com/contract;1";
  function exampleComponent() {
    /* ... implementation ... */
  }
  exampleComponent.prototype = {
    /* ... implementation ... */ 
  };
  let factory = XPCOMUtils.generateNSGetFactory([exampleComponent])(classId);
  // WARNING: this assumes that Thunderbird is already running, as
  // Components.manager.registerFactory will be unavailable for a few
  // milliseconds after startup.
  Components.manager.registerFactory(classID, "exampleComponent", contractID, 
    factory);
  context.callOnClose({close(){
    Components.manager.unregisterFactory(classID, factory);
  }});
  /* ... */
}
/* ... */
```

For complex cases, it might be reasonable to put the implementation and optionally its registration in a separate JavaScript module.

## Replacing various discontinued features within Experiment code

Many parts of XUL are discontinued, and there are some other changes that prevent legacy code to run unchanged in an Experiment API. A separate article deals with these changes:

{% page-ref page="changes.md" %}

## Additional Tips

{% page-ref page="../../tips-and-tricks.md" %}

