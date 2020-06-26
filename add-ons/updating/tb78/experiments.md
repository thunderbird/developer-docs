# Guide for Experiments

{% hint style="info" %}
This document is _work-in-progress_.
{% endhint %}

Experiments consist of three parts: 

* 1. A [_schema_](https://firefox-source-docs.mozilla.org/toolkit/components/extensions/webextensions/schema.html) describes the API that can be accessed by the WebExtension part of the add-on. The schema may define functions that can be called by the add-on and events the add-on can register listeners for. 
* 2. A _parent_ implementation implements the API in Thunderbird's main process. All features that were available to a bootstrapped legacy extension can be used here. 
* 3. A _child_ implementation implements the API in the content process\(es\). In most cases, it will get generated automatically to call the parent implementation – but manual adjustments may be necessary if function parameters cannot be transferred to a different process \(for example, if an API function accepts a function as argument\). If the API does not need to interact with anything running in the main thread, it is also possible to implement an API completely within the child implementation.

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

Experiments do not seem to support moz-extension://-URLs obtained by `extension.getURL()` when loading JSM, instead `extension.rootURI.resolve()` should be used to get the raw file://-URL.

