# Chat Core

Chat Core is the code for instant messaging that is used by Thunderbird. It provides a number of functions and capabilities, including:

* [Communications protocols](chat-core-protocols.md) - IRC, Matrix, XMPP (and XMPP-based protocols)
* [Message styles](message-styles.md)
* Account configuration
* [Contacts storage](contacts.md)
* Message logging
* [Emoticon (smileys) handling](broken-reference)

The Chat Core code lives in the chat/ directory of the [Thunderbird](https://github.com/thunderbird/thunderbird-desktop) repository.

#### [Chat Core Protocols](chat-core-protocols.md)

The Chat Core code used by Thunderbird has some abstractions to deal with the differences between protocols (e.g. IRC vs. XMPP).

#### [Contacts](contacts.md)

Contacts are at the heart of instant messaging, and thus the Chat Core has a way to abstract to a "person" (represented by an [`imIContact`](https://github.com/thunderbird/thunderbird-desktop/blob/main/chat/components/public/imIContactsService.idl) instance), which might connect to multiple networks, etc.

#### [Keyboard shortcuts](keyboard-shortcuts.md)

This is a list of the available keyboard shortcuts with brief descriptions of what they do.

#### [Message Styles](message-styles.md)

Chat Core uses a message style system based on HTML, JS and CSS that is very similar to the one created for [Adium](https://adium.im/).  If you plan to create a message style, reading the Adium documentation on the topic may be helpful -- see this [tutorial](https://web.archive.org/web/20160408094746/https://trac.adium.im/wiki/CreatingMessageStyles/Tutorial) and this [reference sheet](https://web.archive.org/web/20160715205801/https://trac.adium.im/wiki/CreatingMessageStyles).

#### [Notifications](notifications.md)

This is a page for documenting the notifications in Thunderbird. This is likely out of date. Notifications are grouped by interface you need to attach the observer to.
