# Updating Legacy Extensions for Thunderbird 68

A _lot_ of work has been done to ensure that [legacy extension](../about-add-ons.md#legacy-extensions) \(overlay extension and bootstrap extensions\) will work in Thunderbird 68. Technically, they have to be converted to MailExtensions, but by activating the legacy mode, the general structure of the legacy extension does not need to be changed.

## Required Changes

There are two types of changes which are required to make your legacy extensions compatible with Thunderbird 68:

* The legacy extension must be converted to a MailExtension by replacing the old `install.rdf` by a `manifest.json`.

{% page-ref page="overlays.md" %}

{% page-ref page="bootstrapped.md" %}

* All legacy extensions need to be updated to reflect changes in Thunderbird core, like renamed/replaced API calls, removed support for some XUL elements \(need to use HTML elements now\) and much more. 

{% page-ref page="changes.md" %}

{% hint style="danger" %}
Even though it is possible to have both `install.rdf` and `manifest.json` files in your extension, so you _could_ release a version compatible with Thunderbird 60 and 68, it is _not_ suggested for the following reasons:

* The amount of changes is huge and some changes are incompatible with Thunderbird 60 so it will require extra steps to ensure the modified version still runs with Thunderbird 60. 
* You may actually break your add-on for Thunderbird 60 users by releasing a backward compatible version for Thunderbird 68 \("Do not fix something, that is not broken"\).
* We think the time and resources needed to code and test backward compatible add-ons is not justified by the small amount of users running older versions of Thunderbird.

If you do want to stay backward compatible, you will find useful information [here](https://github.com/cleidigh/ThunderStorm).
{% endhint %}

{% hint style="success" %}
It **is** possible to maintain a legacy version **and** a WebExtension version of your add-on in parallel on ATN! You just need to use a higher major version number for the WebExtension version of your add-on and keep the old major version number when releasing a new legacy version. Basically releasing them in two different branches.
{% endhint %}

