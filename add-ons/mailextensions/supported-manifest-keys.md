---
description: Descriptions of all manifest keys supported by Thunderbird.
---

# Supported Manifest Keys

_Multiple manifest keys in the following table are common to Thunderbird and Firefox and link to MDN description pages. Please be aware, that MDN is dedicated to browsers and of course to Firefox. Some information listed there may not apply to Thunderbird._

<table>
  <thead>
    <tr>
      <th style="text-align:left">Manifest Key</th>
      <th style="text-align:left">Description</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td style="text-align:left"><a href="https://developer.mozilla.org/en-US/docs/Mozilla/Add-ons/WebExtensions/manifest.json/author">author</a>
      </td>
      <td style="text-align:left">Defines the extension&apos;s author. If the <a href="https://developer.mozilla.org/en-US/Add-ons/WebExtensions/manifest.json/developer"><code>developer</code></a> key
        is supplied and it contains the <code>name</code> property, it will override
        the <code>author</code> key. There is no way to specify multiple authors.
        This is a <a href="https://developer.mozilla.org/en-US/Add-ons/WebExtensions/Internationalization#Internationalizing_manifest.json">localizable property</a>.</td>
    </tr>
    <tr>
      <td style="text-align:left"><a href="https://developer.thunderbird.net/add-ons/mailextensions#background-page">background</a>
      </td>
      <td style="text-align:left">Use the <code>background</code> key to include one or more background scripts,
        and optionally a background page in your extension. Background scripts
        are loaded as soon as the extension is loaded and stay loaded until the
        extension is disabled or uninstalled,</td>
    </tr>
    <tr>
      <td style="text-align:left"><a href="https://thunderbird-webextensions.readthedocs.io/en/latest/browserAction.html">browser_action</a>
      </td>
      <td style="text-align:left">A <a href="https://developer.thunderbird.net/add-ons/mailextensions/supported-ui-elements#browser-action-button">browser action is a button</a> that
        your extension can add to Thunderbird&apos;s main mailTab toolbar. The
        button has an icon, and may optionally have a popup whose content is specified
        using HTML, CSS, and JavaScript.</td>
    </tr>
    <tr>
      <td style="text-align:left"><a href="https://developer.mozilla.org/en-US/docs/Mozilla/Add-ons/WebExtensions/manifest.json/chrome_settings_overrides">chrome_settings_overrides</a>
      </td>
      <td style="text-align:left"><b>[to be confirmed]</b>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">c<a href="https://developer.mozilla.org/en-US/docs/Mozilla/Add-ons/WebExtensions/manifest.json/chrome_url_overrides">hrome_url_overrides</a>
      </td>
      <td style="text-align:left"><b>[to be confirmed]</b>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"><a href="https://thunderbird-webextensions.readthedocs.io/en/latest/cloudFile.html">cloud_file</a>
      </td>
      <td style="text-align:left">Defines a file link provider, which can be used to upload large attachments
        to a server, instead of attaching them directly to the email.</td>
    </tr>
    <tr>
      <td style="text-align:left"><a href="https://thunderbird-webextensions.readthedocs.io/en/latest/commands.html">comm</a>
        <a
        href="https://thunderbird-webextensions.readthedocs.io/en/latest/commands.html">ands</a>
      </td>
      <td style="text-align:left">Use the commands API to add keyboard shortcuts that trigger actions in
        your extension, for example, an action to open a browser action popup.</td>
    </tr>
    <tr>
      <td style="text-align:left"><a href="https://developer.mozilla.org/en-US/docs/Mozilla/Add-ons/WebExtensions/manifest.json/content_scripts">content_scripts</a>
      </td>
      <td style="text-align:left">Instructs Thunderbird to load <a href="https://developer.mozilla.org/en-US/docs/Mozilla/Add-ons/WebExtensions/Content_scripts">content scripts</a> into
        web page tabs and windows, whose URL matches a given pattern.</td>
    </tr>
    <tr>
      <td style="text-align:left"><a href="https://developer.mozilla.org/en-US/docs/Mozilla/Add-ons/WebExtensions/manifest.json/content_security_policy">content_security_policy</a>
      </td>
      <td style="text-align:left">The default policy restricts the sources from which a content script can
        load<a href="https://developer.mozilla.org/en-US/docs/Web/HTML/Element/script"> <code>&lt;script&gt;</code></a> and
        <a
        href="https://developer.mozilla.org/en-US/docs/Web/HTML/Element/object"><code>&lt;object&gt;</code>
          </a>resources, and disallows potentially unsafe practices such as the use
          of <a href="https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/eval"><code>eval()</code></a>.
          See <a href="https://developer.mozilla.org/en-US/Add-ons/WebExtensions/Content_Security_Policy#Default_content_security_policy">Default content security policy</a> to
          learn more about the implications of this.</td>
    </tr>
    <tr>
      <td style="text-align:left"><a href="https://thunderbird-webextensions.readthedocs.io/en/latest/composeAction.html">compose_action</a>
      </td>
      <td style="text-align:left">A <a href="https://developer.thunderbird.net/add-ons/mailextensions/supported-ui-elements#compose-action-button">compose action is a button</a> that
        your extension can add to the toolbar of Thunderbird&apos;s message compose
        tabs. The button has an icon, and may optionally have a popup whose content
        is specified using HTML, CSS, and JavaScript.</td>
    </tr>
    <tr>
      <td style="text-align:left"><a href="https://developer.mozilla.org/en-US/docs/Mozilla/Add-ons/WebExtensions/manifest.json/default_locale">default_locale</a>
      </td>
      <td style="text-align:left">This key must be present if the extension contains the <code>_locales</code> directory,
        and must be absent otherwise. It identifies a subdirectory of <code>_locales</code>,
        and this subdirectory will be used to find the default strings for your
        extension. See <a href="https://developer.mozilla.org/en-US/Add-ons/WebExtensions/Internationalization">Internationalization</a>.</td>
    </tr>
    <tr>
      <td style="text-align:left"><a href="https://developer.mozilla.org/en-US/docs/Mozilla/Add-ons/WebExtensions/manifest.json/description">description</a>
      </td>
      <td style="text-align:left">Defines a short description of the extension, intended for display in
        the Add-ons Manager. This is a <a href="https://developer.mozilla.org/en-US/Add-ons/WebExtensions/Internationalization#Internationalizing_manifest.json">localizable property</a>.</td>
    </tr>
    <tr>
      <td style="text-align:left"><a href="https://developer.mozilla.org/en-US/docs/Mozilla/Add-ons/WebExtensions/manifest.json/developer">developer</a>
      </td>
      <td style="text-align:left">Defines the name of the extension&apos;s developer and their homepage
        URL, intended for display in the add-on manager tab. The <code>name</code> and <code>url</code> properties,
        if present, will override the <a href="https://developer.mozilla.org/en-US/Add-ons/WebExtensions/manifest.json/author"><code>author</code></a> and
        <a
        href="https://developer.mozilla.org/en-US/Add-ons/WebExtensions/manifest.json/homepage_url"><code>homepage_url</code>
          </a>keys, respectively. This is a <a href="https://developer.mozilla.org/en-US/Add-ons/WebExtensions/Internationalization#Internationalizing_manifest.json">localizable property</a>.</td>
    </tr>
    <tr>
      <td style="text-align:left"><a href="https://developer.mozilla.org/en-US/docs/Mozilla/Add-ons/WebExtensions/manifest.json/devtools_page">devtools_page</a>
      </td>
      <td style="text-align:left"><b>[to be confirmed]</b> - Use this key to enable your extension to extend
        the built-in devtools.</td>
    </tr>
    <tr>
      <td style="text-align:left"><a href="https://developer.mozilla.org/en-US/docs/Mozilla/Add-ons/WebExtensions/manifest.json/dictionaries">dictionaries</a>
      </td>
      <td style="text-align:left"><b>[to be confirmed]</b><em> - </em>The <code>dictionaries</code> key specifies
        the <code>locale_code</code> for which your extension supplies a dictionary.</td>
    </tr>
    <tr>
      <td style="text-align:left"><a href="https://developer.mozilla.org/en-US/docs/Mozilla/Add-ons/WebExtensions/manifest.json/homepage_url">homepage_url</a>
      </td>
      <td style="text-align:left">
        <p>URL for the extension&apos;s home page. If the <a href="https://developer.mozilla.org/en-US/Add-ons/WebExtensions/manifest.json/developer"><code>developer</code></a> key
          is supplied and it contains the <code>url</code> property, this will override
          the <code>homepage_url</code> key.</p>
        <p>This is a <a href="https://developer.mozilla.org/en-US/Add-ons/WebExtensions/Internationalization#Internationalizing_manifest.json">localizable property</a>.</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"><a href="https://developer.mozilla.org/en-US/docs/Mozilla/Add-ons/WebExtensions/manifest.json/icons">icons</a>
      </td>
      <td style="text-align:left">The <code>icons</code> key specifies icons for your extension. Those icons
        will be used to represent the extension in components such as the Add-ons
        Manager.</td>
    </tr>
    <tr>
      <td style="text-align:left"><a href="https://developer.mozilla.org/en-US/docs/Mozilla/Add-ons/WebExtensions/manifest.json/manifest_version">manifest_version</a>
      </td>
      <td style="text-align:left">
        <p>This key specifies the version of manifest.json used by this extension.</p>
        <p>Currently, this must always be 2.</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"><a href="https://thunderbird-webextensions.readthedocs.io/en/latest/messageDisplayAction.html">message_display_action</a>
      </td>
      <td style="text-align:left">A <a href="https://developer.thunderbird.net/add-ons/mailextensions/supported-ui-elements#message-display-action-button">message display action is a button</a> that
        your extension can add to the toolbar of Thunderbird&apos;s message display
        tabs. The button has an icon, and may optionally have a popup whose content
        is specified using HTML, CSS, and JavaScript.</td>
    </tr>
    <tr>
      <td style="text-align:left"><a href="https://developer.mozilla.org/en-US/docs/Mozilla/Add-ons/WebExtensions/manifest.json/name">name</a>
      </td>
      <td style="text-align:left">Defines the name of the extension. This is used to identify the extension
        in the Add-on manager and on sites like addons.thunderbird.net.</td>
    </tr>
    <tr>
      <td style="text-align:left"><a href="https://developer.mozilla.org/en-US/docs/Mozilla/Add-ons/WebExtensions/manifest.json/optional_permissions">optional_permissions</a>
      </td>
      <td style="text-align:left">Defines permissions, which should be requested dynamically (when needed)
        and not during install.</td>
    </tr>
    <tr>
      <td style="text-align:left"><a href="https://developer.mozilla.org/en-US/docs/Mozilla/Add-ons/WebExtensions/manifest.json/options_ui">options_ui</a>
      </td>
      <td style="text-align:left">Defines an <a href="https://developer.mozilla.org/en-US/docs/Mozilla/Add-ons/WebExtensions/Options_pages">options page</a> for
        your extension.</td>
    </tr>
    <tr>
      <td style="text-align:left"><a href="https://developer.mozilla.org/en-US/docs/Mozilla/Add-ons/WebExtensions/manifest.json/permissions">permissions</a>
      </td>
      <td style="text-align:left">This key requests <a href="https://developer.thunderbird.net/add-ons/mailextensions#permissions">special powers for your extension</a>.
        This key is an array of strings, and each string is a request for a permission.</td>
    </tr>
    <tr>
      <td style="text-align:left"><a href="https://developer.mozilla.org/en-US/docs/Mozilla/Add-ons/WebExtensions/manifest.json/protocol_handlers">protocol_handlers</a>
      </td>
      <td style="text-align:left"><b>[to be confirmed]</b>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"><a href="https://developer.mozilla.org/en-US/docs/Mozilla/Add-ons/WebExtensions/manifest.json/short_name">short_name</a>
      </td>
      <td style="text-align:left">
        <p>Short name for the extension. If given, this will be used in contexts
          where the <a href="https://developer.mozilla.org/en-US/Add-ons/WebExtensions/manifest.json/name">name</a> field
          is too long. It&apos;s recommended that the short name should not exceed
          12 characters. If the short name field is not included in manifest.json,
          then name will be used instead and may be truncated.</p>
        <p>This is a <a href="https://developer.mozilla.org/en-US/Add-ons/WebExtensions/Internationalization#Internationalizing_manifest.json">localizable property</a>.</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"><a href="https://developer.mozilla.org/en-US/docs/Mozilla/Add-ons/WebExtensions/manifest.json/theme">theme</a>
      </td>
      <td style="text-align:left">This key defines a static theme to be applied to Thunderbird.</td>
    </tr>
    <tr>
      <td style="text-align:left"><a href="https://developer.mozilla.org/en-US/docs/Mozilla/Add-ons/WebExtensions/manifest.json/theme_experiment">theme_experiment</a>
      </td>
      <td style="text-align:left">This key enables the definition of experimental <a href="https://developer.mozilla.org/en-US/docs/Mozilla/Add-ons/WebExtensions/manifest.json/theme"><code>theme</code></a> key
        properties for the Thunderbird interface. These experiments are a precursor
        to proposing new theme features for inclusion in Thunderbird.</td>
    </tr>
    <tr>
      <td style="text-align:left"><a href="https://developer.mozilla.org/en-US/docs/Mozilla/Add-ons/WebExtensions/manifest.json/user_scripts">user_scripts</a>
      </td>
      <td style="text-align:left"><b>[to be confirmed]</b> - Instructs the browser to load a script packaged
        in the extension, known as the API script, this script is used to export
        a set of custom API methods for use in user scripts.</td>
    </tr>
    <tr>
      <td style="text-align:left"><a href="https://developer.mozilla.org/en-US/docs/Mozilla/Add-ons/WebExtensions/manifest.json/version">version</a>
      </td>
      <td style="text-align:left">The version of the extension, formatted as numbers and ASCII characters
        separated by dots. For the details of the version format, see the <a href="https://developer.mozilla.org/en-US/docs/Toolkit_version_format">Version format</a> page.</td>
    </tr>
    <tr>
      <td style="text-align:left"><a href="https://developer.mozilla.org/en-US/docs/Mozilla/Add-ons/WebExtensions/manifest.json/web_accessible_resources">web_accessible_resources</a>
      </td>
      <td style="text-align:left">This key enables an extension to make resources bundled with the extension
        (for example images, HTML, CSS or JavaScript) available to web pages.</td>
    </tr>
  </tbody>
</table>

