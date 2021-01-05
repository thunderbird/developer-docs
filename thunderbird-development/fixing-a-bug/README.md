---
description: Tutorial on how to fix a bug from beginning to end.
---

# Fixing a Bug

All the issues, bugs, work in progress patches, or updates related to Thunderbird, are listed on [Bugzilla](https://bugzilla.mozilla.org), and are properly organized per **Product**, **Component**, and **Status**.

## Create a Bugzilla Account

Creating an account is necessary in order to submit patches, leave comments, and interact with any other aspect of Bugzilla. If you're currently using a username in one of our Matrix chat rooms \(e.g. [\#maildev](https://chat.mozilla.org/#/room/#maildev:mozilla.org)\), we recommend saving your profile name with the current format `Firstname Lastname (:username)` in order to be easily searchable and allow the Thunderbird team to offer better support.

## Find a Bug

Use the [Advanced Search](https://bugzilla.mozilla.org/query.cgi?format=advanced) section to find bugs you want to take care of, and be sure that the bug doesn't currently have any user listed as _Assignee_ and the _Status_ is set to `NEW`.

## Search for Code References

Making sense of the **Thunderbird** source code, and knowing where to look, will take some time. The code base is pretty big and if you never worked with `XBL` or `Custom Elements` it can be overwhelming at first. We recommend using our code search engine, [SearchFox](https://searchfox.org/comm-central/source/), to inspect the source code and find snippets and references to help you out while investigating a bug.

## Debugging Core Code

JavaScript code can be debugged using the built-in [developer tools toolbox](https://extensionworkshop.com/documentation/develop/debugging/). Debugging core C++ code requires [external tools](https://firefox-source-docs.mozilla.org/contributing/index.html).

## Creating Patches

### Configuring Mercurial

To ensure your work is correctly attributed to you, and to make the reviewer's task easier, these options should be set in your Mercurial configuration file \(`Mecurial.ini` on Windows, `$HOME/.hgrc` elsewhere\).

```text
[ui]
username = Your Name <your@email.address>

[diff]
git = 1
showfunc = 1
unified = 8
```

### Mercurial Workflows

Mercurial is pretty flexible in terms of allowing writing your own code and keeping it separate from the main code base. Based on your knowledge level and preference, you can choose between two different methods:

* [Using Queues](using-mercurial-queues.md)
* [Using Bookmarks](using-mercurial-bookmarks.md)

The majority of the Thunderbird developers use queues as they're easy to import and export, and avoid merging issues while pulling updates from upstream. For a first time contributor, bookmarks may be easier to get you started, but do not work well when working with multiple patches.

## Upload a Patch

Once you finished taking care of your favorite bug and have your patch at hand, it is time to upload your patch. Open the patch file in your code editor and make sure it includes all your code changes, and your name and commit message at the top.

If everything looks good, you can access the selected bug in [Bugzilla](https://bugzilla.mozilla.org) and click on the **Attach File** link located above the first comment.

## Ask for a Review

When uploading a patch to Bugzilla, you can request a review from the user who opened the bug or another developer. Simply select the `?` in the dropdown selector in the _review_ option of the **Flags** section. An input field will appear which will allow you to type the name or username of the user you want to review your patch.

