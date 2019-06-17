# Convert Bootstrapped Extension to MailExtension

## Switch to JSON manifest

You _must_ switch from an RDF manifest \(`install.rdf`\) to a JSON manifest \(`manifest.json`\). Here is a basic example. This RDF manifest:

```markup
<?xml version="1.0" encoding="utf-8"?>
<RDF xmlns="http://www.w3.org/1999/02/22-rdf-syntax-ns#"
     xmlns:em="http://www.mozilla.org/2004/em-rdf#">
  <Description about="urn:mozilla:install-manifest">
    <em:id>myextension@sample.extensions.thunderbird.net</em:id>
    <em:type>2</em:type>
    <em:bootstrap>true</em:bootstrap>
    <em:name>Extension</em:name>
    <em:description>Does a thing!</em:description>
    <em:version>1.0</em:version>
    <em:optionsURL>chrome://myextension/content/options.xul</em:optionsURL>
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

  "legacy": {
    "type" : "bootstrap",
    "options": {
      "page": "chrome://myextension/content/options.xul",
      "open_in_tab": true
    }
  }
}
```

The `legacy` key enables Thunderbird’s legacy support, for bootstrap extension you have to set the `type` key to `bootstrap`.

The shown example also specifies an optional `options` key to define the options page. The key `open_in_tab` is optional and defaults to a value of`false`. A value of`true` corresponds to optionsType 3 in the RDF manifest.

{% hint style="info" %}
This example is only in English. You probably want to use translated strings in your manifest. Read [this MDN article about it](https://developer.mozilla.org/en-US/docs/Mozilla/Add-ons/WebExtensions/Internationalization#Internationalizing_manifest.json). Unfortunately that means you now need two sets of translated strings, one \(that you already have\) for your extension and another for the manifest.
{% endhint %}

## Possible timing issues with the Window Mediator Service

The changes made in Thunderbird for bootstraped add-ons to use `manifest.json` _may_ have changed the timing of the window listener, so that the notification is now send before the window has actually been fully loaded. 

This is not new in general, but may be more observable now. If your add-on does not wait for the onload event of newly opened windows, before accessing its DOM, you probably have to make the neccessary changes now.

More information on using the Window Mediator Service in bootstrapped add-ons can be found [here](https://www.oxymoronical.com/blog/2011/01/Playing-with-windows-in-restartless-bootstrapped-extensions).

