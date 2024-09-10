# Convert legacy overlay extension to legacy WebExtension

## Switch to JSON manifest

You _must_ switch from an RDF manifest (`install.rdf`) to a JSON manifest (`manifest.json`). Here is a basic example. This RDF manifest:

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
    <em:iconURL>chrome://myextension/content/icon32x32.png</em:iconURL>
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
      "strict_min_version": "68.0"
    }
  },
  "name": "Extension",
  "description": "Does a thing",
  "version": "2.0",
  "icons": {
    "32": "content/icon32x32.png"
  },
  "legacy": {
    "type": "xul",
    "options": {
      "page": "chrome://myextension/content/options.xul",
      "open_in_tab": true
    }
  }
}
```

Detailed information about the possible config options for `manifest.json` can be found in the [MDN documentation](https://developer.mozilla.org/en/docs/Mozilla/Add-ons/WebExtensions/manifest.json).

The `legacy` key enables Thunderbird’s legacy WebExtension support. Setting the `type` key to `xul` engages the new XUL overlay loader. The overlay loader is a Thunderbird component that takes XUL code as written in an overlay extension and applies it to the UI. In Thunderbird 60, this was a part of the core UI library, but it was removed. We have built a new overlay loader to replace as much of the removed code as possible.

{% hint style="warning" %}
The URL for icons must no longer be full chrome URL as before, but a simple path relative to the root directory of the add-on.
{% endhint %}

The shown example also specifies an optional `options` key to define the options page. The key `open_in_tab` is optional and defaults to a value of`false`. If your old RDF manifest included an `em:optionsType` of 3, you can set `open_in_tab` to `true`, to have your options opened again in a new tab instead of a new window.

{% hint style="info" %}
This example is only in English. You probably want to use translated strings in your manifest. Read [this MDN article about it](https://developer.mozilla.org/en-US/docs/Mozilla/Add-ons/WebExtensions/Internationalization#Internationalizing\_manifest.json). Unfortunately that means you now need two sets of translated strings, one (that you already have) for your extension and another for the manifest.
{% endhint %}

**Examples of overlay extension converted like this are:**

* [Open With](https://github.com/darktrojan/openwith/blob/VERSION\_6.9/manifest.json)
* [Shrunked Image Resizer](https://github.com/darktrojan/shrunked/blob/VERSION\_4.5/manifest.json)

## Notes about overlaying in general

A lot of effort has been done to create the new overlay loader, but still things might not work as before. We are tracking this in bug [1476259](https://bugzilla.mozilla.org/show\_bug.cgi?id=1476259).

Overlays in Thunderbird itself (except the calendar extensions) have been removed, so extensions can not overlay the removed Thunderbird overlays any more. For example, if your add-on overlaid `mailWindowOverlay.xul`, that needs to be changed; in this example you most likely need to overlay `messenger.xul` now.

Furthermore, the new overlay loader does not properly support dependencies between overlays in different add-ons. As a result, you should only reference elements from the original document you're overlaying, or other overlays in the same extension. Most notably, you need to switch to non-overlay methods when altering the calendar user interface in the main window, or your add-on will not load reliably.

## Notes about Chrome manifest

The `overlay` and `style` lines in your`chrome.manifest` are now handled by the new overlay loader. You’ll see lines like this in the Error Console:

```
Ignoring unrecognized chrome manifest directive 'overlay'.
```

Those errors come from the old system, which no longer deals with such things.

You might see the same line, but regarding `interfaces`.

{% hint style="danger" %}
**Registering your own interfaces using `.xpt`files is no longer possible.** However, they can be converted to JavaScript Modules (the Quicktext add-on includes an [example migration](https://github.com/thunderbird/quicktext/commit/e1ef58dce2816ac1e685f562d30882968e24a391)).
{% endhint %}

## Notes about `<script>` tags

`<script>` tags added to an overlay file are now run _after_ the application of the entire overlay, regardless of their position in the overlay. This may cause unexpected behaviour if your script previously ran before elements were inserted. For elements with event handlers these event handlers may run when the element is added, and they may fail if they rely on content being set up by a script which now runs after the creation of the element.

You may be used to putting the contents of a script directly in a document. This currently still works, but it may break in the future. **Inline scripts are strongly discouraged.** Use a file instead.
