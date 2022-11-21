# Adapt to Changes in Thunderbird 103-115

This document tries to cover all the internal changes that may be needed to make add-ons compatible with Thunderbird Beta. If you find changes which are not yet listed on this page, you can ask for help and advice in one of our [communication channels](https://developer.thunderbird.net/#getting-plugged-into-the-community).

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

