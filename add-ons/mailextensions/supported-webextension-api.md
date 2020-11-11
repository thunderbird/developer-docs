# Supported WebExtension API

## Thunderbird WebExtension APIs

Thunderbird provides the following messenger related WebExtension APIs, which are sometimes referred to as MailExtension APIs.

| API | Permission | Description |
| :--- | :--- | :--- |
| [accounts](https://thunderbird-webextensions.readthedocs.io/en/latest/accounts.html) | accountsRead | Enables an extension to access information of accounts and identities configuered in Thunderbird\`s account manager. |
| [addressBooks](https://thunderbird-webextensions.readthedocs.io/en/latest/addressBooks.html) | addressBooks | Enabled an extension to access, modify and create Thunderbird address books. |
| [browserAction](https://thunderbird-webextensions.readthedocs.io/en/latest/browserAction.html) |  | Interact with a browser action button. |

You can find more information in the [Thunderbird WebExtensions API documentation ](https://thunderbird-webextensions.readthedocs.io/).

## Firefox WebExtension APIs

As Thunderbird is based on the same code base as Firefox, we can reuse many of the WebExtension APIs provided by Firefox. Some methods listed on the API pages may not be supported. Each API page should include a compatibility chart and if that lists support for Firefox, it works in Thunderbird as well. 

_Please be aware, that MDN is dedicated to browsers and of course to Firefox. Some information listed there may not apply to Thunderbird._

<table>
  <thead>
    <tr>
      <th style="text-align:left">API</th>
      <th style="text-align:left">Permission</th>
      <th style="text-align:left">Description</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td style="text-align:left"><a href="https://developer.mozilla.org/en-US/docs/Mozilla/Add-ons/WebExtensions/API/browserSettings">browserSettings</a>
      </td>
      <td style="text-align:left">browserSettings</td>
      <td style="text-align:left">Enables an extension to modify certain global browser settings. Because
        these are global settings, it&apos;s possible for extensions to conflict.
        See the documentation for <a href="https://developer.mozilla.org/en-US/Add-ons/WebExtensions/API/types/BrowserSetting/set"><code>BrowserSetting.set()</code></a> for
        details of how conflicts are handled.</td>
    </tr>
    <tr>
      <td style="text-align:left"><a href="https://developer.mozilla.org/en-US/docs/Mozilla/Add-ons/WebExtensions/API/clipboard">clipboard</a>
      </td>
      <td style="text-align:left">clipboardWrite</td>
      <td style="text-align:left">Enables an extension to copy items to the system clipboard. Currently
        the API only supports copying images, but it&apos;s intended to support
        copying text and HTML in the future.</td>
    </tr>
    <tr>
      <td style="text-align:left"><a href="https://developer.mozilla.org/en-US/docs/Mozilla/Add-ons/WebExtensions/API/contentScripts">contentScripts</a>
      </td>
      <td style="text-align:left"></td>
      <td style="text-align:left">
        <p>Use this API to register content scripts. Registering a content script
          instructs the browser to insert the given content scripts into pages that
          match the given URL patterns.</p>
        <p></p>
        <p>There is no <code>contentScripts</code> API permission, but an extension
          must have the appropriate <a href="https://developer.mozilla.org/en-US/Add-ons/WebExtensions/manifest.json/permissions#Host_permissions">host permissions</a> for
          any patterns it passes to <code>register()</code>.</p>
        <p></p>
        <p>Content scripts can be used in web pages loaded into tabs.</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"><a href="https://developer.mozilla.org/en-US/docs/Mozilla/Add-ons/WebExtensions/API/cookies">cookies</a>
      </td>
      <td style="text-align:left">cookies (*)</td>
      <td style="text-align:left">
        <p>Enables an extension to get and set cookies, and be notified when they
          change.</p>
        <p>
          <br />(*) In addition to the <code>cookies</code> permission, this API also needs
          a <a href="https://developer.mozilla.org/en-US/Add-ons/WebExtensions/manifest.json/permissions#Host_permissions">host permissions</a> for
          the sites whose cookies are to be accessed. See <a href="https://developer.mozilla.org/en-US/Add-ons/WebExtensions/API/cookies#Permissions">cookie Permissions</a>.</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"><a href="https://developer.mozilla.org/en-US/docs/Mozilla/Add-ons/WebExtensions/API/dns">dns</a>
      </td>
      <td style="text-align:left">dns</td>
      <td style="text-align:left">Enables an extension to resolve domain names.</td>
    </tr>
    <tr>
      <td style="text-align:left"><a href="https://developer.mozilla.org/en-US/docs/Mozilla/Add-ons/WebExtensions/API/downloads">downloads</a>
      </td>
      <td style="text-align:left">downloads</td>
      <td style="text-align:left">Enables extensions to interact with the browser&apos;s download manager.
        You can use this API to <a href="https://thunderbird.topicbox.com/groups/addons/Td4979394bb2782c5-Me7445c7edfb7410f6d2973a2/save-as-the-email-send">save files to disk</a>.</td>
    </tr>
    <tr>
      <td style="text-align:left"><a href="https://developer.mozilla.org/en-US/docs/Mozilla/Add-ons/WebExtensions/API/extension">extensions</a>
      </td>
      <td style="text-align:left"></td>
      <td style="text-align:left">Utilities related to an extension. Gets URLs to resources packages with
        an extension. Gets the <a href="https://developer.mozilla.org/en-US/docs/Web/API/Window"><code>Window</code></a> object
        for some of the extension&apos;s pages. Get the values for various settings.</td>
    </tr>
    <tr>
      <td style="text-align:left"><a href="https://developer.mozilla.org/en-US/docs/Mozilla/Add-ons/WebExtensions/API/i18n">i18n</a>
      </td>
      <td style="text-align:left"></td>
      <td style="text-align:left">Functions to internationalize an extension. It can be used to get localized
        strings from locale files packaged with an extension and to find out Thunderbird&apos;s
        current language.</td>
    </tr>
    <tr>
      <td style="text-align:left"><a href="https://developer.mozilla.org/en-US/docs/Mozilla/Add-ons/WebExtensions/API/identity">identity</a>
      </td>
      <td style="text-align:left">identity</td>
      <td style="text-align:left">Use the identity API to get an <a href="https://oauth.net/2/">OAuth2</a> authorization
        code or access token, which an extension can then use to access user data
        from a service that supports OAuth2 access (such as Google or Facebook).</td>
    </tr>
    <tr>
      <td style="text-align:left"><a href="https://developer.mozilla.org/en-US/docs/Mozilla/Add-ons/WebExtensions/API/idle">idle</a>
      </td>
      <td style="text-align:left">idle</td>
      <td style="text-align:left">Find out when the user&apos;s system is idle, locked, or active.</td>
    </tr>
    <tr>
      <td style="text-align:left"><a href="https://developer.mozilla.org/en-US/docs/Mozilla/Add-ons/WebExtensions/API/privacy">privacy</a>
      </td>
      <td style="text-align:left">privacy</td>
      <td style="text-align:left">Access and modify various privacy-related settings.</td>
    </tr>
    <tr>
      <td style="text-align:left"><a href="https://developer.mozilla.org/en-US/docs/Mozilla/Add-ons/WebExtensions/API/management">management</a>
      </td>
      <td style="text-align:left">management (*)</td>
      <td style="text-align:left">Gets information about installed add-ons.
        <br />
        <br />(*) Permission is only needed to access information of other add-on.</td>
    </tr>
    <tr>
      <td style="text-align:left"><a href="https://developer.mozilla.org/en-US/docs/Mozilla/Add-ons/WebExtensions/API/notifications">notifications</a>
      </td>
      <td style="text-align:left">notifications</td>
      <td style="text-align:left">Display notifications to the user, using the underlying operating system&apos;s
        notification mechanism.</td>
    </tr>
    <tr>
      <td style="text-align:left"><a href="https://developer.mozilla.org/en-US/docs/Mozilla/Add-ons/WebExtensions/API/permissions">permissions</a>
      </td>
      <td style="text-align:left"></td>
      <td style="text-align:left">Enables extensions to request extra permissions at runtime, after they
        have been installed.</td>
    </tr>
    <tr>
      <td style="text-align:left"><a href="https://developer.mozilla.org/en-US/docs/Mozilla/Add-ons/WebExtensions/API/pkcs11">pkcs11</a>
      </td>
      <td style="text-align:left">pkcs11</td>
      <td style="text-align:left">Enables an extension to enumerate <a href="https://en.wikipedia.org/wiki/PKCS_11">PKCS #11</a> security
        modules and to make them accessible as sources of keys and certificates.</td>
    </tr>
    <tr>
      <td style="text-align:left"><a href="https://developer.mozilla.org/en-US/docs/Mozilla/Add-ons/WebExtensions/API/proxy">proxy</a>
      </td>
      <td style="text-align:left">proxy (*)</td>
      <td style="text-align:left">
        <p>Enables an extension to proxy web requests. Use the <a href="https://developer.mozilla.org/en-US/docs/Mozilla/Add-ons/WebExtensions/API/proxy/onRequest"><code>proxy.onRequest</code></a> event
          listener to intercept web requests, and return an object that describes
          whether and how to proxy them.</p>
        <p>
          <br />(*) In addition to the <code>proxy</code> permission, this API also needs
          a <a href="https://developer.mozilla.org/en-US/Add-ons/WebExtensions/manifest.json/permissions#Host_permissions">host permissions</a> for
          the URLs of intercepted requests.</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"><a href="https://developer.mozilla.org/en-US/docs/Mozilla/Add-ons/WebExtensions/API/runtime">runtime</a>
      </td>
      <td style="text-align:left"></td>
      <td style="text-align:left">
        <p>This module provides information about the extension and the environment
          it&apos;s running in. It also provides messaging APIs to:</p>
        <ul>
          <li>communicate between different parts of the extension</li>
          <li>communicate with other extensions</li>
          <li>communicate with native applications</li>
        </ul>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"><a href="https://developer.mozilla.org/en-US/docs/Mozilla/Add-ons/WebExtensions/API/storage">storage</a>
      </td>
      <td style="text-align:left">storage, <a href="https://developer.mozilla.org/en-US/docs/Mozilla/Add-ons/WebExtensions/manifest.json/permissions#Unlimited_storage">unlimitedStorage</a>
      </td>
      <td style="text-align:left">Enables extensions to store and retrieve data, and listen for changes
        to stored items.</td>
    </tr>
    <tr>
      <td style="text-align:left"><a href="https://developer.mozilla.org/en-US/docs/Mozilla/Add-ons/WebExtensions/API/theme">theme</a>
      </td>
      <td style="text-align:left">theme</td>
      <td style="text-align:left">Enables extensions to update the theme, thus creating a dynamic theme.</td>
    </tr>
    <tr>
      <td style="text-align:left"><a href="https://developer.mozilla.org/en-US/docs/Mozilla/Add-ons/WebExtensions/API/userScripts">userScripts</a>
      </td>
      <td style="text-align:left"></td>
      <td style="text-align:left">
        <p>Use this API to register user scripts, third-party scripts designed to
          manipulate webpages or provide new features. Registering a user script
          instructs the browser to attach the script to pages that match the URL
          patterns specified during registration.</p>
        <p>This API offers similar capabilities to <a href="https://developer.mozilla.org/en-US/docs/Mozilla/Add-ons/WebExtensions/API/contentScripts"><code>contentScripts</code></a> but
          with features suited to handling third-party scripts.</p>
        <p>
          <br />User scripts can be used in web pages loaded into tabs.</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"><a href="https://developer.mozilla.org/en-US/docs/Mozilla/Add-ons/WebExtensions/API/webNavigation">webNavigation</a>
      </td>
      <td style="text-align:left">webNavigation</td>
      <td style="text-align:left">Add event listeners for the various stages of a navigation. A navigation
        consists of a frame in the browser transitioning from one URL to another,
        usually (but not always) in response to a user action like clicking a link.</td>
    </tr>
    <tr>
      <td style="text-align:left"><a href="https://developer.mozilla.org/en-US/docs/Mozilla/Add-ons/WebExtensions/API/webRequest">webRequest</a>
      </td>
      <td style="text-align:left">webRequest (*), webRequestBlocking (*)</td>
      <td style="text-align:left">Add event listeners for the various stages of making an HTTP request,
        which includes websocket requests on <code>ws://</code> and <code>wss://</code>.
        The event listener receives detailed information about the request and
        can modify or cancel the request.
        <br />
        <br />(*) In addition to the <code>webRequest</code> permission, this API also
        needs the <a href="https://developer.mozilla.org/en-US/Add-ons/WebExtensions/manifest.json/permissions#Host_permissions">host permission</a> for
        the requested host. To use the blocking feature, the extension must also
        have the <code>webRequestBlocking</code> API permission.</td>
    </tr>
  </tbody>
</table>

 You can find more information about these APIs in the [MDN WebExtension API documentation](https://developer.mozilla.org/en-US/docs/Mozilla/Add-ons/WebExtensions/API).

