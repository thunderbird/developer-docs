# Introducing Experiments

This document aggregates information on topics that commonly arise when developing a new Experiment. For a complete documentation on each individual topic, refer to the linked articles.

{% hint style="info" %}
Since Experiments directly interact with Thunderbird's core functions, it is necessary to get used to the source code of Thunderbird itself. We gathered the most useful resources on the [Documentation & Resources](../resources/#legacy-extension-development) page.
{% endhint %}

{% hint style="info" %}
Thunderbird does contain a few useful features related to Experiments [whose documentation is no longer generated](https://bugzilla.mozilla.org/show\_bug.cgi?id=1556460#c23). Especially if you're writing an Experiment with complex interactions between the WebExtension and your Experiment, it may be helpful to read the documentation blocks within [ExtensionCommon.sys.mjs](https://searchfox.org/mozilla-central/source/toolkit/components/extensions/ExtensionCommon.sys.mjs) and possibly other modules in the same source code folder.
{% endhint %}

{% hint style="info" %}
For reference, the parent implementations of all built-in APIs can be found in\
[/comm/mail/components/extensions/parent](https://searchfox.org/comm-central/source/mail/components/extensions/parent).
{% endhint %}

{% hint style="danger" %}
Experiment APIs have full access to Thunderbird's core functions and can bypass the WebExtension permission system entirely. Including one or more Experiment APIs will therefore disable the individual permission prompt and instead prompt the user only for the [_Have full, unrestricted access to Thunderbird, and your computer_](https://support.mozilla.org/kb/permission-request-messages-thunderbird-extensions) permission.

The use of optional permissions is not supported for the same reason.
{% endhint %}

## Designing the API

When you encounter a feature that cannot get implemented with existing WebExtension APIs, it may be helpful to first read through the documentation of some built-in APIs, this document and some linked documentation to get a feeling about how existing APIs encapsulate common problems (i.e. listener registration through events, excessive use of `Promise`, ...) and what limitations your API will have to live with (i.e. potential complexity when passing functions or raw DOM elements).

Afterwards, think about (hypothetical or real) add-ons that would have similar needs and try to design an API that would be useful for a wide range of add-ons:

*   A good API provides a single feature with as little complexity as possible (smaller is usually better).

    Example: an API adding both a menu item and a toolbar item for a single callback function might be useful, but it would be better to split it in two separate operations and combine them in the WebExtension.
*   A good API can be used in a generic way and is not tied to your add-on's logic or functionality.

    Example: an API to add a menu item that will perform a fixed action might solve the specific issue you're working on, but it would be better to permit WebExtension code to dynamically specify the action to perform.
*   A good API adheres to the conventions of WebExtension APIs.

    Example: a `registerListener` function might be a good idea in isolation, but it would be better to use events (that also simplifies the implementation!).

Depending on your time constraints, experience, and the concrete feature you're trying to write an API for, it is not necessarily reasonable to satisfy all three criteria, they are just a guideline to aim for.

## Building the Structure

Once you have a draft for your API, you can start to build the Experiment. Experiments consist of three parts, which are registered through [manifest.json](https://firefox-source-docs.mozilla.org/toolkit/components/extensions/webextensions/basics.html#webextensions-experiments):

1. A [_schema_](https://firefox-source-docs.mozilla.org/toolkit/components/extensions/webextensions/schema.html) describes the API that can be accessed by the WebExtension part of the add-on.
2. A _parent_ implementation implements the API in Thunderbird's main process. All features that were available to a bootstrapped legacy extension can be used here.
3. A _child_ implementation implements the API in the content process(es). This permits more complex interactions with WebExtension code and potentially improves performance, at the cost of not being able to access the main process.

A typical entry in the manifest.json file to register an Experiment:

```json
    "experiment_apis": {
        "LegacyPrefs": {
            "schema": "api/LegacyPrefs/schema.json",
            "parent": {
                "scopes": ["addon_parent"],
                "paths": [["LegacyPrefs"]],
                "script": "api/LegacyPrefs/implementation.js",
                "events": ["startup"]
            }
        }
    }
```

Either _parent_ or _child_ implementation may be omitted. Full examples for [a simple function with parent and child implementations](https://firefox-source-docs.mozilla.org/toolkit/components/extensions/webextensions/functions.html) and [events add-ons can listen for](https://firefox-source-docs.mozilla.org/toolkit/components/extensions/webextensions/events.html) are available in the Firefox source documentation.

{% hint style="info" %}
Technically speaking, Thunderbird is not actually using multiple processes (yet). However, the APIs were designed with multiple processes in mind and enforce at least some constraints as if the parts were in different processes.
{% endhint %}

In most cases, you can start by formalizing your API draft into a schema and adding a _parent_ implementation using one of the linked articles as base. Add or switch to a _child_ implementation if you have performance considerations or need to pass more complex data (see below).

{% hint style="info" %}
Check out the [Experiment Generator](https://darktrojan.github.io/generator/generator.html), which creates all the needed files.
{% endhint %}

{% hint style="info" %}
Check out the Example Experiments:

* [Activity Manager Experiment](https://github.com/thunderbird/webext-examples/tree/master/manifest\_v2/experiment.activityManager)
* [Open SearchDialog Experiment](https://github.com/thunderbird/webext-examples/tree/master/manifest\_v2/experiment.openSearchDialog)
* [Remove Attachments If Junk Experiment](https://github.com/thunderbird/webext-examples/tree/master/manifest\_v2/experiment.removeAttachmentsIfJunk)
* [Restart Experiment](https://github.com/thunderbird/webext-examples/tree/master/manifest\_v2/experiment.restart)
{% endhint %}

{% hint style="warning" %}
Avoid declaring global variables in the implementation of your Experiment, as that can cause collisions with other Experiments loaded. Instead declare them as members of your API, or use a closure.
{% endhint %}

## Managing your Experiment's lifecycle

Experiments are loaded on demand. In order for an Experiment to get loaded, you thus need to either use the API from the WebExtension or register an implementation for the `startup` event (which calls the `onStartup()` method of that implementation's `ExtensionAPI` object once your add-on is loaded).

If your add-on is unloaded, Thunderbird will call the `onShutdown()` method of each _loaded_ implementation's `ExtensionAPI` object. You should perform any cleanup tasks in that method, for example you **must** invalidate Thunderbird's startup cache whenever your add-on is unloaded for a non-shutdown reason:

```javascript
Services.obs.notifyObservers(null, "startupcache-invalidate", null);​
```

Failure to invalidate caches may cause parts of the add-on's Experiment APIs to be cached across updates of the Add-on, even if they are changed in the update. It is thus usually a good idea to execute the code above in the `onShutdown()` method of an Experiment that is always loaded.

In addition to your Experiment being loaded and unloaded as a whole, that Experiment's API will get loaded into each WebExtension context independently. As there can be multiple contexts at the same time, an Experiment may have multiple loaded APIs in parallel. You can perform context-specific loading tasks directly in `getAPI()`, and register context-specific unloading code through `context.callOnClose()`.

## Passing data to and from a WebExtension

### Parent implementations

In general, you can always pass simple data structures as function parameters and return values of an API. Thunderbird will automatically adapt them using the [structured clone algorithm](https://developer.mozilla.org/en-US/docs/Web/API/Web\_Workers\_API/Structured\_clone\_algorithm), so you do not need to worry about them.

If your API function is supposed to return a value, it must be defined as `async` in the schema file.

### Child implementations

If you want to pass more complex data structures, especially functions or instances of custom classes, you can do so from a _child_ implementation. There is a big caveat, though: the Experiment's implementation scripts are privileged relative to WebExtension scripts, which causes their scopes to be disjunct:

* **Accessing data that belongs to the WebExtension from an Experiment:** the code in the Experiment gains [xray vision](https://developer.mozilla.org/en-US/docs/Mozilla/Tech/Xray\_vision), permitting it to directly access the chrome implementation of the given object. Usually, you don't need to worry about that and things work out just fine – but if they don't, you can opt-out via `Components.utils.waiveXrays()`.
*   **Accessing data that belongs to an Experiment from a WebExtension:** it is not possible to directly access chrome-scoped objects from a WebExtension (but it can hold references on it).

    There are two options to work around that: either the Experiment clones the object into the unprivileged scope of the WebExtension or it directly constructs an unprivileged object.

    The first option usually boils down to invoking [Components.utils.cloneInto()](http://www.devdoc.net/web/developer.mozilla.org/en-US/docs/Components.utils.cloneInto.html) or a related function with `context.cloneScope` as target scope. A notable exception is returning data from async API functions or wrapping Promises via [context.wrapPromise()](https://firefox-source-docs.mozilla.org/toolkit/components/extensions/webextensions/reference.html#BaseContext.wrapPromise), which causes automatic cloning of the result (unless the result is wrapped into a `ExtensionCommon.NoCloneSpreadArgs`).

    The second option is using the constructors in `context.cloneScope` directly from the Experiment. Their results can be used from the WebExtension without further cloning.

{% hint style="info" %}
Common pitfall: `async` functions return a `Promise` in the scope of the function, so you need to wrap such functions before cloning them into a WebExtension scope.
{% endhint %}

## Structuring Experiment code

If your Experiment API is so complex that it does not reasonably fit into a single source file, you can use system modules with some additional boilerplate. The [LegacyHelper](https://github.com/thunderbird/webext-support/tree/master/experiments/LegacyHelper) Experiment can be used to define custom global URLs. In this example, we are registering a custom `resource://` URL with the namespace `myaddon`:

```javascript
// Register a resource:// url with a custom namespace, which points to the
// "modules" folder. The namespace should be unique to avoid conflicts with
// other add-ons.
await messenger.LegacyHelper.registerGlobalUrls([
    ["resource", "myaddon", "modules/"],
]);
```

The file `TestModule.sys.mjs` in the `modules` folder will then be accessible via\
`resource://myaddon/TestModule.sys.mjs`.&#x20;

Since system modules cannot be unloaded, we have to append a unique query, to make sure cached files are not re-used after an update. In the following example the `version` key from `manifest.json` is used. This allows us to use the same identifier throughout the entire add-on, but load the new version whenever the add-on has been updated:

```javascript
const { ExtensionParent } = ChromeUtils.importESModule(
    "resource://gre/modules/ExtensionParent.sys.mjs"
);
const extension = ExtensionParent.GlobalManager.getExtension(
    "<id-of-your-extensions>"
);
const query = extension.manifest.version;

// Load TestModule.sys.mjs.
var { TestModule } = ChromeUtils.importESModule(
  "resource://myaddon/TestModule.sys.mjs?" + query
)
```

## Accessing WebExtensions directly from an Experiment

Experiments should be standalone and have no dependencies to any WebExtension components. If possible, design your Experiments as if they were a feature of Thunderbird that does not know about your add-on, and keep the scope as narrow as possible. If you design your APIs correctly, you will not need to access the WebExtension part of your add-on.

In some rare cases, it may be possible that that is not feasible to follow this recommended practice for the current update cycle. In that case, it is possible to tear down the separation between the Experiment and the WebExtension. That can permit you to treat an Experiment as if it were a part of the WebExtension, except for the restrictions regarding passing data outlined above.

In a _child_ implementation, you can directly access the real WebExtension scope via `Components.utils.waiveXrays(context.cloneScope)`.

Outside of a _child_ implementation, or if you need the scope of the background page in particular, you can extract the background page's scope from an `extension` object:

```javascript
const webextScope = Array.from(extension.views).find(
    view => view.viewType === "background").xulBrowser.contentWindow
    .wrappedJSObject;
```

This hack only works because Thunderbird is internally not (yet) using multiple processes. Again, it is highly recommended to design your APIs in a way that these interactions are not necessary as it is likely that this technique will stop working in future versions of Thunderbird.
