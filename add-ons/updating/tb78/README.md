---
description: Required steps to convert deprecated Legacy WebExtensions to MailExtensions.
---

# Update for Thunderbird 78

Support for legacy extensions was removed from Thunderbird Beta version 74, released in February 2020. Therefore, only modern MailExtensions are compatible with Thunderbird 78. This guide is intended to help developers to port their Legacy WebExtensions to MailExtensions.

{% hint style="warning" %}
We do not suggest to convert older Legacy Bootstrapped Extensions or Legacy Overlay Extensions \(as used in Thunderbird 60\) directly to MailExtensions. They should first be converted to Legacy WebExtensions as described in the [update guide for Thunderbird 68](https://developer.thunderbird.net/add-ons/updating/tb68).
{% endhint %}

If you need any help, get in touch with the add-on developer community:

{% page-ref page="../../community.md" %}

## Dropping the legacy key and adding new entry points

The technical conversion from a Legacy WebExtension to a MailExtension is simple: drop the `legacy` key from the `manifest.json` file.

Now your add-on should install in current versions of Thunderbird without issues, but it will do nothing. You need to define one or more entry points as documented in the [WebExtensions course on MDN](https://developer.mozilla.org/en-US/docs/Mozilla/Add-ons/WebExtensions/Anatomy_of_a_WebExtension), but for a MailExtension the most common option will be adding a background script to `manifest.json`:

```javascript
"background": {
  "scripts": ["background-script.js"]
}
```

Adding this section to your `manifest.json` will cause the file `background-script.js` to be loaded and evaluated by Thunderbird. For bootstrap typed Legacy WebExtensions, the existing `bootstrap.js` script itself is a good starting point for a background script – for overlay typed Legacy WebExtensions it may be reasonable to start with an empty script and convert overlays using the guidelines below, gradually building up the background script.

Contrary to the bootstrap script in legacy add-ons, the background scripts will _not_ get evaluated in a privileged browser context. Instead it is added to an HTML document \(a.k.a the "background page"\) living in a content process, which only has access to [MailExtension APIs](https://thunderbird-webextensions.readthedocs.io/en/latest/index.html) and some WebExtension APIs inherited from the underlying Firefox code base \(they are listed further down on [this page](https://thunderbird-webextensions.readthedocs.io/en/latest/)\). Any interaction with Thunderbird must occur through these APIs. Whenever code needs to be added to the background script, you need to make sure to migrate calls to XPCOM or other native Thunderbird features to these APIs.

{% hint style="info" %}
The document "[Comparison with XUL/XPCOM extensions](https://extensionworkshop.com/documentation/develop/comparison-with-xul-xpcom-extensions/)" from the Extensions Workshop provides a comprehensive overview about which legacy API can be replaced by which WebExtension API.
{% endhint %}

{% hint style="info" %}
Since the WebExtension technology originates in Browsers like Google Chrome and Firefox, the namespace for this new kind of API is `chrome.*` or `browser.*` which work in Thunderbird as well. Thunderbird also supports `messenger.*`, which is a better fit for MailExtensions.
{% endhint %}

## Experiment-ing with new APIs

While the Thunderbird team plans to add more APIs with upcoming releases, the current set of APIs will not be sufficient to port most add-ons. To work around this limitation, add-ons can introduce their own, additional APIs as so-called [_experiments_](https://thunderbird-webextensions.readthedocs.io/en/latest/how-to/experiments.html).

{% hint style="info" %}
Any feature that was available in previous versions of Thunderbird remains available in Thunderbird 78 inside of experimental APIs.
{% endhint %}

As experiments usually run in the main process and have unrestricted access to any aspect of Thunderbird, they are expected to require updates for each new version of Thunderbird. To reduce the maintenance burden in the future, it is in your own interest to use experimental APIs only to the extent necessary for the add-on.

Best practice: Try to write APIs that would be useful for a wide range of add-ons, not just the one you're porting. That way, you can later on propose the API you designed for inclusion in Thunderbird, with your add-on serving as reference implementation. If your APIs become a part of Thunderbird, you no longer need to maintain them as part of the add-on.

A more thorough description of experimental APIs can be found in a separate article:

{% page-ref page="experiments.md" %}

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

The settings themselves should be stored using one of the new APIs to store data, such as [`storage`](https://developer.mozilla.org/en-US/docs/Mozilla/Add-ons/WebExtensions/API/storage), so it may be necessary to add an experiment to migrate existing settings from nsIPrefBranch or other mechanisms not accessible through modern APIs.

The [legacyPrefMigration example add-on](https://github.com/thundernest/sample-extensions/tree/master/legacyPrefMigration) includes such a migration API.

## Replacing chrome.manifest

The `chrome.manifest` file is no longer supported. Many mechanisms have a more or less direct equivalent in the WebExtension world:

* **interfaces**   It should no longer be necessary to use binary interfaces, as binary components are deprecated for a long time. To access custom methods on JS-implemented classes from an experiment, use `.wrappedJSObject` to get access to the raw JS implementation.   
* **component**, **contract**   See section '[Replacing XPCOM registration](https://developer.thunderbird.net/add-ons/updating/tb78#replacing-xpcom-registration)' below.  
* **category**   Use [`Services.catMan.addCategory()`](https://developer.mozilla.org/en-US/docs/Mozilla/Tech/XPCOM/Reference/Interface/nsICategoryManager) \(`nsICategoryManager`\) through an experiment.  
* **content**, **skin**, **resource**  
  WebExtensions mostly access files by specifying a relative path inside the extensions directory structure and not by global `chrome://*`, `resource://*` or `skin://*` URLs. The WebExtension technology itself does not provide a way to register such URLs anymore. If you need global URLs in a WebExtension, use `file://*` URLs generated by `extension.rootURI.resolve()`.  


  Inside experimental APIs some calls do not work with `file://*` URLs \(e.g. `new ChromeWorker()`\) , here one needs to manually register a `chrome://*` URL, as shown in our [LoadModule example extension.](https://github.com/cleidigh/ThunderStorm/tree/master/examples/MailExtensions/LoadModule)  

* **locale**   Localization for WebExtensions is handled using the [`i18n` API](https://developer.mozilla.org/en-US/docs/Mozilla/Add-ons/WebExtensions/API/i18n), which uses a single `messages.json` file to store translations. We have created a tool to convert the legacy DTD and property files. See section '[Converting locale files](https://developer.thunderbird.net/add-ons/updating/tb78#converting-locale-files)' below.  
* **style** Use an [experiment to monitor open windows](https://github.com/cleidigh/ThunderStorm/tree/master/examples/MailExtensions/WindowListener), and inject the style through that experiment. 
* **overlay**  See section ['Replacing Overlays' ](./#replacing-overlays)below.

There are no direct equivalents to manifest flags, so add-ons now need to provide their own mechanisms to switch code or resources depending on the runtime environment. Relevant information is accessible through the [`runtime` API](https://developer.mozilla.org/en-US/docs/Mozilla/Add-ons/WebExtensions/API/runtime/).

## Converting locale files

You may use our python script [`migrateLocale.py`](https://github.com/cleidigh/EditEmailSubject-MX/tree/master/scripts/dev-tools/localization/dtd-converter-py) to convert the legacy DTD and property files into the new JSON format. That script will merge the new entries into a potentially existing `messages.json` file.

To access the new locales use [`messenger.i18n.getMessage()`](https://developer.mozilla.org/en-US/docs/Mozilla/Add-ons/WebExtensions/API/i18n/getMessage), from within an experimental API use `extension.localeData.localizeMessage()`.

There is no automatic replacement of locale placeholder entities like `&myLocaleIdentifier;` in HTML or XHTML files anymore. Instead use placeholders like `__MSG_myLocaleIdentifier__` and include the following script:

```javascript
(function (keyPrefix){

    let localization = {
        updateString(string) {
            let re = new RegExp(keyPrefix + "(.+?)__", "g");
            return string.replace(re, matched => {
                const key = matched.slice(keyPrefix.length, -2);
                return messenger.i18n.getMessage(key) || matched;
            });
        },

        updateSubtree(node) {
            const texts = document.evaluate(
                'descendant::text()[contains(self::text(), "' + keyPrefix + '")]',
                node,
                null,
                XPathResult.ORDERED_NODE_SNAPSHOT_TYPE,
                null
            );
            for (let i = 0, maxi = texts.snapshotLength; i < maxi; i++) {
                const text = texts.snapshotItem(i);
                if (text.nodeValue.includes(keyPrefix)) text.nodeValue = this.updateString(text.nodeValue);
            }

            const attributes = document.evaluate(
                'descendant::*/attribute::*[contains(., "' + keyPrefix + '")]',
                node,
                null,
                XPathResult.ORDERED_NODE_SNAPSHOT_TYPE,
                null
            );
            for (let i = 0, maxi = attributes.snapshotLength; i < maxi; i++) {
                const attribute = attributes.snapshotItem(i);
                if (attribute.value.includes(keyPrefix)) attribute.value = this.updateString(attribute.value);
            }
        },

        async updateDocument() {
            this.updateSubtree(document);
        }
    };

    document.addEventListener('DOMContentLoaded', () => {
        localization.updateDocument();
    }, { once: true });

})("__MSG_");
```

You may specify a different `keyPrefix`, if `__MSG_` does not fit your needs.

## Replacing Overlays

XUL overlays are no longer supported and you need to find an alternative:

* Overlays just loading a script without a user interface relationship: 
  * Move the script's content to a background script.   
* Overlays extending the user interface in a way that can be replaced by calls in the background script to built-in APIs:
  * [Example](https://github.com/cleidigh/EditEmailSubject-MX/blob/30c8dd9bf6a7326873a1ad37541384ec8c4bfb36/src/background.js#L11-L16) for adding context menu items using the [`menus` API](https://thunderbird-webextensions.readthedocs.io/en/latest/menus.html)    
* Overlays extending the user interface beyond the built-in APIs:
  * Use an [experimental API with a window listener](https://github.com/cleidigh/ThunderStorm/tree/master/examples/MailExtensions/WindowListener) to manually add UI elements.

## Replacing XUL windows and dialogs

While it would be possible to attempt to re-use existing XUL code in an experiment, it is probably a better idea to use the more future-proof [`windows` API](https://thunderbird-webextensions.readthedocs.io/en/latest/windows.html) to create a window displaying an HTML dialog:

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

1. Manually inject the button, menuitem or whatever is opening the dialog via a window listener experiment \(see our [WindowListener example extension](https://github.com/cleidigh/ThunderStorm/tree/master/examples/MailExtensions/WindowListener)\).
2.  An event handler attached to the injected element runs in privileged chrome context and can call functions like `window.open()` or `window.openDialog()`. 
3. You need a global path to specify the location of your XUL file, either use a`file://*` or `chrome://*` path as described in the section ["Replacing chrome.manifest"](https://developer.thunderbird.net/add-ons/updating/tb78#replacing-chrome-manifest) above.
4. In TB78 you need to rename your `*.xul` file to `*.xhtml` .

Any JavaScript file/module loaded by your XUL dialog also runs in privileged chrome context and does not have direct access to MailExtension or WebExtension APIs.

## Replacing XPCOM registration

Components and contract IDs can get registered by calling [`Components.manager.registerFactory()`](https://developer.mozilla.org/en-US/docs/Mozilla/Tech/XPCOM/Reference/Interface/nsIComponentRegistrar#registerFactory%28%29) from an experiment. Remember to also call [`Components.manager.unregisterFactory()`](https://developer.mozilla.org/en-US/docs/Mozilla/Tech/XPCOM/Reference/Interface/nsIComponentRegistrar#unregisterFactory%28%29) when the experiment shuts down.

To get a factory implementation, copy the component's existing implementation into an experiment's implementation script and use its NSGetFactory method to build a factory to register:

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
  /* ... */
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

## Replacing various discontinued features within experiment code

Many parts of XUL are discontinued, and there are some other changes that prevent legacy code to run unchanged in an experiment. A separate article deals with these changes:

{% page-ref page="changes.md" %}

## Additional Tips

Some general tips to speed up your porting workflow:

* To debug code running in a content page of your extension \(e.g.: your background script\) or to interactively use Web-/MailExtension APIs, you need to select "debug add-ons" from the gear icon in the add-on tab and then analyze your add-on.   
* To debug code running in the browser context \(e.g.: your experiments\) you must use the global browser console \(Ctrl+Shift+J\) or developer toolbox \(Ctrl+Shift+I\).   
* The add-on debugging tools accessible through the add-on page's gear icon permit to directly install add-ons without packaging them, similar to linking a legacy add-on in the profile folder. Using that option permits to reload the add-on without restarting Thunderbird.   
* Experiment code may get cached, and these caches are not always cleared when uninstalling or replacing an add-on. You can either always restart Thunderbird with the `-purgecaches` command line parameter or register a `close()` function for your experiment \(see our [experiment example](https://github.com/thundernest/sample-extensions/blob/master/experiment/implementation.js#L29-L31)\) which includes the following line:

```javascript
    // after JSM unloading also flush all caches
    Services.obs.notifyObservers(null, "startupcache-invalidate", null);
```

{% hint style="warning" %}
Many thanks to github user [rsjtdrjgfuzkfg](https://github.com/rsjtdrjgfuzkfg) for his large contributions to this document.
{% endhint %}

