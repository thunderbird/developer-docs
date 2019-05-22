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
      "strict_min_version": "67.0a1"
    }
  },
  "name": "Extension",
  "description": "Does a thing",
  "version": "2.0",

  "legacy": {
    "type" : "bootstrap"
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

## Update handling of windows on startup

Prior to Thunderbird 68, the `startup()` function for bootstrapped extensions was called after windows were fully loaded. Therefore, it was common for bootstrapped extensions to assume that document elements they wanted to modify or augment already existed. However, bootstrapped extensions can no longer assume that the document is fully loaded when they start up.

Here's an example of how to deal with this.

```javascript
const {Services} = ChromeUtils.import("resource://gre/modules/Services.jsm");

var allInOneObserver = {
    observe: function(aSubject, aTopic, aData) {
        // Triggered by Ci.nsIWindowWatcher when a new window is created. At
        // that point the window isn't fully loaded yet, so we add an event
        // listener to handle when it is.
        aSubject.addEventListener("load", this, true);
    },
    handleEvent: function(aEvent) {
        // Triggered by the event listener added in `observe` or
        // `handleExistingWindow`.
        var document = aEvent.originalTarget;
        document.defaultView.removeEventListener("load", this, true);
        this.handleDocument(document);
    },
    handleExistingWindow: function(window) {
        // Called on startup to either add an event listener, if the window
        // isn't fully loaded yet, or do the needful if it is.
        var document = window.document;
        if (document.readyState != "complete") {
            window.addEventListener("load", this, true);
            return;
        }
        this.handleDocument(document);
    },
    handleDocument: function(document) {
        // Here's where you do what you actually want to do once the window
        // is fully loaded. Note that (as per the comment below) you should
        // check to make sure you're working with the right kind of window for
        // your extension, and if not, then just return without doing anything.
    },
}

function startup() {
    // If your extension only needs to run code in one type of window, then you
    // might wish to specify the window type here instead of `null`.
    // Alternatively, it might be easier to just check above in
    // `handleDocument` to see if the document has the expected elements, and
    // if not, then assume it's the wrong type of window and don't do anything.
    var windows = Services.wm.getEnumerator(null);
    while (windows.hasMoreElements()) {
        allInOneObserver.handleExistingWindow(windows.getNext());
    }
    Cc["@mozilla.org/embedcomp/window-watcher;1"].
        getService(Ci.nsIWindowWatcher).registerNotification(
            allInOneObserver);
}

function shutdown() {
    Cc["@mozilla.org/embedcomp/window-watcher;1"].
        getService(Ci.nsIWindowWatcher).unregisterNotification(
            allInOneObserver);
}
```
