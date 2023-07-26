---
description: Extending the example extension to use a background page.
---

# Using a Background Page

In the third part of the Hello World Extension Tutorial, we will introduce the concept of the WebExtension background page.

We will keep track of incoming mails, add a menu entry to the tools menu and also a context menu entry to our button in Thunderbird's main toolbar and a click on both will open notifications with the collected information from the last 24 hours.

![](../../.gitbook/assets/hello-word-3.png)

## Background Page and Background Scripts

In the first two parts of the Hello World Extension Tutorial, we used well-defined UI hooks to load HTML pages when the user opened one of our popups. In contrast, the background page - if defined - is automatically loaded when the add-on is enabled during Thunderbird start or after the add-on has been manually enabled or installed. It is automatically destroyed when the add-on is shutting down.

The background page is a standard HTML page, supporting the same technologies as ordinary HTML pages, but it is never shown to the user. Its main purpose is to load one or more JavaScript files into the background. Those background scripts can be used to listen for events or to initialize and properly set up the add-on. As [described in the MailExtension guide](../mailextensions/#background-page), there are two ways to load background scripts:

1. Actually defining a background HTML page, that uses `script` tags to load the JavaScript files.
2. Just defining the to-be-loaded JavaScript files and let Thunderbird create a background page on-the-fly.

The author of this example prefers the first option. It allows to use the `await` keyword in file scope code and it allows to load other [ES6 modules](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Modules). We therefore add the following section to our `manifest.json` file:

```json
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
    <meta charset="utf-8" />
    <script type="module" src="background.js"></script>
</head>

</html>
```
{% endcode %}

Let's also create an empty `background.js` script file in the `hello-world` project folder.

### Listening for New Messages

In order to listen for new messages, we have to add a listener for the [`onNewMessageReceived`](https://webextension-api.thunderbird.net/en/91/messages.html#onnewmailreceived) event to our background script:

{% code title="background.js" lineNumbers="true" %}
```javascript
// Import all functions defined in the messageTools module.
import * as messageTools from '/modules/messageTools.mjs';

messenger.messages.onNewMailReceived.addListener(async (folder, messages) => {
    let { messageLog } = await messenger.storage.local.get({ messageLog: [] });

    for await (let message of messageTools.iterateMessagePages(messages)) {
        messageLog.push({
            folder: folder.name,
            time: Date.now(),
            message: message
        })
    }

    await messenger.storage.local.set({ messageLog });
})
```
{% endcode %}

{% hint style="info" %}
The above code is using an inline arrow function to define the callback function for the event listener (which is called for each `onNewMailReceived` event). This is identical to the following implicit function definition:

\
`async function onNewMailReceivedCallback(folder, messages) {`\
&#x20;  `...`\
`}`\
`messenger.messages.onNewMailReceived.addListener(`\
&#x20; `onNewMailReceivedCallback`\
`);`
{% endhint %}

The callback function of the `onNewMailReceived` event receives two parameters: `folder` being a [`MailFolder`](https://webextension-api.thunderbird.net/en/91/folders.html#folders-mailfolder) and `messages` being a [`MessageList`](https://webextension-api.thunderbird.net/en/91/messages.html#messages-messagelist). The defined event listener stores the folder and the message information of the new received mail in the extensions storage.

{% hint style="warning" %}
The `onNewMessageReceived` event requires the <mark style="color:red;">`accountsRead`</mark> permission, which needs to be added to the `permissions` key in our `manifest.json` file.
{% endhint %}

#### messenger.storage.local.get()

In line `5` of the shown background script, we request the current `messageLog` entry from the WebExtensions local storage. The used syntax allows to define a default value of `[]`(an empty Array), if there is currently no `messageLog` entry stored.

We could also request multiple values from the local storage:

```javascript
let rv = await messenger.storage.local.get({
  messageLog: [],
  aBoolValue: true,
  aStringValue: "none"
});
console.log(rv);
```

The call to `storage.local.get()` returns a Promise for a single object with the requested entries, for example the above `console.log(rv)` could produce the following output:

```json
{
  messageLog: [],
  aBoolValue: false,
  aStringValue: "The sky is the limit."
}
```

To access the content of the `messageLog` member, one would have to use `rv.messageLog`. That is sometimes not the desired behaviour, and instead we store the requested value directly in a variable. This is called _object destructering,_ and it maps the content of the `messageLog` member of the returned object to the `messageLog` variable. Any other non-matching returned member is ignored.

{% hint style="warning" %}
Access to the local storage requires the <mark style="color:red;">`storage`</mark> permission, which needs to be added to the `permissions` key in our `manifest.json` file.
{% endhint %}

#### messageTools.iterateMessagePages()

The listener is using a helper function to be able to loop over the received messages. The `iterateMessagePages` function is defined in an ES6 module, which is loaded in line `2` of the background script shown above. So, we create a `modules` subfolder into our `hello-world` project folder. Then, we place the `messageTools.mjs` file within. Here follows what it should contain.

{% code title="messageTools.mjs" %}
```javascript
// A wrapper function returning an async iterator for a MessageList. Derived from
// https://webextension-api.thunderbird.net/en/91/how-to/messageLists.html
export async function* iterateMessagePages(page) {
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
```
{% endcode %}

To identify the script file as an ES6 module, which does not include file scope code, but only defines functions, we use the `*.mjs` file extension.

Since Thunderbird's WebExtension API potentially has to handle a lot of messages, the [`MessageList`](https://webextension-api.thunderbird.net/en/91/messages.html#messagelist) data type is paginated. Please check the [Working with Message Lists](https://webextension-api.thunderbird.net/en/91/how-to/messageLists.html) tutorial for more information.

The provided `iterateMessagePages()` wrapper function is doing most of the heavy lifting and allows to asynchronously loop over the returned messages in line `7` of the shown background script. For each received message, we subsequently push a new entry into the `messageLog` Array.

#### messenger.storage.local.set()

In line `15` we store the updated `messageLog` Array back into the local storage. We use the object shorthand notation, which allows leaving out the actual value definition, if the value is stored in a variable with the same name as the object's member name. If the shorthand notation is unwanted, one could instead write the following:

```javascript
await messenger.storage.local.set({ messageLog: messageLog });
```

### Adding Menu Entries and their Actions

Let's add the following code to our background script, which will add two menu entries and will react to them being clicked:

{% code lineNumbers="true" %}
```javascript
// Create the menu entries.
let menu_id = await messenger.menus.create({
    title: "Show received email",
    contexts: [
        "browser_action",
        "tools_menu"
    ],
});

// Register a listener for the menus.onClicked event.
messenger.menus.onClicked.addListener(async (info, tab) => {
    if (info.menuItemId == menu_id) {
        // Our menu entry was clicked
        let { messageLog } = await messenger.storage.local.get({ 
           messageLog: []
        });

        let now = Date.now();
        let last24h = messageLog.filter(e => (now - e.time) < 24 * 60 * 1000);

        for (let entry of last24h) {
            messenger.notifications.create({
                "type": "basic",
                "iconUrl": "images/internet.png",
                "title": `${entry.folder}: ${entry.message.author}`,
                "message": entry.message.subject
                });                
        }
    }
});
```
{% endcode %}

#### messenger.menus.create()

In line `2` we create a new menu entry. We use the title `Show received email` and we add it to the `browser_action` context and to the `tools_menu` context. A list of other available contexts can be found on the [Supported UI Elements](../mailextensions/supported-ui-elements.md#menu-items) page.

The [menus.create()](https://webextension-api.thunderbird.net/en/91/menus.html#create-createproperties-callback) function returns the `id` of the new menu, which we can use to identify our menu, or - for example - add submenus by using the `id` as the `parentId` for other menu entries.

{% hint style="warning" %}
Using the `menus` API requires the <mark style="color:red;">`menus`</mark> permission, which needs to be added to the `permissions` key in our `manifest.json` file.
{% endhint %}

#### messenger.menus.onClicked()

In order to do something when our menu is clicked, we add a listener for the [onClicked](https://webextension-api.thunderbird.net/en/91/menus.html#onclicked) event. We check the `id` of the clicked menu to see which of our menus was clicked (we only added one, but checking here anyhow).

#### messenger.notifications.create()

After we have retrieved the current `messageLog` from the local storage, we loop over all entries and [create a notification](https://developer.mozilla.org/en-US/docs/Mozilla/Add-ons/WebExtensions/API/notifications) for each entry in line `20`.

{% hint style="warning" %}
Using the `notifications` API requires the <mark style="color:red;">`notifications`</mark> permission, which needs to be added to the `permissions` key in our manifest.json file.
{% endhint %}

## Testing the Extension

Let's double-check that we made the [correct changes](https://github.com/thundernest/sample-extensions/commit/bb719ee48994101c52a3d8849d7c07ac6a46db01?diff=unified) and have all the files in the right places:

```
hello-world/
  ├── background.html
  ├── background.js
  ├── manifest.json
  ├── images/
      ├── internet.png
      ├── internet-16px.png
      └── internet-32px.png
  ├── mainPopup/
      ├── popup.css
      ├── popup.html
      └── popup.js
  ├── messagePopup/
      ├── popup.css
      ├── popup.html
      └── popup.js
  └── modules/
      └── messageTools.mjs
```

This is how our `manifest.json` should now look like:

{% code title="manifest.json" %}
```json
{
    "manifest_version": 2,
    "name": "Hello World Example",
    "description": "A basic Hello World example extension!",
    "version": "3.0",
    "author": "Thunderbird Team",
    "browser_specific_settings": {
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
        "messagesRead",
        "accountsRead",
        "storage",
        "menus",
        "notifications"
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

Our background script should look as follows:

{% code title="background.js" %}
```javascript
// Import all functions defined in the messageTools module.
import * as messageTools from '/modules/messageTools.mjs';

// Add a listener for the onNewMailReceived events.
messenger.messages.onNewMailReceived.addListener(async (folder, messages) => {
    let { messageLog } = await messenger.storage.local.get({ messageLog: [] });

    for await (let message of messageTools.iterateMessagePages(messages)) {
        messageLog.push({
            folder: folder.name,
            time: Date.now(),
            message: message
        })
    }

    await messenger.storage.local.set({ messageLog });
})

// Create the menu entries.
let menu_id = await messenger.menus.create({
    title: "Show received email",
    contexts: [
        "browser_action",
        "tools_menu"
    ],
});

// Register a listener for the menus.onClicked event.
await messenger.menus.onClicked.addListener(async (info, tab) => {
    if (info.menuItemId == menu_id) {
        // Our menu entry was clicked
        let { messageLog } = await messenger.storage.local.get({ messageLog: [] });

        let now = Date.now();
        let last24h = messageLog.filter(e => (now - e.time) < 24 * 60 * 1000);

        for (let entry of last24h) {
            messenger.notifications.create({
                "type": "basic",
                "iconUrl": "images/internet.png",
                "title": `${entry.folder}: ${entry.message.author}`,
                "message": entry.message.subject
            });
        }
    }
});
```
{% endcode %}

### Installing

As described in the [first part of the Hello World Extension Tutorial](./#installing), go to the Add-ons Manager to open the Debug Add-on Page and temporarily install the extension.

### Trying it Out

After you have received one or more new messages, while the add-on has been active, open the context menu of our `browser_action` button in Thunderbird's main toolbar and click on "Show received emails". For each received message, you should see a notification.

![](../../.gitbook/assets/hello-word-3-try.png)
