# Guide for Experiments

This document aggregates information on topics that commonly arise when developing a new experiment for Thunderbird 78. For a complete documentation on each individual topic, refer to the linked articles.

{% hint style="info" %}
Thunderbird does contain a few useful features related to experiments [whose documentation is no longer generated](https://bugzilla.mozilla.org/show_bug.cgi?id=1556460#c23). Especially if you're writing an experiment with complex interactions between the WebExtension and your experiment, it may be helpful to read the documentation blocks within [`resource://gre/modules/ExtensionCommon.jsm`](https://hg.mozilla.org/mozilla-central/file/tip/toolkit/components/extensions/ExtensionCommon.jsm) and possibly other modules in the same source code folder.
{% endhint %}

## Designing the API

When porting an add-on, it is easy to think about everything in the context of your existing add-on. But that view is likely limiting your options: when you encounter a feature that cannot get implemented with existing WebExtension APIs, it may be helpful to first think about alternative ways to present similar functionality – maybe it is possible to implement the same high-level feature in a different way?

If not, you should first read through the documentation of some buit-in APIs, this document and some linked documentation to get a feel about how existing APIs encapsulate common problems \(i.e. listener registration through events, excessive use of `Promise`, ...\) and what limitations your API will have to live with \(i.e. potential complexity when passing functions or raw DOM elements\).

Afterwards, think about \(hypothetical or real\) add-ons that would have similar needs and try to write APIs that would be useful for a wide range of add-ons:

* A good API provides a single feature with as little complexity as possible \(smaller is usually better\).

  Example: an API adding both a menu item and a toolbar item for a single callback function might be useful, but it would be better to split it in two separate operations and combine them in the WebExtension.

* A good API can be used in a generic way and is not tied to your add-on's logic or functionality.

  Example: an API to add a menu item that will perform a fixed action might solve the specific issue you're working on, but it would be better to permit WebExtension code to dynamically specify the action to perform.

* A good API adheres to the conventions of WebExtension APIs.

  Example: a `registerListener` function might be a good idea in isolation, but it would be better to use events \(that also simplifies the implementation!\).

Depending on your time constraints, experience, and the concrete feature you're trying to write an API for it is not necessarily reasonable to satisfy all three criteria, they are just a guideline to aim for.

## Building the Structure

Once you have a draft for your API, you can start to build the experiment. Experiments consist of three parts, [which are registered through `manifest.json`](https://firefox-source-docs.mozilla.org/toolkit/components/extensions/webextensions/basics.html#webextensions-experiments):

1. A [_schema_](https://firefox-source-docs.mozilla.org/toolkit/components/extensions/webextensions/schema.html) describes the API that can be accessed by the WebExtension part of the add-on.
2. A _parent_ implementation implements the API in Thunderbird's main process. All features that were available to a bootstrapped legacy extension can be used here.
3. A _child_ implementation implements the API in the content process\(es\). This permits more complex interactions with WebExtension code and potentially improves performance, at the cost of not being able to access the main process.

Either _parent_ or _child_ implementation may be omitted. Full examples for [a simple function with parent and child implementations](https://firefox-source-docs.mozilla.org/toolkit/components/extensions/webextensions/functions.html) and [events add-ons can listen for](https://firefox-source-docs.mozilla.org/toolkit/components/extensions/webextensions/events.html) are available in the Firefox source documentation.

{% hint style="info" %}
Technically speaking, Thunderbird 78 is not actually using multiple processes \(yet\). However, the APIs were designed with multiple processes in mind and enforce at least some constraints as if the parts were in different processes.
{% endhint %}

In most cases, you can start by formalizing your API draft into a schema and adding a _parent_ implementation using one of the linked articles as base. Add or switch to a _child_ implementation if you have performance considerations or need to pass more complex data \(see below\).

{% hint style="info" %}
Check out the [experiment generator](https://darktrojan.github.io/generator/generator.html), which creates all the needed files.
{% endhint %}

## Passing data to / from an WebExtension

In general, you can always pass simple data structures as function parameters and return values of an API. Thunderbird will automatically adapt them using the [structured clone algorithm](https://developer.mozilla.org/en-US/docs/Web/API/Web_Workers_API/Structured_clone_algorithm), so you do not need to worry about them.

If you want to pass more complex data structures, especially functions or instances of custom classes, you can do so form a _child_ implementation. There is a big caveat, though: the experiment's scripts are privileged relative to WebExtension scripts, which causes their scopes to be disjunct:

* **Accessing data that belongs to the WebExtension from an Experiment:** the code in the experiment gains [xray vision](https://developer.mozilla.org/en-US/docs/Mozilla/Tech/Xray_vision), permitting it to directly access the chrome implementation of the given object. Usually, you don't need to worry about that and things work out just fine – but if they don't, you can opt-out via `Components.utils.waiveXrays()`.
* **Accessing data that belongs to an Experiment from a WebExtension:** it is not possible to directly access chrome-scoped objects from a WebExtension \(but it can hold references on it\).

  There are two options to work around that: either the experiment clones the object into the unprivileged scope of the WebExtension or it directly constructs an unprivileged object.

  The first option usually boils down to invoking [`Components.utils.cloneInto()`](https://developer.mozilla.org/en-US/docs/Mozilla/Tech/XPCOM/Language_Bindings/Components.utils.cloneInto) or a related function with `context.cloneScope` as target scope. A notable exception is returning data from async API functions or wrapping Promises via [`context.wrapPromise()`](https://firefox-source-docs.mozilla.org/toolkit/components/extensions/webextensions/reference.html#BaseContext.wrapPromise), which causes automatic cloning of the result \(unless the result is wrapped into a `ExtensionCommon.NoCloneSpreadArgs`\).

  The second option is using the constructors in `context.cloneScope` directly from the experiment. Their results can be used from the WebExtension without further cloning.

{% hint style="info" %}
Common pitfall: `async` functions return a `Promise` in the scope of the function, so you need to wrap such functions before cloning them into a WebExtension scope.
{% endhint %}

## Structuring experiment code

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

Experiments do not seem to support `moz-extension://*`URLs obtained by `extension.getURL()` when loading a JSM, instead `extension.rootURI.resolve()` should be used to get the raw `file://*`URL.

An alternative to using `file://*` URLs is to register a custom `chrome://*` URL \(see this [TopicBox thread](https://thunderbird.topicbox.com/groups/addons/T9ec97ac794c127a2-M3358fc3e5647c0f0c8243d02/using-ctypes-chromeworker-in-webextension-experiment) for more details\).

{% hint style="danger" %}
Please be aware, that a JSM loaded via a `file://*` URL and also via `chrome://*` URL in different parts of the extension will somehow be treated as two separate JSMs and they will **NOT** share the same scope. So do not mix the different URL types.

You also need to load and unload a JSM using the same URL type.
{% endhint %}

{% hint style="warning" %}
Avoid declaring global variables in the implementation of your experiment, as that can cause collisions with other experiments loaded. Instead declare them as members of your API \([example](https://github.com/jobisoft/quicktext/blob/pro/content/api/ConversionHelper/implementation.js)\).
{% endhint %}

## Accessing WebExtensions directly from an experiment

Experiments should be standalone and have no dependencies to any WebExtension components. If possible, design your experiments as if they were a feature of Thunderbird that does not know about your add-on, and keep the scope as narrow as possible. If you design your APIs correctly, you will not need to access the WebExtension part of your add-on.

In some rare cases, it may be possible that that is not feasible to follow this recommended practice for the current update cycle. In that case, it is possible to tear down the separation between the experiment and the WebExtension. That can permit you to treat an experiment as if it were a part of the WebExtension, except for the restrictions regarding passing data outlined above.

In a _child_ implementation, you can directly access the real WebExtension scope via `Components.utils.waiveXrays(context.cloneScope)`.

Outside of a _child_ implementation, or if you need the scope of the background page in particular, you can extract the background page's scope from an `extension` object:

```javascript
const webextScope = Array.from(extension.views).find(
    view => view.viewType === "background").xulBrowser.contentWindow
    .wrappedJSObject;
```

This hack only works because Thunderbird is internally not \(yet\) using multiple processes. Again, it is highly recommended to design your APIs in a way that these interactions are not necessary as it is likely that this technique will stop working in future versions of Thunderbird.

{% hint style="warning" %}
Many thanks to github user [rsjtdrjgfuzkfg](https://github.com/rsjtdrjgfuzkfg) for his large contributions to this document.
{% endhint %}

