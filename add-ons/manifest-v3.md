# Manifest V3

> Manifest V3 is the next iteration of WebExtensions, and offers the opportunity to introduce improvements that would otherwise not be possible due to concerns with backward compatibility. MV2 had architectural constraints that made some issues difficult to address; with MV3 we are able to make changes to address this.
>
> source: [Mozilla Add-on Community Blog](https://blog.mozilla.org/addons/2022/05/18/manifest-v3-in-firefox-recap-next-steps/)

One of the key concepts of MV3 is to deprecate persistent background pages. The original proposal from Google meant to replace background pages completely by service workers, which however resulted in criticism from the community. Most add-ons would have to be rewritten to use service workers, and some add-ons could not have been converted at all.

Mozilla introduced [Limited Event Pages](https://github.com/w3c/webextensions/issues/134#issue-1049674790) to allow add-on developers to keep using the known concept of background pages. These event pages are terminated when they become idle, and restarted whenever an event for which they are registered occurs.

In addition to these fundamental operational changes, some APIs are changed to remove inconsistencies. All methods and properties marked as deprecated are removed.

