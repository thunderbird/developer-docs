# Adapt to Changes in Thunderbird 61-68

This document tries to cover all the changes that may by needed to make add-ons compatible with Thunderbird 68. If you find stuff that is no longer working but is not yet on this list, ask for help and advice in the mozilla.dev.apps.thunderbird newsgroup or check our [communication channels](https://wiki.mozilla.org/Thunderbird/CommunicationChannels#If_you.27re_a_developer).

The changes are grouped by category and are listed in the order we became aware of them.

## Removed global Variables

A bunch of global variables available in some window scopes were removed. If you have used any of these, there are still available as the same names underneath`Components.interfaces.`

### main window

* nsMsgFolderFlags

### message composition window

* `nsIMsgCompDeliverMode`
* `nsIMsgCompSendFormat`
* `nsIMsgCompConvertible`
* `nsIMsgCompType`
* `nsIMsgCompFormat`
* `nsIAbPreferMailFormat`
* `nsIPlaintextEditorMail`
* `nsISupportsString`
* `mozISpellCheckingEngine`

## Removed XBL bindings

XBL is on death row. Many XBL bindings have been replaced or simply no longer exist. The remainder are being removed. This may result in slight behaviour changes for some UI components.

With [this query](https://bugzilla.mozilla.org/buglist.cgi?o1=equals&v1=1484976&f1=blocked), you can see all the bugs related to de-XBL-ing Thunderbird, and see how the removal of each binding is handled.

{% hint style="info" %}
If you have your own XBL bindings, you can convert them to [custom elements](https://developer.mozilla.org/en-US/docs/Web/Web_Components/Custom_Elements). Here are some notes on the process of[ converting an XBL binding into a custom element](https://wiki.mozilla.org/Thunderbird/de-xbl).
{% endhint %}

{% hint style="info" %}
As part of the de-XBL effort, the usage of the [nsIDOMDocumentXBL](https://developer.mozilla.org/en-US/docs/Archive/Mozilla/XBL/XBL_1.0_Reference/DOM_Interfaces) Interface has also been deprecated. This includes:

* getAnonymousElementByAttribute\(\)
* getAnonymousNodes\(\)

They have to be replaced by [`document.getElementById()`](https://developer.mozilla.org/en-US/docs/Web/API/Document/getElementById), [`document.querySelectorAll()`](https://developer.mozilla.org/en-US/docs/Web/API/Element/querySelectorAll) or similar methods. The documents these methods have been used with have probably changed dramatically. Check out [searchfox.org](https://searchfox.org/) to learn about the current layouts.
{% endhint %}

## Removed XUL elements

Some XUL elements \(or some of their attributes\) no longer exist and must be replaced by an HTML element or some other XUL element. It does not matter, if you use these elements in a XUL file \(as in overlay extensions\) or create them via JavaScript \(as in bootstrapped extensions\).

In order to use HTML elements in a XUL file, you must load the HTML namespace into your overlay or dialog:

```markup
<dialog
    xmlns:html="http://www.w3.org/1999/xhtml"
    xmlns="http://www.mozilla.org/keymaster/gatekeeper/there.is.only.xul">
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

Furthermore, a few dedicated `listbox/richtlistbox`methods have been removed and can be replaced as follows:

* `listbox.appendItem(label, value)`:

```javascript
let newNode = document.createXULElement("richlistitem");

// Store the value in the list item as before.
newNode.value = value; 
let newLabel = document.createXULElement("label");
// The label is now stored in the value attribute of the label element.
newLabel.value = label;

newNode.appendChild(newLabel);
listbox.appendChild(newNode);
```

* `listbox.insertItemAt(index, label, value)`:

```javascript
let newNode = document.createXULElement("richlistitem");

// See above example.

let refNode = listbox.getItemAtIndex(index);
refNode.parentNode.insertBefore(newNode, refNode);
```

* `listbox.removeItemAt(index)`:

```javascript
listbox.getItemAtIndex(index).remove();
```

### &lt;textbox multiline="true"&gt;

Removed. Use

```markup
<html:textarea>
```

### &lt;prefwindow&gt;, &lt;prefpane&gt;, &lt;preferences&gt; and &lt;preference&gt;

All preference related XUL elements have been removed. If you have something like this in your add-on:

{% tabs %}
{% tab title="preferences.xul" %}
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
{% endtab %}
{% endtabs %}

it must be replaced by a `dialog` as follows:

{% tabs %}
{% tab title="preferences.xul" %}
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
{% endtab %}
{% endtabs %}

Note that the `DOCTYPE` changed and the `preference` attribute now contains the full ID of the preference. If you used more than one `prefpane` you need to rework the UI into [tabs](https://developer.mozilla.org/de/docs/Mozilla/Tech/XUL/tabbox).

Furthermore, note the included JavaScript file [`preferencesBindings.js`](https://searchfox.org/mozilla-central/source/toolkit/content/preferencesBindings.js) at the bottom, which is mandatory to recreate the functionality of the `preference` attribute. It is also mandatory, that you include a custom JavaScript file \(as`preferences.js` in the above example\) afterwards, which defines the types of the used preferences \(which was formerly done inside the `preferences` tag\). The file can be as short as this:

{% tabs %}
{% tab title="preferences.js" %}
```javascript
Preferences.addAll([
    { id: "extensions.nameOfAddon.pref1", type: "bool" },
    { id: "extensions.nameOfAddon.pref2", type: "string" },
    { id: "extensions.nameOfAddon.pref3", type: "int" },
]);
```
{% endtab %}
{% endtabs %}

Per default, all preferences will be saved instantly after they have been changed. If you want to postpone saving until the user clicks the OK button, add a `type` attribute and a cancel button to the dialog:

{% tabs %}
{% tab title="preferences.xul" %}
```markup
<dialog    
   id="appPreferences"
   type="child"
   buttons="accept, cancel"
   xmlns="http://www.mozilla.org/keymaster/gatekeeper/there.is.only.xul">
```
{% endtab %}
{% endtabs %}

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
let menulist = document.createXULElement("menulist", { is : "menulist-editable" });
menulist.setAttribute("is", "menulist-editable");
menulist.setAttribute("editable", "true");
```

### &lt;menulist&gt; and &lt;menupopup&gt;

Even though the `menupopup` element is defined as a direct child of the `menulist` element in the above example, it cannot be accessed by

```javascript
menuListElement.firstChild
```

anymore. You may assign its own ID to the `menupop` element, or get it by

```text
menuListElement.getElementsByTagName("menupopup")
```

You may use the inspector of the developer tools to see the full DOM structure of the `menulist` element.

### &lt;groupbox&gt; and &lt;caption&gt;

These two do not display as before. You now need to include the following css file:

```markup
<?xml-stylesheet type="text/css" href="chrome://messenger/skin/messenger.css"?>
```

While the `groupbox` tag continues to work, the `caption` tag has been removed. Use the following now:

```markup
<groupbox>
  <hbox class="groupbox-title">
    <label class="header">Your caption label</label>
  </hbox>
  Your groupbox body content.
</groupbox>
```

{% hint style="info" %}
In the current Thunderbird 68 Beta, this still looks a bit wrong, but will be fixed in Beta 3. More details can be found in [bug 1559964](https://bugzilla.mozilla.org/show_bug.cgi?id=1559964).
{% endhint %}

### &lt;datepicker&gt; and &lt;timepicker&gt;

Removed. Currently there is no working replacement part of Thunderbird itself, but Lightning has its own `datetimepicker`, which can be used.

```markup
<datetimepicker id="myDateTimePicker" />
```

Its value is a JavaScript `Date` object:

```javascript
document.getElementById("myDateTimePicker").value = new Date('December 17, 1995 03:24:00');
```

To be able to use the `datetimepicker`, the following CSS files need to be included:

```markup
<?xml-stylesheet type="text/css" href="chrome://calendar-common/skin/widgets/minimonth.css"?>
<?xml-stylesheet type="text/css" href="chrome://calendar/content/widgets/calendar-widget-bindings.css"?>
<?xml-stylesheet type="text/css" href="chrome://lightning-common/skin/datetimepickers.css"?>
```

The following JavaScript files also need to be included:

```text
<script src="chrome://calendar/content/calendar-ui-utils.js"/>
<script src="chrome://messenger/content/customElements.js"/>
<script src="chrome://calendar/content/datetimepickers/datetimepickers.js"/>
```

If you do not want to be dependent on Lightning being installed, you need to include the above files with your add-on.

### &lt;notificationbox&gt;

Removed. Thunderbird now has fixed build in notification boxes, where notifications can be added. The following list shows how to access some of them:

* Main window:  `let notifyBox = specialTabs.msgNotificationBar;`
* Message composer window:  `let notifyBox = gNotification.notificationbox;`
* Below the receipient list in the message composer window:  `let notifyBox = gMessageNotificationBar.msgNotificationBar;`
* Most calendar dialogs: `let notifyBox = gNotification.notificationbox;`

{% hint style="warning" %}
Since you no longer "own" notification boxes, you should not clear them by calling

`notifyBox.removeAllNotifications();`

as that would remove notifications added by others. You can get a specific notification by calling

`let notification = notifyBox.getNotificationWithValue(value);`

and remove only that via

`notificationbox.removeNotification(notification);`
{% endhint %}

You can still add notification boxes wherever you want, if you do not want to use the build in notification boxes \(or if there is none\). More details can be found [here](https://github.com/thundernest/developer-docs/issues/31#issuecomment-508872843).

### &lt;textbox type="search"&gt;

Removed. Use:

```text
<textbox is="search-textbox" class="searchBox">
```

## Renamed XUL elements

### &lt;mail-multi-emailHeaderField&gt;

The element `mail-multi-emailHeaderField` has been renamed into `mail-multi-emailheaderfield` \(no camelCase anymore\).

See e.g. [https://searchfox.org/comm-central/source/mail/base/content/msgHdrView.inc.xul\#238](https://searchfox.org/comm-central/source/mail/base/content/msgHdrView.inc.xul#238)

```text
<mail-multi-emailheaderfield id="expandedfromBox" flex="1"/>
```

## Changed XUL elements

### &lt;treecol&gt;

Each `treecol` can be either shown or hidden via the column picker. Up to TB68 the column picker closed after a column had been selected/toggled. By adding `closemenu="none"` to a `treecol`, the column picker stays open after the display state of associated `treecol` has been toggled.

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

To prevent closing of the dialog, call `preventDefault()`. A return value is not needed.

This is valid for `ondialogaccept`, `ondialogextra1`, `ondialogextra2` and `ondialogcancel`.

### &lt;wizard&gt; and &lt;wizardpage&gt; events

The section about `<dialog>` events also applies to all `onwizard…` events on `<wizard>`, and `onpage…` events on `<wizardpage>`.

## Changes to Geko JavaScript Engine

As JavaScript itself evolves, browser engines will change accordingly. The following changes represent changes that affect Javascript extension developers.

### String Generic Methods Deprecated

The nonstandard generic string methods have been deprecated and removed in the Geko engine and therefore Thunderbird 68+. A deprecation exception is issued if any methods are used. The updated paradigm uses String instance methods allowing for the use on any object.

Deprecated Generics Syntax:

```javascript
var strWithPadding = "   Hello There   "; 
String.trim(strWithPadding);
```

Instance Method Replacement:

```javascript
var strWithPadding = "   Hello There   "; 
strWithPadding.trim();
```

The following String methods are affected by the change:

```javascript
  contains(), substring(), toLowerCase(), toUpperCase(), charAt(),
  charCodeAt(), indexOf(), lastIndexOf(), startsWith(), endsWith(),
  trim(), trimLeft(), trimRight(), toLocaleLowerCase(), normalize(),
  toLocaleUpperCase(), localeCompare(), match(), search(), slice(),
  replace(), split(), substr(), concat(), localeCompare()
```

\(All other String methods use the instance paradigm already\)

## Changes for JavaScript modules

### Renamed JavaScript modules

A number of Javascript modules have been renamed with the `.jsm` extension. Most notably:

* `mailServices.js` has been renamed to `MailServices.jsm`. This change was originally backwards-compatible with a deprecation warning, but the changes to module importing \(see [below](changes.md#changed-javascript-module-import)\) made that pointless and the old file has now been removed completely.
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

### LoginManager

The function to retrieve passwords has lost its first parameter. Instead of

```text
var logins = Services.logins.findLogins({}, origin, formOrigin, realm);
```

call it as

```text
var logins = Services.logins.findLogins(origin, formOrigin, realm);
```

### document.persist\(id, attribute\)

Removed, use:

```text
Services.xulStore.persist(node, attribute);
```

Note: document.persist\(\) used the ID attribute whereas xulStore.persist\(\) uses the actual DOM node. More details in bug [1476678](https://bugzilla.mozilla.org/show_bug.cgi?id=1476678).

### AddonManager

All methods of the [AddonManager](https://developer.mozilla.org/en-US/docs/Mozilla/JavaScript_code_modules/Add-on_Manager/AddonManager#Method_Overview) API now return a Promise instead of executing a callback. Instead of calling it as

```javascript
AddonManager.getAddonByID(addon_id, callback_function)
```

you need to call it as

```javascript
AddonManager.getAddonByID(addon_id).then(callback_function)
```

### nsIMsgAccountManager

The `defaultAccount` member can \(since TB 65\) have a `null` value - if for some reason it doesn't have, or can't work out, the default account.

Hence you need to null-check it, and possibly handle not having an account at all \(if in no other way than by an error message\).

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
  // The old API passes the stream as third parameter.
  if (args[2] instanceof Ci.nsIInputStream)
    return this.onOldDataAvailableCalled(args[2], args[3], args[4]);

  // The new API uses the second parameter.
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
// ... you'll need to add the loadInfo parameter.
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

### nsIScriptableUnicodeConverter

The method `convertFromByteArray` has been removed. The new preferred way to deal with unicode is through the `TextEncoder` and `TextDecoder` classes.

To convert byte arrays to different charsets, use

```javascript
let charset = 'utf-8';
let encodedByteArray = [ /* ... */ ];
let decoder = new TextDecoder(charset); // charset can be omitted, default is utf-8
let decodedString = decoder.decode(new Uint8Array(encodedByteArray));
```

### nsIDOMElement, nsIDOMNode and other basic DOM interfaces

Removed. Use `Element`, `Node`, etc. instead, which are now available in all scopes.

### nsIDOMParser, nsIDOMSerializer, nsIXMLHttpRequest

These no longer need to be created by `Cc[...].createInstance(Ci....)`, but simply via the `new` keyword:

* `new DOMParser();`
* `new XMLSerializer();`
* `new XMLHttpRequest();`

They _should_ be available in all scopes now. If not, the following is needed:

* `Cu.importGlobalProperties(["DOMParser"]);`
* `Cu.importGlobalProperties(["XMLSerializer"]);`
* `Cu.importGlobalProperties(["XMLHttpRequest"]);`

### nsITreeBoxObject, nsITreeColumn, nsITreeView

Trees have changed a lot. The `tree` object is now a `XULTreeElement`. It has lost the`treeBoxObject` property, because the `nsITreeBoxObject` has been removed. Most of its methods can now be accessed directly through the `tree` object. For example:

```javascript
// The treeBoxObject has been removed.
// tree.treeBoxObject.getLastVisibleRow();
tree.getLastVisibleRow();
```

Furthermore, `nsITreeColumn` has been replaced by the `TreeColumn` object. Even though their interfaces look the same, they could behave differently. Check your implementation, as this affects almost all methods of `nsITreeView`.

Some noteworthy changes:

* [`tree.getCellAt()`](https://dxr.mozilla.org/mozilla-central/source/dom/chrome-webidl/XULTreeElement.webidl#132) now returns a [`TreeCellInfo`](https://dxr.mozilla.org/mozilla-central/source/dom/chrome-webidl/XULTreeElement.webidl#10).
* If a method requires a `TreeColumn` parameter,  a simple `{ id: columnName }` object no longer works. Get a proper `TreeColumn` object via `tree.columns.getNamedColumn(columnName)`.
* Trees no longer support selecting individual cells. The `TreeColumn` object no longer has a `selectable` attribute and `nsITreeView` has lost its `isSelectable()` method.

In general, check [searchfox.com](https://searchfox.org/) to see the current definitions of tree related implementations:

* [XULTreeElement](https://searchfox.org/mozilla-beta/rev/b906de73ca1c11ffb73473731920f7867d364557/dom/chrome-webidl/XULTreeElement.webidl)
* [nsITreeView](https://searchfox.org/mozilla-beta/rev/b906de73ca1c11ffb73473731920f7867d364557/layout/xul/tree/nsITreeView.idl)
* [TreeColumn](https://searchfox.org/mozilla-beta/rev/b906de73ca1c11ffb73473731920f7867d364557/dom/webidl/TreeColumn.webidl)

### XPCOMUtils.generateQI\(\)

Removed. Use

```text
ChromeUtils.generateQI()
```

### Services.io.newChannelFromURI2\(\)

Renamed to

```text
Services.io.newChannelFromURI()
```

A long time ago newChannelFromURI2\(\) has been added as an alternative to newChannelFromURI\(\), which as become deprecated by now. With ESR68 the old name is used again.

