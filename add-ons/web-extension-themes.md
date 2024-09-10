---
description: How to create themes for Thunderbird.
---

# A Guide to Themes

A theme is a Thunderbird add-on that allows to change the appearance of Thunderbird. This document covers the following topics:

* [Static Themes](web-extension-themes.md#static-themes)
* [Dynamic Themes](web-extension-themes.md#dynamic-themes)
* [Theming of message-compose-windows and message-display-tabs](web-extension-themes.md#theming-message-compose-windows-and-message-display-tabs)
* [Theme Experiments](web-extension-themes.md#theme-experiments)

## Static Themes

Static themes, like the name implies - are static and do not change. They have a set of colors or images that make up the theme, and this does not change. It typically consists of two files, zipped up with an .xpi extension just as any other add-on:

* manifest.json
* image.png or .jpg

### The `manifest.json` file

You must prepare a JSON manifest, named `manifest.json` just as with other add-ons. Below is a basic example:

```javascript
{
  "manifest_version":2,
  "browser_specific_settings":{
    "gecko":{
      "id":"mytheme@sample.themes.thunderbird.net",
      "strict_min_version":"60.0"
    }
  },
  "name":"Thunderbird ExampleTheme",
  "description":"Static theme version of an example Thunderbird theme.",
  "version":"1.0",
  "theme":{
    "images":{
      "theme_frame":"thunderbirdimage.jpg"
    },
    "colors":{
      "frame":"#000000",
      "tab_background_text":"#ffffff"
    }
  }
}
```

The following manifest keys define basic properties:

* `manifest_version`: A _mandatory key_ defining the Manifest version used by the theme. Supported versions are `2` and `3` (since Thunderbird Beta 110). The Manifest defines the basic rules how a theme needs to be crafted and how it can interact with Thunderbird.
* `name` : A _mandatory key_ to set the name of the theme.
* `version` : A _mandatory key_ to define a number that denotes the version of the theme.
* `description` : A brief description of the theme.
* `author` : The name of a person or company representing the developer.

The `browser_specific_settings.gecko` manifest key defines the following properties:

* `strict_min_version`: Defines the lowest targeted version of Thunderbird.
* `strict_max_version`: Defines the highest targeted version of Thunderbird. It can be set to a specific version or a broader match to limit it to a branch (for example `102.*`). Usually not needed.
* `id`: The id serves as a unique identifier for the theme and is _mandatory_ in order to upload a theme to ATN or to be able to install it from an XPI file.

{% hint style="danger" %}
Although not required by Firefox, Thunderbird requires an`id.`Thunderbird does not sign add-ons, and themes will not install without it.

Best practice is to use an "email-address-style" id (but not a real email address) on a domain you control, for example `name-of-your-addon@example.com`, if you own `example.com`. As the id of your theme cannot be changed once it is published, it is highly recommended to use a domain that you plan to keep for the forseeable future. If you don't have a domain to use, feel free to use:\
`<atn-user-name>.<add-on-name>@addons.thunderbird.net`\


Alternatively, you may use an UUID enclosed in curly braces, for example:\
`{e4aa2097-8ee9-49a4-9ec7-c633b1e8dfda}`
{% endhint %}

### Additional theme properties

Although the above theme will work as-is, there are other properties which can be added. All currently supported properties are listed in the [`ThemeType`](https://webextension-api.thunderbird.net/en/beta-mv2/theme.html#theme-themetype) definition in our WebExtension API documentation.

### Icons

You can add an icon for your theme, like other types of add-ons, with the following code:

```javascript
  "icons": {
    "16": "icon.png"
  },
```

Don't forget to put the `icon.png` file in your add-on as well.

### A complete example

Here is a `manifest.json` from a theme that uses all the above features, thanks to Paenglab:

```javascript
{
  "manifest_version": 2,
  "name": "Nuvola WebExtension theme",
  "version": "1.1",
  "browser_specific_settings": {
    "gecko": {
      "id": "nuvola@paenglab.ch",
      "strict_min_version": "60.0"
    }
  },
  "description": "Light theme with some gradients.",
  "icons": {
    "16": "icon.png"
  },
  "theme": {
    "colors": {
      "frame": "#e7e8ec",
      "tab_text": "#000",
      "tab_line": "#1f9afd",
      "tab_loading": "#1f9afd",
      "tab_background_text": "#000",
      "bookmark_text": "#333",
      "toolbar_field": "#f2f2f2",
      "toolbar_field_text": "#444",
      "toolbar_field_highlight": "#1f9afd",
      "toolbar_field_highlight_text": "#fff",
      "toolbar_field_border": "#999",
      "toolbar_field_focus": "#f2f2f2",
      "toolbar_field_text_focus": "#000",
      "toolbar_field_border_focus": "#1f9afd",
      "toolbar_top_separator": "#aaa",
      "toolbar_bottom_separator": "#888",
      "toolbar_vertical_separator": "#888",
      "sidebar": "#fbfbfb",
      "sidebar_text": "#000",
      "sidebar_highlight": "rgba(11,113,220,.6)",
      "sidebar_highlight_text": "#fff",
      "sidebar_border": "#999",
      "popup": "#e6e8ef",
      "popup_text": "#000",
      "popup_border": "#666",
      "popup_highlight": "#1f9afd",
      "popup_highlight_text": "#fff"
    },
    "images": {
      "theme_frame": "background.png"
    }
  }
}
```

## Dynamic Themes

Dynamic themes are actually normal extensions, that use the [`update()`](https://webextension-api.thunderbird.net/en/beta-mv2/theme.html#update-windowid-details) method of the [`theme`](https://webextension-api.thunderbird.net/en/beta-mv2/theme.html#theme) API instead of a static [`theme`](https://webextension-api.thunderbird.net/en/beta-mv2/theme.html) manifest key. They can set the same theme properties like static themes, but they can change them dynamically. For instance the [Night and Day theme](https://addons.thunderbird.net/addon/night-and-day-dynamic/) is a dynamic theme that changes the theme colors based on the time of day.

{% content-ref url="mailextensions/" %}
[mailextensions](mailextensions/)
{% endcontent-ref %}

## Theming message-compose-windows and message-display-tabs

The built-in theming properties do not modify the message-compose-windows and the message-display-tabs. These can be manipulated by injecting CSS files using the following WebExtension API methods:

* [messageDisplayScripts.register()](https://webextension-api.thunderbird.net/en/beta-mv2/messageDisplayScripts.html#register-messagedisplayscriptoptions)
* [composeScripts.register()](https://webextension-api.thunderbird.net/en/beta-mv2/composeScripts.html#register-composescriptoptions)

To inject the file `compose.css` into the message-compose-window, register it in your background script as follows:

```javascript
messenger.composeScripts.register({
  css : [ { file: "compose.css"} ]
});
```

## Theme Experiments

A theme experiment allows modifying the user interface of Thunderbird beyond what is currently possible using the built-in color, image and property keys of the [`theme`](https://webextension-api.thunderbird.net/en/beta-mv2/theme.html) API. These experiments are a precursor to proposing new theme features for inclusion in Thunderbird.&#x20;

Experimentation is done by exposing already existing internal CSS variables (e.g. `--arrowpanel-dimmed`) to the [`theme`](https://webextension-api.thunderbird.net/en/beta-mv2/theme.html) API and by loading additional stylesheets to define new CSS variables, extending the theme-able areas of Thunderbird.

Use the [browser toolbox](https://developer.mozilla.org/en-US/docs/Tools/Browser\_Toolbox) to discover CSS selectors for Thunderbird UI elements or internal Thunderbird CSS variables.

{% hint style="info" %}
Further information regarding theme experiments can be found in our [WebExtension API documentation of the theme API](https://webextension-api.thunderbird.net/en/beta-mv2/theme.html).
{% endhint %}

{% hint style="info" %}
Our example repository includes an [add-on using a theme experiment](https://github.com/thunderbird/webext-examples/tree/master/manifest_v2/theme\_experiment) to change the color of the chat icon.
{% endhint %}

