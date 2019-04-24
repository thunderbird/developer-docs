# List of changes in core up to Thunderbird 68

This document tries to cover all the changes that may by needed to make add-ons compatible with Thunderbird 68. If you find stuff that is no longer working but is not yet on this list, ask for help and advice in the mozilla.dev.apps.thunderbird newsgroup or check our [communication channels](https://wiki.mozilla.org/Thunderbird/CommunicationChannels#If_you.27re_a_developer).

The changes are grouped by category and are listed in the order we became aware of them.

## Removed global Variables

A bunch of globals in the message composition window were removed: 

* `nsIMsgCompDeliverMode`
* `nsIMsgCompSendFormat`
* `nsIMsgCompConvertible`
* `nsIMsgCompType`
* `nsIMsgCompFormat`
* `nsIAbPreferMailFormat`
* `nsIPlaintextEditorMail`
* `nsISupportsString`
* `mozISpellCheckingEngine`

If you use any of these, there are available as the same names underneath`Components.interfaces.`

## Removed XBL bindings

XBL is on death row. Many XBL bindings have been replaced or simply no longer exist. The remainder are being removed. This may result in slight behaviour changes for some UI components.

With [this](https://bugzilla.mozilla.org/buglist.cgi?o1=equals&v1=1484976&f1=blocked) query, you can see all the bugs related to de-xbl-ing Thunderbird, and see how the removal of each binding is handled.

If you have your own XBL bindings, you should get rid of them. Mostly the Firefox and Thunderbird teams are using [custom elements](https://developer.mozilla.org/en-US/docs/Web/Web_Components/Custom_Elements) instead.

## Removed XUL elements

Some XUL elements \(or some of their attributes\) no longer exist and must be replaced by an HTML element or some other XUL element. It does not matter, if you use these elements in a XUL file \(as in overlay extensions\) or create them via JavaScript \(as in bootstrapped extensions\). 

In order to use HTML elements in a XUL file, you must load the HTML namespace into your overlay or dialog:

```markup
<dialog
    xmlns:html="http://www.w3.org/1999/xhtml"
    xmlns="http://www.mozilla.org/keymaster/gatekeeper/there.is.only.xul"/>
```

The following list also includes deprecated elements, which could still be used but Thunderbird has already started to purge their usage. In that case you must update your overlay files which are overlaying such elements, otherwise your overlay will not be applied. Check [SearchFox](https://searchfox.org/comm-central/source) for the current state of the files you are overlaying.

{% hint style="info" %}
The replacements listed here might work in subtly different ways. Check your functionality!
{% endhint %}

### &lt;colorpicker&gt;

  Removed. Use 

```markup
<html:input type="color"> 
```

### &lt;progressmeter&gt;

Removed. Use

```markup
<html:progress max="100">
```

### &lt;listbox&gt;, &lt;listitem&gt;, &lt;listcell&gt; and &lt;listcols&gt;

All `<listbox>` related elements have been removed. Use[`<richlistbox>`](https://developer.mozilla.org/en-US/docs/Mozilla/Tech/XUL/richlistbox) instead. A `<richlistbox>` does not support cells or columns, just one `<richlistitem>` per row \(which can contain multiple other elements like `hbox`, `vbox`, `label`or `image` elements\)

Furthermore, dedicated `listbox`methods have been removed and can be replaced as follows:

* `.insertItemAt(index)`:

```javascript
let newNode = document.createElement("richlistitem");
...
let refNode = listbox.getItemAtIndex(index);
refNode.parentNode.insertBefore(newNode, refNode);
```

* `.removeItemAt(index)`:

```javascript
listbox.getItemAtIndex(index).remove()
```

### &lt;textbox type="number"&gt;

Removed. Use

```markup
<html:input type="number">
```

### &lt;textbox multiline="true"&gt;

Removed. Use

```markup
<html:textarea>
```

### &lt;prefwindow&gt;, &lt;prefpane&gt;, &lt;preferences&gt; and &lt;preference&gt;

All preference related XUL elements have been removed. If you have something like this in your add-on:

{% code-tabs %}
{% code-tabs-item title="preferences.xul" %}
```markup
<?xml version="1.0"?>
<?xml-stylesheet type="text/css" href="chrome://global/skin/"?>
<?xml-stylesheet type="text/css" href="chrome://messenger/skin/preferences/preferences.css"?>
<!DOCTYPE prefwindow SYSTEM "chrome://path/to/locale.dtd">

<prefwindow 
   id="appPreferences"
   xmlns="http://www.mozilla.org/keymaster/gatekeeper/there.is.only.xul">
  <prefpane 
     id="pane1" 
     label="&title">
    <preferences>
      <preference 
         id="pref1" 
         name="extensions.nameOfAddon.pref1" 
         type="bool"/>
      <preference 
         id="pref2" 
         name="extensions.nameOfAddon.pref2" 
         type="string"/>
    </preferences>
     
    <checkbox
       id="checkbox1" 
       preference="pref1"
       label="&checkbox1.label;"
       accesskey="&checkbox1.accesskey;"/>
    <textbox 
      id="textbox1" 
      preference="pref2"/>       
  </prefpane>
</prefwindow>
```
{% endcode-tabs-item %}
{% endcode-tabs %}

it must be replaced by a `dialog` as follows:

{% code-tabs %}
{% code-tabs-item title="preferences.xul" %}
```markup
<?xml version="1.0"?>
<?xml-stylesheet type="text/css" href="chrome://global/skin/"?>
<?xml-stylesheet type="text/css" href="chrome://messenger/skin/preferences/preferences.css"?>
<!DOCTYPE dialog SYSTEM "chrome://path/to/locale.dtd">

<dialog	
   id="appPreferences"
   buttons="accept"
   xmlns="http://www.mozilla.org/keymaster/gatekeeper/there.is.only.xul">
  <vbox>
    <checkbox 
       id="checkbox1" 
       preference="extensions.nameOfAddon.pref1"
       label="&checkbox1.label;" 
       accesskey="&checkbox1.accesskey;"/>
    <textbox 
       id="textbox1" 
       preference="extensions.nameOfAddon.pref2"/>
  </vbox>          
  
  <script 
     src="chrome://global/content/preferencesBindings.js" 
     type="application/javascript"/>
  <script 
     src="chrome://path/to/preferences.js" 
     type="application/javascript"/>          
</dialog>
```
{% endcode-tabs-item %}
{% endcode-tabs %}

Note that the `DOCTYPE` changed and the `preference` attribute now contains the full ID of the preference. If you used more than one `prefpane` you need to rework the UI into [tabs](https://developer.mozilla.org/de/docs/Mozilla/Tech/XUL/tabbox).

Furthermore, note the included JavaScript file [`preferencesBindings.js`](https://searchfox.org/mozilla-central/source/toolkit/content/preferencesBindings.js) at the bottom, which is mandatory to recreate the functionality of the `preference` attribute. It is also mandatory, that you include a custom JavaScript file \(as`preferences.js` in the above example\) afterwards, which defines the types of the used preferences \(which was formerly done inside the `preferences` tag\). The file can be as short as this:

{% code-tabs %}
{% code-tabs-item title="preferences.js" %}
```javascript
Preferences.addAll([
	{ id: "extensions.nameOfAddon.pref1", type: "bool" },
	{ id: "extensions.nameOfAddon.pref2", type: "unichar" },
]);
```
{% endcode-tabs-item %}
{% endcode-tabs %}

Per default, all preferences will be saved instantly after they have been changed. If you want to postpone saving until the user clicks the OK button, add a `type` attribute and a cancel button to the dialog:

{% code-tabs %}
{% code-tabs-item title="preferences.xul" %}
```markup
<dialog	
   id="appPreferences"
   type="child"
   buttons="accept, cancel"
   xmlns="http://www.mozilla.org/keymaster/gatekeeper/there.is.only.xul">
```
{% endcode-tabs-item %}
{% endcode-tabs %}

{% hint style="info" %}
If you are doing or plan to do any advanced stuff with the preferences in JavaScript, like validating user entered values and such, it is recommended to abandon the usage of the `preference` attribute \(and `preferencesBindings.js`\) and directly use the [preferences service](https://developer.mozilla.org/en-US/docs/Mozilla/Tech/XPCOM/Reference/Interface/nsIPrefService) instead. Check out [options.xul](https://github.com/darktrojan/shrunked/blob/master/content/options.xul) and [options.js](https://github.com/darktrojan/shrunked/blob/master/content/options.js) of the Shrunked Image Resizer.
{% endhint %}

### &lt;stringbundleset&gt; and &lt;stringbundle&gt;

Both elements have been removed. To load and access your own string property files, include the following in your JavaScript:

```javascript
let bundle = Services.strings.createBundle("chrome://path/to/your/string.property");
let stringProp = bundle.GetStringFromName("...");
```

### **&lt;statusbar&gt; and &lt;statusbarpanel&gt;**

Both elements are deprecated and its usage in Thunderbird is removed. They can be replaced by `hbox`elements with an appropriate `class` identifier:

```markup
<hbox class="statusbar">
  <label class="statusbarpanel"
         context="..."
         popup="..."
         value="label" />
</hbox>
```

You may actually use other elements besides `hbox` as a replacement for the `statusbarpanel`, like `label` or `image`.

### &lt;menulist editable="true"&gt;

The XUL element `menulist` no longer supports the `editable` attribute. However, editable menulists have been re-implemented as [custom elements](https://developer.mozilla.org/en-US/docs/Web/Web_Components/Custom_Elements). To be able to use it, you need some extra files to be linked from your document:

```markup
<?xml version="1.0"?>
<?xml-stylesheet type="text/css" href="chrome://global/skin/global.css"?>
<!-- New stylesheet needed: -->
<?xml-stylesheet type="text/css" href="chrome://messenger/skin/menulist.css"?>
<!-- Annoyingly, in Thunderbird 66, you want chrome://messenger/content/menulist.css. -->

<page xmlns="http://www.mozilla.org/keymaster/gatekeeper/there.is.only.xul">
  <!-- New script needed: -->
  <script type="application/javascript" src="chrome://messenger/content/customElements.js"/>

  <!-- Note the additional "is" attribute: -->
  <menulist is="menulist-editable" editable="true">
    <menupopup>
      <menuitem value="foo" label="foo"/>
      <menuitem value="bar" label="bar"/>
    </menupopup>
  </menulist>
</page>
```

An editable menulist can also be created via JavaScript:

```javascript
let menulist = document.createElement("menulist", { is : "menulist-editable" });
menulist.setAttribute("is", "menulist-editable");
menulist.setAttribute("editable", "true");
```

## Changed event behavior

### &lt;dialog&gt; events

Previously, if you had a `<dialog>` and you wanted to respond to the buttons being pressed, you’d have something like this:

```markup
<dialog buttons="accept,cancel"
        ondialogaccept="return onAccept();">
  <!-- dialog contents -->
</dialog>
```

The event handler would return true if the dialog should close, or false to prevent closing. This no longer works. Instead, add the event handlers in JavaScript:

```javascript
document.addEventListener("dialogaccept", function(event) {
  event.preventDefault(); // Prevent the dialog closing.
});
```

No return value is needed. 

### &lt;wizard&gt; and &lt;wizardpage&gt; events

The section about `<dialog>` events also applies to all `onwizard…` events on `<wizard>`, and `page…` events on `<wizardpage>`.

## Changes for JavaScript modules

### Renamed JavaScript modules

A number of Javascript modules have been renamed with the `.jsm` extension. Most notably:

* `mailServices.js` has been renamed to `MailServices.jsm`. This change was originally backwards-compatible with a deprecation warning, but the changes to module importing \(see [below](list-of-changes-in-thunderbird-core.md#changed-javascript-module-import)\) made that pointless and the old file has now been removed completely.
* `MailUtils.js` is now `MailUtils.jsm`.
  * `MailUtils.getFolderForUri` was renamed to `MailUtils.getExistingFolder`.

### Changed import of JavaScript modules

In Thunderbird 67, a major backwards-incompatible change was made to importing JavaScript modules. Where once you used any of these:

```javascript
Components.utils.import("resource://foo/modules/Foo.jsm");
// or…
Cu.import("resource://foo/modules/Foo.jsm");
// or…
ChromeUtils.defineModuleGetter(this, "Foo", "resource://foo/modules/Foo.jsm");
```

Or the two-argument variation:

```javascript
var { Foo } = Cu.import("resource://foo/modules/Foo.jsm", null);
// or…
var scope = {}; Cu.import("resource://foo/modules/Foo.jsm", scope); // scope.Foo…
```

You should now do this:

```javascript
var { Foo } = ChromeUtils.import("resource://foo/modules/Foo.jsm");
// or…
var scope = ChromeUtils.import("resource://foo/modules/Foo.jsm"); // scope.Foo…
```

`ChromeUtils.import` is a replacement for `Components.utils.import` \(which was also changed in this way\). Note that no second argument is supplied. The returned object is a dictionary of only the objects listed in `EXPORTED_SYMBOLS`.

## Changed API

### AddonManager

All methods of the [AddonManager](https://developer.mozilla.org/en-US/docs/Mozilla/JavaScript_code_modules/Add-on_Manager/AddonManager#Method_Overview) API now return a Promise instead of executing a callback. Instead of calling it as

```javascript
AddonManager.getAddonByID(addon_id, callback_function)
```

you need to call it as 

```javascript
AddonManager.getAddonByID(addon_id).then(callback_function)
```

### nsIStringBundleService

Removed. Use 

```javascript
Services.strings.createBundle("chrome://...")
```

### **nsIStreamListener**

The `onDataAvailable` method lost its `context` argument. This was removed in [bug 1525319](https://bugzilla.mozilla.org/show_bug.cgi?id=1525319) which breaks the API.

To be backward compatible you need to probe the parameters. In case the third parameter is an nsIInputStream it is the old API. If the second one is an input stream it is the new API.

```javascript
onDataAvailable(...args) {
  // The old API passes the stream as third parameter
  if (args[2] instanceof Ci.nsIInputStream)
    return this.onOldDataAvailableCalled(args[2], args[3], args[4]);

  // The new API uses the second parameter
  if (args[1] instanceof Ci.nsIInputStream)
    return this.onNewDataAvailableCalled(args[1], args[2], args[3]);

  throw new Error("Unknown signature for nsIStreamListener.onDataAvailable()");
}
```

### **nsIRequestObserver**

The `onStartRequest` and `onStopRequest` methods also no longer have a `context` argument, which could be detected in a similar way.

### **nsIProtocolHandler**

The obsolete method `newChannel` was removed and `newChannel2` was renamed to `newChannel`. \([Bug 1528971](https://bugzilla.mozilla.org/show_bug.cgi?id=1528971).\)

As `newChannel` has been unused for a long time it should be safe to just replace the old `newChannel` implementation with the `newChannel2` and forward calls from `newChannel2`.

```javascript
// Change the signature to the new one...
// ... you'll need to add the loadInfo parameter
//
// Note loadInfo may be null in Thunderbird 60.
newChannel(URI, loadInfo) {
  // Do your logic here
}

// Keep the old method as it will be needed for backward compatibility...
// ... and forward the request to the new method.
newChannel2(URI, loadInfo) {
  return this.newChannel(URI, loadInfo);
}
```

