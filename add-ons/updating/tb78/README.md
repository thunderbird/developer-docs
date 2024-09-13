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

Converting a legacy WebExtension into a modern WebExtension will be a complex task: almost all interactions with Thunderbird will need to be re-written to use the new APIs. If these APIs are not yet sufficient for your add-on, you may even need to implement additional Experiment APIs yourself. Don't worry though: you can find information on all aspects of the migration process below, including links to many advanced topics.

{% hint style="warning" %}
Before working on an update, it is adviced to read some information about the WebExtension technology first. Our [Extension guide](../../mailextensions/) and our ["Hello World" Extension Tutorial](../../hello-world-add-on/) are good starting points.
{% endhint %}

## Step 1: Dropping the legacy key

The technical conversion from a legacy WebExtension to a modern WebExtension is simple: drop the `legacy` key from the `manifest.json` file.

Your add-on should now install in current versions of Thunderbird without issues, but it will not yet do anything. The file `chrome.manifest` will no longer be read.

## Step 2: Converting locale files

One of the first steps should be to convert your locale files (DTD and property files) into the new JSON format used by WebExtensions. Our [`localeConverter.py`](https://github.com/thunderbird/addon-developer-support/tree/master/tools/locale-converter) python script will do the heavy lifting, it will merge the new entries into potentially existing `messages.json` files.

The new locale data can be accessed through the [i18n](https://developer.mozilla.org/en-US/docs/Mozilla/Add-ons/WebExtensions/API/i18n) WebExtension API:

```javascript
browser.i18n.getMessage("a-locale-string");
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

Most legacy extensions stored their preferences in an `nsIPrefBranch`. Modern WebExtension should eventually use the WebExtension [`storage`](https://developer.mozilla.org/en-US/docs/Mozilla/Add-ons/WebExtensions/API/storage); however, to minimize code changes, it may be easier to keep accessing the legacy preferences for now. This can be achieved by including the [LegacyPrefs](https://github.com/thunderbird/webext-support/tree/master/experiments/LegacyPrefs) Experiment. The converted WebExtension options page is then able to access the existing `extensions.addon123.color` preference entry as follows:

```javascript
let color = await browser.LegacyPrefs.getPref("extensions.addon123.color");
```

There is no automatic replacement of locale placeholder entities like `&myLocaleIdentifier;` in WebExtension HTML files any more. Instead, you can use placeholders like `__MSG_myLocaleIdentifier__` in your markup and include the [`i18n.js`](https://github.com/thunderbird/webext-support/tree/master/scripts/i18n) script provided by the [webext-support](https://github.com/thunderbird/webext-support/tree/master/scripts/i18n) repository and replace all `__MSG_*__` locale placeholders on page load. The script is using the `i18n` API to read the modern JSON locale files created in step #1.

## Step 4: Find matching WebExtension entry points and WebExtension APIs

Now it's time to find out how your add-on can leverage the existing WebExtension entry points. What UI elements did you use? Do any of the [supported WebExtension UI elements](../../mailextensions/supported-ui-elements.md) fit?

Even if they are not a perfect match, try to replace as many of your legacy UI entry points by WebExtension entry points:

* [menus and context menus](../../mailextensions/supported-ui-elements.md#menu-items)
* action buttons (normal or [menu-typed](https://github.com/thunderbird/webext-examples/tree/master/manifest\_v2/menuActionButton))
* action popus
* message display scripts ([manipulate/overlay the displayed message](https://github.com/thunderbird/webext-examples/tree/master/manifest\_v2/messageDisplayScript.pdfPreview))
* compose scripts ([interact with the editor, manipulate the DOM, the selection, the cursor position](https://github.com/jobisoft/quicktext/blob/WebExt/scripts/compose.js))
* content tabs
*   content popup windows\\

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

If certain crucial features of your add-on cannot be implemented using the available WebExtension APIs or Web APIs, you can create your own Experiment APIs.

As Experiments usually run in the main process and have unrestricted access to any aspect of Thunderbird, they are expected to require updates for each new version of Thunderbird. To reduce the maintenance burden in the future, it is in your own interest to use Experiment APIs only to the extent necessary for the add-on.

Best practice: Try to write APIs that would be useful for a wide range of add-ons, not just the one you're porting. That way, you can later on propose the API you designed for inclusion in Thunderbird, with your add-on serving as the reference implementation. If your APIs become a part of Thunderbird, you no longer need to maintain them as part of the add-on.

A basic description of Experiment APIs can be found in a separate article:

{% content-ref url="../../mailextensions/experiments.md" %}
[experiments.md](../../mailextensions/experiments.md)
{% endcontent-ref %}

### Overlay methods

Manipulating Thunderbirds UI through Experiments is historically referred to as _overlaying_. The basic principle of overlaying is to get hold of a native Thunderbird window object and to add or remove DOM elements (or monkey-patch functions living inside that native window to change some behaviour).

Adding or removing DOM elements can be achieved through JavaScript (note that Thunderbird sometimes still uses non-standard XUL elements, which are however slowly replaced by standard HTML elements):

```javascript
const { document } = window;
const rows = document.getElementById("attachemnt-rows");
const urlLabel = document.createXULElement("label");
urlLabel.setAttribute("class", "text-link");
urlLabel.setAttribute("value", url);
urlLabel.setAttribute("tooltiptext", url);
rows.appendChild(urlLabel);
```

A more detailed explanation of the shown code snippet is beyond the scope of this guide. It is advised to study [Thunderbird's code](https://searchfox.org/comm-central/search?q=symbol:%23createXULElement\&redirect=false) for more details.

Generating complex and nested DOM elements through JavaScript can become cumbersome, and legacy add-ons were able to provide a simple DOM string instead. This is still possible by using the following helper function:

<pre class="language-javascript"><code class="lang-javascript"><strong>// Helper function to inject a legacy XUL string into the DOM of Thunderbird.
</strong>// All injected elements will get the data attribute "data-extension-injected"
// set to the extension id, for easy removal.
const injectElements = function (extension, window, xulString, debug = false) {
  function checkElements(stringOfIDs) {
    let arrayOfIDs = stringOfIDs.split(",").map((e) => e.trim());
    for (let id of arrayOfIDs) {
      let element = window.document.getElementById(id);
      if (element) {
        return element;
      }
    }
    return null;
  }

  function localize(entity) {
    let msg = entity.slice("__MSG_".length, -2);
    return extension.localeData.localizeMessage(msg);
  }

  function injectChildren(elements, container) {
    if (debug) console.log(elements);

    for (let i = 0; i &#x3C; elements.length; i++) {
      if (
        elements[i].hasAttribute("insertafter") &#x26;&#x26;
        checkElements(elements[i].getAttribute("insertafter"))
      ) {
        let insertAfterElement = checkElements(
          elements[i].getAttribute("insertafter")
        );

        if (debug)
          console.log(
            elements[i].tagName +
            "#" +
            elements[i].id +
            ": insertafter " +
            insertAfterElement.id
          );
        if (
          debug &#x26;&#x26;
          elements[i].id &#x26;&#x26;
          window.document.getElementById(elements[i].id)
        ) {
          console.error(
            "The id &#x3C;" +
            elements[i].id +
            "> of the injected element already exists in the document!"
          );
        }
        elements[i].setAttribute("data-extension-injected", extension.id);
        insertAfterElement.parentNode.insertBefore(
          elements[i],
          insertAfterElement.nextSibling
        );
      } else if (
        elements[i].hasAttribute("insertbefore") &#x26;&#x26;
        checkElements(elements[i].getAttribute("insertbefore"))
      ) {
        let insertBeforeElement = checkElements(
          elements[i].getAttribute("insertbefore")
        );

        if (debug)
          console.log(
            elements[i].tagName +
            "#" +
            elements[i].id +
            ": insertbefore " +
            insertBeforeElement.id
          );
        if (
          debug &#x26;&#x26;
          elements[i].id &#x26;&#x26;
          window.document.getElementById(elements[i].id)
        ) {
          console.error(
            "The id &#x3C;" +
            elements[i].id +
            "> of the injected element already exists in the document!"
          );
        }
        elements[i].setAttribute("data-extension-injected", extension.id);
        insertBeforeElement.parentNode.insertBefore(
          elements[i],
          insertBeforeElement
        );
      } else if (
        elements[i].id &#x26;&#x26;
        window.document.getElementById(elements[i].id)
      ) {
        // existing container match, dive into recursively
        if (debug)
          console.log(
            elements[i].tagName +
            "#" +
            elements[i].id +
            " is an existing container, injecting into " +
            elements[i].id
          );
        injectChildren(
          Array.from(elements[i].children),
          window.document.getElementById(elements[i].id)
        );
      } else {
        // append element to the current container
        if (debug)
          console.log(
            elements[i].tagName +
            "#" +
            elements[i].id +
            ": append to " +
            container.id
          );
        elements[i].setAttribute("data-extension-injected", extension.id);
        container.appendChild(elements[i]);
      }
    }
  }

  if (debug) console.log("Injecting into root document:");
  let localizedXulString = xulString.replace(
    /__MSG_(.*?)__/g,
    localize
  );
  injectChildren(
    Array.from(
      window.MozXULElement.parseXULToFragment(localizedXulString, []).children
    ),
    window.document.documentElement
  );
};
</code></pre>

The function supports XUL strings with WebExtension `__MSG_*__` locale placeholders. It also supports `insertbefore` or `insertafter` attributes, to specify where the element should be added. If an existing `id` is specified, the element will be added as a child inside the existing element:

```javascript
injectElements(extension, window, `
  <tab insertafter="QuotaTab" id="FlagsTab" hidefor="rss,nntp" label="__MSG_folderflags.tab.label__"/>
  <vbox insertafter="quotaPanel" id="folderflags-tabPanel" align="start">
      <hbox align="center" valign="middle">
          <label>__MSG_folder__</label><label id="folderflags-folderName" />
      </hbox>
      <vbox id="folderflags-flaglist">
      </vbox>
  </vbox>
`);
```

A more detailed explanation of the shown code snippet is beyond the scope of this guide. The shown code is taken from the [FolderFlags](https://github.com/voccs/folderflags) add-on. The [Restart Experiment Example](https://github.com/thunderbird/webext-examples/tree/master/manifest\_v2/experiment.restart) is also using this method.

### Overlay strategies

In order to add custom UI entry points, the add-on has to manipulate the native window object of all already open windows/tabs and also any window/tab which is opened in the future. The two most common concepts to achieve this are described below.

#### Detect open windows/tabs through WebExtension APIs

This is the preferred method, since the add-on can leverage existing APIs and reduces the amount of code which has to be maintained by the add-on developer.  For example, to manipulate all message display tabs, the following code can be used in the WebExtension background script:

```javascript
// Handle all already open/displayed messages.
let tabs = await browser.tabs.query({ type: ["messageDisplay", "mail"] })
for (let tab of tabs) {
  let message = await browser.messageDisplay.getDisplayedMessage(tab.id);
  if (message) {
    await removeAttachmentsIfJunk(tab, message);
  }
}

// React on any new message being displayed.
browser.messageDisplay.onMessageDisplayed.addListener(removeAttachmentsIfJunk);

async function removeAttachmentsIfJunk(tab, message) {
  // Only remove attachments, if message is junk.
  if (!message.junk) {
    return;
  }

  // browser.MessageDisplayAttachment.removeAttachments is an Experiment API,
  // which operates on the given tab and removes all displayed attachments.
  await browser.MessageDisplayAttachment.removeAttachments(tab.id);
}
```

The Experiment implementation could be as follows:

```javascript
var MessageDisplayAttachment = class extends ExtensionCommon.ExtensionAPI {
  getAPI(context) {

    // Get the native about:message window from the tabId.
    function getMessageWindow(tabId) {
      let { nativeTab } = context.extension.tabManager.get(tabId);
      if (nativeTab instanceof Ci.nsIDOMWindow) {
        return nativeTab.messageBrowser.contentWindow
      } else if (nativeTab.mode && nativeTab.mode.name == "mail3PaneTab") {
        return nativeTab.chromeBrowser.contentWindow.messageBrowser.contentWindow
      } else if (nativeTab.mode && nativeTab.mode.name == "mailMessageTab") {
        return nativeTab.chromeBrowser.contentWindow;
      }
      return null;
    }

    return {
      MessageDisplayAttachment: {
        removeAttachments: async function (tabId) {
          let window = getMessageWindow(tabId);
          if (!window) {
            return;
          }

          // The following code depends in internal Thunderbird methods and may
          // change, which will break the add-on.
          for (let index = window.currentAttachments.length; index > 0; index--) {
            let idx = index - 1;
            window.currentAttachments.splice(idx);
          }

          await window.ClearAttachmentList();
          window.gBuildAttachmentsForCurrentMsg = false;
          await window.displayAttachmentsForExpandedView();
          window.gBuildAttachmentsForCurrentMsg = true;
        },
      },
    };
  }
};
```

The shown code is taken from the [Remove Attachments If Junk Experiment Example](https://github.com/thunderbird/webext-examples/tree/master/manifest\_v2/experiment.removeAttachmentsIfJunk). An example which manipulates the main window using the same strategy is the [Restart Experiment Example](https://github.com/thunderbird/webext-examples/tree/master/manifest\_v2/experiment.restart).

#### Detect open windows/tabs through the Experiment

If the window of interest is not supported by WebExtension APIs, it is not detectable through WebExtension APIs. The detection code has to live inside an Experiment. This can be achieved through a global window listener, which reacts only on the window of interest (`activity.xhtml` in this case). The global window listener is registered in the Experiment's `onStartup` function, and is removed in its `onShutdown` function:

```javascript
onStartup() {
  const { extension } = this;

  // Register a listener for newly opened activity windows.
  ExtensionSupport.registerWindowListener(extension.id, {
    chromeURLs: [
      "chrome://messenger/content/activity.xhtml",
    ],
    onLoadWindow(window) {
      // Add our event listener.
      window._exampleAddOnClickHandler = (e) => {
        console.log("The button was clicked, let's do something!")
      }
      window.document.getElementById("clearListButton").addEventListener(
        "click",
        window._exampleAddOnClickHandler
      );
    },
  });
}

onShutdown(isAppShutdown) {
  if (isAppShutdown) {
    return;
  }

  // Remove our event listener.
  const { extension } = this;
  for (let window of ExtensionSupport.openWindows) {
    if ([
      "chrome://messenger/content/activity.xhtml",
    ].includes(window.location.href)) {
      // Remove our event listener.
      window.document.getElementById("clearListButton").removeEventListener(
        "click",
        window._exampleAddOnClickHandler
      );
      delete window._exampleAddOnClickHandler;
    }
  }

  // Unregister our listener for newly opened windows.
  ExtensionSupport.unregisterWindowListener(extension.id);  
}
```

This code is based on the [Activity Manager Experiment Example](https://github.com/thunderbird/webext-examples/tree/master/manifest\_v2/experiment.activityManager).

### Custom WebExtension events

So far we have only discussed Experiments which perform a direct action _inside_ the Experiment implementation. To move as much code out of the Experiment implementation, we can send a standard WebExtension event and let any follow-up action be handled by the WebExtension.

A common use case is a custom button added to Thunderbird's UI through an Experiment. The action which is triggered by clicking on the button should not be handled in the Experiment, but by the WebExtension background script, which has registered a listener for that button being pressed. For this to work we need to define an EventEmitter in the Experiment:

```javascript
// An EventEmitter has the following basic functions:
// * EventEmitter.on(emitterName, callback): Registers a callback for a
//   custom emitter.
// * EventEmitter.off(emitterName, callback): Unregisters a callback for a
//   custom emitter.
// * EventEmitter.emit(emitterName, ...args): Emit a custom emitter, all
//   provided args will be forwarded to the registered callbacks.
const emitter = new ExtensionCommon.EventEmitter();
```

The boilerplate to declare a WebExtension event is as follows:

```javascript
getAPI(context) {
  return {
    ActivityManager: {
      onCommand: new ExtensionCommon.EventManager({
        context,
        name: "ActivityManager.onCommand",
        register(fire) {
          function callback(event, x, y) {
            // Let the event return the coordinates of the click.
            return fire.async(x, y);
          }

          emitter.on("activity-manager-command", callback);
          return function () {
            emitter.off("activity-manager-command", callback);
          };
        },
      }).api(),
    },
  };
}
```

This connects the internals of the WebExtension event to the defined `EventEmitter`. The defined callback function has the `x` and `y` parameters, which are passed through to `fire.async()`, transmitting them to the WebExtension:

```javascript
browser.ActivityManager.onCommand.addListener((x, y) => {
  console.log(`Received an onCommand event with parameters: (${x},${y}).`)
});
```

The remaining missing part is to actually trigger the EventEmitter and force its callback to be executed:

```javascript
onStartup() {
  const { extension } = this;

  // Register a listener for newly opened activity windows.
  ExtensionSupport.registerWindowListener(extension.id, {
    chromeURLs: [
      "chrome://messenger/content/activity.xhtml",
    ],
    onLoadWindow(window) {
      // Add our event listener.
      window._exampleAddOnClickHandler = (e) => {
        emitter.emit("activity-manager-command", e.clientX, e.clientY);
      }
      window.document.getElementById("clearListButton").addEventListener(
        "click",
        window._exampleAddOnClickHandler
      );
    },
  });
}
```

This is the example from the previous section, but this time we emit the `activity-manager-command` event instead of just logging a message. The [Activity Manager Experiment Example](https://github.com/thunderbird/webext-examples/tree/master/manifest\_v2/experiment.activityManager) will provide the full overview.

## Replacing various discontinued features within Experiment code

Many parts of XUL are discontinued, and there are some other changes that prevent legacy code to run unchanged in an Experiment API. A separate article deals with these changes:

{% content-ref url="changes.md" %}
[changes.md](changes.md)
{% endcontent-ref %}

## Additional Tips

{% content-ref url="../../resources/tips-and-tricks.md" %}
[tips-and-tricks.md](../../resources/tips-and-tricks.md)
{% endcontent-ref %}
