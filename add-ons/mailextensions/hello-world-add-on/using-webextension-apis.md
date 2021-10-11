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
        "default_popup": "mainPopup/popup.html",
        "default_title": "Hello World",
        "default_icon": "images/internet-32px.png"
    },
    "message_display_action": {
        "default_popup": "messagePopup/popup.html",
        "default_title": "Details",
        "default_icon": "images/internet-32px.png"
    },
    "icons": {
        "64": "images/internet.png",
        "32": "images/internet-32px.png",
        "16": "images/internet-16px.png"
    }
}
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
