# Updating Legacy Extensions for Thunderbird 78

## Removing legacy support for MailExtensions 

It's not yet clear exactly when it will be removed from the code base, but it should be whitin the Thunderbird 72 time frame - so by end of 2019. The next major version of Thunderbird, version 78, will be out around June 2020. Up until then, code wise many things are going to change. For instance, what is left of XUL will be gradually going away, and documents will shifted to being XHTML with a less and less XUL flavor.

As an author of a legacy add-ons \(including MailExtensions with legacy support\), what should you do? There are two routes: 

1. **Convert your add-on to a MailExtension. If the API you need doesn't exist yet,** [**tell us about it**](https://bugzilla.mozilla.org/enter_bug.cgi?product=Thunderbird&component=General)**.** 
2. **Convert your add-on to a** [**Web Extension Experiment**](https://thunderbird-webextensions.readthedocs.io/en/68/how-to/experiments.html)**. Most add-ons should be able to be converted to an experiment with a reasonable effort.** 

The recommended path forward is to convert it to a MailExtension though. That will make sure the add-on works without significant changes over many years. If you go with option 2, you'll have to maintain a lot of more code yourself and breakages can and will be bad unless you keep up really close. 

MailExtension Experiments should be seen as such, experiments with the goal of getting the API they need into Thunderbird core. Please work with us on getting the needed pieces in as a supported API. Initially we'll be allowing experiments to be exposed to the general public, but over time \(years\) Thunderbird will gravitate towards not having the experiments available to the general public, the same way it works for Firefox.

