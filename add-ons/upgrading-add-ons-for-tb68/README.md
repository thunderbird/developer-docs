# Updating Legacy Extensions for Thunderbird 68

## The Future

It’s difficult to say for sure what the future will hold for Thunderbird extensions. A _lot_ of work has been done to ensure that [legacy extension](../about-add-ons.md#legacy-extensions) \(overlay extension and bootstrap extensions\) will work in Thunderbird 68. Beyond that, we just don’t know.

Overlay extensions are problematic because so much of what they depended on no longer exists. Bootstrapped extensions are less of a problem but are still considered at-risk. 

## Required Changes

There are two types of changes which might be required to make your legacy extensions compatible with Thunderbird 68:

* Overlay extensions need to engage the new overlay loader. The overlay loader is a Thunderbird component that takes XUL code as written in an overlay extension and applies it to the UI. In Thunderbird 60, this was a part of the core UI libary, but it was removed. We have built a new overlay loader to replace as much of the removed code as possible.

{% page-ref page="how-to-invoke-the-new-overlay-loader.md" %}

* All extensions need to be updated to reflect changes in Thunderbird core, like renamed/replaced API calls, removed support for some XUL elements \(need to use HTML elements now\) and much more. 

{% page-ref page="list-of-changes-in-thunderbird-core.md" %}

**Remember**: Thunderbird is based on the Firefox code, and they have been changing things all over the place.

{% hint style="danger" %}
Even though you _can_ write code compatible with both Thunderbird 60 and Thunderbird 68, it is _not_ suggested for the following reasons:

* The amount of changes is huge and some changes are incompatible with Thunderbird 60 so it will require extra steps to ensure the modified version still runs with Thunderbird 60. 
* You may actually break your add-on for Thunderbird 60 users by releasing a backward compatible version for Thunderbird 68 \("Do not fix something, that is not broken"\).
* We think the time and resources needed to code and test backward compatible add-ons is not justified by the small amount of users running older versions of Thunderbird.

If you do want to stay backward compatible, you will find useful information [here](https://github.com/cleidigh/ThunderStorm).
{% endhint %}

