---
description: Extending the simple example extension to make use of WebExtension APIs.
---

# Using WebExtension APIs

In this step we will add a button to the message display header area and a click on it will show some information about the currently displayed message.

## Adding a `message_display_action`

Similar to adding the `browser_action` in the first part, we have to extend the `manifest.json` to add the message_display.:

```
"message_display_action": {
    "default_popup": "messagePopup/messagePopup.html",
    "default_title": "Show Details",
    "default_icon": "images/internet-32px.png"
},
```

The full `manifest.json` file now looks like so:

```
{
    "manifest_version": 2,
    "name": "Hello World",
    "description": "Your basic Hello World extension!",
    "version": "2.0",
    "author": "[Your Name Here]",
    "applications": {
        "gecko": {
            "id": "helloworld@yoursite.com",
            "strict_min_version": "78.0"
        }
    },
    "browser_action": {
        "default_popup": "mainPopup/mainPopup.html",
        "default_title": "Hello World",
        "default_icon": "images/internet-32px.png"
    },
    "message_display_action": {
        "default_popup": "messagePopup/messagePopup.html",
        "default_title": "Hello World",
        "default_icon": "images/internet-32px.png"
    },
    "permissions": [
        "messagesRead"
    ],
    "background": {
        "scripts": ["background.js"]
    },
    "icons": {
        "64": "images/internet.png",
        "32": "images/internet-32px.png",
        "16": "images/internet-16px.png"
    }
}
```
