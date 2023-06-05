---
description: How to create extensions for Thunderbird.
---

# A Guide to Extensions

An extension is a Thunderbird add-on, that provides additional functionality by adding new user interface elements, alter content, or perform background tasks.&#x20;

## The `manifest.json` file

The main configuration file of an extension is called `manifest.json`, also referred to as the _manifest_. Besides defining some of the extension's basic properties like name, description and ID, it also defines how the extension hooks into Thunderbird:

{% code title="manifest.json" %}
```json
{
    "manifest_version": 2,
    "name": "Hello World",
    "description": "A basic Hello World extension!",
    "version": "1.0",
    "author": "[Your Name Here]",
    "browser_specific_settings": {
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
        "page": "background.html"
    },
    "options_ui": {
      "page": "options/options.html",
      "open_in_tab": false,
      "browser_style": true
    },
    "permissions": [
      "storage"
    ]
}
```
{% endcode %}

A list of all manifest keys supported by Thunderbird can be found in the following document:

{% content-ref url="supported-manifest-keys.md" %}
[supported-manifest-keys.md](supported-manifest-keys.md)
{% endcontent-ref %}

The following manifest keys define basic properties:

* `manifest_version`: A _mandatory key_ defining the Manifest version used by the extension. Supported versions are `2` and `3` (since Thunderbird Beta 110). The Manifest defines the basic rules how a WebExtension needs to be crafted and how it can interact with Thunderbird.
* `name` : A _mandatory key_ to set the name of the extension.
* `version` : A _mandatory key_ to define a number that denotes the version of the extension.
* `description` : A brief description of what the extension does.
* `author` : The name of a person or company representing the extension developer.

{% hint style="info" %}
The `name` and the `description` of the shown example are only in English. [This MDN article about **Localization**](https://developer.mozilla.org/en-US/docs/Mozilla/Add-ons/WebExtensions/Internationalization#Internationalizing\_manifest.json) explains how to use the WebExtension i18n API to localize these keys.
{% endhint %}

The `browser_specific_settings.gecko` manifest key defines the following properties:

* `strict_min_version`: Defines the lowest targeted version of Thunderbird.
* `strict_max_version`: Defines the highest targeted version of Thunderbird. It can be set to a specific version or a broader match to limit it to a branch (for example `102.*`). Usually not needed.
* `id`: The id serves as a unique identifier for the extension and is mandatory in order upload an extension to ATN or to be able to install it from an XPI file.

{% hint style="info" %}
Best practice is to use an "email-address-style" id (but not a real email address) on a domain you control, for example `name-of-your-addon@example.com`, if you own `example.com`. As the id of your add-on cannot be changed once it is published, it is highly recommended to use a domain that you plan to keep for the forseeable future. If you don't have a domain to use, feel free to use:\
`<atn-user-name>.<add-on-name>@addons.thunderbird.net`\


Alternatively, you may use an UUID enclosed in curly braces, for example:\
`{e4aa2097-8ee9-49a4-9ec7-c633b1e8dfda}`
{% endhint %}

### Extension Icons

The `icons` manifest key tells Thunderbird the location of icons, which should be used to represent the MailExtension. Thunderbird supports basic image types like PNG files, but also SVG files. Thunderbird uses different file icon sizes in different places and allows registering a dedicated file for each size. The MailExtension will use the standard puzzle icon, if no icons have been defined.

```json
    "icons": {
        "64": "images/icon-64px.png",
        "32": "images/icon-32px.png",
        "16": "images/icon-16px.png"
    }
```

### Background Page

The extension's background page is loaded in a hidden window when the add-on is started and can be used to load additional JavaScript files.

```json
    "background": {
        "page": "background.html"
    }
```

The background page specifies the JavaScript files to be loaded:

```markup
<html lang="en">
    <head>
        <meta charset="utf-8">
        <script src="common.js"></script>
        <script type="module" src="background.js"></script>
    </head>
    <body>
    </body>
</html>
```

{% hint style="success" %}
The `type` attribute in the shown `script` tag of `background.js` defines it as a top level module and enables it to use modern [ES6 modules](https://hacks.mozilla.org/2015/08/es6-in-depth-modules/).
{% endhint %}

#### Defining one or more background scripts directly

Instead of defining a background page, the extension can specify a simple list of JavaScript files. This will auto-generate a background page and then load the JavaScript files.

```json
    "background": {
        "scripts": [
            "common.js",
            "background.js"
        ]
    }
```

{% hint style="danger" %}
This approach does not allow to use [ES6 modules](https://hacks.mozilla.org/2015/08/es6-in-depth-modules/) in any of the specified files.
{% endhint %}

### Options Page

The `options_ui` manifest key defines the standard MailExtension options page. The defined page will be displayed in the add-on manager by default.

```json
    "options_ui": {
      "page": "options/options.html",
      "open_in_tab": false,
      "browser_style": true
    }
```

The appearance of the options page can be configured as follows:

* `open_in_tab` : Open the options page in a tab instead of inline in the add-on manager.
* `browser_style`: Use default browser styles for the options page (recommended).

An inline options page may look as follows:

![](../../.gitbook/assets/options.png)

### User Interface Elements

Some UI elements MailExtensions can use are controlled by manifest keys, for example

* `browser_action` (renamed to `action` in Manifest v3)
* `compose_action`
* `message_display_action`

Further information about these UI elements can be found in the following document:

{% content-ref url="supported-ui-elements.md" %}
[supported-ui-elements.md](supported-ui-elements.md)
{% endcontent-ref %}

### Permissions

A core principle of the WebExtension technology is the use of permissions, so users can see which areas of Thunderbird an add-on wants to access. Add-on developers can predefine all requested permissions in the `permissions` manifest key:

```json
    "permissions": [
      "storage"
    ]
```

Information about required permissions can be found in the following document:

{% content-ref url="supported-webextension-api.md" %}
[supported-webextension-api.md](supported-webextension-api.md)
{% endcontent-ref %}

For most permissions, the user must either accept all of the requested permissions during add-on install, or abort the install. Some permissions however can be requested as [optional permissions](https://developer.mozilla.org/en-US/docs/Mozilla/Add-ons/WebExtensions/manifest.json/optional\_permissions), which can be managed by the user during runtime.

## WebExtension Scripts

All JavaScript files loaded by an extension have access to:

* Standard JavaScript methods
* [Web API](https://developer.mozilla.org/docs/Web/API) (if the browser compatibility chart lists Firefox, the API also works in Thunderbird)
* WebExtension APIs (see restrictions for content scripts and cloudFile scripts below)

A list of all WebExtension APIs supported by Thunderbird can be found in the following document:

{% content-ref url="supported-webextension-api.md" %}
[supported-webextension-api.md](supported-webextension-api.md)
{% endcontent-ref %}

### Content Scripts

Content scripts (including [compose scripts](https://webextension-api.thunderbird.net/en/latest/composeScripts.html) and [message display scripts](https://webextension-api.thunderbird.net/en/latest/messageDisplayScripts.html)) can only use the following WebExtension APIs:

* [runtime.connect()](https://developer.mozilla.org/en-US/docs/Mozilla/Add-ons/WebExtensions/API/runtime/connect)
* [runtime.getManifest()](https://developer.mozilla.org/en-US/docs/Mozilla/Add-ons/WebExtensions/API/runtime/getManifest)
* [runtime.getURL()](https://developer.mozilla.org/en-US/docs/Mozilla/Add-ons/WebExtensions/API/runtime/getURL)
* [runtime.onConect](https://developer.mozilla.org/en-US/docs/Mozilla/Add-ons/WebExtensions/API/runtime/onConnect)
* [runtime.onMessage](https://developer.mozilla.org/en-US/docs/Mozilla/Add-ons/WebExtensions/API/runtime/onMessage)
* [runtime.sendMessage](https://developer.mozilla.org/en-US/docs/Mozilla/Add-ons/WebExtensions/API/runtime/sendMessage)
* [i18n.getMessage()](https://developer.mozilla.org/en-US/docs/Mozilla/Add-ons/WebExtensions/API/i18n/getMessage)
* [i18n.getAcceptLanguages()](https://developer.mozilla.org/en-US/docs/Mozilla/Add-ons/WebExtensions/API/i18n/getAcceptLanguages)
* [i18n.getUILanguage()](https://developer.mozilla.org/en-US/docs/Mozilla/Add-ons/WebExtensions/API/i18n/getUILanguage)
* [i18n.detectLanguage()](https://developer.mozilla.org/en-US/docs/Mozilla/Add-ons/WebExtensions/API/i18n/detectLanguage)
* [menus.getTargetElement](https://developer.mozilla.org/en-US/docs/Mozilla/Add-ons/WebExtensions/API/menus/getTargetElement)
* [storage](https://developer.mozilla.org/en-US/docs/Mozilla/Add-ons/WebExtensions/API/storage).\*

Content scripts can [communicate with background scripts](https://developer.mozilla.org/en-US/Add-ons/WebExtensions/Content\_scripts#Communicating\_with\_background\_scripts) using runtime messaging, and thereby can indirectly access the WebExtension APIs. See our [messageDisplayScript example extension](https://github.com/thundernest/sample-extensions/tree/master/manifest\_v3/messageDisplayScript.pdfPreview).

### CloudFile Management Scripts

A script loaded from a CloudFile[ `management_url`](https://webextension-api.thunderbird.net/en/latest/cloudFile.html#manifest-file-properties) can only use the following WebExtension APIs:

* [cloudFile](https://webextension-api.thunderbird.net/en/stable/cloudFile.html).\*
* [extension](https://developer.mozilla.org/en-US/docs/Mozilla/Add-ons/WebExtensions/API/extension).\*
* [i18n](https://developer.mozilla.org/en-US/docs/Mozilla/Add-ons/WebExtensions/API/i18n).\*
* [runtime](https://developer.mozilla.org/en-US/docs/Mozilla/Add-ons/WebExtensions/API/runtime).\*
* [storage](https://developer.mozilla.org/en-US/docs/Mozilla/Add-ons/WebExtensions/API/storage).\*

CloudFile management scripts can [communicate with background scripts](https://developer.mozilla.org/en-US/Add-ons/WebExtensions/Content\_scripts#Communicating\_with\_background\_scripts) using runtime messaging, and thereby can indirectly access the WebExtension APIs.

## Experiment APIs

The currently available WebExtension APIs are not yet sufficient, as some areas of Thunderbird are not accessible through these APIs. We are working on improving the situation.

Currently and for the foreseeable future Thunderbird supports Experiment APIs (a.k.a. Experiments), which are WebExtension APIs that are bundled and shipped together with a MailExtension. They interact directly with Thunderbird's internal APIs and allow add-ons to use additional features not yet available via built-in WebExtension APIs.

These additional APIs can be registered in the `manifest.json` file by defining an implementation script and a schema file describing the interface:

```json
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
    }
```

{% hint style="danger" %}
Experiment APIs have full access to Thunderbird's core functions and can bypass the WebExtension permission system entirely. Including one or more Experiment APIs will therefore disable the individual permission prompt and instead prompt the user only for the [_Have full, unrestricted access to Thunderbird, and your computer_](https://support.mozilla.org/kb/permission-request-messages-thunderbird-extensions) permission.

The use of optional permissions is not supported for the same reason.
{% endhint %}

If you'd like to learn more about experiments, check out this detailed introduction:

{% content-ref url="experiments.md" %}
[experiments.md](experiments.md)
{% endcontent-ref %}

### Sharing Experiment APIs

Developers can share and re-use Experiments, if their add-ons have similar needs. Before starting to work on your own Experiment, check if any of the following APIs could already provide the functionality you need. Using them and providing feedback to their developers will help to improve these APIs.

<table><thead><tr><th width="289.3333333333333">Name</th><th width="121" align="center"></th><th>Description</th></tr></thead><tbody><tr><td><a href="https://github.com/rsjtdrjgfuzkfg/thunderbird-experiments/tree/master/experiments/cachingfix">CachingFix</a></td><td align="center"></td><td>Adding this Experiment API will automatically <a href="https://developer.thunderbird.net/add-ons/mailextensions/experiments#managing-your-experiments-lifecycle">fix caching issues when the add-on is updated, disabled or uninstalled</a>.</td></tr><tr><td><a href="https://github.com/thundernest/tb-web-ext-experiments/blob/master/calendar">Calendar</a></td><td align="center"><a href="https://thunderbird.topicbox.com/groups/addons/Ta66e29a70cfa405f-M7357f9e59c7228cc1632b601/draft-for-mailextensions-related-to-calendaring">💬</a> <a href="https://docs.google.com/document/d/15awbKiVfdOTmsRpgD1dxm3gvOt08EQZDSnMl8QRBFoY/edit?usp=sharing">📝</a></td><td>Draft for calendar-related APIs in Thunderbird.</td></tr><tr><td><a href="https://github.com/gruemme/tb-api-compose_message_headers">ComposeMessageHeaders</a></td><td align="center"></td><td><p>Adds missing functionality to add headers to a newly composed message. <br></p><p><em>Note: Adding <code>X-</code> headers is supported by the compose API since Thunderbird 102</em>.</p></td></tr><tr><td><a href="https://github.com/rsjtdrjgfuzkfg/thunderbird-experiments/tree/master/experiments/customui">CustomUI</a></td><td align="center"><a href="https://thunderbird.topicbox.com/groups/addons/T07afba099782a5c5/customui-api-experiment">💬</a>​</td><td>A generic UI extension framework based on iframes registered at fixed extension points.<br><br><em>Note: Does not yet fully support Thunderbird Supernova</em></td></tr><tr><td><a href="https://github.com/thundernest/addon-developer-support/tree/master/auxiliary-apis/FileSystem">FileSystem</a></td><td align="center"></td><td>An API to access files in the users profile folder. Until Mozilla has made a final decision about including the <a href="https://web.dev/file-system-access/">Chrome FileSystem API</a>, this API can be used as an interim solution.</td></tr><tr><td><a href="https://github.com/thundernest/addon-developer-support/tree/master/auxiliary-apis/LegacyCSS">LegacyCSS</a></td><td align="center"></td><td>Load custom CSS files into Thunderbird windows.</td></tr><tr><td><a href="https://github.com/thundernest/addon-developer-support/tree/master/auxiliary-apis/LegacyPrefs">LegacyPrefs</a></td><td align="center"></td><td>Access Thunderbird system preferences.</td></tr><tr><td><a href="https://github.com/jobisoft/notificationbar-API/tree/master/notificationbar">NotificationBar</a></td><td align="center"><a href="https://thunderbird.topicbox.com/groups/addons/T576f843ea846049c-M2b3bf1c77ba25dfb1d78e7d4/notification-api-proposal">💬</a> <a href="https://docs.google.com/document/d/1mTwVozOiEcDCw3QQKxVz-N5yHY8SVFC9KUL4_x0IN68/">📝</a></td><td>Add Thunderbird notification bars.</td></tr><tr><td><a href="https://github.com/thundernest/addon-developer-support/tree/master/auxiliary-apis/NotifyTools">NotifyTools</a></td><td align="center"></td><td>Provides a messaging system between legacy code and the WebExtension background.</td></tr><tr><td><a href="https://github.com/thundernest/addon-developer-support/tree/master/auxiliary-apis/ResourceUrl">ResourceUrl</a></td><td align="center"></td><td>Register a custom <code>resource://</code> URL to be able to load custom JSM files.<br><br><em>Note: Thunderbird is moving away from JSM files and their use should be avoided.</em></td></tr><tr><td><a href="https://github.com/rsjtdrjgfuzkfg/thunderbird-experiments/tree/master/experiments/runtime">Runtime.onDisable</a></td><td align="center"></td><td>Permit WebExtensions to perform (time-limited) cleanup tasks after the add-on is disabled or uninstalled.</td></tr><tr><td><a href="https://github.com/rsjtdrjgfuzkfg/thunderbird-experiments/tree/master/experiments/tcp">TCP</a></td><td align="center"></td><td>TCP support based on ArrayBuffers (currently client side only).</td></tr></tbody></table>

💬: API has a public announcement post\
📝: API has a public interface discussion

If you have created an Experiment API which you think could be beneficial to other developers, please [tell us about it](https://github.com/thundernest/developer-docs/issues/new), so we can include it here.

### Proposing APIs to be included in Thunderbird

Creating a good WebExtension API for Thunderbird is not an easy task. New APIs need to be generic and distinct from other APIs. Their interfaces have to be designed with foresight as we should avoid scenarios, where we have to make backward incompatible changes later because we have missed something.

If you want to propose and maybe collaborate on a new API, the following process is suggested:

1. Announcing the idea and a first outline of the suggested API on [discuss.thunderbird.net](https://discuss.thunderbird.net/groups/addons). An actual implementation is not yet needed, but a general concept of how the API is supposed to work is helpful. This allows the add-on developer community to provide feedback and to make sure the design will cover their needs as well.
2. Publishing a detailed API description, which can be viewed and commented. Google docs have worked quite well (see [here](https://docs.google.com/document/d/15awbKiVfdOTmsRpgD1dxm3gvOt08EQZDSnMl8QRBFoY/edit?usp=sharing) and [here](https://docs.google.com/document/d/1mTwVozOiEcDCw3QQKxVz-N5yHY8SVFC9KUL4\_x0IN68/edit)), but any platform that allows to collaborate will be sufficient. The goal is to shape out the event, function and type definitions of the API.
3. Creating a [tracking bug on bugzilla](https://bugzilla.mozilla.org/enter\_bug.cgi?product=Thunderbird\&component=Add-Ons%3A+Extensions+API), referencing the API description, so the core development team is notified and can comment as well.
4. Publishing a working implementation, so add-on developers can use it and provide feedback.
5. Adding a patch to the tracking bug and request review.
