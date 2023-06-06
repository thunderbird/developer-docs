# Adapt to Changes in Thunderbird 103-115

This document tries to cover all the internal changes that may be needed to make Experiment add-ons compatible with Thunderbird Supernova. If you find changes which are not yet listed on this page, you can ask for help and advice in one of our [communication channels](https://developer.thunderbird.net/add-ons/community).

## Rework of Thunderbird's main mail window

Thunderbird Supernova has received an overhaul, which is more than just a facelift:

* The tab system has been improved. Each tab is now loaded into its own browser element, and a lot of functionality has been moved from the top level messenger window into either `about:3pane` or `about:message`, which is important if add-ons need to manipulate the UI or call system methods.&#x20;
* The mail toolbar has been replaced by the unified toolbar. Adding your own buttons will become difficult, because the unified toolbar tends to remove unknown objects. Instead, use the [browserAction API](https://webextension-api.thunderbird.net/en/latest/browserAction.html) to add buttons.
* The `about:3pane` (the mail tab) has been reworked, most notably the XUL `tree` elements of `folderTree` and `threadTree` have been replaced by HTML `lists` and HTML `tables`.&#x20;

**A general and very helpful introduction to the new front end can be found in its** [**official documentation**](https://developer.thunderbird.net/thunderbird-development/codebase-overview/mail-front-end)**.**

### Using WebExtension APIs

It is recommended to leverage WebExtension APIs as much a possible. Instead of adjusting to core changes, the following WebExtension APIs can be helpful:

* Use the [menus API](https://webextension-api.thunderbird.net/en/latest/menus.html) to add entries to Thunderbird's context menu (for example in the folder pane or in  thread pane)
* Use the [commands API](https://webextension-api.thunderbird.net/en/latest/commands.html) to register keyboard shortcuts. Additional benefit: all WebExtension shortcuts can be adjusted by the user in the Add-on Manager according to their needs.
* Use the [mailTabs API](https://webextension-api.thunderbird.net/en/latest/mailTabs.html) to interact with the mail tab.

### Using shared Experiments

There may already be a shared Experiment, which could help with add-on updates (or which could give helpful hints):

* [LegacyCSS API](https://github.com/thundernest/addon-developer-support/tree/master/auxiliary-apis/LegacyCSS): Inject into `about:3pane` or `about:message`, to style the `folderpane` (see [Hide Local Folders](https://addons.thunderbird.net/addon/hide-local-folders-for-tb78/versions/?page=1#version-3.0.1) Add-on), the `threadpane` or the message display area.
* [WindowListener API](https://github.com/thundernest/addon-developer-support/tree/master/wrapper-apis/WindowListener): Inject into `about:3pane` or `about:message`, to manipulate the `folderpane` (see [Phoenity Icons](https://addons.thunderbird.net/addon/phoenity-icons/versions/?page=1#version-3.9) Add-On), the `threadpane` or the message display area.
* [UnifiedFolders API](https://github.com/thundernest/addon-developer-support/tree/master/auxiliary-apis/UnifiedFolders): Interact with the "smart" folder display mode.

More interesting Experiments are available at the [addon-developer-support](https://github.com/thundernest/addon-developer-support/tree/master/auxiliary-apis) repository and on [DTN](https://developer.thunderbird.net/add-ons/mailextensions#sharing-experiment-apis).

## XUL flexbox changes

Mozilla continued to remove XUL in favour of standard HTML5/CSS. The most relevant changes are related to the XUL flexbox. A very helpful read is [this blogpost from the responsible developer](https://crisal.io/words/2023/03/30/xul-layout-is-gone.html).

Known attributes which have to be replaced:

* `height`: replace with [CSS height property](https://developer.mozilla.org/en-US/docs/Web/CSS/height)
* `width`: replace with [CSS width property](https://developer.mozilla.org/en-US/docs/Web/CSS/width)
* `flex`: replace with [CSS flex](https://developer.mozilla.org/en-US/docs/Web/CSS/flex) (a very helpful tutorial is available at [css-tricks.com](https://css-tricks.com/snippets/css/a-guide-to-flexbox/))

**Important:** If add-ons still create old-fashioned XUL dialogues and load \*`.xhtml` files, it is not recommended to invest time into fixing them. It is more efficient to re-create them as pure `*.html` files. They can be opened using the [tabs API](https://webextension-api.thunderbird.net/en/latest/tabs.html) or the [windows API](https://webextension-api.thunderbird.net/en/latest/windows.html).

## Removed JSM files

### Services.jsm

Removed in Thunderbird 103. The [service object is now globally available](https://firefox-source-docs.mozilla.org/toolkit/components/extensions/webextensions/basics.html#globals-available-in-the-api-scripts-global) in API implementation scripts. If needed in self-created JSMs, it can be accessed as follows:

```javascript
const Services = globalThis.Services;
```

For a backward compatible solution, use

```javascript
const Services = globalThis.Services || ChromeUtils.import("resource://gre/modules/Services.jsm").Services;
```

### osfile.jsm

Removed in Thunderbird 115. Can be replaced as follows:

* `OS.Constants.Sys.Name` -> `Services.appinfo.OS`
* `OS.Constants.Path.profileDir` -> `PathUtils.profileDir`
* `OS.Constants.Path.tmpDir`-> `PathUtils.tempDir`
* `OS.File.*` -> [`IOUtils.*`](https://searchfox.org/comm-central/source/mozilla/dom/chrome-webidl/IOUtils.webidl)&#x20;
* `OS.Path.*` -> [`PathUtils.*`](https://searchfox.org/comm-central/source/mozilla/dom/chrome-webidl/PathUtils.webidl)

## Changed API

### calITimezoneService.timezoneIds

In Thunderbird 106, this has been changed from an enumerator to a simple array.

### callTimezoneService.aliasIds

Removed in Thunderbird 106.

### nsIMsgDatabase.ContainsKey()

Renamed in Thunderbird 108 to `containsKey()`. Example `msgHdr.folder.msgDatabase.containsKey()`.

