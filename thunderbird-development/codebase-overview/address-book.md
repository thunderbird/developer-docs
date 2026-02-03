# Address Book

The Address Book code is found in two places: the UI is primarily in `mail/components/addrbook`, while the back end is in `mailnews/addrbook`. This documentation only describes the back end.

## Interfaces and Classes

### Address Book Manager

The address book manager is responsible for organising individual "directories" (address books), including reading the configuration from the preferences at start-up and creating the directory objects. It is defined by the interface `nsIAbManager` and implemented as `AddrBookManager`. You can get a reference to the manager at `MailServices.ab`.

The address book manager can get a reference to a particular directory in a number of ways:

* By URI. Registered directories have a URI defined by the type of directory and any information needed to identify an individual directory. For example `jsaddrbook://abook.sqlite`.
* By unique identifier (UID). For example `70139e91-37a5-47fd-8856-2f0756f43ef1`.
* By location in the preferences. Directories store their preferences at `ldap_2.servers.<some identifier>`.&#x20;

`nsIAbManager` also defines the `DIRECTORY_TYPE` constants mentioned below.

### Directories

Individual address books are referred to as "directories" or "books" in the code. They implement `nsIAbDirectory`.&#x20;

There are different implementations:

* A base class `AddrBookDirectory` which handles most of the implementation detail using data provided by a sub-class. It should not be instantiated directly.
* The most common type is `SQLiteDirectory` which, as the name implies, stores data in an SQLite database in the user profile. It inherits from `AddrBookDirectory`. Directories of this type have URIs with the scheme `jsaddrbook`, and are of type `JS_DIRECTORY_TYPE`.
* `CardDAVDirectory` extends `SQLiteDirectory` by adding CardDAV capabilities. Directories of this type have URIs with the scheme `jscarddav`, and are of type `CARDDAV_DIRECTORY_TYPE`.
* `LDAPDirectory` extends `AddrBookDirectory` and adds LDAP capabilities. Directories of this type have URIs with the scheme `moz-abldapdirectory`, and are of type `LDAP_DIRECTORY_TYPE.`
* `nsAbDirProperty` provides a base C++ implementation of `nsIAbDirectory` to OS-specific address books `nsIAbOSXDirectory` and `nsAbOutlookDirectory`. These are both of type `MAPI_DIRECTORY_TYPE`. The OS-specific types are likely to be discontinued.
* WebExtension APIs can provide contact suggestions to the user when composing messages. This is done via a `ASYNC_DIRECTORY_TYPE` directory.

### Contacts

Directories contain contacts or "cards", which implement the interface `nsIAbCard`. The are accessible by the `childCards` property as well as methods for searching, adding, modifying, and deleting contacts. (Confusingly a "card" can also refer to a mailing list, see below.)

Contacts are implemented by `AddrBookCard` for all of the JS code, or `nsAbCardProperty` in C++.

The current implementation stores contact data as key/value pairs. Work is underway to replace this with a more capable data storage based on the [vCard format](https://tools.ietf.org/html/rfc6350).

### Mailing Lists

A mailing list (or just a "list" in many places) is a collection of contacts. To be added to a mailing list, a contact _must_ have an email address. A mailing lists can be both `nsIAbDirectory` and `nsIAbCard`, depending on the situation. Directories provide an array of their mailing lists as `nsIAbDirectory` with the `childNodes` property. They also appear as `nsIAbCard` in the `childCards` property and some of the methods that handle cards.

A mailing list can not have any `childNodes` of its own. However in a confusing quirk, it can have the card of another mailing list in its `childCards`.

Mailing lists are implemented in `AddrBookMailingList`.

## Notifications

The address book code fires observer service notifications. The notification names should be self-explanatory.

### Notifications about directories

In all cases the "subject" of the notification is the directory.

* addrbook-directory-created
* addrbook-directory-updated – the "data" contains the property that changed, and only the "DirName" property can change&#x20;
* addrbook-directory-deleted
* addrbook-directory-invalidated – many contacts changed, anything storing or displaying contacts should be thrown away and reloaded
* addrbook-directory-synced – CardDAV sync succeeded
* addrbook-directory-sync-failed – CardDAV sync failed

### Notifications about contacts

In all cases the "subject" of the notification is the contact, and unless otherwise stated "data" contains the UID of the directory containing the contact.

* addrbook-contact-created
* addrbook-contact-updated
* addrbook-contact-properties-updated – "data" contains a JSON-stringified object containing the old and new values of any properties that changed
* addrbook-contact-deleted

### Notifications about lists

In these cases the "subject" of the notification is the list as `nsIAbDirectory` and "data" is the parent directory's UID.

* addrbook-list-created
* addrbook-list-updated
* addrbook-list-deleted

In these cases the "subject" is the contact in question, and "data" is the list's UID.

* addrbook-list-member-added
* addrbook-list-member-removed

## Transition to vCard Storage

As mentioned above, contact properties are stored as key/value pairs. This has limitations in a modern address book:

* There's a fixed set of keys. Although there's no real restrictions on keys you _could_ use, no user interface is available for anything outside of the known keys.
* Multiple values for the same key aren't possible. For example if a contact has three email addresses, the first two can be stored in `PrimaryEmail` and `SecondEmail`, but there's nowhere to store the third.
* Storing meta information about the values is impossible. There's no way to mark an email address as a work address or a home address, for example.

To combat this, from Thunderbird 102 contacts stored in local directories will be converted to use the [industry standard vCard format](https://tools.ietf.org/html/rfc6350). CardDAV directories already use vCard, although the data was converted to key/value pairs so the UI could use it.

LDAP and OS-specific address books will continue to use keys/values.

### New interface properties

`nsIAbCard` gains two new members:

* `supportsVCard` a boolean value indicating support for vCard (or lack thereof). Only `AddrBookCard` objects currently support vCard.
* `vCardProperties` is a `VCardProperties` object if the card supports vCard, or null.

The `VCardProperties` class contains methods for parsing, manipulating, and serialising vCards. Each piece of information in a card is represented by a `VCardPropertyEntry` object. See [VCardUtils.sys.mjs](https://github.com/thunderbird/thunderbird-desktop/blob/main/mailnews/addrbook/modules/VCardUtils.sys.mjs) for more information.

### Storage changes

In practice, we'll be storing the vCard data as just another key/value pair. The key used will be `_vCard` and the value will be the entire vCard.

When a card is saved to a directory, the following things happen:

* If it supports vCard, the `vCardProperties` member is serialised.
* If it doesn't, the existing key/value pairs are converted to a `VCardProperties` object, then serialised.
* Some properties are collected from the card:
  * display name (`DisplayName`)
  * first and last names (`FirstName` and `LastName`)
  * first and second preference email addresses (`PrimaryEmail` and `SecondEmail`)
  * nick name (`NickName`)
* The serialised vCard, these properties, and any key/value properties which _can't_ be stored in a vCard are saved.
* Any key/value properties which _can_ be stored in the vCard are abandoned. No information is lost because the values are part of the vCard.

The names and addresses stored separately are for performance reasons. The vCard is considered the true source of information.

{% hint style="info" %}
No migration takes place when the user upgrades to a Thunderbird version that supports vCard. However at this point the version number of the database storage is incremented (from 3 to 4) and a backup is automatically created.

Cards are only migrated when they are saved.
{% endhint %}

### Data conversion between keys/values and vCard

[VCardUtils.sys.mjs](https://github.com/thunderbird/thunderbird-desktop/blob/main/mailnews/addrbook/modules/VCardUtils.sys.mjs) contains a number of utility functions for converting between the storage types:

* `VCardUtils.abCardToVCard` converts an `nsIAbCard` (any implementation) into a vCard string.
* `VCardUtils.propertyMapToVCard` converts a `Map` of keys and values to a vCard string.
* `VCardUtils.vCardToAbCard` converts a vCard string to an `AddrBookCard`.
* `VCardProperties.fromVCard` converts vCard string to a `VCardProperties` object.
* `VCardProperties.fromPropertyMap` converts a `Map` of keys and values to a `VCardProperties` object.
* `VCardProperties.prototype.toPropertyMap` converts a `VCardProperties` object to a `Map` of keys and values.
* `VCardProperties.prototype.toVCard` converts a `VCardProperties` object to a vCard string.

{% hint style="warning" %}
Conversion from a key/value dictionary to vCard should not result in any data loss. In the other direction data loss is possible.
{% endhint %}

To see exactly what fields are converted and what they are converted to, see `typeMap` in VCardUtils.sys.mjs.
