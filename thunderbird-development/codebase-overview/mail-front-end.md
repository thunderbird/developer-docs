---
description: A.K.A. the 3-pane tabs and message tabs/windows
---

# Mail Front-End

In January 2023 the mail front-end was rebuilt from scratch, replacing what evolved from the original Netscape front-end. This is a developers' guide to the new UI.

## Mail tab types

The mail front-end consists of two types of tabs (and a standalone window, more about that later) – the 3-pane tab `mail3PaneTab` and the message tab `mailMessageTab`. These are defined in [mailTabs.js](https://searchfox.org/comm-central/source/mail/base/content/mailTabs.js) and provide the `tabInfo` objects for tabmail to control. Most code from outside the tabs will go through here in some form, although knowing the specific details should be unnecessary.

Each mail tab `tabInfo` object has these read-only properties:

* `chromeBrowser` – This is a XUL `<browser>` object which displays the tab's contents, either `about:3pane` or `about:message`. As with any `<browser>` object you can access the displayed page with the `contentWindow` and `contentDocument` properties.
* `browser` and `linkedBrowser` – Both refer to the XUL `<browser>` currently displaying content (an email message or a web page) to the user, or `null` if there isn't any.
* `message` – The currently displayed message as an `nsIMsgDBHdr`, if there is one.
* `folder` – The folder containing the currently displayed message, an `nsIMsgFolder`.

## Accessing tabs

If the mail tab you're interested in is the current tab, the following properties of tabmail point to it:

* `currentTabInfo` – The `tabInfo` object described above.
* `currentAbout3Pane` – The `window` object of the page displayed in the `chromeBrowser`, if the current tab is a 3-pane tab.
* `currentAboutMessage` – The `window` object of the message display page, which for message tabs is the page displayed in the `chromeBrowser`, and for 3-pane tabs a page within that.

If it's not the current tab, you can get the `tabInfo` object from tabmail and use the properties listed in the previous section to access it.

## Mail windows

The standalone mail window also contains a XUL `<browser>` displaying `about:message`. The browser can be accessed from the window's `messageBrowser` property.

## about:3pane

`about:3pane` is the main UI that users see when Thunderbird starts: the folder pane, the thread pane, and the message pane. It lives in the tree as [about3Pane.xhtml](https://searchfox.org/comm-central/source/mail/base/content/about3Pane.xhtml) and similarly named [JS](https://searchfox.org/comm-central/source/mail/base/content/about3Pane.js), [CSS](https://searchfox.org/comm-central/source/mail/themes/shared/mail/about3Pane.css) and [Fluent](https://searchfox.org/comm-central/source/mail/locales/en-US/messenger/about3Pane.ftl) files.

### Folder pane

The folder pane displays the accounts and folders within them. Various modes of display are available.

### Thread pane

The thread pane displays the list of messages in the current folder, and the Quick Filter bar for filtering those messages.

### Message pane

This mesage pane contains more XUL `<browser>`s for displaying various things:

* `webBrowser` – displays web content in a child process as Firefox does.
* `multiMessageBrowser` – displays messages if more than one is selected.
* `messageBrowser` – displays a single message using `about:message`.

Only one is visible at any given time.

### Account central

If an account is selected in the folder pane instead of a folder, yet another `<browser>`, `accountCentralBrowser` – displays Account Central, a page of various things you can do in Thunderbird.

## about:message

`about:message` is all of the UI that displays a single message, including the message headers and attachments. It is used as the message pane in `about:3pane` and by itself as a message tab or window. Like `about:3pane` it lives in the tree as [aboutMessage.xhtml](https://searchfox.org/comm-central/source/mail/base/content/aboutMessage.xhtml), [aboutMessage.js](https://searchfox.org/comm-central/source/mail/base/content/aboutMessage.js), [messageHeader.css](https://searchfox.org/comm-central/source/mail/themes/shared/mail/messageHeader.css) and [about3Pane.ftl](https://searchfox.org/comm-central/source/mail/locales/en-US/messenger/about3Pane.ftl) files.

Message contents themselves are displayed in a `<browser>` (if you're counting, we're now three deep) which can be accessed by the `content` property of an `about:message` `window`.
