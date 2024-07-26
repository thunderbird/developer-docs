---
description: Tips and tricks for successful Thunderbird add-on development.
---

# Tips and Tricks

## Debugging

Some general tips to speed up your development workflow:

* To debug code running in the browser context (e.g.: your Experiment APIs) you must use the global browser console (Ctrl+Shift+J) or developer toolbox (Ctrl+Shift+I). \

* To debug code running in a content page of your extension (e.g.: your background script), you need to select "debug add-ons" from the gear icon in the add-on tab and then inspect your add-on. \
  \
  **Hint:** Alternatively you can enable content messages in the global browser console or in the developer toolbox as well. This will also show console output from popus (e.g. from browser\_action):

![](<../../.gitbook/assets/ind2ex (1).png>)

* The add-on debugging tools accessible through the add-on page's gear icon permit to directly install add-ons without packaging them. Using that option permits to reload the add-on without restarting Thunderbird.

There is also a [debug guide from the extension workshop](https://extensionworkshop.com/documentation/develop/debugging/) with more details and the most recent information on debugging. It is written for Mozilla Firefox but applies for Thunderbird as well.

## Experiments: Forcing modified JavaScript to reload

One of the stumbling blocks that legacy add-on developers frequently encounter is Thunderbird's caching mechanisms, which cache JavaScript files for performance reasons so that they don't have to be re-read from their source and parsed or compiled repeatedly. If you're developing an Experiment, you may find that Thunderbird insists on continuing to use old versions even after you've changed them and restarted.

### `-purgecaches` command-line option

DMO [claims](https://developer.mozilla.org/en-US/docs/Mozilla/Command\_Line\_Options#-purgecaches) that specifying the `-purgecaches` command line option when launching Thunderbird will force it to purge the JavaScript cache.

### Invalidate caches programmatically

Executing this JavaScript code inside Thunderbird will cause the XUL cache to be invalidated the next time it restarts:

```javascript
const { Services } = ChromeUtils.import("resource://gre/modules/Services.jsm");​
Services.obs.notifyObservers(null, "startupcache-invalidate", null);
```

One way to make this happen automatically is by installing the [userChromeJS add-on](https://addons.thunderbird.net/thunderbird/addon/userchromejs-2/) and then putting the code above into the file `chrome/userChrome.js` within your Thunderbird profile directory.
