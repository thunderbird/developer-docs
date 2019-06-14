# Updating Legacy Extensions for Thunderbird 68

## The Future

It’s difficult to say for sure what the future will hold for Thunderbird extensions. A _lot_ of work has been done to ensure that [legacy extension](../about-add-ons.md#legacy-extensions) \(overlay extension and bootstrap extensions\) will work in Thunderbird 68. Beyond that, we just don’t know.

Overlay extensions are problematic because so much of what they depended on no longer exists. Bootstrapped extensions are less of a problem but are still considered at-risk. 

## Required Changes

There are two types of changes which are required to make your legacy extensions compatible with Thunderbird 68:

* The legacy extension must be converted to a MailExtension by replacing the old `install.rdf` by a `manifest.json`.

{% page-ref page="overlays.md" %}

{% page-ref page="bootstrapped.md" %}

* All extensions need to be updated to reflect changes in Thunderbird core, like renamed/replaced API calls, removed support for some XUL elements \(need to use HTML elements now\) and much more. 

{% page-ref page="changes.md" %}

{% hint style="danger" %}
Even though it is possible to have both `install.rdf` and `manifest.json` files in your extension, so you _could_ release a version compatible with Thunderbird 60 and 68, it is _not_ suggested for the following reasons:

* The amount of changes is huge and some changes are incompatible with Thunderbird 60 so it will require extra steps to ensure the modified version still runs with Thunderbird 60. 
* You may actually break your add-on for Thunderbird 60 users by releasing a backward compatible version for Thunderbird 68 \("Do not fix something, that is not broken"\).
* We think the time and resources needed to code and test backward compatible add-ons is not justified by the small amount of users running older versions of Thunderbird.

If you do want to stay backward compatible, you will find useful information [here](https://github.com/cleidigh/ThunderStorm).
{% endhint %}

**Remember**: Thunderbird is based on the Firefox code, and they have been changing things all over the place.

## Uploading your updated add-on

After updating your add-on for Thunderbird 68, it will be treated as a WebExtension. While uploading it to addons.thunderbird.net \(ATN\), you will see a warning, that you cannot upload a new legacy version of your add-on afterwards. However:

{% hint style="success" %}
It **is** possible to maintain a legacy version **and** a WebExtension version of your add-on in parallel on ATN! You just need to use a higher major version number for the WebExtension version of your add-on and keep the old major version number when releasing a new legacy version. Basically releasing them in two different branches.
{% endhint %}

Let's assume the last published legacy version of your add-on for Thunderbird 60 is `2.6`. Upload the new WebExtension version for Thunderbird 68 as `3.0` and increase the version number with each new release of the WebExtension as usual. Every time you update the legacy version for Thunderbird 60, pick a version number from the `2.x` branch. 

## Testing Thunderbird 68

At the moment, there is no official beta version for Thunderbird 68. However, there are beta candidates:

[http://ftp.mozilla.org/pub/thunderbird/candidates/68.0b1-candidates/](http://ftp.mozilla.org/pub/thunderbird/candidates/68.0b1-candidates/)

{% hint style="warning" %}
Even though we think that the beta candidates will update themselves automatically to the real beta, once released, we ask you to check on regular basis, if the beta has been released and your system updated accordingly.
{% endhint %}

