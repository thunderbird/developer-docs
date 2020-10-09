---
description: How to build MailExtensions for Thunderbird.
---

# A Guide to MailExtensions

## Overview

MailExtensions are based on the WebExtension technology, which is also used by many web browsers. Such an extension is a simple collection of files which modify Thunderbirds appearance and behavior. It can add user interface elements, alter content, or perform background tasks. MailExtensions are created using standard JavaScript, CSS and HTML. Interaction with Thunderbird itself, like adding UI elements or accessing the users messages or contacts is done through special WebExtension APIs.

![](../../.gitbook/assets/webext_diagram%20%281%29.png)

Unlike older legacy extensions, MailExtensions access functionality through stable WebExtension APIs and do not have direct access to Thunderbird's internal components or UI elements. Consequently, MailExtensions are less likely to break and do not need frequent and complex updates when Thunderbirds internals change.

## The `manifest.json` File

The main configuration file of your MailExtension is a file called `manifest.json`.  Besides defining some basic properties like name, description and ID of your extension, it also defines how your extension hooks into Thunderbird:

{% code title="manifest.json" %}
```javascript
{
    "manifest_version": 2,
    "name": "Hello World",
    "description": "A basic Hello World extension!",
    "version": "1.0",
    "author": "[Your Name Here]",
    "applications": {
        "gecko": {
            "id": "helloworld@yoursite.com",
            "strict_min_version": "78.0"
        }
    },
    "icons": {
        "64": "images/internet.png",
        "32": "images/internet-32px.png",
        "16": "images/internet-16px.png"
    },
    "background": {
        "scripts": [
            "background.js"
        ],
    },    
    "browser_action": {
        "default_popup": "browser_popup.html",
        "default_title": "Hello World",
        "default_icon": "images/internet-32px.png"
    },
    "compose_action": {
        "default_popup": "compose_popup.html",
        "default_title": "Hello World",
        "default_icon": "images/internet-32px.png"
    },          
    "options_ui": {
      "page": "options/options.html"
    },
    "permissions": [
    ]    
}
```
{% endcode %}

At a glance there may appear to be a lot going on in this file, but it's actually pretty straightforward. The `manifest_version` key is mandatory and it signals compatibility to Thunderbird. 

The following keys define basic properties:

* `name` : the name of your add-on
* `description` : a brief description of what your add-on does
* `version` : a number that denotes which version of your add-on you are working on
*  `author` : should be your name or company

{% hint style="info" %}
The `name` and the `description` of this example are only in English. If you want to use translated strings in your manifest, you can read [this MDN article about **Localization**](https://developer.mozilla.org/en-US/docs/Mozilla/Add-ons/WebExtensions/Internationalization#Internationalizing_manifest.json).
{% endhint %}

What you need to know about the `applications` portion of the `manifest.json`:

* `id` : serves as a unique identifier for your add-on, common practice is to use `add-on-name@your-website`
* `strict_min_version:`should be the lowest version of Thunderbird you are targeting. Make sure to test your add-on with the version you put there to ensure it works properly. 

The `icons` key tells Thunderbird the location of your add-ons icons. Thunderbird supports basic image types like PNG files, but also SVG files.

The following keys correspond directly to the entry points outlined in the diagram at the beginning of this page:

* `background`: Defines one or more scripts that are loaded into your background page. These scripts are executed in order, either while Thunderbird is starting, or when the add-on is being enabled. 
* `browser_action`: This adds a button to Thunderbirds main toolbar. If a `default_popup` is defined, that popup will be opened, when the button is clicked. Alternatively, you can use the `browserAction` API in your background script to react on button clicks.

![](../../.gitbook/assets/browseraction.png)

* `compose_action`: Similar to `browser_action`, but places a button in the toolbar of the composer window. The `composeAction` API can be used to interact with that button, for example from your background script.

![](../../.gitbook/assets/composeaction.png)

* `options_ui`: Defines the standard add-on options page. The defined page will be displayed in the add-on manager.

![](../../.gitbook/assets/options.png)

The possible manifest entries are not limited to the shown examples. Future APIs may introduce new entry points. You can learn about them in our [official Thunderbird WebExtension API documentation](https://thunderbird-webextensions.readthedocs.io/en/latest/index.html).

## WebExtension Scripts

Most entry points defined in your `manifest.json` allow adding scripts. Most prominent of course the background script\(s\). Furthermore, each defined HTML page, like the `options_ui` page or popups of `browser_actions` and `compose_actions` allow including scripts via standard HTML `<script>` tags. All these scripts have access to:

* Standard JavaScript methods
* [Web API](https://developer.mozilla.org/de/docs/Web/API) \(if the browser compatibility chart lists Firefox, the API also works in Thunderbird\)

As Thunderbird is based on the same code base as Firefox, we can reuse many of the WebExtension APIs provided by Firefox:

* [downloads](https://developer.mozilla.org/en-US/docs/Mozilla/Add-ons/WebExtensions/API/downloads)
* [experiments](https://firefox-source-docs.mozilla.org/toolkit/components/extensions/webextensions/basics.html#webextensions-experiments)
* [extension](https://developer.mozilla.org/en-US/docs/Mozilla/Add-ons/WebExtensions/API/extension)
* [i18n](https://developer.mozilla.org/en-US/docs/Mozilla/Add-ons/WebExtensions/API/i18n)
* [management](https://developer.mozilla.org/en-US/docs/Mozilla/Add-ons/WebExtensions/API/management)
* [permissions](https://developer.mozilla.org/en-US/docs/Mozilla/Add-ons/WebExtensions/API/permissions)
* [pkcs11](https://developer.mozilla.org/en-US/docs/Mozilla/Add-ons/WebExtensions/API/pkcs11)
* [runtime](https://developer.mozilla.org/en-US/docs/Mozilla/Add-ons/WebExtensions/API/runtime)
* [storage](https://developer.mozilla.org/en-US/docs/Mozilla/Add-ons/WebExtensions/API/storage)
* [theme](https://developer.mozilla.org/en-US/docs/Mozilla/Add-ons/WebExtensions/API/theme)

Thunderbird of course requires additional interfaces to messenger specific functions. These additional APIs are also WebExtension APIs and are sometimes called MailExtension APIs, to indicate their dedication to Thunderbird. You can find detailed information about our own WebExtensions here: 

* [Thunderbird WebExtensions API documentation ](https://thunderbird-webextensions.readthedocs.io/)

## Creating a MailExtension: Examples

Our [sample-extensions repository](https://github.com/thundernest/sample-extensions/tree/master/experiment) includes a few simple MailExtensions, which showcase different APIs and which will get you started to create your own extension.

We also prepared a step-by-step guide for a small Hello World MailExtension:

{% page-ref page="hello-world-add-on.md" %}

## Experiment APIs

The currently available WebExtension APIs are not yet sufficient, as some areas of Thunderbird are not accessible through these APIs. While we are working on improving the situation, you can create your own API by registering an implementation script and a schema file describing the interface in your `manifest.json` file:

```javascript
	"experiment_apis": {
		"LegacyPrefs": {
			"schema": "api/LegacyPrefs/schema.json",
			"parent": {
				"scopes": ["addon_parent"],
				"paths": [["LegacyPrefs"]],
				"script": "api/LegacyPrefs/implementation.js",
				"events": ["startup"]
			}
		}
	}, 
```

That API can then be used by your extension like any other WebExtension API.

An Experiment API, specifically its implementation script has access to Thunderbirds internal functions, components and UI elements. Instead of using WebExtensions APIs, you need to interact with and get used to the actual source code of Thunderbird.

{% page-ref page="experiments.md" %}

Currently and for the foreseeable future Experiments can be used in extensions released to the general public. In the meantime, we will be dedicated to API development and to improve the situation for add-on developers. If you need an API that does not exist yet, please [tell us about it](https://bugzilla.mozilla.org/enter_bug.cgi?product=Thunderbird&component=Add-Ons%3A+Extensions+API). 

If you have created an Experiment API which you think is a good fit for general use in Thunderbird, please [tell us about it](https://developer.thunderbird.net/add-ons/community).

