# Engage the new overlay loader

## Switch to JSON manifest

To use the new overlay loader, overlay extensions _must_ switch from an RDF manifest \(`install.rdf`\) to a JSON manifest \(`manifest.json`\).

{% hint style="warning" %}
 This should be done for overlay extensions only, _not_ for bootstrapped extensions.
{% endhint %}

Here’s a basic example. This RDF manifest:

```markup
<?xml version="1.0" encoding="utf-8"?>
<RDF xmlns="http://www.w3.org/1999/02/22-rdf-syntax-ns#"
     xmlns:em="http://www.mozilla.org/2004/em-rdf#">
  <Description about="urn:mozilla:install-manifest">
    <em:id>myextension@sample.extensions.thunderbird.net</em:id>
    <em:type>2</em:type>
    <em:name>Extension</em:name>
    <em:description>Does a thing!</em:description>
    <em:version>1.0</em:version>
    <em:optionsURL>chrome://myextension/content/options.xul</em:optionsURL>
    <em:optionsType>3</em:optionsType><!-- Options in a tab -->
    <em:targetApplication>
      <Description>
        <em:id>{3550f703-e582-4d05-9a08-453d09bdfdc6}</em:id>
        <em:minVersion>60.0</em:minVersion>
        <em:maxVersion>60.*</em:maxVersion>
      </Description>
    </em:targetApplication>
  </Description>
</RDF>
```

Becomes this JSON manifest:

```javascript
{
  "manifest_version": 2,
  "applications": {
    "gecko": {
      "id": "myextension@sample.extensions.thunderbird.net",
      "strict_min_version": "67.0a1"
    }
  },
  "name": "Extension",
  "description": "Does a thing",
  "version": "2.0",

  "legacy": {
    "options": {
      "page": "chrome://myextension/content/options.xul",
      "open_in_tab": true
    }
  }
}
```

Note the `legacy` key. It’s a special key to engage Thunderbird’s new overlay loader. The shown example also specifies an options page. The key `open_in_tab` is optional and defaults to a value of`false`. A value of`true` corresponds to optionsType 3 in the RDF manifest.

If no options page is needed, the legacy key can just be set to true:

```javascript
"legacy": true
```

This example is only in English. You probably want to use translated strings in your manifest. Read [this MDN article about it](https://developer.mozilla.org/en-US/docs/Mozilla/Add-ons/WebExtensions/Internationalization#Internationalizing_manifest.json). Unfortunately that means you now need two sets of translated strings, one \(that you already have\) for your extension and another for the manifest.

**Examples of overlay extension converted like this are:**

* [Open With](https://github.com/darktrojan/openwith/blob/VERSION_6.9/manifest.json)
* [Shrunked Image Resizer](https://github.com/darktrojan/shrunked/blob/VERSION_4.5/manifest.json)

{% hint style="danger" %}
 It _is_ possible to have both `install.rdf` and `manifest.json` files in your extension, so you _could_ release a version compatible with Thunderbird 60 and 68. It is **not** **recommended**.
{% endhint %}

## Notes about the new overlay loader

A lot of effort has been done to create the new overlay loader, but still things might not work as before. We are tracking this in bug [1476259](https://bugzilla.mozilla.org/show_bug.cgi?id=1476259).

## Notes about Chrome manifest

The `overlay` and `style` lines in your`chrome.manifest` are now handled by the new overlay loader. You’ll see lines like this in the Error Console:

```text
Ignoring unrecognized chrome manifest directive 'overlay'.
```

Those errors come from the old system, which no longer deals with such things.

You might see the same line, but regarding `interfaces`. **Registering your own interfaces using `.xpt`files is no longer possible.**

## Notes about overlaying

Overlays in Thunderbird itself \(except the calendar extensions\) have been removed, so extensions can not overlay the removed Thunderbird overlays any more. For example, if your add-on overlaid`mailWindowOverlay.xul`, that needs to be changed; in this example you most likely need to overlay `messenger.xul` now.

## Notes about `<script>` tags

`<script>` tags added to an overlay file are now run _after_ the application of the entire overlay, regardless of their position in the overlay. This may cause unexpected behavior if your script previously ran before elements were inserted. For elements with event handlers these event handlers may run when the element is added, and they may fail if they rely on content being set up by a script which now runs after the creation of the element.

You may be used to putting the contents of a script directly in a document. This currently still works but it may break in the future. **Inline scripts are strongly discouraged.** Use a file instead.

