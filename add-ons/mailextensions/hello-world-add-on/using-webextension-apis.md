---
description: Extending the simple example extension to make use of WebExtension APIs.
---

# Using WebExtension APIs

In this step we will add a button to the message display header area and a click on it will show some information about the currently displayed message.

## Adding the messageDisplayAction button

Similar to adding the browserAction button in the first part, we have to extend the manifest.json to add the messageDisplayAction button:

```
    "browser_action": {
        "default_popup": "mainPopup/mainPopup.html",
        "default_title": "Hello World",
        "default_icon": "images/internet-32px.png"
    },
```
