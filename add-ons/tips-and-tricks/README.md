---
description: Tips and tricks for successful Thunderbird add-on development.
---

# Tips and Tricks

## Parallel hosting of legacy add-ons and MailExtensions on addons.thunderbird.net \(ATN\)

After updating your add-on for Thunderbird 68 and beyond, it will be treated as a WebExtension. While uploading it to addons.thunderbird.net \(ATN\), you will see a warning, that you cannot upload a new legacy version of your add-on afterwards. However:

{% hint style="success" %}
It **is** possible to maintain a legacy version **and** a WebExtension version of your add-on in parallel on ATN! You just need to use a higher major version number for the WebExtension version of your add-on and keep the old major version number when releasing a new legacy version. Basically releasing them in two different branches.
{% endhint %}

Let's assume the last published legacy version of your add-on for Thunderbird 60 is `2.6`. Upload the new WebExtension version for Thunderbird 68 as `3.0` and increase the version number with each new release of the WebExtension as usual. Every time you update the legacy version for Thunderbird 60, pick a version number from the `2.x` branch.

## Forcing modified XUL files and JavaScript to reload

One of the stumbling blocks that add-on developers frequently encounter is Thunderbird's caching mechanisms, which cache XUL and JavaScript files for performance reasons so that they don't have to be re-read from their source and parsed or compiled repeatedly. If you're developing an add-on and iterating through changes to XUL and JavaScript files, you may find that Thunderbird insists on continuing to use old versions even after you've changed them and restarted. There are three known workarounds which seem to work sometimes, though it's not clear \(I mean, I'm sure it's clear to _someone_, but it's not clear to the author of this page\) when each of them works. At least one of them should work in all contexts, so when you run into problems, try them all until one works.

### `nglayout.debug.disable_xul_cache` preference

Go into the advanced preferences editor, create a boolean preference called `nglayout.debug.disable_xul_cache` if it doesn't already exist, set it to true, and restart Thunderbird.

### `-purgecaches` command-line option

DMO [claims](https://developer.mozilla.org/en-US/docs/Mozilla/Command_Line_Options#-purgecaches) that specifying the `-purgecaches` command line option when launching Thunderbird will force it to purge the JavaScript cache.

### Tell XUL Runtime service to invalidate caches

Executing this JavaScript code inside Thunderbird will cause the XUL cache to be invalidated the next time it restarts:

```text
Components.classes["@mozilla.org/xre/app-info;1"].
    getService(Components.interfaces.nsIXULRuntime).invalidateCachesOnRestart();
```

One way to make this happen automatically is by installing the [userChromeJS add-on](https://addons.thunderbird.net/thunderbird/addon/userchromejs-2/) and then putting the code above into the file `chrome/userChrome.js` within your Thunderbird profile directory.

