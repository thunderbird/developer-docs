# Adapt to Changes in Thunderbird 103-115

This document tries to cover all the internal changes that may be needed to make add-ons compatible with Thunderbird Beta. If you find changes which are not yet listed on this page, you can ask for help and advice in one of our [communication channels](https://developer.thunderbird.net/#getting-plugged-into-the-community).

Thunderbird Supernova has received an overhaul, which is more than just a facelift. The `about:3pane` has been reworked, most notably the XUL tree elements of `folderTree` and `threadTree` have been replaced by HTML lists and HTML tables. The mail toolbar has been replaced by the unified toolbar.

**A general and very helpful introduction to the new front end can be found in its** [**official documentation**](https://developer.thunderbird.net/thunderbird-development/codebase-overview/mail-front-end)**.**

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

## Changed API

### calITimezoneService.timezoneIds

In Thunderbird 106, this has been changed from an enumerator to a simple array.

### callTimezoneService.aliasIds

Removed in Thunderbird 106.

### nsIMsgDatabase.ContainsKey()

Renamed in Thunderbird 108 to `containsKey()`. Example `msgHdr.folder.msgDatabase.containsKey()`.

