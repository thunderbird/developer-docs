---
description: Tutorial on how to fix a bug from beginning to end.
---

# Fixing a Bug

All the issues, bugs, work in progress patches, or updates related to Thunderbird, are listed on [BugZilla](https://bugzilla.mozilla.org), and are properly organized per **Product**, **Component**, and **Status**.

## Create a BugZilla account

Creating an account is necessary in order to submit patches, leave comments, and interact with any other aspect of BugZilla. If you're currently using an `IRC` username in the `#maildev` channel, we recommend saving your profile name with the current format `Firstname Lastname (:username)` in order to be easily searchable and allow the Thunderbird team to offer better support.

## Find a Bug

Use the [Advanced Search](https://bugzilla.mozilla.org/query.cgi?format=advanced) section to find bugs you want to take care of, and be sure that the bug doesn't currently have any user listed as *Assignee* and the *Status* is set to `NEW`.

## Search for code references

Making sense of the **Thunderbird** source code, and knowing where to look, will take some time. The code base is pretty big and if you never worked with `XBL` or `Custom Elements` it can be overwhelming at first. We recommend using our code search engine, [SearchFox](https://searchfox.org/comm-central/source/), to inspect the source code and find snippets and references to help you out while investigating a bug.

## Mercurial Workflow

Mercurial is pretty flexible in terms of allowing writing your own code and keeping it separate from the main code base. The majority of the Thunderbird developers use queues as they're easy to import and export, and avoid merging issues while pulling updates from upstream.

Based on your knowledge level and preference, you can choose between three different methods:

* [Using Queues](using-mercurial-queues.md)
* [Using Bookmarks](using-mercurial-bookmarks.md)
* [Using Branches](using-mercurial-branches.md)

Once you finished taking care of your favorite bug and you're ready to submit your code for a review, it means it's time to export a patch.

## Create a New Patch

{% hint style="info" %}
You can skip this step if you're already using Mercurial Queues
{% endhint %}

Create a new patch with the command `hg qnew -m "Bug ###### - fixing something amazing" patch-name`. Let's quickly analyze this command:

* `qnew` is the command to initial a new patch.
* `-m` is the command that allows you to write a commit message.
* `"Bug ###### - fixing something amazing"` is the format we recommend using for the commit message, specifying the number of the Bug you're working on and a small description stating what you fixed.
* `patch-name` is, obviously, the name of your patch, and that can be anything.

## Export a Patch

Be sure all your current changes are part of the patch you want to export by typing `hg qrefresh` and `hg qdiff` to visualize a recap of all your changes and diffs in your files.

If you're happy with the result, you can export the patch with the command `hg export qtip > ~/Your/Chosen/Directory/patch-name.patch`. Let's quickly analyze this command:

* `export qtip` will grab all your changes and code updates currently at the *tip* of your mercurial queues.
* `> ~/Your/Chosen/Directory/patch-name.patch` will create a file in the directory you specified with the name of your choice.

## Upload a Patch

Open your `patch-name.patch` file in your code editor and be sure it includes all your code changes, and your name and commit message at the top.

If everything looks good, you can access the selected bug in [BugZilla](https://bugzilla.mozilla.org) and click on the **Attach File** link located above the first comment.

## Ask for a Review

When uploading a patch to BugZilla, you can request a review from the user who opened the bug or another developer. Simply select the `?` in the dropdown selector in the *review* option of the **Flags** section.
An input field will appear which will allow you to type the name or username of the user you want to review your patch.