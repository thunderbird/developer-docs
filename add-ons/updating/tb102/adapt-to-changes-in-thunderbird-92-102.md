# Adapt to Changes in Thunderbird 92-102

This document tries to cover all the internal changes that may be needed to make add-ons compatible with Thunderbird Beta. If you find changes which are not yet listed on this page, you can ask for help and advice in one of our [communication channels](https://developer.thunderbird.net/#getting-plugged-into-the-community).

## String comparison against the version string

Some developers use the version string to determine which function to call in add-ons which try to be backward compatible. For example:

```javascript
if (xulAppInfo.version >= "91.0") {
  restartButton.addEventListener(
    "command", 
    () => MailUtils.restartApplication()
  );
} else {
  restartButton.addEventListener(
    "command", 
    () => BrowserUtils.restartApplication()
  );
}
```

This fails for Thunderbird 100 and newer, because this is a string comparison and not an integer comparison. A function to get the integer values could look like so:

```javascript
function getThunderbirdVersion() {
    let parts = Services.appinfo.version.split(".");
    return {
        major: parseInt(parts[0]),
        minor: parseInt(parts[1]),
        revision: parts.length > 2 ? parseInt(parts[2]) : 0,
    }
}
```

And then just use `getThunderbirdVersion().major >= 91` to check the version.

In this specific case, one could also use feature detection itself:

```javascript
if ("restartApplication" in MailUtils)
  restartButton.addEventListener(
    "command", 
    () => MailUtils.restartApplication()
  );
} else {
  restartButton.addEventListener(
    "command", 
    () => BrowserUtils.restartApplication()
  );
}
```

## Changed DOM Elements

### composer

The id of the editor element in the composer has been renamed from [`content-frame`](https://searchfox.org/comm-esr91/source/mail/components/compose/content/messengercompose.xhtml#2478) to [`messageEditor`](https://searchfox.org/comm-central/rev/36c713a3d0bf0d0ca2208fdf74f8a133d4ed7edd/mail/components/compose/content/messengercompose.xhtml#2460).

### message window

The id of the additional header area in the message display window has been renamed from [`expandedHeaders2`](https://searchfox.org/comm-esr91/source/mail/base/content/msgHdrView.inc.xhtml#268) to [`extraHeadersArea`](https://searchfox.org/comm-central/rev/7e775d6f5ba8334b968619bed2082cd967615a75/mail/base/content/msgHdrView.inc.xhtml#408). The element has also been converted from a `table` to a `div`.

The class `headerName` used for the styling header entries has been renamed to `message-header-label`. The class`message-header-row` has been added, styling the entire row.

The class `msgHeaderView-button` used to style toolbar buttons has been renamed to `message-header-view-button`.

## Changed API

### calICalendar.\*

Since Thunderbird 96, [many calendar functions return Promises](https://searchfox.org/comm-central/source/calendar/base/public/calICalendar.idl). This includes:

* `getItem()`
* `addItem()`
* `adoptItem()`
* `modifyItem()`
* `deleteItem()`
* `deleteOfflineItem()`
* `getItemOfflineFlag()`

The former methods to promisify these functions have been removed together with `calAsyncUtils.jsm`. Additionally, the `getItem()` method will return the item directly instead of an arrayi with the item. Replace

```javascript
let pcal = cal.async.promisifyCalendar(calendar.wrappedJSObject);
let item = await pcal.getItem(itemId)[0];
```

by

```javascript
let item = await calendar.getItem(itemId);
```

The `calIOperationListener` and `calIOperation` interfaces are still used in various places but the general direction is to remove them eventually after 102 in favor of Promises and ReadableStreams. If you have code that uses them internally, please update where feasible.

If your code is synchronous, you will have to rework it to make use of asynchronous functions. Feel free to reach out for further help on this through our[ community channels](../../community.md).


#### Provider changes in calICalendar.addItem/adoptItem/modifyItem
Calendar providers need to change above mentioned functions to be asynchronous. Calling the
listeners is no longer necessary. Instead, you should return the item from the
`addItem`/`adoptItem`/`modifyItem` functions and make sure to throw an error in case of failure.

For providers with offline support, you need to call listeners set by the cache layer using the
`_cachedAdoptItemCallback` and `_cachedModifyItemCallback` properties on your provider class. This
is an unfortunate hack needed to maintain the order the `onAddItem` event is fired by
`calCachedCalendar`. These listeners need to be called just before returning.

```javascript
class CalendarProvider extends cal.provider.BaseClass {
    _cachedAdoptItemCallback = null;
    _cachedModifyItemCallback = null;

    async addItem(aItem) {
        return this.adoptItem(aItem.clone());
    }

    async adoptItem(aItem) {
        try {
            let item = aItem; // Create your item here
            if (this._cachedAdoptItemCallback) {
                await this._cachedAdoptItemCallback(
                    this.superCalendar,
                    Cr.NS_OK,
                    Ci.calIOperationListener.ADD,
                    item.id,
                    item
                );
            }
            return item;
        } catch (e) {
            if (this._cachedAdoptItemCallback) {
                await this._cachedAdoptItemCallback(
                    this.superCalendar,
                    e.result || Cr.NS_ERROR_FAILURE;,
                    Ci.calIOperationListener.ADD,
                    aItem.id,
                    aItem
                );
            }
            throw e;
        }
    }

    async modifyItem(aNewItem, aOldItem) {
        try {
            let item = aNewItem; // Modify your item here
            if (this._cachedModifyItemCallback) {
                await this._cachedModifyItemCallback(
                    this.superCalendar,
                    Cr.NS_OK,
                    Ci.calIOperationListener.MODIFY,
                    item.id,
                    item
                );
            }
            return item;
        } catch (e) {
            if (this._cachedModifyItemCallback) {
                let code = e.result || Cr.NS_ERROR_FAILURE;
                await this._cachedModifyItemCallback(
                    this.superCalendar,
                    code,
                    Ci.calIOperationListener.MODIFY,
                    aNewItem.id,
                    aNewItem
                );
            }
            throw e;
        }
    }
}
```

#### calICalendar.getItems()

Since Thunderbird 96, `calICalendar.getItems()` returns a `ReadableStream`. Replace

```javascript
let operationListener = {
    QueryInterface: ChromeUtils.generateQI(["calIOperationListener"]),
    onOperationComplete(calendar, status, operationType, id, detail) {
        // Completed
    },
    onGetResult(calendar, status, itemType, detail, itemsArg) {
        for (let item of itemsArg) {
            // Do something with item.
        }
    },
};
calendar.getItems(itemFilter, count, rangeStart, rangeEnd, operationListener);
```

by

```javascript
let iterator = cal.iterate.streamValues(
    calendar.getItems(itemFilter, count, rangeStart, rangeEnd)
);

for await (let items of iterator) {
    for (let item of items) {
        // Do something with item.
    }
}
```

If you are implementing a provider you will need to adapt your code to return a `ReadableStream`.
For cached providers, ensure you are returning the result from the offline cache:

```javascript
getItems(aFilter, aCount, aRangeStart, aRangeEnd) {
    // Previous code may have been missing the return, and used aListener
    return this.mOfflineStorage.getItems(...arguments);
}
```

#### calICalendar.getItemsAsArray()

This is a new addition to the API that returns the results as an array instead of a `ReadableStream`. The `BaseClass` provider has a default 
implementation however providers not extending it should provide their own implementation. If you intend to use this method, please be careful about memory usage with large queries.

### calStorageCalendar.resetItemOfflineFlag()

Since Thunderbird 96, this function returns a Promise. Replace

```javascript
let resetListener = {
    QueryInterface: ChromeUtils.generateQI(["calIOperationListener"]),
    onGetResult(calendar, status, itemType, detail, items) { },
    onOperationComplete(calendar, status, opType, id, detail) {
      // Reset completed.
    },
}
storage.resetItemOfflineFlag(item, resetListener);
```

by

```javascript
await storage.resetItemOfflineFlag(item);
// Reset completed.
```

### `ChromeUtils.import()`

Since Thunderbird 101, it is no longer possible to load JSMs via extension URLs, for example

```javascript
var { myModule } = ChromeUtils.import(extension.rootURI.resolve("myModule.jsm"));
```

It is now mandatory to register an internal URL, for example a `resource://` URL. We have created the [ResourceUrl Experiment API](https://github.com/thundernest/addon-developer-support/tree/master/auxiliary-apis/ResourceUrl), which is doing the heavy lifting. It is used by our [Experiment API example](https://github.com/thundernest/sample-extensions/tree/master/experiment).

### `NotificationBox.appendNotification()`

The parameters have [changed](https://searchfox.org/mozilla-central/rev/f8576fec48d866c5f988baaf1fa8d2f8cce2a82f/toolkit/content/widgets/notificationbox.js#78-149) in Thunderbird 94. Most former properties have moved into an object. Replace this

```javascript
notificationbox.appendNotification(
  notificationLabel,
  notificationId,
  imageUrl,
  notificationbox.PRIORITY_CRITICAL_HIGH,
  buttons,
  eventCallback,
  customElementClass
);
```

with this

```javascript
let notification = notificationbox.appendNotification(
    notificationId,
    {
        label: notificationLabel,
        priority: notificationbox.PRIORITY_CRITICAL_HIGH,
        eventCallback: eventCallback,
        notificationIs: customElementClass
    },
    buttons
);
notification.messageImage.src = imageUrl;
```
