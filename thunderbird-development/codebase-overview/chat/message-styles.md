# Message Styles

[Chat Core](./) uses a message style system based on HTML, JS and CSS that is very similar to the one created for [Adium](https://adium.im/).  If you plan to create a message style, reading the Adium documentation on the topic may be helpful -- see this [tutorial](https://web.archive.org/web/20160408094746/https://trac.adium.im/wiki/CreatingMessageStyles/Tutorial) and this [reference sheet](https://web.archive.org/web/20160715205801/https://trac.adium.im/wiki/CreatingMessageStyles).

On the other hand, you may prefer to jump right in, using the default message styles as examples: [https://searchfox.org/comm-central/source/mail/components/im/messages](https://searchfox.org/comm-central/source/mail/components/im/messages)

The rest of this page includes a variety of information about the specifics for creating a message theme for Thunderbird.

#### Theme files <a id="theme_files"></a>

The minimal content of chrome/ is:

* Info.plist which contains metadata about the theme
* main.css
* Incoming/Content.html

The other files are optional:

* Style information in CSS files
  * **main.css** is the stylesheet used for the default variant of the theme, and is also included before the variant-specific stylesheet used for other variants.
  * Variants/_&lt;variant name&gt;_.css contains the stylesheet for the variant _&lt;variant name&gt;_.
* Other files used by the stylesheets \(e.g. images\)
* HTML files: these files are used to build the HTML markup of the conversation.

| File Name | Fallback if missing | Usage |
| :--- | :--- | :--- |
| Footer.html | None \(will use an empty string\) | Displayed at the bottom of a conversation. This is the right place to \[\[Instantbird:Message\_Styles:JavaScript\|include JavaScript code\]\]. |
| Header.html | None \(will use an empty string\) | Optionally \(user preference\) displayed at the top of a conversation |
| Status.html | Incoming/Content.html | Used for status messages |
| NextStatus.html | Status.html | Used for status messages directly following another status message added a short time before. |
| Incoming/Content.html | None \(this file is **required**\) | Used for incoming messages. |
| Incoming/Context.html | Incoming/Content.html | Used for incoming messages in an old conversation displayed to give context. |
| Incoming/NextContent.html | Incoming/Content.html | Used for incoming messages directly following another incoming message added a short time before. |
| Incoming/NextContext.html | Incoming/NextContent.html | Used for incoming messages  directly following another incoming message added a short time before in an old conversation displayed to give context. |
| Outgoing/Content.html | Incoming/Content.html | Used for outgoing messages. |
| Outoing/Context.html | Outoing/Content.html | Used for outgoing messages in an old conversation displayed to give context. |
| Outgoing/NextContent.html | Incoming/NextContent.html | Used for outgoing messages directly following another outgoing message added a short time before. |
| Outgoing/NextContext.html | Outgoing/NextContent.html | Used for outgoing messages  directly following another outgoing message added a short time before in an old conversation displayed to give context. |

### HTML Template Replacements <a id="html_template_replacements"></a>

These labels will be replaced with message-specific data when used in HTML files bracketed by percentage signs, e.g. %chatName%.

#### Replacements in header and footer templates <a id="replacements_in_header_and_footer_templates"></a>

* **chatName**: Title of the conversation,
* **sourceName**: The account used for this conversation \(the local alias is used if it is set, otherwise the account name is used\).
* **destinationName**: The name of the conversation,
* **destinationDisplayName**: Title of the conversation,
* **incomingIconPath**: URL to the buddy icon of the person you are talking to. Will fallback to "incoming\_icon.png" if no icon is available,
* **outgoingIconPath**: Should be the URL to the buddy icon of the account used for this conversation. Currently, this always "outgoing\_icon.png",
* **timeOpened**, **timeOpened{format}**: The time when the conversation started, takes an optional format argument.

#### Replacement in both messages and status messages <a id="replacement_in_both_messages_and_status_messages"></a>

* **message**: The text of the message. The actual text will be wrapped in a span node, like this:

```text
<span class="ib-msg-txt">message</span>
```

* **time**, **time{format}**: The time when the message was sent. Takes an optional format parameter \(unfortunately, the formats supported are not the same on all the OSes as this internally calls the native strftime function of the OS\),
* **timestamp**: The time when the message was sent, as an integer value \(number of seconds since 1970\). Useful to compute intervals between messages. Added between 0.2beta2 and 0.2 final,
* **datetime**: The date and time when the message was sent.
* **shortTime**: The time when the message was sent,
* **messageClasses**: CSS classes that apply to the message. This can typically be used in a class attribute of an HTML node. Possible values include:
  * "action": message starting with /me,
  * "message": regular message \(not status/system messages\),
  * "incoming": incoming message,
  * "outgoing": outgoing message,
  * "autoreply": message sent automatically, for example to reply with an away message,
  * "event": system/status message,
  * "nick": chat message that contains your nick,
  * "error": error message \(for example "&lt;user&gt; as cancelled the transfer of &lt;file&gt;"\),
  * "delayed": delayed message \(Currently this seems to be used only to show the recent message history when joining a Jabber chat room\),
  * "notification": notification message \(messages requesting the user's attention\).

#### Replacements in messages \(incoming or outgoing\) only <a id="replacements_in_messages_(incoming_or_outgoing)_only"></a>

* **userIconPath**: URL to the buddy icon of the person you wrote the message. Fallbacks to "Incoming/buddy\_icon.png" if the icon is missing. For outgoing messages, it always uses "Outgoing/buddy\_icon.png",
* **sender**: The name of the sender of the message: the alias if one exists, or the username otherwise,
* **senderColor**: The color associated with the sender of the message. In chatrooms, this will contain a color string valid in a CSS rule. In IM conversations, it will be an empty string.
* **senderStatusIcon**: URL of an icon associated with the current status \(idle, away, offline\) of the sender of the message,
* **messageDirection**: Direction of the message. Always "ltr",
* **senderDisplayName**: Currently identical to **sender**. Should be the server-side alias in the future,
* **senderScreenName**: The username of the sender of the message,
* **service**: Name of the protocol through which the message transited \(e.g AIM, MSN, XMPP, Google Talk, ...\),
* **textbackgroundcolor**: Should be a color string based on the formatting information included in the message. Currently, this is always "transparent".

#### Replacements in status messages only <a id="replacements_in_status_messages_only"></a>

* **status**: Should be a string indicating the nature of the event that caused this message to appear. Currently this isn't implemented, and the result is always an empty string,
* **statusIcon**: URL of an icon associated with the current status \(idle, away, offline\) of the other person in the conversation.

### Info.plist Keys <a id="info.plist_keys"></a>

The file **Info.plist** is a [property list](http://en.wikipedia.org/wiki/Property_list) file containing metadata about the theme.

The following keys are used by Instantbird and Thunderbird:

* **DefaultVariant**: The name of the default variant. Optional. "default" will be used as the name if this key doesn't exist.
* **MessageViewVersion**: If the version number provided is &gt;= 3, the main.css file will be used for all variants, otherwise it will be used only for the default variant \(this is for compatibility with old Adium themes. In new themes, use the value 4\).
* **DisplayNameForNoVariant**: The display name for the default variant \(that is, when no variant is selected\). Optional. If this key doesn't exist, a localized version of the string "Default" will be used for display in the theme selection UI.
* **DisableCombineConsecutive**: This will disable the use of NextContent.html/NextContext.html/NextStatus.html HTML templates. Consécutive messages won't be treated differently from other messages.
* **ActionMessageTemplate**: This is used to specify how action \(/me\) messages should be displayed. The value of this key will be used to replace %message% in Content.html, before doing the replacement. Optional. If this key doesn't exist, the default value "\* %message% \*" will be used.

The following keys work, but only use them if you really feel you absolutely need them, because they make it impossible for the user to select the font in the usual way via Preferences -&gt; Content:

* **DefaultFontFamily**: the default font family to use for the conversation. Values in CSS stylesheet files can override this.
* **DefaultFontSize**: the default font size to use for the conversation. Values in CSS stylesheet files can override this.

If your theme needs to work with Adium too, you need more keys, see [this page](http://trac.adium.im/wiki/CreatingMessageStyles/SandBox/Tutorial1/MessageStyleInfoPlist) for details.

**Example:**

```text
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
	<key>ActionMessageTemplate</key>
	<string>&lt;span class="pseudo" style="%senderColor%"&gt;%sender%&lt;/span&gt; %message%</string>
	<key>DefaultVariant</key>
	<string>Normal</string>
	<key>MessageViewVersion</key>
	<integer>4</integer>
</dict>
</plist>
```

### Including JavaScript in message styles <a id="including_javascript_in_message_styles"></a>

The right file to put JavaScript in a message style is `inline.js`. Some Adium themes use a custom `Template.html` file to include JavaScript in the theme or include JavaScript in other theme files, this is **NOT** supported in Thunderbird.

The code that you insert in the `inline.js` file will be executed as soon as the conversation is loaded. It is a good place to register event listeners.

DOM Mutation events are of particular interest, because they allow you to execute scripts when a new message is added. See the [DOM Events documentation](https://developer.mozilla.org/en-US/docs/Web/Guide/Events/Event_handlers).

**Example:**

This can be placed in the file `inline.js`:

```text
var body = document.getElementById("ibcontent");
body.addEventListener("DOMNodeInserted", function(aEvent) {
   if (!(aEvent.originalTarget instanceof HTMLElement))
     return;
   var node = aEvent.originalTarget;
   /* the node variable contains the inserted node,
      do something useful with it */
}, false);

```

### Thunderbird-specific features <a id="instantbirdthunderbird-specific_features"></a>

Below lists the known differences between message styles in Adium and in Chat Core. Keep in mind that Adium displays conversation using Webkit and that Thunderbird use Gecko, so the rendering may differ slightly.

#### Files <a id="files"></a>

* **NextStatus.html** Optional. Used for status messages that quickly follow another status message. If this file doesn't exist, Status.html will be used instead.

#### Info.plist keys <a id="info.plist_keys"></a>

* **ActionMessageTemplate** This is used to specify how action \(/me\) messages should be displayed. The value of this key will be used to replace %message% in Content.html, before doing the replacement. If this key is not provided, the default value "\* %message% \*" will be used.
* **NoScript** This theme does not include an `inline.js` file for JavaScript.

#### CSS Classes <a id="css_classes"></a>

These classes can be used in the CSS files of themes.

* **ib-msg-txt** This class is present on all texts that are actually a message. The %message% replacement in the HTML templates actually adds a span node with this class around all messages.
* **ib-img-smile** This class is present on all img tags that were added in messages by the smiley system.
* **ib-nick** This class is present on participants' nicknames in multi-user chats \(MUCs\). It carries the "left" attribute when the participant has left the chatroom. You may need an !important if you wish to override the default styling of bold and coloured text. The colour hue of the nick \(for use in CSS HSL colour values\) is also available stored in the "nickColor" DOM attribute of the DOM element carrying the ib-nick class.
* [Mozilla-specific pseudo-classes](https://developer.mozilla.org/en-US/docs/Mozilla/Chat_Core/docs/Web/CSS/Reference/Mozilla_Extensions#Pseudo-elements_and_pseudo-classes), for example: \*:-moz-any-link

#### Unread messages ruler <a id="unread_messages_ruler"></a>

The ruler that appears between read and unread messages \(from Thunderbird 38\) is a `hr` element with the id **\#unread-ruler**. When styling it, changes which override those in the default style for this element \(in conv.css\) must be marked `!important`.

Note: it is possible to style the unread messages themselves by using a CSS sibling selector on the unread ruler.

#### Element IDs which must not be used by message styles <a id="element_ids_which_must_not_be_used_by_message_styles"></a>

The following element IDs are used internally by Thunderbird and must not be given to any DOM elements by message styles:

* **insert-before**
* **actual-insert**
* **next-messages-start**
* **next-messages-end**
* **unread-ruler**
* **end-of-split-block**

