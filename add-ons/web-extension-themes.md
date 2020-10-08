---
description: How to create Themes for Thunderbird.
---

# A Guide to Themes

A Theme is a Thunderbird add-on that allows to change the appearance of Thunderbird.

## Dynamic Themes

Dynamic themes are actually MailExtensions, that use the [`theme`](https://developer.mozilla.org/en-US/docs/Mozilla/Add-ons/WebExtensions/API/theme) API to do more interesting things with the look of Thunderbird. For instance, [one example](https://developer.mozilla.org/en-US/docs/Mozilla/Add-ons/Themes/Theme_concepts#Dynamic_themes) used in the Firefox documentation is a dynamic theme that changes the theme colors based on the time of day.

{% page-ref page="mailextensions/" %}

## Static Themes

Static themes, like the name implies - are static and do not change. They have a set of colors or images that make up the theme and this does not change. It typically consists of two files, zipped up with an .xpi extension just as any other add-on:

* manifest.json
* image.png or .jpg

### The `manifest.json` file

You must prepare a JSON manifest, named `manifest.json` just as with other WebExtensions. Below is a basic example:

```javascript
{
  "manifest_version":2,
  "applications":{
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

* `manifest_version` is always 2.
* `id` is a unique GUID for your theme.

{% hint style="danger" %}
Although not required by Firefox, Thunderbird requires an `applications` object with an id set for the add-on! Because we don't sign add-ons, themes will not install without it.
{% endhint %}

### Additional color properties

Although the above theme will work as-is, there are other properties which can be added.

For the `colors` object, there are many options. Those known to work are listed below:

* bookmark\_text
* frame
* frame\_inactive
* popup
* popup\_text
* popup\_border
* popup\_highlight
* popup\_highlight\_text
* popup\_border
* sidebar
* sidebar\_text
* sidebar\_highlight
* sidebar\_highlight\_text
* sidebar\_border
* tab\_text
* tab\_line
* tab\_loading
* tab\_background\_text
* toolbar\_field
* toolbar\_field\_text
* toolbar\_field\_highlight
* toolbar\_field\_highlight\_text
* toolbar\_field\_border
* toolbar\_field\_focus
* toolbar\_field\_text\_focus
* toolbar\_field\_border\_focus
* toolbar\_top\_separator
* toolbar\_bottom\_separator
* toolbar\_vertical\_separator

### Icons

You can add an icon for your theme, like other types of add-ons, with the following code:

```javascript
  "icons": {
    "16": "icon.png"
  },
```

Don't forget to put the `icon.png` file in your add-on as well.

### A complete example

Here is a `manifest.json` from a Theme that uses all the above features, thanks to Paenglab:

```javascript
{
  "manifest_version": 2,
  "name": "Nuvola WebExtension theme",
  "version": "1.1",
  "applications": {
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

