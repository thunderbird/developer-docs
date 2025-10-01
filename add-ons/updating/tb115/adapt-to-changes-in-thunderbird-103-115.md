# Adapt to Changes in Thunderbird 103-115

This document tries to cover the internal changes that may be needed to make Experiment add-ons compatible with Thunderbird Supernova. If you find changes which are not yet listed on this page, you can ask for help and advice in one of our [communication channels](https://developer.thunderbird.net/add-ons/community).

## Rework of Thunderbird's main messenger window

### Changed tab implementation

Each `mail3PaneTab` and `mailMessageTab` in Thunderbird's main messenger window is now loaded into its own browser element, instead of sharing and updating a single browser.

**A general and very helpful introduction to the new front end can be found in its** [**official documentation**](https://developer.thunderbird.net/thunderbird-development/codebase-overview/mail-front-end)**.**

The `mail3PaneWindow` (`about:3pane`) can now be accessed as follows:

1. `window.gTabmail.currentAbout3Pane`
2. `window.gTabmail.currentTabInfo.chromeBrowser.contentWindow`
3. `window.gTabmail.tabInfo[0].chromeBrowser.contentWindow`
4. `window.gTabmail.tabInfo.find(`\
   `t => t.mode.name == "mail3PaneTab"`\
   `).chromeBrowser.contentWindow`

Options _#1 & #2_ will only work if the current active tab is a `mail3PaneTab`. Option _#3_ assumes the first tab is always a `mail3PaneTab`.\
\
The `mailMessageWindow` (`about:message`) can be accessed similarly:

1. `window.gTabmail.currentAboutMessage`
2. `window.gTabmail.currentTabInfo.chromeBrowser.contentWindow`
3. `mail3PaneWindow.messageBrowser.contentWindow`
4. `window.messageBrowser.contentWindow`

Option _#1_ will only work if the current tab is a `mail3PaneTab` or a `mailMessageTab`. Option _#2_ will return the `mailMessageWindow`, if the current tab is a `mailMessageTab` (it will return the `mail3PaneWindow`, if the current tab is a `mail3PaneTab`). Option _#3_ will return the nested message browser of a `mail3PaneTab` through its `mail3PaneWindow` as defined in the previous section. Option _#4_ will return the message browser of a message window.

#### Moved global objects

Some of the global objects defined in Thunderbird's main messenger window have been moved into the `mail3PaneWindow` (`about:3pane`) and/or the `mailMessageWindow` (`about:message`). Some objects have been removed.

* `gDBView`: Available in `mail3PaneWindow` and in `mailMessageWindow`.
* `gFolderDisplay`: Removed. Find displayed folder via `mail3PaneWindow.gFolder`.
* `gMessageDisplay`: Removed. Find displayed message via `mailMessageWindow.gMessage` or `mailMessageWindow.gMessageURI`.

Useful functions, methods and objects which have been moved from elsewhere:

* `mail3PaneWindow.displayFolder(folderURI)`
* `mail3PaneWindow.messagePane.displayMessage(messageURI)`
* `mail3PaneWindow.messagePane.displayMessages(messageURIs)`
* `mail3PaneWindow.messagePane.displayWebPage(url)`
* `mail3PaneWindow.folderPane.*`
* `mail3PaneWindow.folderTree.*`
* `mail3PaneWindow.threadPane.*`
* `mail3PaneWindow.threadTree.*`
* `mailMessageWindow.currentHeaderData`
* `mailMessageWindow.currentAttachments`

This [topicbox post](https://thunderbird.topicbox.com/groups/addons/Te5f62259df8c0c74-M7ea49d57cdc60d61a620c6b0) holds instructions on how to find other moved objects and functions.

#### Using WebExtension APIs

It is recommended to leverage WebExtension APIs as much a possible. Instead of adjusting to core changes, the following WebExtension APIs can be helpful:

* Use the [browserAction API](https://webextension-api.thunderbird.net/en/mv2/browserAction.html) to add buttons to Thunderbird's unified toolbar
* Use the [menus API](https://webextension-api.thunderbird.net/en/mv2/menus.html) to add entries to Thunderbird's context menu (for example in the folder pane or in the thread pane)
* Use the [commands API](https://webextension-api.thunderbird.net/en/mv2/commands.html) to register keyboard shortcuts. Additional benefit: all WebExtension shortcuts can be adjusted by the user in the Add-on Manager according to their needs.
* Use the [mailTabs API](https://webextension-api.thunderbird.net/en/mv2/mailTabs.html) to interact with the mail tab.

#### Using shared Experiments

There may already be a shared Experiment, which could help with add-on updates (or which could give helpful hints):

* community Experiments listed on [DTN](https://developer.thunderbird.net/add-ons/mailextensions#sharing-experiment-apis)
* Experiments from the [webext-support](https://github.com/thunderbird/webext-support/tree/master/experiments) repository
* Experiments from the [webext-experiments](https://github.com/thunderbird/webext-experiments) repository (scheduled to be shipped with Thunderbird soon)

### Unified Toolbar

The mail toolbar has been replaced by the unified toolbar. Adding your own buttons will become difficult, because the unified toolbar tends to remove unknown objects. Instead, use the [browserAction API](https://webextension-api.thunderbird.net/en/mv2/browserAction.html) to add buttons.

### XUL Tree replacement

The `folderTree` and `threadTree` in `about:3pane` (the `mail3PaneTab`) no longer use the deprecated XUL `tree` elements, but have been replaced by HTML `lists` and HTML `tables`.

### XUL flexbox changes

Mozilla continued to remove XUL in favour of standard HTML5/CSS. The most relevant changes are related to the XUL flexbox. A very helpful read is [this blogpost from the responsible developer](https://crisal.io/words/2023/03/30/xul-layout-is-gone.html).

Known attributes which have to be replaced:

* `height`: replace with [CSS height property](https://developer.mozilla.org/en-US/docs/Web/CSS/height)
* `width`: replace with [CSS width property](https://developer.mozilla.org/en-US/docs/Web/CSS/width)
* `flex`: replace with [CSS flex](https://developer.mozilla.org/en-US/docs/Web/CSS/flex) (a very helpful tutorial is available at [css-tricks.com](https://css-tricks.com/snippets/css/a-guide-to-flexbox/))

**Important:** If add-ons still create old-fashioned XUL dialogues and load \*`.xhtml` files, it is not recommended to invest time into fixing them. It is more efficient to re-create them as pure `*.html` files. They can be opened using the [tabs API](https://webextension-api.thunderbird.net/en/mv2/tabs.html) or the [windows API](https://webextension-api.thunderbird.net/en/mv2/windows.html).

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
* `OS.File.*` -> [IOUtils.*](https://searchfox.org/comm-central/source/mozilla/dom/chrome-webidl/IOUtils.webidl)
* `OS.Path.*` -> [PathUtils.*](https://searchfox.org/comm-central/source/mozilla/dom/chrome-webidl/PathUtils.webidl)

## Changed API

### calITimezoneService.timezoneIds

In Thunderbird 106, this has been changed from an enumerator to a simple array.

### callTimezoneService.aliasIds

Removed in Thunderbird 106.

### nsIMsgDatabase.ContainsKey()

Renamed in Thunderbird 108 to `containsKey()`. Example `msgHdr.folder.msgDatabase.containsKey()`.

## Process Restrictions

Content processes, such as the extension process executing "child" experiment code, are subject to additional restrictions compared to Thunderbird 102. Known limitations include:

* Raw TCP socket operations never complete, even if timeouts are set up. It is likely that other networking primitives are affected in a similar way.

In consequence, you might need to move some functionality from "child" experiment code to the "parent" context. One way to achieve this is to implement the necessary functionality as a regular asynchronous API method in the "parent" experiment (without extending the API schema), then using `await context.childManager.callParentAsyncFunction("your_api_name.some_function", [arguments, passed, to, some_function])` to call it from the child experiment. Note that function arguments passed in between processes are subject to the [structured clone algorithm](https://developer.mozilla.org/en-US/docs/Web/API/Web\_Workers\_API/Structured\_clone\_algorithm).

## Removed CSS features

### -moz-image-region

The CSS feature `-moz-image-region` has been removed. It is no longer possible to specify a button icon or a `list-style-image` as a region from a larger image. Add-on developers have to resort to individual images.
