# Update for Thunderbird 78

## Removing support for legacy extensions \(overlay and bootstrapped extensions\)

Support for legacy extensions was removed from the Thunderbird Beta version 74, released in February 2020.

The next major release version of Thunderbird, version 78, will be out around June 2020. Until then, code wise many things are going to change. For instance, what is left of XUL will be gradually going away. Thunderbird's XUL documents have already been converted to XHTML, and will have less and less of a XUL flavor until the conversion from XUL to HTML is complete.

Authors and maintainers of legacy add-ons \(including WebExtensions with legacy support\), need to [convert their add-ons to a MailExtension](convert-legacy.md). If the existing APIs are not sufficient to port the add-on, they have two options:

1. [**tell us about it**](https://bugzilla.mozilla.org/enter_bug.cgi?product=Thunderbird&component=General)**.**
2. **write experimental APIs themselves \(a.k.a.**[ **WebExtension Experiments**](https://thunderbird-webextensions.readthedocs.io/en/68/how-to/experiments.html)**\).**

WebExtension Experiments should be seen as such, experiments with the goal of getting the API they need into Thunderbird core. Please work with us on getting the needed pieces in as a supported API. Initially we'll be allowing experiments to be exposed to the general public, but over time \(years\) Thunderbird will gravitate towards not having the experiments available to the general public, the same way it works for Firefox.

