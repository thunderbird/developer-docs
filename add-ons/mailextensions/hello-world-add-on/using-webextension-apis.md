---
description: Extending the simple example extension to make use of WebExtension APIs.
---

# Using WebExtension APIs

In this step we will add a button to the message display header area and a click on it will show some information about the currently displayed message.

## Adding a `message_display_action`

Similar to adding the `browser_action` in the first part, we have to extend the `manifest.json` to add the `message_display_action`.:

```
"message_display_action": {
    "default_popup": "messagePopup/popup.html",
    "default_title": "Details",
    "default_icon": "images/internet-32px.png"
},
```

## popup.html

The HTML file for our popup needs some placeholders, which we can later fill using JavaScript and Thunderbird's WebExtension APIs. Create a `messagePopup` folder inside the `hello-world` project folder and place the following `popup.html` file in the newly created folder:

```
<!DOCTYPE html>
<html>
<head>
    <meta charset="utf-8">
    <title>Details</title>
    <link rel="stylesheet" type="text/css" media="screen" href="popup.css">
    <script src="popup.js"></script>
</head>
<body>
    <div class="grid-container">
        <div class="header">Subject:</div><div id="subject" class="content"></div>
        <div class="header">From:</div><div id="from" class="content"></div>
    </div>
</body>
</html>
```

## popup.css

Place the following `popup.css` file in the same folder as the `popup.html` file.

```
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

We use modern CSS styling to format our HTML into a tabular view (instead of using tables). The `display: grid` container defines how our 4 DIV elements inside the container DIV are aligned. Check the [grid documentation](https://developer.mozilla.org/de/docs/Web/CSS/CSS_Grid_Layout) or this [grid guide](https://css-tricks.com/snippets/css/complete-guide-grid/) for more details.

## popup.js

Most if not all WebExtension API functions return a [Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Using_promises) instead of an actual value. This is similar to callback based asynchronous functions, but still allow writing sequential code.

The author of this example prefers [the `async`/`await` syntax](https://developer.mozilla.org/en-US/docs/Learn/JavaScript/Asynchronous/Async_await) over the `.then()` approach for handling Promises. The `load()` function defined in the following `popup.js` is therefore defined as `async`. All used WebExtension API calls, or better all returned Promises of WebExtension API calls are awaited, which means the execution flow in this function sort of halts until the individual Promise is fulfilled. 

```
async function load() {
  // The user clicked our button, get the active tab in the current window using
  // the tabs API.
  let tabs = await messenger.tabs.query({active: true, currentWindow: true});

  // Get the message currently displayed in the active tab, using the
  // messageDisplay API
  // Note: As stated in the documentation this needs the messagesRead permission.

  // The returned message is a MessageHeader object with the most relevant
  // information
  let message = await messenger.messageDisplay.getDisplayedMessage(tabs[0].id);

  // Update the HTML fields with the message subject and sender.
  document.getElementById("subject").textContent = message.subject;
  document.getElementById("from").textContent = message.author;
}

document.addEventListener("DOMContentLoaded", load);
```

{% hint style="info" %}
In Thunderbird, all WebExtension API can be accessed through the _browser.\*_ namespace, as with Firefox, but also through the _messenger.\*_ namespace, which is a better fit for Thunderbird.
{% endhint %}

### messenger.tabs.query

The [tabs API](https://webextension-api.thunderbird.net/en/91/tabs.html) provides access to Thunderbird's tabs. We need to get hold of the current active tab to learn which message is displayed there. We use the [`query`](using-webextension-apis.md#adding-a-message_display_action) method to find it.

{% hint style="info" %}
Using `messeger.tabs.getCurrent()` will not work, as that always returns the tab in which it is being called from. In our case, the call is executed from inside the popup of the `message_display_action` and not from inside the tab we are looking for.
{% endhint %}

### messenger.messageDisplay.getDisplayedMessage

The [`getDisplayedMessage`](https://webextension-api.thunderbird.net/en/91/messageDisplay.html#getdisplayedmessage-tabid) method of the [messageDisplay API](https://webextension-api.thunderbird.net/en/91/messageDisplay.html) provides access to the currently viewed message(s) in a given tab. It returns a [MessageHeader](https://webextension-api.thunderbird.net/en/91/messages.html#messageheader) object from the [messages API](https://webextension-api.thunderbird.net/en/91/messages.html) with basic information.

At this stage we are interested in the subject (line 26 in popup.js) and the author (line 27 in popup.js).

As mentioned in the documentation of the getDisplayMessage method, it requires the <mark style="color:red;">`messagesRead`</mark> permission. So we have to add a `permissions` section to our `manifest.json`.

```
"permissions": [
    "messagesRead"
],
```
