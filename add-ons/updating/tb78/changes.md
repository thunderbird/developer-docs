# Adapt to Changes in Thunderbird 69-78

This document tries to cover all the internal changes that may by needed to make add-ons compatible with Thunderbird 78. If you find stuff that is no longer working but is not yet on this list, ask for help and advice in of our [communication channels](https://developer.thunderbird.net/#getting-plugged-into-the-community).

The changes are grouped by category and are listed in the order we became aware of them.

## Removed XUL elements

{% hint style="warning" %}
As of Thunderbird 74, the built-in overlay loader has been removed, which means XUL overlay files are no longer supported. The preferred way to interact with Thunderbird is through WebExtension and MailExtension APIs, which only support HTML/CSS. 

However, Thunderbird itself is still using XUL for its UI, and it is still possible to interact with that XUL based UI through experimental APIs. If for example you need a UI feature not yet available via MailExtension API, you can write an experimental API using a window listener and manipulate the UI as needed via JavaScript \(check out the [restart example](https://github.com/thundernest/sample-extensions/tree/master/restart)\). There are two ways to add XUL elements, either via [pure JavaScript](https://github.com/thundernest/sample-extensions/blob/master/restart/implementation.js#L23-L30) or via [parseXULToFragment\(\)](https://github.com/thundernest/addon-developer-support/blob/af11a8f7688bdfcd6467a9db8a4871fb3d081fe1/wrapper-apis/WindowListener/implementation.js#L183-L188).

Since XUL is still usable, this document includes information about deprecated and removed XUL elements.
{% endhint %}

{% hint style="warning" %}
Some XUL elements have been converted to [custom elements](https://developer.mozilla.org/en-US/docs/Web/Web_Components/Using_custom_elements), which have an additional `is="something"` attribute. If such a custom element is to be created with `createXULElement()`, the `is` parameter needs to be passend in via the second argument:

```javascript
let toolbar = document.createXULElement(
    "toolbar", 
    { is : "customizable-toolbar" }
);
toolbar.setAttribute("is", "customizable-toolbar");
```
{% endhint %}

### &lt;textbox&gt;

Removed completely in TB71. Use

```markup
<html:input>
```

All former values of the `type` parameter of the `textbox` element are supported by `html:input` as well. For proper styling include the following css file: `chrome://messenger/skin/input-fields.css`

The `flex` parameter is no longer supported and should be removed. Attach the `input-container` class to a surrounding `hbox` to force the input field to behave like a former `flex="1"` `textbox`.

If the input fields context menu is not working, you need to include two JavaScript files by adding:

```markup
<script src="chrome://global/content/globalOverlay.js"/>
<script src="chrome://global/content/editMenuOverlay.js.js"/>
```

{% hint style="info" %}
JavaScript methods that are using `element.localName == "textbox"` , `getElementsByTagName("textbox")`or similar need to be updated as well.

Visually compare the fields before and after the conversion to be sure the UI, sizing, and spacing doesn't change.
{% endhint %}

### &lt;toolbar customizable="true"&gt;

In TB78 the XUL element `toolbar` with attribute `customizable` has been re-implemented as a [custom element](https://developer.mozilla.org/en-US/docs/Web/Web_Components/Custom_Elements). It needs an additional `is` attribute. The following example is taken from the [source of the Thunderbird calender](https://searchfox.org/comm-central/rev/444b626fc442cb92b1b29ee47912600bc61bab1f/calendar/base/content/dialogs/calendar-event-dialog.xhtml#574):

```markup
<!-- Note the additional "is" attribute: -->
<toolbar is="customizable-toolbar"
   id="event-toolbar"
	 customizable="true"   
	 toolboxid="event-toolbox"
	 class="chromeclass-toolbar"
	 labelalign="end"
	 defaultlabelalign="end"
	 context="event-dialog-toolbar-context-menu"
	 defaultset="button-url,button-delete"/>
```

## Changed API

### document.createElement\(\)

With TB69, the default namespace for createElement has switched from XUL to XHTML. So you can no longer create XUL elements with `document.createElement()` \(but XHTML elements\). To create XUL elements, use:

```javascript
document.createXULElement();
```

### nsISocketTransportService

Bug [1558726](https://bugzilla.mozilla.org/show_bug.cgi?id=1558726) introduced a breaking change to the `nsISocketTransportService` interface:

The first parameter used to be an array and the second one its length. This length parameter has been dropped, causing all subsequent arguments to shift by one. Furthermore, to create a default socket, you now have to pass an empty array as first parameter, instead of null.

To stay backward compatible, check the argument count of "createTransport". In case it is 4 is is the new interface, in case it is 5 you got the old interface. Alternatively, you may also check if the version of Thunderbird is 69 or later.

```javascript
   if (transportService.createTransport.length === 4)
      return transportService.createTransport(((secure) ? ["starttls"] : []), host, port, proxyInfo);

    if (transportService.createTransport.length === 5) {
      if (secure)
        return transportService.createTransport(["starttls"], 1, host, port, proxyInfo);

      return transportService.createTransport(null, 0, host, port, proxyInfo);
   }

   throw new Error("Unknown Create Transport signature");
```

### nsICookie2

Merged into nsICookie.

### window.QueryInterface\(\)

With TB70, window objects don't need to be and cannot be QI'ed to nsIDOMChromeWindow, nsIDOMWindow and nsIInterfaceRequestor. You can just remove its usage, this is backwards compatible to TB 68. See the fix applied to [enigmail](https://gitlab.com/enigmail/enigmail/commit/cebbbc2a2f3d380e34ae8e4600f66e7986a0889d#6a57d3cb2d256247fb48986b448b6c289de8ffea).

{% hint style="info" %}
This does not apply to `nsIXULWindow`, which for example is used in `nsIWindowMediatorListener.onOpenWindow(xulWindow)`.
{% endhint %}

Equally, TreeColumns and TreeContentView don't need to be and cannot be QI'ed to nsITreeView.

### Services.scriptSecurityManager.createCodebasePrincipal\(\)

Renamed. Use

```javascript
Services.scriptSecurityManager.createContentPrincipal()
```

### nsIStringBundle.formatStringFromName\(\)

The 3rd parameter has been droped, which was the length of the array passed in as the 2nd parameter. See the [patch applied to Thunderbird itself](https://bug1557829.bmoattachments.org/attachment.cgi?id=9071511).

### IOUtils.js

Module file extension changed in TB78 from js to jsm, now available via

```javascript
const { IOUtils } = ChromeUtils.import("resource:///modules/IOUtils.jsm");
```

### AddRecipient\(\)

In TB78 the function `AddRecipient()` in the contact sidebar has been renamed to `awAddRecipientsArray(`\).

### MailServices.headerParser.parseHeadersWithArray\(\)

In TB71 `MailServices.headerParser.parseHeadersWithArray()` has been removed. Instead use:

```javascript
let addresses = MailServices.headerParser.parseEncodedHeader(address);
for (let addr of addresses) {
  let email = addr.email
  let name = addr.name
  let fullname = addr.toString();
}
```

###  nsIPromptService.select\(\)

Since TB69 `nsIPromptService.select()` / `Services.prompt.select()` has dropped the parameter which specifies the length of the array of the item list which the user can chose from. See the [patch applied to Thunderbird itself](https://phabricator.services.mozilla.com/differential/changeset/?ref=1068287).

###  nsIEditorStyleSheets has been removed

The [removal of `nsIEditorStyleSheets`](https://bugzilla.mozilla.org/show_bug.cgi?id=1449522) in TB77 necessitates the following changes:

* `nsIEditorStyleSheets.addOverrideStyleSheet(uri)`  -&gt; `windowUtils.loadSheetUsingURIString(uri, windowUtils.AGENT_SHEET)` 
* `nsIEditorStyleSheets.removeOverrideStyleSheet(uri)` -&gt; `windowUtils.removeSheet(uri, windowUtils.AGENT_SHEET)` 
* `nsIEditorStyleSheets.enableStyleSheet(uri, enable)` Either manually load or remove the stylesheet:  -&gt; `windowUtils.loadStyleSheetUsingURIString(uri, windowUtils.AGENT_SHEET)`  -&gt; `windowUtils.removeSheet(uri, windowUtils.AGENT_SHEET)`

## Changes to the Address Book

### Searching a nsIAbDirectory

In TB68 a `nsIAbDirectory` could be searched by simply attaching a search query to the URI of the directory when calling `getDirectory()`:

```javascript
let cards = MailServices.ab.getDirectory(URI + "?" + searchQuery).childCards;
while (cards.hasMoreElements()) {
  let card = cards.getNext().QueryInterface(Components.interfaces.nsIAbCard);
  ...
}
```

In TB78 `getDirectory()` no longer accepts search queries and throws an error. Instead, use the `search()` method, which uses an `nsIAbDirSearchListener`. A simple promisified implementation could look like so:

```javascript
searchDirectory: function (uri, search) {
  return new Promise((resolve, reject) => {
    let listener = {
      cards : [],
      onSearchFinished(aResult, aErrorMsg) {
        resolve(this.cards);
      },
      onSearchFoundCard(aCard) {
        this.cards.push(aCard.QueryInterface(Components.interfaces.nsIAbCard));
      }
    }
    MailServices.ab.getDirectory(uri).search(search, listener);
  });
}

let cards = await searchDirectory(URI, searchQuery);
for (let card of cards) {
  ...
}
```

## Changes to the Preference System

### Removal of onsyncfrompreference and onsynctopreference Attributes

With TB70, these two attributes have been removed from XUL elements like `textbox`, `radio`, `checkbox` and friends. This is no longer supported:

```markup
<textbox
    id="my-textbox"
    preference="some.preference.name"
    onsynctopreference="return onToPref();"
    onsyncfrompreference="return onFromPref();"/>
```

Most of the XUL based preferences system has been long removed and had to be replaced by either using the [`preferencesBindings.js`](https://developer.thunderbird.net/add-ons/updates/tb68#less-than-prefwindow-greater-than-less-than-prefpane-greater-than-less-than-preferences-greater-than-and-less-than-preference-greater-than) [wrapper](https://developer.thunderbird.net/add-ons/updates/tb68#less-than-prefwindow-greater-than-less-than-prefpane-greater-than-less-than-preferences-greater-than-and-less-than-preference-greater-than) or by manually reimplementing that functionality using the`nsIPrefService`.

The second approach is not affected by this change, as it has full control over the load and save process for all its preferences. The[`preferencesBindings.js`](https://developer.thunderbird.net/add-ons/updates/tb68#less-than-prefwindow-greater-than-less-than-prefpane-greater-than-less-than-preferences-greater-than-and-less-than-preference-greater-than) [wrapper](https://developer.thunderbird.net/add-ons/updates/tb68#less-than-prefwindow-greater-than-less-than-prefpane-greater-than-less-than-preferences-greater-than-and-less-than-preference-greater-than) has been updated and now supports:

```javascript
Preferences.addSyncFromPrefListener(
     document.getElementById("my-textbox"),
     function() { return onFromPref() });

Preferences.addSyncToPrefListener(
     document.getElementById("my-textbox"),
     function() { return onToPref() });
```

{% hint style="danger" %}
The `nsIPrefService`is not available via WebExtension APIs. It is therefore adviced to move away from storing addon preferences in a preference brach and instead use the local storage via the [`storage`](https://developer.mozilla.org/en-US/docs/Mozilla/Add-ons/WebExtensions/API/storage) API. More information can be found in the [update guide](https://developer.thunderbird.net/add-ons/updating/tb78#replacing-options).
{% endhint %}

