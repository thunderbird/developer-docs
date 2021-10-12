---
description: Extending the simple example extension to use a backgroud page.
---

# Using a Background Page

In this third section of the Hello World Example, we will introduce the concept of the WebExtension background page.

We will keep track of incoming mail, add a context menu entry to our toolbar button in the main Thunderbird toolbar and a click on it will open a notification which will print the collected information from the last 24h.

## Background Page and Background Scripts

In the first two sections of the Hello World Example, we used well-defined UI hooks to load HTML pages when the user opened one of our popups. Those HTML pages support the same technologies as normal web pages (HTML, CSS, JavaScript, Web APIs) and additionally WebExtension APIs to interact with Thunderbird. 

In contrast to the fact that those HTML pages are only loaded after the user has initiated an action, the background page - if defined - is loaded automatically when the add-on is loaded during Thunderbird start or after the add-on has been manually enabled or installed. The background page is automatically destroyed when the add-on is shutting down.

The background page is a standard HTML page, supporting the same technologies as ordinary HTML pages, but it is never shown to the user. Its main purpose is to load one or more JavaScript scripts. Those background scripts can be used to listen for events or to initialize and properly set up the add-on. As [described in the MailExtension guide](../#background-page), there are two ways to load background scripts:

1. Actually defining a background HTML page, that uses `script` tags to load the JavaScript files.
2. Just defining the to-be-loaded JavaScript files and let Thunderbird create a background page on-the-fly.

The author of this example prefers the first option, as it allows declaring the loaded JavaScript file (in the script tag) as `type="module"`, which enables support for [using ES6 modules](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Modules). We therefore add the following section to our `manifest.json` file:

```
"background": {
    "page": "background.html"
},
```

We place the following `background.html` file into our `hello-world` project folder:

{% code title="background.html" %}
```html
<!DOCTYPE html>
<html>
<head>
    <script src="background.js" type="module"></script>
</head>
</html>
```
{% endcode %}

Since we declare the `background.js` file as a module, we can use the JavaScript [`import`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Modules#importing_features_into_your_script) statement to load other JavaScript module files and have full control over our dependencies from within `background.js` and never have to adjust the `manifest.json` file or the `background.js` file in order to load additional JavaScript files.

Let's create an empty `background.js` script in the `hello-world` project folder, which we can extend in the following sections.

## Listening for New Messages

In order to listen for new messages, we have to add a listener for the [`onNewMessageReceived`](https://webextension-api.thunderbird.net/en/91/messages.html#onnewmailreceived) event:

```javascript
messenger.messages.onNewMailReceived.addListener(async (folder, messages) => {
    // Do something with folder and messages.
})
```

The above code is using an inline arrow function to define the callback function, which is called for each `onNewMailReceived` event. This is identical to the following implicit function definition:

```java
async function onNewMailReceivedCallback(folder, messages) {
    // Do something with folder and messages.
}
messenger.messages.onNewMailReceived.addListener(onNewMailReceivedCallback)
```

The author of this example prefers to use inline arrow functions, if the function is not used elsewhere.

As described in the [documentation of the `onNewMailReceived`](https://webextension-api.thunderbird.net/en/91/messages.html#onnewmailreceived)event, its callback function has two parameters: `folder` being a [`MailFolder`](https://webextension-api.thunderbird.net/en/91/folders.html#folders-mailfolder) and `messages` being a [`MessageList`](https://webextension-api.thunderbird.net/en/91/messages.html#messages-messagelist). 

{% hint style="info" %}
Since Thunderbird's WebExtension API potentially has to handle a lot of messages, messages in a `MessageList` object are paginated. For more information, please check the [Working with Message Lists](https://webextension-api.thunderbird.net/en/91/how-to/messageLists.html) tutorial.
{% endhint %}

To store the information of the new received mail, we add the following code to our background script:

```javascript
// A wrapper function returning an async iterator for a MessageList.
async function* iterateMessagePages(page) {
    for (let message of page.messages) {
        yield message;
    }

    while (page.id) {
        page = await messenger.messages.continueList(page.id);
        for (let message of page.messages) {
            yield message;
        }
    }
}

// Listener for new message events.
messenger.messages.onNewMailReceived.addListener(async (folder, messages) => {
    let { messageLog } = await messenger.storage.local.get({ messageLog: [] });

    for await (let message of iterateMessagePages(messages)) {
        messageLog.push({
            folder: folder.name,
            time: Date.now(),
            message: message
        })
    }

    await messenger.storage.local.set({ messageLog });
})

```

### messenger.storage.local.get

In line 17 of the shown background script, we request the current `messageLog` entry from the WebExtensions local storage. The used syntax allows defining the default value of `[]`(an empty array), if there currently is no `messageLog` entry stored. 

We could also request multiple values:

```javascript
let storage = await messenger.storage.local.get({
  messageLog: [],
  aBoolValue: true,
  aStringValue: "none"
});
console.log(storage);
```

The call to `storage.local.get` returns a single object with the requested entries, for example the above `console.log(storage)` could print the following:

```
{
  messageLog: [],
  aBoolValue: false,
  aStringValue: "The sky is the limit."
}
```

To access the content of the `messageLog` member, one would have to use `storage.messageLog`. That is sometimes not the desired behavior, and instead one could store the requested value directly in a variable, as shown in line 17 of our background script. This is called _object destructering_ and it is mapping the content of the `messageLog` member of the returned object to the `messageLog` variable. Any other non-matching returned member is ignored.

## Testing the Extension

Let's double-check that we have all the files in the right places:

```
hello-world/
  ├── manifest.json
  ├── background.html
  ├── background.js
  ├── mainPopup/
      ├── popup.html
      ├── popup.css
      └── popup.js
  ├── messagePopup/
      ├── popup.html
      ├── popup.css
      └── popup.js
  └── images/
      ├── internet.png
      ├── internet-32px.png
      └── internet-16px.png
```

This is how our `manifest.json` should now look like:

{% code title="manifest.json" %}
```json
{
    "manifest_version": 2,
    "name": "Hello World",
    "description": "Your basic Hello World extension!",
    "version": "3.0",
    "author": "[Your Name Here]",
    "applications": {
        "gecko": {
            "id": "helloworld@yoursite.com",
            "strict_min_version": "78.0"
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
    "background": {
        "page": "background.html"
    },
    "icons": {
        "64": "images/internet.png",
        "32": "images/internet-32px.png",
        "16": "images/internet-16px.png"
    }
}
```
{% endcode %}

### Installing

As described in the [first section of the Hello World Example](./#installing), go to the Add-ons Manager to open the Debug Add-on Page and temporarily install the extension.

### Trying it Out

TBD
