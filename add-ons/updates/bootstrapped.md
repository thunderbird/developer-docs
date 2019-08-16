# Convert Bootstrapped Extension to MailExtension

## Switch to JSON manifest

You _must_ switch from an RDF manifest \(`install.rdf`\) to a JSON manifest \(`manifest.json`\). Here is a basic example. This RDF manifest:

```markup
<?xml version="1.0" encoding="utf-8"?>
<RDF xmlns="http://www.w3.org/1999/02/22-rdf-syntax-ns#"
     xmlns:em="http://www.mozilla.org/2004/em-rdf#">
  <Description about="urn:mozilla:install-manifest">
    <em:id>myextension@sample.extensions.thunderbird.net</em:id>
    <em:type>2</em:type>
    <em:bootstrap>true</em:bootstrap>
    <em:name>Extension</em:name>
    <em:description>Does a thing!</em:description>
    <em:version>1.0</em:version>
    <em:optionsURL>chrome://myextension/content/options.xul</em:optionsURL>
    <em:targetApplication>
      <Description>
        <em:id>{3550f703-e582-4d05-9a08-453d09bdfdc6}</em:id>
        <em:minVersion>60.0</em:minVersion>
        <em:maxVersion>60.*</em:maxVersion>
      </Description>
    </em:targetApplication>
  </Description>
</RDF>
```

Becomes this JSON manifest:

```javascript
{
  "manifest_version": 2,
  "applications": {
    "gecko": {
      "id": "myextension@sample.extensions.thunderbird.net",
      "strict_min_version": "68.0"
    }
  },
  "name": "Extension",
  "description": "Does a thing",
  "version": "2.0",

  "legacy": {
    "type" : "bootstrap",
    "options": {
      "page": "chrome://myextension/content/options.xul",
      "open_in_tab": true
    }
  }
}
```

The `legacy` key enables Thunderbird’s legacy support, for bootstrap extension you have to set the `type` key to `bootstrap`.

The shown example also specifies an optional `options` key to define the options page. The key `open_in_tab` is optional and defaults to a value of`false`. A value of`true` corresponds to optionsType 3 in the RDF manifest.

{% hint style="info" %}
This example is only in English. You probably want to use translated strings in your manifest. Read [this MDN article about it](https://developer.mozilla.org/en-US/docs/Mozilla/Add-ons/WebExtensions/Internationalization#Internationalizing_manifest.json). Unfortunately that means you now need two sets of translated strings, one \(that you already have\) for your extension and another for the manifest.
{% endhint %}

## Possible timing issues

The changes made in Thunderbird for bootstrapped add-ons to use `manifest.json` may have changed when your code runs relative to events or notifications you've been listening for.

* Use the window mediator or window watcher services to be notified about opening and closing windows, rather than listening for notifications.
* Wherever you access a window, always check if it has been completely loaded \(`document.readyState == "complete"`\), or otherwise, wait for the load event.

In the following example, `loadIntoWindow` is waiting for the window to be fully loaded and eventually calls `loadIntoWindowAfterWindowIsReady` to actually do something with it. There is no need to listen to any other load events outside of `loadIntoWindow`. This example also checks the state of already open windows during startup \(line 14\).

{% code-tabs %}
{% code-tabs-item title="bootstrap.js" %}
```javascript
var { Services } = ChromeUtils.import("resource://gre/modules/Services.jsm");

function install(data, reason) {
}

function uninstall(data, reason) {
}

function startup(data, reason) {
  // Check if the window we want to modify is already open.
  let windows = Services.wm.getEnumerator("mail:3pane");
  while (windows.hasMoreElements()) {
    let domWindow = windows.getNext().QueryInterface(Ci.nsIDOMWindow);
    WindowListener.loadIntoWindow(domWindow);
  }

  // Wait for any new windows to open.
  Services.wm.addListener(WindowListener);
}

function shutdown(data, reason) {
  // When the application is shutting down we normally don't have to clean
  // up any UI changes made.
  if (reason == APP_SHUTDOWN) {
    return;
  }

  let windows = Services.wm.getEnumerator("mail:3pane");
  while (windows.hasMoreElements()) {
    let domWindow = windows.getNext().QueryInterface(Ci.nsIDOMWindow);
    WindowListener.unloadFromWindow(domWindow);
  }

  // Stop listening for any new windows to open.
  Services.wm.removeListener(WindowListener);
}

var WindowListener = {

  async loadIntoWindow(window) {
    console.log("load (1/2): " + window.document.readyState);
    if (window.document.readyState != "complete") {
      // Make sure the window load has completed.
      await new Promise(resolve => {
        window.addEventListener("load", resolve, { once: true });
      });
    }

    this.loadIntoWindowAfterWindowIsReady(window);
  },

  loadIntoWindowAfterWindowIsReady(window) {
    console.log("load (2/2): " + window.document.readyState);
    let document = window.document;

    // Take any steps to add UI or anything to the window
    // document.getElementById() etc. will work here.
  },

  unloadFromWindow(window) {
    console.log("unload: " + window.document.readyState);
    let document = window.document;

    // Take any steps to remove UI or anything from the window
    // document.getElementById() etc. will work here.
  },

  // nsIWindowMediatorListener functions
  onOpenWindow(xulWindow) {
    // A new window has opened.
    let domWindow = xulWindow.QueryInterface(Ci.nsIInterfaceRequestor)
                             .getInterface(Ci.nsIDOMWindow);

    // Check if the opened window is the one we want to modify.
    if (domWindow.document.documentElement
                 .getAttribute("windowtype") === "mail:3pane") {
      this.loadIntoWindow(domWindow);
    }
  },

  onCloseWindow(xulWindow) {
  },

  onWindowTitleChange(xulWindow, newTitle) {
  },
};

```
{% endcode-tabs-item %}
{% endcode-tabs %}

So you basically have to rename your current `loadIntoWindow` to `loadIntoWindowAfterWindowIsReady` and add the new asynchronous `loadIntoWindow` , to make sure to access the window only after it has been fully loaded.

