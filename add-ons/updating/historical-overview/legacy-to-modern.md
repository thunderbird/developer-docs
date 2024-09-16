# Convert legacy WebExtensions to modern WebExtensions

{% hint style="warning" %}
We do not suggest to convert older legacy bootstrapped extensions or legacy overlay extensions (as used in Thunderbird 60) directly to modern WebExtensions. They should first be converted to legacy WebExtensions.
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

## Step 2: Replace the `chrome.manifest` file

The most common entries in the `chrome.manifest` file are listed below:

```
content    myaddon                 chrome/content/
resource   myaddon                 chrome/
locale     myaddon   en-US         chrome/locale/en-US/
locale     myaddon   de-DE         chrome/locale/de-DE/

skin       myaddon   classic/1.0   chrome/skin/classic/

style      chrome://messenger/content/activity.xul   chrome://myaddon/skin/myaddon.css
overlay    chrome://messenger/content/messenger.xul  chrome://myaddon/content/messenger.xul
```

#### **content, resource and locale**

These entries registered global URLs used by the extension to access its assets. Use the [LegacyHelper](https://github.com/thunderbird/webext-support/tree/master/experiments/LegacyHelper) Experiment to register `content`, `resource` and `locale` entries. To replicate the entries shown in the previous example, add the following to your background script:

```javascript
await browser.LegacyHelper.registerGlobalUrls([
  ["content",  "myaddon", "chrome/content/"],
  ["resource", "myaddon", "chrome/"],
  ["locale",   "myaddon", "en-US", "chrome/locale/en-US/"],
  ["locale",   "myaddon", "de-DE", "chrome/locale/de-DE/"],  
]);
```

There are no direct equivalents to manifest flags, so add-ons now need to provide their own mechanisms to switch code or resources depending on the runtime environment. Relevant information is accessible through the [`runtime` API](https://developer.mozilla.org/en-US/docs/Mozilla/Add-ons/WebExtensions/API/runtime/).

#### **skin**

This entry type is no longer supported, it has to be replaced by a `resource` entry. In the above example we had the following `skin` definition:

```
skin       myaddon   classic/1.0   /chrome/skin/classic/
```

The `skin` folder is a subfolder of `/chrome/`, which is already available as a `resource://` URL. We can therefore replace all usages of

```
chrome://subjects_prefix_switch/skin/*
```

by

```
resource://subjects_prefix_switch/skin/classic/*
```

#### style

This entry is no longer supported, it has to be replaced by the [LegacyCSS](https://github.com/thunderbird/webext-support/tree/master/experiments/LegacyCSS) Experiment. To replicate the `style` entry shown in the previous example, add the following to your background script (note that most `*.xul` files have been renamed to `*.xhtml` files in recent versions of Thunderbird):

```javascript
// Define all CSS files for core windows.
let files = {
   "chrome://messenger/content/activity.xhtml": "style.css"
}

// Inject CSS into all open windows during add-on start (if any).
for (let [url, file] of Object.entries(files) ) {
    messenger.LegacyCSS.inject(url, file);
}

// Listen for opened windows and inject CSS.
messenger.LegacyCSS.onWindowOpened.addListener((url) => {
    if (files.hasOwnProperty(url)) {
        messenger.LegacyCSS.inject(url, files[url]);
    }
});
```

This should only be a temporary step. After the initial conversion from a `style` entry to using the LegacyCSS Experiment, the required styles should be applied by using [standard WebExtension theming support](../../web-extension-themes.md).

#### **overlay**

This entry type is no longer supported. Replacing it will be the main conversion work and is described in [step 7](legacy-to-modern.md#step-7-creating-missing-ui-entry-points-and-apis-as-experiments) and later.

#### interfaces, component, contract, category

These entries are no longer supported.

## Step 3: Replace the `/defaults/preferences/` folder

Most legacy extensions stored their preferences in an `nsIPrefBranch`, and the `/defaults/preferences/` folder contained JavaScript files with default preference values. An example default preference file could look like this:

```javascript
pref("extensions.myaddon.enableDebug", false);
pref("extensions.myaddon.retries", 5);
pref("extensions.myaddon.greeting", "Hello");
```

This file can be removed, and the default values must be set in the background script by using the [LegacyPrefs](https://github.com/thunderbird/webext-support/tree/master/experiments/LegacyPrefs) Experiment:

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

## Step 4: The XUL options dialog

The XUL options dialog is no longer registered, after the `legacy` key has been removed from `manifest.json`. We will use the [LegacyHelper ](https://github.com/thunderbird/webext-support/tree/master/experiments/LegacyHelper)Experiment to open the XUL options dialog via a menu entry in the `tools` menu. Add the following to your background script:

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

## Step 5: Converting locale files

Even though the LegacyHelper Experiment allows to register legacy locales, the technology itself is deprecated: WebExtension HTML pages cannot acces DTD or property files. Instead, they use the [i18n API](https://developer.mozilla.org/en-US/docs/Mozilla/Add-ons/WebExtensions/API/i18n) to access locales stored in simple JSON files.&#x20;

The [`localeConverter.py` python script from the webext-support repository](https://github.com/thunderbird/webext-support/tree/master/tools/locale-converter) will do most of the work to convert your locale files (DTD and property files) into the new JSON format.

The new locale data can be accessed from any WebExtension script:

```javascript
browser.i18n.getMessage("a-locale-string");
```

## Step 6: Converting the XUL options page

Instead of a XUL dialog, WebExtensions use an HTML page for their options page, which will be accessible to the user through the add-on manager. The page is registered in `manifest.json`:

```javascript
"options_ui": {
  "page": "options.html"
}
```

Javascript loaded by that `options.html` document can access all WebExtension APIs in the same way as for example the background script.

In this step the old XUL options dialog has to be re-created as an HTML page, using only HTML elements, JavaScript and CSS. It is no longer possible to use XUL elements. Some custom elements and 3rd party libraries to simplify this step can be found in the [webext-support](https://github.com/thunderbird/webext-support/tree/master/ui) repository.

It may help duringh development, that the old XUL options page can still be opend through the tools menu.

### Localisation

There is no automatic replacement of locale placeholder entities like `&myLocaleIdentifier;` in WebExtension HTML files any more. Instead, you can use placeholders like `__MSG_myLocaleIdentifier__` in your markup and include the [`i18n.js`](https://github.com/thunderbird/webext-support/tree/master/scripts/i18n) script provided by the [webext-support](https://github.com/thunderbird/webext-support/tree/master/scripts/i18n) repository and automatically replace all `__MSG_*__` locale placeholders on page load. The script is using the `i18n` API to read the modern JSON locale files created in the previous step.

### Alternative for `preferencesBindings.js`

The legacy XUL options page used a framework to automatically load and save preference values, controled by the `preferencesBindings.js` script. That automatism does not exist for HTML option pages. But it is possible to implement a similar mechanism using a `data-preference` attribute:

```html
<div>
  <input type="checkbox" id="debug" data-preference="enableDebug"/>
  <label for="debug">__MSG_debug.label__</label>
</div>
```

We can loop over all elements which have such an attribute, and load their value from storage. Additionally we can attach an event listener to store the value after the input field has been changed by the user:

```javascript
let prefElements = document.querySelectorAll('[data-preference]');
for (let prefElement of prefElements) {
    let value = await browser.LegacyPrefs.getPref(
        `extensions.myaddon.${prefElement.dataset.preference}`
    );
    
    // handle checkboxes
    if (prefElement.tagName == "INPUT" && prefElement.type == "checkbox") {
        if (value == true) {
            prefElement.setAttribute("checked", "true");
        }
        // enable auto save
        prefElement.addEventListener("change", () => {
            browser.LegacyPrefs.setPref(
                `extensions.myaddon.${prefElement.dataset.preference}`,
                prefElement.checked
            );
        })
    }
}
```

## Step 7: Find matching WebExtension entry points and WebExtension APIs

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

## Step 8: Creating missing UI entry points and APIs as Experiments

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

The following API implementation is based on the [Remove Attachments If Junk Experiment Example](https://github.com/thunderbird/webext-examples/tree/master/manifest\_v2/experiment.removeAttachmentsIfJunk):

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

An example which manipulates the main window using the same strategy is the [Restart Experiment Example](https://github.com/thunderbird/webext-examples/tree/master/manifest\_v2/experiment.restart).

#### Detect open windows/tabs through the Experiment

If the window of interest is not supported by WebExtension APIs, it is not detectable through WebExtension APIs. The detection code has to live inside an Experiment.

The following example is based on the [Activity Manager Experiment Example](https://github.com/thunderbird/webext-examples/tree/master/manifest\_v2/experiment.activityManager). Its background script triggeres the Experiment to register a global window listener, which manipulates the window of interest:

```javascript
await browser.ActivityManager.registerWindowListener();
```

The implementation of the Experiment could be is as follows:

```javascript
class ActivityManager extends ExtensionCommon.ExtensionAPI {
  getAPI(context) {
    return {
      // This key must match the class name.
      ActivityManager: {
        registerWindowListener() {
          // Register a listener for newly opened activity windows.
          ExtensionSupport.registerWindowListener(context.extension.id, {
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
        },
      },
    };
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
}
```

### Custom WebExtension events

So far we have only discussed Experiments which perform a direct action _inside_ the Experiment implementation. To move as much code out of the Experiment implementation, we can send a standard WebExtension event and let any follow-up action be handled by the WebExtension.

A common use case is a custom button added to Thunderbird's UI through an Experiment. The action which is triggered by clicking on the button should not be handled in the Experiment, but by the WebExtension background script, which has registered a listener for that button being pressed. For this to work we need to define an `EventEmitter` in the Experiment:

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

The boilerplate, which  connects the internals of a WebExtension event to the defined `EventEmitter`, is the added `EventManger` in lines 5-21 of the following example. The glue part to actually trigger the event is&#x20;

```javascript
emitter.emit("activity-manager-command", e.clientX, e.clientY);
```

in line 31:

{% code lineNumbers="true" %}
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

      registerWindowListener() {
        // Register a listener for newly opened activity windows.
        ExtensionSupport.registerWindowListener(context.extension.id, {
          chromeURLs: [
            "chrome://messenger/content/activity.xhtml",
          ],
          onLoadWindow(window) {
            // Add our event listener.
            window._exampleAddOnClickHandler = (e) => {
              console.log("The button was clicked, let's do something!")
              emitter.emit("activity-manager-command", e.clientX, e.clientY);
            }
            window.document.getElementById("clearListButton").addEventListener(
              "click",
              window._exampleAddOnClickHandler
            );
          },
        });
      },

    },
  };
}
```
{% endcode %}

The callback of the `EventEmitter` (line 9) has the `x` and `y` parameters, which are passed through to `fire.async()`, transmitting them to the WebExtension:

```javascript
browser.ActivityManager.onCommand.addListener((x, y) => {
  console.log(`Received an onCommand event with parameters: (${x},${y}).`)
});
```

A working implementation of this example can be found in the [Activity Manager Experiment Example](https://github.com/thunderbird/webext-examples/tree/master/manifest\_v2/experiment.activityManager).
