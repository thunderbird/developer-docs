---
description: What you need to know about making add-ons for Thunderbird.
---

# Introduction

Current Thunderbird add-ons are based on the WebExtension technology, which is also used by many web browsers. Browser vendors usually refer to their add-ons as _WebExtensions._ Thunderbird however has a lot of features not available in Browsers, and add-ons written for Thunderbird will most likely not work in Browsers. They are therefore referred to as _MailExtensions_ instead.

MailExtensions are a collection of files which modify Thunderbirds appearance and behaviour. They can add user interface elements, alter content, or perform background tasks. Thunderbird add-ons are created using standard JavaScript, CSS and HTML.

![Structure of a Thunderbird add-on and how it interacts with Thunderbird.](<../.gitbook/assets/webext\_diagram (1).png>)

Key features of MailExtensions (Thunderbird add-ons based on WebExtension technology):

* They use stable APIs and do not need to be updated when a new version of Thunderbird is released.
* They use a permission mechanism that requires users to confirm any permission requested by add-ons before they can be installed. These permission requests enable users to know what an add-on is actually doing.&#x20;
* Advanced add-on developers (with in-depth knowledge of Thunderbird source code) can extend the current available set of APIs by creating their own APIs and shipping them with the add-ons. These APIs are called [Experiments](mailextensions/#experiment-apis), and they interact directly with Thunderbird's internal APIs, allowing add-ons to use additional features not yet available via built-in WebExtension APIs.

An add-on can either be an **extension** (adding functionality or changing the way Thunderbird works) or a **theme** (changing the way Thunderbird looks).

## Add-On Guides

For more information on the two add-on types supported by Thunderbird, see their respective guides:

{% content-ref url="mailextensions/" %}
[mailextensions](mailextensions/)
{% endcontent-ref %}

{% content-ref url="web-extension-themes.md" %}
[web-extension-themes.md](web-extension-themes.md)
{% endcontent-ref %}
