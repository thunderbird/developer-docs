---
description: Descriptions of all WebExtension API supported by Thunderbird.
---

# Supported WebExtension APIs

## Thunderbird WebExtension APIs

Thunderbird provides the following messenger related WebExtension APIs, which are sometimes referred to as MailExtension APIs.

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
      <td style="text-align:left"><a href="https://thunderbird-webextensions.readthedocs.io/en/latest/accounts.html">accounts</a>
      </td>
      <td style="text-align:left">accountsRead</td>
      <td style="text-align:left">Enables an extension to access information of accounts and identities
        configured in Thunderbird&apos;s account manager.</td>
    </tr>
    <tr>
      <td style="text-align:left"><a href="https://thunderbird-webextensions.readthedocs.io/en/latest/addressBooks.html">addressBooks</a>
      </td>
      <td style="text-align:left">addressBooks</td>
      <td style="text-align:left">Enables an extension to access, modify, create and delete Thunderbird
        address books.</td>
    </tr>
    <tr>
      <td style="text-align:left"><a href="https://thunderbird-webextensions.readthedocs.io/en/latest/browserAction.html">browserAction</a>
      </td>
      <td style="text-align:left"></td>
      <td style="text-align:left">
        <p>Enables an extension to interact with a <a href="https://developer.thunderbird.net/add-ons/mailextensions/supported-ui-elements#browser-action">browser action button</a>.
          <br
          />
        </p>
        <p><em>Permission info:</em>
        </p>
        <ul>
          <li>This API does not require a permission, but a <code>browser_action</code> manifest
            key.</li>
        </ul>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"><a href="https://thunderbird-webextensions.readthedocs.io/en/latest/cloudFile.html">cloudFile</a>
      </td>
      <td style="text-align:left"></td>
      <td style="text-align:left">
        <p>Enables an extension to register a cloudFile provider, which can be used
          to upload large attachments to a server, instead of attaching them directly
          to the email.</p>
        <p></p>
        <p><em>Permission info:</em>
        </p>
        <ul>
          <li>This API does not require a permission, but a <code>cloudFile</code> manifest
            key.</li>
        </ul>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"><a href="https://thunderbird-webextensions.readthedocs.io/en/latest/commands.html">commands</a>
      </td>
      <td style="text-align:left"></td>
      <td style="text-align:left">
        <p>The commands API adds keyboard shortcuts that can trigger actions in an
          extension.
          <br />
          <br /><em>Permission info:</em>
        </p>
        <ul>
          <li>This API does not require a permission, but a <code>commands</code> manifest
            key.</li>
        </ul>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"><a href="https://thunderbird-webextensions.readthedocs.io/en/latest/compose.html">compose</a>
      </td>
      <td style="text-align:left">compose</td>
      <td style="text-align:left">Enables an extension to open a new message compose window or react to
        events while the message is being composed.</td>
    </tr>
    <tr>
      <td style="text-align:left"><a href="https://thunderbird-webextensions.readthedocs.io/en/latest/composeAction.html">composeAction</a>
      </td>
      <td style="text-align:left"></td>
      <td style="text-align:left">
        <p>Enables an extension to interact with a <a href="https://developer.thunderbird.net/add-ons/mailextensions/supported-ui-elements#compose-action">compose action button</a>.</p>
        <p>
          <br /><em>Permission info:</em>
        </p>
        <ul>
          <li>This API does not require a permission, but a <code>compose_action</code> manifest
            key.</li>
        </ul>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"><a href="https://thunderbird-webextensions.readthedocs.io/en/latest/composeScripts.html">composeScripts</a>
      </td>
      <td style="text-align:left">compose</td>
      <td style="text-align:left">Functionally it is the same as the <a href="https://developer.mozilla.org/en-US/docs/Mozilla/Add-ons/WebExtensions/API/contentScripts">contentScripts</a> API
        except that it works on the document of email messages during composition.</td>
    </tr>
    <tr>
      <td style="text-align:left"><a href="https://thunderbird-webextensions.readthedocs.io/en/latest/contacts.html">contacts</a>
      </td>
      <td style="text-align:left">addressBooks</td>
      <td style="text-align:left">Enables an extension to access, modify, create and delete contacts in
        Thunderbird address books.</td>
    </tr>
    <tr>
      <td style="text-align:left"><a href="https://thunderbird-webextensions.readthedocs.io/en/latest/folders.html">folders</a>
      </td>
      <td style="text-align:left">accountsFolders</td>
      <td style="text-align:left">Enables an extension to access, modify, create and delete mail account
        folders.</td>
    </tr>
    <tr>
      <td style="text-align:left"><a href="https://thunderbird-webextensions.readthedocs.io/en/latest/mailingLists.html">mailingLists</a>
      </td>
      <td style="text-align:left">addressBooks</td>
      <td style="text-align:left">Enables an extension to access, modify, create and delete mailing lists
        in Thunderbird address books.</td>
    </tr>
    <tr>
      <td style="text-align:left"><a href="https://thunderbird-webextensions.readthedocs.io/en/latest/mailTabs.html">mailTabs</a>
      </td>
      <td style="text-align:left">accountsFolders</td>
      <td style="text-align:left">
        <p>Enables an extension to interact with Thunderbird&apos;s main window.
          <br
          />
          <br /><em>Permission info:</em>
        </p>
        <ul>
          <li>The <code>accountsFolders</code> permission is only needed to set the currently
            displayed folder.</li>
        </ul>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"><a href="https://thunderbird-webextensions.readthedocs.io/en/latest/menus.html">menus</a>
      </td>
      <td style="text-align:left">
        <p>menus,
          <br /><a href="https://thunderbird-webextensions.readthedocs.io/en/latest/menus.html#overridecontext-contextoptions">menus.overrideContext</a>,</p>
        <p>accountsRead, messagesRead,</p>
        <p>activeTab</p>
      </td>
      <td style="text-align:left">
        <p>Enables an extension to add (context-) menu entries to Thunderbird menus.
          <br
          />
          <br /><em>Permission info:</em>
        </p>
        <ul>
          <li>The <code>menus.overrideContext</code> permission is needed to <a href="https://thunderbird-webextensions.readthedocs.io/en/latest/menus.html#overridecontext-contextoptions">override a default menu entry</a>.</li>
          <li>The <code>accountsRead</code> and <code>messagesRead</code> permissions are
            needed to populate the associated fields in the <a href="https://thunderbird-webextensions.readthedocs.io/en/latest/menus.html#onclickdata"><code>OnClickedData</code></a> object.</li>
          <li>The <code>activeTab</code> permission is (currently) needed to get advanced
            information for the <code>onShown</code> event (see <a href="https://bugzilla.mozilla.org/show_bug.cgi?id=1664423">bug 1664423</a>)</li>
        </ul>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"><a href="https://thunderbird-webextensions.readthedocs.io/en/latest/messageDisplay.html">messageDisplay</a>
      </td>
      <td style="text-align:left">messagesRead</td>
      <td style="text-align:left">Enables an extension to react on and interact with the currently displayed
        messages.</td>
    </tr>
    <tr>
      <td style="text-align:left"><a href="https://thunderbird-webextensions.readthedocs.io/en/latest/messageDisplayAction.html">messageDisplayAction</a>
      </td>
      <td style="text-align:left"></td>
      <td style="text-align:left">
        <p>Enables an extension to interact with a <a href="https://developer.thunderbird.net/add-ons/mailextensions/supported-ui-elements#message-display-action">message display action button</a>.
          <br
          />
          <br /><em>Permission info:</em>
        </p>
        <ul>
          <li>This API does not require a permission, but a <code>message_display_action</code> manifest
            key.</li>
        </ul>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"><a href="https://thunderbird-webextensions.readthedocs.io/en/latest/messageDisplayScripts.html">messageDisplayScripts</a>
      </td>
      <td style="text-align:left">messagesModify</td>
      <td style="text-align:left">Functionally it is the same as the <a href="https://developer.mozilla.org/en-US/docs/Mozilla/Add-ons/WebExtensions/API/contentScripts">contentScripts</a> API
        except that it works on the document of email messages being displayed.</td>
    </tr>
    <tr>
      <td style="text-align:left"><a href="https://thunderbird-webextensions.readthedocs.io/en/latest/messages.html">messages</a>
      </td>
      <td style="text-align:left">
        <p>messagesRead,</p>
        <p>messagesMove,
          <br />accountsRead</p>
      </td>
      <td style="text-align:left">
        <p>Enables an extension to list, search, read, copy, move and delete messages.</p>
        <p>
          <br /><em>Permission info:</em>
        </p>
        <ul>
          <li>The <code>messagesRead</code> permission is needed to list, read, mark and
            tag messages.</li>
          <li>The <code>messagesMove</code> permission is needed to copy, move and delete
            messages.</li>
          <li>The <code>accountsRead</code> permission is needed by any function/event
            which involves folder information.</li>
        </ul>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"><a href="https://thunderbird-webextensions.readthedocs.io/en/latest/tabs.html">tabs</a>
      </td>
      <td style="text-align:left">tabs, activeTab, compose, messageModify</td>
      <td style="text-align:left">
        <p>Enables an extension to interact with Thunderbird&apos;s tab system. It
          allows to create, modify, and rearrange tabs and to communicate with scripts
          in tabs.
          <br />
          <br /><em>Permission info:</em>
        </p>
        <ul>
          <li>The <code>tabs</code> or <code>activeTab</code> permission is only needed
            if the <code>url</code>, <code>favIconUrl </code>or <code>title </code>information
            of tabs are accessed. The <code>tab</code> permission will allow access to
            that information in all tabs, the <code>activeTab</code> permission restricts
            that to the active tab.
            <br />Note: The <code>activeTab</code> permission implies the <code>&lt;all_urls&gt;</code> host
            permission for the active (web page -) tab.</li>
          <li>The <code>compose</code> permissions is needed to call <code>tabs.executeScript()</code> and <code>tabs.insertCSS()</code> for
            compose tabs.</li>
          <li>The <code>messageModify</code> permission is needed to call <code>tabs.executeScript()</code> and <code>tabs.insertCSS()</code> for
            a message display tab.</li>
        </ul>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"><a href="https://thunderbird-webextensions.readthedocs.io/en/latest/windows.html">windows</a>
      </td>
      <td style="text-align:left">tabs</td>
      <td style="text-align:left">
        <p>Enables an extension to interact with Thunderbird&apos;s windows which
          can contain webpage tabs and also other window types like composer or address
          books that cannot contain webpage tabs. You can use this API to create,
          modify, and rearrange windows.</p>
        <p>
          <br /><em>Permission info:</em>
        </p>
        <ul>
          <li>The <code>tabs</code> permission is needed to populate the <code>url</code>, <code>favIconUrl </code>and <code>title</code> information
            in the <code>tabs</code> member of the <a href="https://thunderbird-webextensions.readthedocs.io/en/latest/windows.html#window"><code>Window</code></a> object.</li>
        </ul>
      </td>
    </tr>
  </tbody>
</table>

You can find more information in the [Thunderbird WebExtensions API documentation ](https://thunderbird-webextensions.readthedocs.io/).

## Firefox WebExtension APIs

As Thunderbird is based on the same code base as Firefox, we can reuse many of the WebExtension APIs provided by Firefox. The APIs listed in the following table are known to work with Thunderbird. Some methods listed on the API pages may not be supported. Each API page should include a compatibility chart and if that lists support for Firefox, it works in Thunderbird as well. 

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
      <td style="text-align:left">
        <p>clipboardWrite,</p>
        <p>clipboardRead</p>
      </td>
      <td style="text-align:left">
        <p>Enables an extension to copy items to the system clipboard. Currently
          the API only supports copying images, but it&apos;s intended to support
          copying text and HTML in the future.
          <br />
          <br /><em>Permission info:</em>
        </p>
        <ul>
          <li>The <code>clipboardRead</code> permission is not used by this API but is
            listed here for completeness. To read from the clipboard, the <a href="https://developer.mozilla.org/en-US/docs/Web/API/Clipboard_API">Clipboard Web API</a> has
            to be used and needs the <code>clipboardRead</code> permission.</li>
        </ul>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"><a href="https://developer.mozilla.org/en-US/docs/Mozilla/Add-ons/WebExtensions/API/contentScripts">contentScripts</a>
      </td>
      <td style="text-align:left"></td>
      <td style="text-align:left">
        <p>Use this API to register content scripts to instruct the browser to insert
          the given content scripts into pages that match the URL patterns specified
          during registration. In Thunderbird, content scripts can only be used in
          web pages loaded into tabs.
          <br />
          <br /><em>Permission info:</em>
        </p>
        <ul>
          <li>This API does not require a dedicated permission, but an appropriate
            <a
            href="https://developer.mozilla.org/en-US/Add-ons/WebExtensions/manifest.json/permissions#Host_permissions">host permission</a>for any patterns it passes to <code>register()</code>.</li>
        </ul>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"><a href="https://developer.mozilla.org/en-US/docs/Mozilla/Add-ons/WebExtensions/API/cookies">cookies</a>
      </td>
      <td style="text-align:left">cookies</td>
      <td style="text-align:left">
        <p>Enables an extension to get and set cookies, and be notified when they
          change.
          <br />
          <br /><em>Permission info:</em>
        </p>
        <ul>
          <li>In addition to the <code>cookies</code> permission, this API also needs
            a <a href="https://developer.mozilla.org/en-US/Add-ons/WebExtensions/manifest.json/permissions#Host_permissions">host permissions</a> for
            the sites whose cookies are to be accessed. See <a href="https://developer.mozilla.org/en-US/Add-ons/WebExtensions/API/cookies#Permissions">cookie Permissions</a>.</li>
        </ul>
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
      <td style="text-align:left">
        <p>downloads,</p>
        <p>downloads.open</p>
      </td>
      <td style="text-align:left">
        <p>Enables extensions to interact with the browser&apos;s download manager.
          You can use this API to <a href="https://thunderbird.topicbox.com/groups/addons/Td4979394bb2782c5-Me7445c7edfb7410f6d2973a2/save-as-the-email-send">save files to disk</a>.
          <br
          />
          <br /><em>Permission info:</em>
        </p>
        <ul>
          <li>The <a href="https://developer.mozilla.org/en-US/docs/Mozilla/Add-ons/WebExtensions/API/downloads/open">downloads.open</a> permission
            is needed to open the saved file with the systems default application.</li>
        </ul>
      </td>
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
      <td style="text-align:left">management</td>
      <td style="text-align:left">
        <p>Gets information about installed add-ons.
          <br />
          <br /><em>Permission info:</em>
        </p>
        <ul>
          <li>The <code>management</code> permission is only needed to access information
            of other add-on.</li>
        </ul>
      </td>
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
      <td style="text-align:left">proxy</td>
      <td style="text-align:left">
        <p>Enables an extension to proxy web requests. Use the <a href="https://developer.mozilla.org/en-US/docs/Mozilla/Add-ons/WebExtensions/API/proxy/onRequest"><code>proxy.onRequest</code></a> event
          listener to intercept web requests, and return an object that describes
          whether and how to proxy them.
          <br />
          <br /><em>Permission info:</em>
        </p>
        <ul>
          <li>In addition to the <code>proxy</code> permission, this API also needs a
            <a
            href="https://developer.mozilla.org/en-US/Add-ons/WebExtensions/manifest.json/permissions#Host_permissions">host permissions</a>for the URLs of intercepted requests.</li>
        </ul>
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
      <td style="text-align:left">Use this API to register user scripts, third-party scripts designed to
        manipulate webpages or provide new features. Registering a user script
        instructs the browser to attach the script to pages that match the URL
        patterns specified during registration. In Thunderbird, user scripts can
        only be used in web pages loaded into tabs.
        <br />
        <br />This API offers similar capabilities to contentScripts but with features
        suited to handling third-party scripts.</td>
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
      <td style="text-align:left">webRequest, webRequestBlocking</td>
      <td style="text-align:left">
        <p>Add event listeners for the various stages of making an HTTP request,
          which includes websocket requests on <code>ws://</code> and <code>wss://</code>.
          The event listener receives detailed information about the request and
          can modify or cancel the request.
          <br />
          <br /><em>Permission info:</em>
        </p>
        <ul>
          <li>In addition to the <code>webRequest</code> permission, this API also needs
            the <a href="https://developer.mozilla.org/en-US/Add-ons/WebExtensions/manifest.json/permissions#Host_permissions">host permission</a> for
            the requested host.</li>
          <li>To use the blocking feature, the extension must also have the <code>webRequestBlocking</code> API
            permission.</li>
        </ul>
      </td>
    </tr>
  </tbody>
</table>

 You can find more information about these APIs in the [MDN WebExtension API documentation](https://developer.mozilla.org/en-US/docs/Mozilla/Add-ons/WebExtensions/API).

