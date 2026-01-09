# Contacts

Contacts are at the heart of instant messaging, and thus the Chat Core has a way to abstract to a "person" \(represented by an [`imIContact`](https://github.com/thunderbird/thunderbird-desktop/blob/main/chat/components/public/imIContactsService.idl) instance\), which might connect to multiple networks, etc.

{% hint style="warning" %}
_Draft: This page is not complete._
{% endhint %}

### Display Name <a id="display_name"></a>

The display name used in the buddy list window or in conversations can come from several sources, by precedence order:

* User-set alias, stored locally \(set when the user renames someone from Thunderbird\)
* Server-stored alias \(some protocol store the aliases online\)
* Display name / Friendly name \(set by the remote contact, stored on the server\)
* Username, the unique identifier of the buddy for this protocol. Can be numbers \(e.g. ICQ, QQ\), email addresses \(e.g. MSN, XMPP\) or some other string.

Possible storage locations:

* mozStorage \(the file blist.sqlite\)
* chat core \(cached copy\)
* server read/write \(server-stored alias\)
* server read only \(display names\)

