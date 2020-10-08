---
description: Tips and tricks for successful Thunderbird add-on development.
---

# Tips and Tricks

## Debugging

Some general tips to speed up your development workflow:

* To debug code running in the browser context \(e.g.: your Experiment APIs\) you must use the global browser console \(Ctrl+Shift+J\) or developer toolbox \(Ctrl+Shift+I\).  
* To debug code running in a content page of your extension \(e.g.: your background script\), you need to select "debug add-ons" from the gear icon in the add-on tab and then inspect your add-on.   **Hint:** Alternatively you can enable content messages in the global browser console or in the developer toolbox as well. This will also show console output from popus \(e.g. from browser\_action\):

![](../.gitbook/assets/ind2ex.png)

* The add-on debugging tools accessible through the add-on page's gear icon permit to directly install add-ons without packaging them. Using that option permits to reload the add-on without restarting Thunderbird.

There is also a [debug guide from the extension workshop](https://extensionworkshop.com/documentation/develop/debugging/) with more details and the most recent information on debugging. It is written for Mozilla Firefox but applies for Thunderbird as well.

## Parallel hosting of legacy add-ons and MailExtensions on addons.thunderbird.net \(ATN\)

After updating your add-on for Thunderbird 68 and beyond, it will be treated as a WebExtension. While uploading it to addons.thunderbird.net \(ATN\), you will see a warning, that you cannot upload a new legacy version of your add-on afterwards. However:

{% hint style="success" %}
It **is** possible to maintain a legacy version **and** a WebExtension version of your add-on in parallel on ATN! You just need to use a higher major version number for the WebExtension version of your add-on and keep the old major version number when releasing a new legacy version. Basically releasing them in two different branches.
{% endhint %}

Let's assume the last published legacy version of your add-on for Thunderbird 60 is `2.6`. Upload the new WebExtension version for Thunderbird 68 as `3.0` and increase the version number with each new release of the WebExtension as usual. Every time you update the legacy version for Thunderbird 60, pick a version number from the `2.x` branch.

## Forcing modified XUL files and JavaScript to reload

One of the stumbling blocks that add-on developers frequently encounter is Thunderbird's caching mechanisms, which cache XUL and JavaScript files for performance reasons so that they don't have to be re-read from their source and parsed or compiled repeatedly. If you're developing an add-on and iterating through changes to XUL and JavaScript files, you may find that Thunderbird insists on continuing to use old versions even after you've changed them and restarted.

### `-purgecaches` command-line option

DMO [claims](https://developer.mozilla.org/en-US/docs/Mozilla/Command_Line_Options#-purgecaches) that specifying the `-purgecaches` command line option when launching Thunderbird will force it to purge the JavaScript cache.

### Invalidate caches programmatically

Executing this JavaScript code inside Thunderbird will cause the XUL cache to be invalidated the next time it restarts:

```javascript
const { Services } = ChromeUtils.import("resource://gre/modules/Services.jsm");​
Services.obs.notifyObservers(null, "startupcache-invalidate", null);
```

One way to make this happen automatically is by installing the [userChromeJS add-on](https://addons.thunderbird.net/thunderbird/addon/userchromejs-2/) and then putting the code above into the file `chrome/userChrome.js` within your Thunderbird profile directory.

### `nglayout.debug.disable_xul_cache` preference \(TB68\)

Go into the advanced preferences editor, create a boolean preference called `nglayout.debug.disable_xul_cache` if it doesn't already exist, set it to true, and restart Thunderbird.

