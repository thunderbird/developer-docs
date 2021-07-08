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

## Commit messages

The commit message should be of the form:

```text
Bug xxxx - Short description of your change. r?reviewer

Optionally, a longer description of the change.
```

## Picking reviewers

All changes need to be reviewed before acceptance into the codebase. It can be pretty tricky to figure out who to ask for a review.

This list of [relevant people](https://wiki.mozilla.org/Thunderbird/Core_Team) might help. Failing that you can always [ask around](https://developer.thunderbird.net/add-ons/community).

Scanning through the recent commits in mercurial should also give you an idea of who is active in various areas of the code.

## Submitting a Patch

The preferred way to submit a patch is via [Phabricator](https://moz-conduit.readthedocs.io/en/latest/phabricator-user.html).

There is a command line tool, `moz-phab`, which makes it easy to submit local changesets as patches.

See the [moz-phab setup and installation](https://moz-conduit.readthedocs.io/en/latest/phabricator-user.html#setting-up-mozphab) docs.

With `moz-phab` you can submit local mercurial changeset\(s\) like this:

```text
$ moz-phab submit [start_changeset] [end_changeset]
```

The start/end changesets are optional, and if omitted `moz-phab` will guess which ones you're likely to mean.

It'll ask for confirmation before uploading, so don't worry too much about accidental submissions.

`moz-phab` will pick the bug number out of the commit message \(`Bug xxxx`\), and link back to the bugzilla bug. If there is a reviewer \(`r?...`\), it will automatically assign them and send them a notification. You can leave the reviewer out, but then one will have to be manually assigned via the phabricator web page.

You can find more details in the `moz-phab` [README](https://github.com/mozilla-conduit/review/blob/master/README.md#submitting-commits-to-phabricator).

### Submitting patches via Bugzilla

The traditionally way to submit patches was to upload a file attachment to the bug in [Bugzilla](https://bugzilla.mozilla.org), set the _review_ flag to `?`, and pick a reviewer.

This still works, but Phabricator is now the preferred method.

## Updating patches

It's very common for patches to require some updates before being accepted. Locally, you can use `hg commit --amend` to update a changeset.

Phabricator tracks uploaded patches by adding a line to the commit message:

```text
Differential Revision: <url>
```

When you submit the patch again with `moz-phab`, it will see that line and realise that you're updating an existing revision rather than creating a brand new one.

{% hint style="warning" %}
If you're juggling and merging local changesets with `hg histedit`, make sure you preserve the `Differential Revision:` line in the commit message for any patches you're planning to resubmit!
{% endhint %}

