# Adapt to Changes in Thunderbird 103-115

This document tries to cover all the internal changes that may be needed to make add-ons compatible with Thunderbird Beta. If you find changes which are not yet listed on this page, you can ask for help and advice in one of our [communication channels](https://developer.thunderbird.net/#getting-plugged-into-the-community).

## Changed API

### calITimezoneService.timezoneIds

In Thunderbird 106, this has been changed from an enumerator to a simple array.

### callTimezoneService.aliasIds

Removed in Thunderbird 106.
