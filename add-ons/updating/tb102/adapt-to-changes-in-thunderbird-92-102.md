# Adapt to Changes in Thunderbird 92-102

This document tries to cover all the internal changes that may be needed to make Experiment add-ons compatible with Thunderbird 102. If you find changes which are not yet listed on this page, you can ask for help and advice in one of our [communication channels](https://developer.thunderbird.net/add-ons/community).

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

The id of the editor element in the composer has been renamed from [content-frame](https://searchfox.org/comm-esr91/source/mail/components/compose/content/messengercompose.xhtml#2478) to [messageEditor](https://searchfox.org/comm-central/rev/36c713a3d0bf0d0ca2208fdf74f8a133d4ed7edd/mail/components/compose/content/messengercompose.xhtml#2460).

### message window

The id of the additional header area in the message display window has been renamed from [expandedHeaders2](https://searchfox.org/comm-esr91/source/mail/base/content/msgHdrView.inc.xhtml#268) to [extraHeadersArea](https://searchfox.org/comm-central/rev/7e775d6f5ba8334b968619bed2082cd967615a75/mail/base/content/msgHdrView.inc.xhtml#408). The element has also been converted from a `table` to a `div`.

The class `headerName` used for the styling header entries has been renamed to `message-header-label`. The class`message-header-row` has been added, styling the entire row.

The class `msgHeaderView-button` used to style toolbar buttons has been renamed to `message-header-view-button`.

### \<stringbundle id="bundle\_smime\_read\_info">

This `stringbundle` element has been removed from `messageWindow.xhtml` and `messenger.xhtml`. To access the strings, you have to load the bundle directly:

```javascript
let bundle = Services.strings.createBundle(
    "chrome://messenger-smime/locale/msgSecurityInfo.properties"
);
```

The method to retrieve strings from the created bundle is slightly different, instead of `getString()` use `GetStringFromName()` .

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

The former methods to promisify these functions have been removed together with `calAsyncUtils.jsm`. Additionally, the `getItem()` method will return the item directly instead of an array with the item. Replace

```javascript
let pcal = cal.async.promisifyCalendar(calendar.wrappedJSObject);
let item = await pcal.getItem(itemId)[0];
```

by

```javascript
let item = await calendar.getItem(itemId);
```

The `calIOperationListener` and `calIOperation` interfaces are still used in various places but the general direction is to remove them eventually after 102 in favor of Promises and ReadableStreams. If you have code that uses them internally, please update where feasible.

If your code is synchronous, you will have to rework it to make use of asynchronous functions. Feel free to reach out for further help on this through our [community channels](../../community.md).

#### Provider changes in calICalendar.addItem/adoptItem/modifyItem

Calendar providers need to change above mentioned functions to be asynchronous. Calling the listeners is no longer necessary. Instead, you should return the item from the `addItem`/`adoptItem`/`modifyItem` functions and make sure to throw an error in case of failure.

For providers with offline support, you need to call listeners set by the cache layer using the `_cachedAdoptItemCallback` and `_cachedModifyItemCallback` properties on your provider class. This is an unfortunate hack needed to maintain the order the `onAddItem` event is fired by `calCachedCalendar`. These listeners need to be called just before returning.

> Note: It is important to store the callbacks before executing any async work to avoid issues when the same operation is run in concurrently. See the example below.

```javascript
class CalendarProvider extends cal.provider.BaseClass {
  _cachedAdoptItemCallback = null;
  _cachedModifyItemCallback = null;

  async addItem(item) {
    return this.adoptItem(item.clone());
  }

  async adoptItem(item) {
    let adoptCallback = this._cachedAdoptItemCallback; // Store callback locally to execute later.
    let createdItem = item; // Create your item here, this may be asynchronous.

    if (adoptCallback) {
      await adoptCallback(
        this.superCalendar,
        Cr.NS_OK,
        Ci.calIOperationListener.ADD,
        createdItem.id,
        createdItem
      );
    }
    return item;
  }

  async modifyItem(newItem, oldItem) {
    let modifyCallback = this._cachedModifyItemCallback;
    let modifiedItem = newItem; // Modify your item here, this may be asynchronous.

    if (modifyCallback) {
      await modifyCallback(
        this.superCalendar,
        Cr.NS_OK,
        Ci.calIOperationListener.MODIFY,
        modifiedItem.id,
        modifiedItem
      );
    }
    return modifiedItem;
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

If you are implementing a provider you will need to adapt your code to return a `ReadableStream`. For cached providers, ensure you are returning the result from the offline cache:

```javascript
getItems(aFilter, aCount, aRangeStart, aRangeEnd) {
    // Previous code may have been missing the return, and used aListener
    return this.mOfflineStorage.getItems(...arguments);
}
```

#### calICalendar.getItemsAsArray()

This is a new addition to the API that returns the results as an array instead of a `ReadableStream`. The `BaseClass` provider has a default implementation however providers not extending it should provide their own implementation. If you intend to use this method, please be careful about memory usage with large queries.

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

It is now mandatory to register an internal URL, for example a `resource://` URL. See our [Activity Manager Experiment example](https://github.com/thunderbird/webext-examples/tree/master/manifest_v2/experiment.activityManager).

### DisplayNameUtils.getCardForEmail()

This method has been removed in 102.3.0. Replace its former usage

```javascript
let cardDetails = DisplayNameUtils.getCardForEmail(emailAddress);
```

by

```javascript
let card = MailServices.ab.cardForEmailAddress(emailAddress);
let cardDetails = {
  card,
  book: card ? MailServices.ab.getDirectoryFromUID(card.directoryUID) : null,
a};
```

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

### nsIAbCard

The interface itself has not changed much, but contact details are handled differently now. Instead of storing the individual contact details as key/value pairs, they are now stored as a vCard string in the `_vCard` property. The interface has gained two new members:

* `supportsVCard` a boolean value indicating support for vCard (or lack thereof).
* `vCardProperties` is a `VCardProperties` object if the card supports vCard, or null

All former standard contact properties (now referred to as [banished properties](https://searchfox.org/comm-esr102/source/mailnews/addrbook/modules/VCardUtils.jsm#306-348)) are migrated into the vCard string and can no longer be updated directly via `card.setProperty()`. A limited set of banished properties can still be read from: `DisplayName`, `FirstName`, `LastName`, `PrimaryEmail`, `SecondEmail`, and `NickName`.

#### Creating a new card

The new `AddrBookCard` object is a wrapper for `nsIAbCard` to enable support for vCards. No longer use `nsIAbCard` to create a new card:

```javascript
let card = Cc["@mozilla.org/addressbook/cardproperty;1"].createInstance(
    Ci.nsIAbCard
);
card.setProperty("DisplayName", "Default User");
card.setProperty("PrimareEmail", "user@inter.net");
addrbook.addCard(card);
```

Instead, create an `AddrBookCard`:

```javascript
const { XPCOMUtils } = ChromeUtils.import(
  "resource://gre/modules/XPCOMUtils.jsm"
);
XPCOMUtils.defineLazyModuleGetters(this, {
  AddrBookCard: "resource:///modules/AddrBookCard.jsm",
  VCardProperties: "resource:///modules/VCardUtils.jsm",
  VCardPropertyEntry: "resource:///modules/VCardUtils.jsm",
});

// Create a new card from a vCard.
let newCard = new AddrBookCard();
newCard.setProperty("_vCard", "BEGIN:VCARD\r\nVERSION:4.0\r\nFN:Default User\r\nUID:123456\r\nEMAIL:user@inter.net\r\nEND:VCARD\r\n");
// When a card is saved, a UID which might have been set in the _vCard string
// is ignored. The UID of the card is the one specified in its UID member. If
// it is not set, a new UID will be generated. Use its vCardProperties object
// to access the parsed UID and manually update the card's UID.
let uid = newCard.vCardProperties.getFirstValue("uid");
if (uid) {
  newCard.UID = uid;
}
addrbook.addCard(newCard);
```

#### Modifying an existing card

To update a card, create a new `AddrBookCard` from the updated vCard string and enforce the same UID:

```javascript
let updatedCard = new AddrBookCard();
card.setProperty("_vCard", "BEGIN:VCARD\r\nVERSION:4.0\r\nFN:Admin User\r\nEMAIL:admin@inter.net\r\nEND:VCARD\r\n");
updatedCard.UID = newCard.UID;
addrbook.modifyCard(newCard);
```

Alternativly, modify the card's `vCardProperties`:

```javascript
newCard.vCardProperties.addValue("email","lazy@inter.net");
newCard.vCardProperties.removeValue("email","admin@inter.net");
newCard.vCardProperties.getFirstEntry("fn").value = "Lazy User";
addrbook.modifyCard(newCard);
```

{% hint style="warning" %}
After an `AddrBookCard` has been created, its `vCardProperties` object is populated on first access from the cards `_vCard` string property. While saving the card, its `_vCard` string property is re-generated from its `vCardProperties`.

This effectivly means that all changes to the `_vCard` string property after its `vCardProperties` object has been used, are ignored.
{% endhint %}

#### Convert between key/value pairs and vCard

[VCardUtils.jsm](https://searchfox.org/comm-esr102/source/mailnews/addrbook/modules/VCardUtils.jsm) contains a number of utility functions for converting between the storage types:

```javascript
// Convert a vCard string into a key/value property Map. Not all details stored
// in the card will be available. Data loss is inevitable.
let vCardProperties = VCardProperties.fromVCard(vCardString);
let propertyMap = vCardProperties.toPropertyMap();

propertyMap.set("Notes", "This is a note");

// Convert the updated property Map back into a vCard.
let vCardPropertiesUpdated = VCardProperties.fromPropertyMap(propertyMap);
let vCardStringUpdated = vCardProperties.toVCard();
```

Read more about the address book implementation in Thunderbirds core documentation:

{% content-ref url="../../../thunderbird-development/codebase-overview/address-book.md" %}
[address-book.md](../../../thunderbird-development/codebase-overview/address-book.md)
{% endcontent-ref %}

### nsICollationFactory

Has been removed in Thunderbird 93. One of its use case was to compare locale strings. You can replace the following:

```javascript
function localeCompare(a, b) { 
  var collator = Cc["@mozilla.org/intl/collation-factory;1"]
    .getService(Components.interfaces.nsICollationFactory)
    .CreateCollation();
  return collator.compareString(0, a, b);
}
```

by

```javascript
function localeCompare(a, b) { 
  return a.localeCompare(b);
}
```

### nsIFileProtocolHandler

The method `getURLSpecFromFile` has been replaced by `getURLSpecFromActualFile` and `getURLSpecFromDir` in Thunderbird 92. Use the variant which fits your `file` object.

### nsIFolderListener

Its generic callback functions have been renamed from `onItem*()` to `onFolder*()` in Thunderbird 94. Where needed, dedicated `onMessage*()` callback functions have been added:

* OnItemAdded(in nsIMsgFolder aParentItem, in nsISupports aItem)\
  \-> _onFolderAdded(in nsIMsgFolder parent, in nsIMsgFolder child)_\
  \-> _onMessageAdded(in nsIMsgFolder parent, in nsIMsgDBHdr msg)_
* OnItemRemoved(in nsIMsgFolder aParentItem, in nsISupports aItem)\
  \-> _onFolderRemoved(in nsIMsgFolder parent, in nsIMsgFolder child)_\
  \-> _onMessageRemoved(in nsIMsgFolder parent, in nsIMsgDBHdr msg)_

The following callback functions were renamed without changing their parameters:

* OnItemEvent -> _onFolderEvent_
* OnItemPropertyChanged -> _onFolderPropertyChanged_
* OnItemIntPropertyChanged -> _onFolderIntPropertyChanged_
* OnItemBoolPropertyChanged -> _onFolderBoolPropertyChanged_
* OnItemUnicharPropertyChanged -> _onFolderUnicharPropertyChanged_
* OnItemPropertyFlagChanged -> _onFolderPropertyFlagChanged_

### nsIMsgCompSendFormat.AskUser

Has been renamed to `nsIMsgCompSendFormat.Auto` in Thunderbird 101.

### nsIMsgSendListener.onGetDraftFolderURI(aFolderURI)

The parameters of this listener have been changed in Thunderbird 102. The header messageId has been added:

```javascript
nsIMsgSendListener.onGetDraftFolderURI(aMsgId, aFolderURI)
```

The new parameter has been added at the first position to [match all the other listeners](https://searchfox.org/comm-central/rev/7923bcceab6029d38d0be76a021424b300997dbe/mailnews/compose/public/nsIMsgSendListener.idl), which already return the messageId.

### nsIPrintSettings.\*

A few members and methods have been removed without replacement:

* `isPrintSelectionRBEnabled` (see alternative described in the following section)
* `isCancelled`
* `saveOnCancel`
* `showPrintProgress`
* `SetupSilentPrinting()`

Instead of `printToFile`, use [outputDestination](https://searchfox.org/mozilla-central/rev/3419858c997f422e3e70020a46baae7f0ec6dacc/widget/nsIPrintSettings.idl#291) with a value from [OutputDestinationType](https://searchfox.org/mozilla-central/rev/3419858c997f422e3e70020a46baae7f0ec6dacc/widget/nsIPrintSettings.idl#93).

### nsIPrintingPromptService.\*

Has been replaced by `nsIPrintDialogService.*`.&#x20;

The function `showPrintDialog()` has also been updated to accept a new parameter to indicate if the entire document or just the current selection should be printed (replacement for the removed `nsIPrintSettings.isPrintSelectionRBEnabled`).

```javascript
await Cc["@mozilla.org/widget/printdialog-service;1"]
 .getService(Ci.nsIPrintDialogService)
 .showPrintDialog(window, selectionOnly, printingSettings);
```

## Changed Core Functions

### OpenMessageInNewTab

The parameters of this function have changed: It is now possible to specify, which msgHdr is to be displayed. To open the currently selected message, update your usage from

```javascript
OpenMessageInNewTab(event);
```

to

```javascript
OpenMessageInNewTab(gFolderDisplay.selectedMessage, { event });
```

## Changed JSM Files

### folderUtils.jsm

Since Thunderbird 97, the JSM no longer exports a set of functions but a single object. The file has also been renamed from `folderUtils.jsm` to `FolderUtils.jsm`. Replace this former usage:

```javascript
var { 
  getFolderProperties,
  allAccountsSorted,
  getMostRecentFolders,
  folderNameCompare 
} = ChromeUtils.import("resource:///modules/folderUtils.jsm");
```

by

```javascript
var { FolderUtils } = ChromeUtils.import("resource:///modules/FolderUtils.jsm");
var {
  getFolderProperties,
  allAccountsSorted,
  getMostRecentFolders,
  folderNameCompare,
} = FolderUtils;
```

or use the FolderUtils namespace to access its functions.
