# Adapt to Changes in Thunderbird 92-102

This document tries to cover all the internal changes that may be needed to make add-ons compatible with Thunderbird Beta. If you find changes which are not yet listed on this page, you can ask for help and advice in one of our [communication channels](https://developer.thunderbird.net/#getting-plugged-into-the-community).

## Renamed DOM Elements

### content-frame

The id of the editor element in the composer has been changed from [`content-frame`](https://searchfox.org/comm-esr91/source/mail/components/compose/content/messengercompose.xhtml#2478) to [`messageEditor`](https://searchfox.org/comm-central/rev/36c713a3d0bf0d0ca2208fdf74f8a133d4ed7edd/mail/components/compose/content/messengercompose.xhtml#2460).

## Changed API

### `ChromeUtils.import()`

Since TB 102, it is no longer possible to load JSMs via extension URLs, for example&#x20;

```
var { myModule } = ChromeUtils.import(extension.rootURI.resolve("myModule.jsm"));
```

It is now mandatory to register an internal URL, for example a `resource://` URL. We have created the [ResourceUrl Experiment API](https://github.com/thundernest/addon-developer-support/tree/master/auxiliary-apis/ResourceUrl), which is doing the heavy lifting. It is used by our [Experiment API example](https://github.com/thundernest/sample-extensions/tree/master/experiment).

### `NotificationBox.appendNotification()`

The parameters have [changed](https://searchfox.org/mozilla-central/rev/f8576fec48d866c5f988baaf1fa8d2f8cce2a82f/toolkit/content/widgets/notificationbox.js#78-149) in TB 94.
