---
description: Extending the example extension to use a content script.
---

# Using Content Scripts

In the fourth part of the Hello World Extension Tutorial, we will introduce the concept of content scripts, to inject code into existing pages.

We will add a banner to the top of the message display area, displaying some information about the currently viewed message. The banner will also include a button to mark the currently viewed message as unread.

![](<../../../.gitbook/assets/hello-world-4 (1).png>)

## Using a Message Display Script

Content Scripts are JavaScript files that are loaded into content pages. This technology was mainly developed for browsers, where it is used to interact with the currently viewed web page.

In addition to [standard content scripts](https://developer.mozilla.org/en-US/docs/Mozilla/Add-ons/WebExtensions/Content_scripts), Thunderbird supports the following special types of content scripts:

* compose scripts loaded into the editor of the message composer
* message display scripts loaded into rendered messages when displayed to the user



We will be using a message display script in this example. In order to register one, we use the [`messageDisplayScripts`](https://webextension-api.thunderbird.net/en/91/messageDisplayScripts.html) API and add the following code to our background script:

```javascript
// Register the message display script.
messenger.messageDisplayScripts.register({
    js: [{ file: "messageDisplay/message-content-script.js" }],
    css: [{ file: "messageDisplay/message-content-styles.css" }],
});
```

Whenever a message is displayed to the user, the registered CSS file will be added and the registered JavaScript file will be injected and executed. Let's create a `messageDisplay` directory inside our `hello-world` project folder with the following two files:

{% code title="message-content-styles.css" %}
```css
.thunderbirdMessageDisplayActionExample {
  background-color: #d70022;
  color: white;
  font-weight: 400;
  padding: 0.25rem 0.5rem;
  margin-bottom: 0.25rem;
  display: flex;
}

.thunderbirdMessageDisplayActionExample_Text {
  flex-grow: 1;
}
```
{% endcode %}

{% code title="message-content-script.js" %}
```javascript
const showBanner = async () => {
  let bannerDetails = await browser.runtime.sendMessage({
    command: "getBannerDetails",
  });

  // get the details back from the formerly serialized content
  const { text } = bannerDetails;

  // create the banner element itself
  const banner = document.createElement("div");
  banner.className = "thunderbirdMessageDisplayActionExample";

  // create the banner text element
  const bannerText = document.createElement("div");
  bannerText.className = "thunderbirdMessageDisplayActionExample_Text";
  bannerText.innerText = text;

  // create a button to display it in the banner
  const markUnreadButton = document.createElement("button");
  markUnreadButton.innerText = "Mark unread";
  markUnreadButton.addEventListener("click", async () => {
    // add the button event handler to send the command to the background script
    browser.runtime.sendMessage({
      command: "markUnread",
    });
  });

  // add text and button to the banner
  banner.appendChild(bannerText);
  banner.appendChild(markUnreadButton);

  // and insert it as the very first element in the message
  document.body.insertBefore(banner, document.body.firstChild);
};

showBanner();
```
{% endcode %}

#### messenger.runtime.sendMessage()

TBD

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
  ├── displayMessage/
      ├── message-content-script.js
      └── message-content-styles.css
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
    "version": "4.0",
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
        "messagesRead",
        "accountsRead",
        "storage",
        "menus",
        "notifications",
        "messagesModify"
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

```javascript
// A wrapper function returning an async iterator for a MessageList. Derived from
// https://webextension-api.thunderbird.net/en/91/how-to/messageLists.html
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

async function load() {

    // Add a listener for the onNewMailReceived events.
    await messenger.messages.onNewMailReceived.addListener(async (folder, messages) => {
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

    /**
     * Add a handler for communication with other parts of the extension,
     * like our message display script.
     *
     * Note: If this handler is defined async, there should be only one such
     *       handler in the background script for all incoming messages.
     */
    messenger.runtime.onMessage.addListener(async (message, sender) => {
        console.log({message, sender});
        if (message && message.hasOwnProperty("command")) {

            // Get the command from the message.
            const { command } = message;
            // Get the tab from the sender.
            const { tab } = sender;

            // Get the message currently displayed in the sending tab, abort if
            // that failed.
            const messageHeader = await messenger.messageDisplay.getDisplayedMessage(tab.id);
            if (!messageHeader) {
                console.log("NO");
                return;
            }

            // Check for known commands.
            console.log({command});
            switch (command) {
                case "getBannerDetails":
                    // Create the information we want to return to our message display script.
                    console.log(`Mail subject is "${messageHeader.subject}"`);
                    return { text: `Mail subject is "${messageHeader.subject}"` };

                case "markUnread":
                    // mark the message as unread
                    messenger.messages.update(messageHeader.id, {
                        read: false,
                    });
                    break;
            }
        }
    });

    // Register the message display script.
    messenger.messageDisplayScripts.register({
        js: [{ file: "messageDisplay/message-content-script.js" }],
        css: [{ file: "messageDisplay/message-content-styles.css" }],
    });
}

document.addEventListener("DOMContentLoaded", load);
```

### Installing

As described in the [first part of the Hello World Extension Tutorial](./#installing), go to the Add-ons Manager to open the Debug Add-on Page and temporarily install the extension.

### Trying it Out

Open a message. There should be a red banner added at the top of it with its subject and a button labeled _"Mark unread"_. Clicking that button should mark the message as unread
