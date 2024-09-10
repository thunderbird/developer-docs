---
description: Extending the simple example extension to make use of WebExtension APIs.
---

# Using WebExtension APIs

In the second part of the Hello World Extension Tutorial, we will add a "_Details_" button to the message header toolbar. A click on it will show some information about the currently displayed message, which we retrieve using Thunderbird's WebExtension APIs.

![](../../.gitbook/assets/hello-word-2.png)

## Using a `message_display_action`

Similar to adding the `browser_action` in the first part of the Hello World Extension Tutorial, we have to extend the `manifest.json` to add the `message_display_action`.:

```json
"message_display_action": {
    "default_popup": "messagePopup/popup.html",
    "default_title": "Details",
    "default_icon": "images/internet-32px.png"
},
```

### popup.html

The HTML file for our popup needs some place-holders, which we can later fill using JavaScript and Thunderbird's WebExtension APIs. We create a `messagePopup` folder inside the `hello-world` project folder and place the following `popup.html` file in the newly created folder:

{% code title="popup.html" %}
```html
<!DOCTYPE html>
<html>

<head>
    <meta charset="utf-8">
    <title>Details</title>
    <link rel="stylesheet" type="text/css" media="screen" href="popup.css">
</head>

<body>
    <div class="grid-container">
        <div class="header">Subject:</div>
        <div id="subject" class="content"></div>
        <div class="header">From:</div>
        <div id="from" class="content"></div>
        <div class="header">Received-Header:</div>
        <div id="received" class="content"></div>
    </div>
    <script type="module" src="popup.js"></script>
</body>

</html>
```
{% endcode %}

### popup.css

We place the following `popup.css` file in the same folder as the `popup.html` file.

{% code title="popup.css" %}
```css
.grid-container {
    display: grid;
    grid-template-columns: 1fr 6fr;
}

.header {
    font-weight: bold
}

.grid-container div {
    margin: 1ex;
}
```
{% endcode %}

Instead of tables, we use modern CSS styling to format our HTML into a tabular view. The `display: grid` container defines how our 6 DIV elements inside the container DIV are aligned. Check the [grid documentation](https://developer.mozilla.org/de/docs/Web/CSS/CSS\_Grid\_Layout) or this [grid guide](https://css-tricks.com/snippets/css/complete-guide-grid/) for more details.

### popup.js

All WebExtension API functions return a [Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Using\_promises) instead of an actual value. This aims to simplify the handling of asynchronous functions.

The author of this example prefers [the `async`/`await` syntax](https://developer.mozilla.org/en-US/docs/Learn/JavaScript/Asynchronous/Async\_await) over the `.then()` approach for handling Promises. This allows us to avoid the so called callback-hell of asynchronous functions and instead keep writing sequential code by simply awaiting all the returned Promises.

{% code title="popup.js" lineNumbers="true" %}
```javascript
// The user clicked our button, get the active tab in the current window using
// the tabs API.
let tabs = await messenger.tabs.query({ active: true, currentWindow: true });

// Get the message currently displayed in the active tab, using the
// messageDisplay API. Note: This needs the messagesRead permission.
// The returned message is a MessageHeader object with the most relevant
// information.
let message = await messenger.messageDisplay.getDisplayedMessage(tabs[0].id);

// Update the HTML fields with the message subject and sender.
document.getElementById("subject").textContent = message.subject;
document.getElementById("from").textContent = message.author;

// Request the full message to access its full set of headers.
let full = await messenger.messages.getFull(message.id);
document.getElementById("received").textContent = full.headers.received[0];

```
{% endcode %}

{% hint style="warning" %}
The `popup.js` file was loaded as a top level ES6 module by specifying `type="module"` in its `script` tag. This allows us to use the `await` keyword directly in file scope code. Otherwise we would need to use an asynchronous wrapper function:\
\
`async function load() {`\
`let tabs = await messenger.tabs.query({`\
`active: true,`\
`currentWindow: true,`\
`});`\
`...`\
`}`\
`load();`
{% endhint %}

{% hint style="info" %}
In Thunderbird, all WebExtension API can be accessed through the `browser.*` namespace, as with Firefox, but also through the `messenger.*` namespace, which is a better fit for Thunderbird.
{% endhint %}

#### messenger.tabs.query()

The [tabs API](https://webextension-api.thunderbird.net/en/beta-mv2/tabs.html) provides access to Thunderbird's tabs. We need to get hold of the current active tab to learn which message is displayed there. We use the [`query`](using-webextension-apis.md#adding-a-message\_display\_action) method to find it in line `3`.

{% hint style="info" %}
Using `messenger.tabs.getCurrent()` will not work, as that always returns the tab in which it is being called from. In our case, the call is executed from inside the popup of the `message_display_action` and not from inside the tab we are looking for.
{% endhint %}

#### messenger.messageDisplay.getDisplayedMessage()

The [`getDisplayedMessage`](https://webextension-api.thunderbird.net/en/beta-mv2/messageDisplay.html#getdisplayedmessage-tabid) method of the [messageDisplay API](https://webextension-api.thunderbird.net/en/beta-mv2/messageDisplay.html) provides access to the currently viewed message in a given tab. It returns a Promise for a [MessageHeader](https://webextension-api.thunderbird.net/en/beta-mv2/messages.html#messageheader) object from the [messages API](https://webextension-api.thunderbird.net/en/beta-mv2/messages.html) with basic information about the message in line `9`.

At this stage we are interested in the subject (line `12`) and the author (line `13`).

{% hint style="warning" %}
The [`getDisplayMessage`](https://webextension-api.thunderbird.net/en/beta-mv2/messageDisplay.html#getdisplayedmessage-tabid) method requires the <mark style="color:red;">`messagesRead`</mark> permission, which needs to be added to the `permissions` key of our `manifest.json` file.
{% endhint %}

```json
"permissions": [
    "messagesRead"
],
```

#### messenger.messages.getFull()

We also want to get the `received` header from the message. That information is not part of the general `MessageHeader` object, so we have to request the full message.

The [`getFull`](https://webextension-api.thunderbird.net/en/beta-mv2/messages.html#getfull-messageid-options) method in line `16` returns a Promise for a [`MessagePart`](https://webextension-api.thunderbird.net/en/beta-mv2/messages.html#messagepart) object, which relates to messages containing multiple MIME parts. The `headers` member of the part returned by `getFull` includes the headers of the message (excluding headers which are part of nested MIME parts available through the `parts` member).

## Testing the Extension

Let's double-check that we made the [correct changes](https://github.com/thunderbird/webext-examples/commit/73f5b3776b0d4f9c0e9281168de8f9313cc474f8?diff=unified) and have all the files in the right places:

```
hello-world/
  ├── manifest.json
  ├── images/
      ├── internet.png
      ├── internet-16px.png
      └── internet-32px.png
  ├── mainPopup/
      ├── popup.css
      ├── popup.html
      └── popup.js
  └── messagePopup/
      ├── popup.css
      ├── popup.html
      └── popup.js
```

This is how our `manifest.json` should now look like:

{% code title="manifest.json" %}
```json
{
    "manifest_version": 2,
    "name": "Hello World Example",
    "description": "A basic Hello World example extension!",
    "version": "2.0",
    "author": "Thunderbird Team",
    "browser_specific_settings": {
        "gecko": {
            "id": "helloworld@yoursite.com",
            "strict_min_version": "128.0"
        }
    },
    "browser_action": {
        "default_popup": "mainPopup/popup.html",
        "default_title": "Hello World",
        "default_icon": "images/internet-32px.png"
    },
    "message_display_action": {
        "default_popup": "messagePopup/popup.html",
        "default_title": "Details",
        "default_icon": "images/internet-32px.png"
    },
    "permissions": [
        "messagesRead"
    ],
    "icons": {
        "64": "images/internet.png",
        "32": "images/internet-32px.png",
        "16": "images/internet-16px.png"
    }
}
```
{% endcode %}

### Installing

As described in the[ first part of the Hello World Extension Tutorial](./#installing), go to the Add-ons Manager to open the Debug Add-on Page and temporarily install the extension.

### Trying it Out

Open any message and you will find a "_Details_" button in the message header toolbar. A click on it will show you the `subject`, the `author` and the first found `received` header of the currently viewed message.
