---
description: What you need to know about making add-ons for Thunderbird.
---

# Introduction

Current Thunderbird add-ons are based on the WebExtension technology, which is also used by many web browsers. Browser vendors usually refer to their add-ons as _WebExtensions._ Thunderbird however has a lot of features not available in Browsers, and add-ons written for Thunderbird will most likely not work in Browsers. They are therefore referred to as _MailExtensions_ instead.

MailExtensions are a collection of files that change the way Thunderbird looks and behaves. They can add user interface elements, alter content, or perform background tasks. Thunderbird add-ons are created using standard JavaScript, CSS and HTML.

<figure><img src="../.gitbook/assets/webext_diagram.png" alt="Structure of a MailExtension (a Thunderbird add-on based on WebExtension technology) and how it interacts with Thunderbird."><figcaption><p>Structure of a MailExtension (a Thunderbird add-on based on WebExtension technology) and how it interacts with Thunderbird.</p></figcaption></figure>

Key features of MailExtensions:

* They use stable APIs and do not need to be updated when a new version of Thunderbird is released.
* They use a permission mechanism that requires users to confirm any permission requested by add-ons before they can be installed. These permission requests enable users to know what an add-on is actually doing.&#x20;
* Advanced add-on developers (with in-depth knowledge of Thunderbird source code) can extend the current available set of APIs by creating their own APIs and ship them with the add-ons. These APIs are called [Experiments](mailextensions/#experiment-apis), and they interact directly with Thunderbird's internal APIs, allowing add-ons to use additional features not yet available via built-in WebExtension APIs.

An add-on can either be an **extension** (adding functionality or changing the way Thunderbird works) or a **theme** (changing the way Thunderbird looks).

## Getting Started

There's a lot of information out there when it comes to add-on development, and finding the most relevant one can be time-consuming. We have therefore put together a tutorial that explains step by step how to create your first extension for Thunderbird:

{% content-ref url="hello-world-add-on/" %}
[hello-world-add-on](hello-world-add-on/)
{% endcontent-ref %}

## Add-On Guides

For more detailed information on the two add-on types supported by Thunderbird, see their respective guides:

{% content-ref url="mailextensions/" %}
[mailextensions](mailextensions/)
{% endcontent-ref %}

{% content-ref url="web-extension-themes.md" %}
[web-extension-themes.md](web-extension-themes.md)
{% endcontent-ref %}
