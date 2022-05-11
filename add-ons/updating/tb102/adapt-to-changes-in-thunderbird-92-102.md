# Adapt to Changes in Thunderbird 92-102

This document tries to cover all the internal changes that may be needed to make add-ons compatible with Thunderbird Beta. If you find changes which are not yet listed on this page, you can ask for help and advice in one of our [communication channels](https://developer.thunderbird.net/#getting-plugged-into-the-community).

## Changed DOM Elements

### composer

The id of the editor element in the composer has been renamed from [`content-frame`](https://searchfox.org/comm-esr91/source/mail/components/compose/content/messengercompose.xhtml#2478) to [`messageEditor`](https://searchfox.org/comm-central/rev/36c713a3d0bf0d0ca2208fdf74f8a133d4ed7edd/mail/components/compose/content/messengercompose.xhtml#2460).

### message window

The id of the additional header area in the message display window has been renamed from [`expandedHeaders2`](https://searchfox.org/comm-esr91/source/mail/base/content/msgHdrView.inc.xhtml#268) to [`extraHeadersArea`](https://searchfox.org/comm-central/rev/7e775d6f5ba8334b968619bed2082cd967615a75/mail/base/content/msgHdrView.inc.xhtml#408). The element has also been converted from a `table` to a `div`.&#x20;

The class `headerName` used for the styling header entries has been renamed to `message-header-label`. The class`message-header-row` has been added, styling the entire row.

The class `msgHeaderView-button` used to style toolbar buttons has been renamed to `message-header-view-button`.

## Changed API

### calICalendar.\*

Since TB 96, many calendar functions return Promises. This includes:

* `addItem()`
* `adoptItem()`
* `deleteItem()`
* `deleteOfflineItem()`
* `getItem()`
* `getItemOfflineFlag()`
* `modifyItem()`

The former methods to promisify these functions have been removed together with `calAsyncUtils.jsm`. Replace

```
let pcal = cal.async.promisifyCalendar(calendar.wrappedJSObject);
let item = await pcal.getItem(itemId);
```

by

```
let item = await calendar.getItem(itemId);
```

If your code is synchronous, you will have to rework it to make use of asynchronous functions. Feel free to reach out for further help on this through our[ community channels](../../community.md).

### calICalendar.getItems()

Since TB 96, `calICalendar.getItems()` returns a `ReadableStream`. Replace

```
let aCalIOperationListener = {
    QueryInterface: ChromeUtils.generateQI(["calIOperationListener"]),
    onOperationComplete(calendar, status, operationType, id, detail) {
        currentView().setSelectedItems(items, false);
    },
    onGetResult(calendar, status, itemType, detail, itemsArg) {
        for (let item of itemsArg) {
            // Do something with item.
        }
    },
};
calendar.getItems(
    aItemFilter,
    aCount,
    aCalIDateTimeRangeStart,
    aCalIDateTimeRangeEndEx,
    aCalIOperationListener
);
```

by

```
let iterator = cal.iterate.streamValues(
    calendar.getItems(
        aItemFilter,
        aCount,
        aCalIDateTimeRangeStart,
        aCalIDateTimeRangeEndEx
    )
);

for await (let items of this.iterator) {
    for (let item of items) {
        // Do something with item.
    }
}
```

### calStorageCalendar.resetItemOfflineFlag()

Since TB 96, this function returns a Promise. Replace

```
let resetListener = {
    QueryInterface: ChromeUtils.generateQI(["calIOperationListener"]),
    onGetResult(calendar, status, itemType, detail, items) { },
    onOperationComplete(calendar, status, opType, id, detail) {
      // Reset completed.
    },
}
storage.resetItemOfflineFlag(aItem, resetListener);
```

by

```
await storage.resetItemOfflineFlag(aItem);
// Reset completed.
```

### `ChromeUtils.import()`

Since TB 102, it is no longer possible to load JSMs via extension URLs, for example&#x20;

```
var { myModule } = ChromeUtils.import(extension.rootURI.resolve("myModule.jsm"));
```

It is now mandatory to register an internal URL, for example a `resource://` URL. We have created the [ResourceUrl Experiment API](https://github.com/thundernest/addon-developer-support/tree/master/auxiliary-apis/ResourceUrl), which is doing the heavy lifting. It is used by our [Experiment API example](https://github.com/thundernest/sample-extensions/tree/master/experiment).

### `NotificationBox.appendNotification()`

The parameters have [changed](https://searchfox.org/mozilla-central/rev/f8576fec48d866c5f988baaf1fa8d2f8cce2a82f/toolkit/content/widgets/notificationbox.js#78-149) in TB 94.
