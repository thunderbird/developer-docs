# Chat Core Protocols

The [Chat Core](./) code used by Thunderbird has some abstractions to deal with the differences between protocols \(e.g. IRC vs. XMPP\).

### Protocol Interfaces <a id="protocol_interfaces"></a>

Protocols are implemented in chat core using JavaScript.

Protocols must implement the proper interfaces and be registered with the category manager in order to be found. Protocols need to implement the [prplI\* interfaces](https://searchfox.org/comm-central/source/chat/components/public/) \(this can mostly be done using [jsProtoHelper](https://searchfox.org/comm-central/source/chat/modules/jsProtoHelper.sys.mjs)\). The minimum set of interfaces to implement are:

* [prplIProtocol](https://searchfox.org/comm-central/source/chat/components/public/prplIProtocol.idl)
* [prplIAccount](https://searchfox.org/comm-central/source/chat/components/public/imIAccount.idl)
* [prplIConversation](https://searchfox.org/comm-central/source/chat/components/public/prplIConversation.idl)

#### Useful Code <a id="useful_code"></a>

* [imXPCOMUtils](https://searchfox.org/comm-central/source/chat/modules/imXPCOMUtils.jsm): Additional XPCOM utilities.
* [JavaScript socket](https://searchfox.org/comm-central/source/chat/modules/socket.sys.mjs): Simplified socket code.
* [jsProtoHelper](https://searchfox.org/comm-central/source/chat/modules/jsProtoHelper.sys.mjs): Includes basic JavaScript implementations of the interfaces and some helper code.
* [XML HTTP Request helper](https://searchfox.org/comm-central/source/mozilla/toolkit/modules/Http.jsm): Simplified HTTP request code

#### Example Implementations <a id="example_implementations"></a>

The code for the JavaScript protocols we ship by default is [here](https://searchfox.org/comm-central/source/chat/protocols).

* [IRC](https://searchfox.org/comm-central/source/chat/protocols/irc): A full implementation, including private chats and MUCs, etc.
* [JavaScript Test Protocol](https://searchfox.org/comm-central/source/chat/protocols/jsTest): An extremely simple example meant to serve as test code for the interfaces.
* [Matrix](https://searchfox.org/comm-central/source/chat/protocols/matrix): An implementation that heavily depends on an external SDK.
* [XMPP](https://searchfox.org/comm-central/source/chat/protocols/xmpp): A full implementation, including private chats and MUCs, etc. There are also other protocols which inherit and customize XMPP:
  * [GTalk](https://searchfox.org/comm-central/source/chat/protocols/gtalk)
  * [Odnoklassniki](https://searchfox.org/comm-central/source/chat/protocols/odnoklassniki)
* There are also some stub implementations for protocols that Thunderbird used to support, but no longer does. These exist purely for the icons to show up and for a nice error message to appear when the account tries to connect.

### Useful Code Snippets <a id="useful_code_snippets"></a>

#### Using Services.core.getProtocols\(\) to list all protocols <a id="using_services.core.getprotocols()_to_list_all_protocols"></a>

This lists the protocol plugins that the core service knows about. You can copy the code \(as it is\), paste it in the error console \(linebreaks will automatically be ignored\) and press "Enter" to run it.

```text
Components.utils.import("resource:///modules/imServices.jsm");
let protocols = Services.core.getProtocols();
let result = "";
for (let p of protocols) {
  let proto = p.QueryInterface(Components.interfaces.prplIProtocol);
  result += proto.name + "\t\t" + proto.id + "\n";
}
Services.console.logStringMessage(result);
```

