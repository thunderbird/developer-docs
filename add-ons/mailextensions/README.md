---
description: How to build MailExtensions for Thunderbird.
---

# A Guide to MailExtensions

## Introduction

MailExtensions are based on the WebExtension technology, which is also used by many web browsers. Such an extension is a simple collection of files which modify Thunderbirds appearance and behavior. It can add user interface elements, alter content, or perform background tasks. MailExtensions are created using standard JavaScript, CSS and HTML. Interaction with Thunderbird itself, like adding UI elements or accessing the users messages or contacts is done through special WebExtension APIs.

![](../../.gitbook/assets/webext_diagram%20%281%29.png)

Unlike older legacy extensions, MailExtensions access functionality through stable WebExtension APIs and do not have direct access to Thunderbird's internal components or UI elements. Consequently, MailExtensions are less likely to break and do not need frequent and complex updates when Thunderbirds internals change.

The main configuration file of a MailExtension is a file called `manifest.json`, also referred to as the _manifest_. Besides defining some of the extension's basic properties like name, description and ID, it also defines how the extension hooks into Thunderbird:

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
            "strict_min_version": "78.0",
            "strict_max_version": "78.*"
        }
    },
    "icons": {
        "64": "images/icon-64px.png",
        "32": "images/icon-32px.png",
        "16": "images/icon-16px.png"
    },
    "background": {
        "scripts": [
            "background.js"
        ],
    }
    "options_ui": {
      "page": "options/options.html",
      "open_in_tab": false,
      "browser_style": true,
    },
    "permissions": [
      "storage"
    ]
}
```
{% endcode %}

##  Manifest keys

A list of all manifest keys supported by Thunderbird can be found in the following document:

{% page-ref page="supported-manifest-keys.md" %}

The most commonly used manifest keys are explained below.

### Basic extension properties

The following manifest keys define basic properties:

* `manifest_version`: mandatory key to signal compatibility to Thunderbird, must be set to `2`
* `name` : the name of the extension
* `description` : a brief description of what the extension does
* `version` : a number that denotes the version of the extension
* `author` : should be the name of a person or company representing the extensions developer

{% hint style="info" %}
The `name` and the `description` of the given example are only in English. [This MDN article about **Localization**](https://developer.mozilla.org/en-US/docs/Mozilla/Add-ons/WebExtensions/Internationalization#Internationalizing_manifest.json) explains how to use the WebExtension i18n API to localize these keys.
{% endhint %}

This `applications.gecko` manifest key defines the following properties:

* `id` : The id serves as a unique identifier for the extension, common practice is to use `add-on-name@your-website`. Providing an id is mandatory in order to install the extension.
* `strict_min_version`: Defines the lowest targeted version of Thunderbird.
* `strict_max_version`:  Defines the highest targeted version of Thunderbird. It can be set to a specific version or a broader match to limit it to a branch \(for example `78.*`\). 

```javascript
    "applications": {
        "gecko": {
            "id": "helloworld@yoursite.com",
            "strict_min_version": "78.0",
            "strict_max_version": "78.*",
        }
    },
```

### Extension Icons

The `icons` manifest key tells Thunderbird the location of icons, which should be used to represent the MailExtension. Thunderbird supports basic image types like PNG files, but also SVG files. Thunderbird uses different file icon sizes in different places and allows registering a dedicated file for each size. The MailExtension will use the standard puzzle icon, if no icons have been defined.

```javascript
    "icons": {
        "64": "images/icon-64px.png",
        "32": "images/icon-32px.png",
        "16": "images/icon-16px.png"
    },
```

### Background Page

Each MailExtension has a hidden background page. Its main purpose is to load and execute JavaScript files when the MailExtension is loaded. There are two options for its definition:

#### Defining one or more background scripts

```javascript
    "background": {
        "scripts": [
            "common.js"
            "background.js"
        ],
    },
```

This will create the background page on-the-fly and load the provided scripts. This is identical to the following background page definition.

#### Defining a background page

```javascript
    "background": {
        "page": "background.html"
    },
```

The defined background page can then load the JavaScript files:

```markup
<html lang="en">
    <head>
        <meta charset="utf-8">
        <script src="common.js"></script>
        <script src="background.js"></script>
    </head>
    <body>
    </body>
</html>
```

### Options Page

The `options_ui` manifest key defines the standard MailExtension options page. The defined page will be displayed in the add-on manager.

{% code title="manifest.json" %}
```javascript
    "options_ui": {
      "page": "options/options.html",
      "open_in_tab": false,
      "browser_style": true,
    },
```
{% endcode %}

The appearance of the options page can be configured as follows:

* `open_in_tab` : Open the options page in a tab instead of inline in the add-ons property page.
* `browser_style`: Use default browser styles for the options page \(recommended\).

An inline options page may look as follows:

![](../../.gitbook/assets/options.png)

### User Interface Elements

Some UI elements MailExtensions can use are controlled by manifest keys, for example

* browser\_action
* compose\_action
* message\_display\_action

Further information about these UI elements can be found in the following document:

{% page-ref page="supported-ui-elements.md" %}

### Permissions

A core principle of the WebExtension technology is the use of permissions, so users can see which areas of Thunderbird a MailExtension wants to access. Add-on developers can predefine all requested permissions in the `permissions` manifest key:

```javascript
    "permissions": [
      "storage"
    ],
```

As permissions allow WebExtensions to use certain APIs, information about supported permissions can be found in the following document:

{% page-ref page="supported-webextension-api.md" %}

## WebExtension Scripts

Most entry points defined in the extensions manifest allow adding scripts. Most prominent of course the background script\(s\). Furthermore, each defined HTML page, like the `options_ui` page allow including scripts via standard HTML `<script>` tags.

{% hint style="info" %}
In order to use modern [ES6 modules](https://hacks.mozilla.org/2015/08/es6-in-depth-modules/) in a loaded script, the `type` attribute of its `script` tag must be set to `module`.

```markup
<script type="module" src="background.js"></script>
```
{% endhint %}

 All these WebExtension scripts have access to:

* Standard JavaScript methods
* [Web API](https://developer.mozilla.org/de/docs/Web/API) \(if the browser compatibility chart lists Firefox, the API also works in Thunderbird\)
* WebExtension API

A list of all WebExtension APIs supported by Thunderbird can be found in the following document:

{% page-ref page="supported-webextension-api.md" %}

##  Creating MailExtensions: Examples

Our [sample-extensions repository](https://github.com/thundernest/sample-extensions/tree/master/experiment) includes a few simple MailExtensions, which showcase different APIs and which will get you started to create your own extension.

We also prepared a step-by-step guide for a small Hello World MailExtension:

{% page-ref page="hello-world-add-on.md" %}

## Experiment APIs

The currently available WebExtension APIs are not yet sufficient, as some areas of Thunderbird are not accessible through these APIs. While we are working on improving the situation, add-on developers can create their own API by registering an implementation script and a schema file describing the interface in the`manifest.json` file:

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

That API can then be used by the MailExtension like any other WebExtension API.

An Experiment API, specifically its implementation script has access to Thunderbirds internal functions, components and UI elements. Instead of using WebExtensions APIs, add-on developers need to interact with and get used to the actual source code of Thunderbird.

{% page-ref page="experiments.md" %}

Currently and for the foreseeable future Experiments can be used in extensions released to the general public. In the meantime, we will be dedicated to API development and to improve the situation for add-on developers. 

If you need an API that does not exist yet, please [tell us about it](https://bugzilla.mozilla.org/enter_bug.cgi?product=Thunderbird&component=Add-Ons%3A+Extensions+API). If you have created an Experiment API which you think is a good fit for general use in Thunderbird, please [tell us about it](https://developer.thunderbird.net/add-ons/community).

