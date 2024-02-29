---
description: Tutorial on how to fix a bug from beginning to end.
---

# Fixing a Bug

All the issues, bugs, work in progress patches, or updates related to Thunderbird, are listed on [Bugzilla](https://bugzilla.mozilla.org), and are properly organized per **Product**, **Component**, and **Status**.

## Create a Bugzilla Account

Creating an account is necessary in order to submit patches, leave comments, and interact with any other aspect of Bugzilla. If you're currently using a username in one of our Matrix chat rooms (e.g. [#maildev](https://chat.mozilla.org/#/room/#maildev:mozilla.org)), we recommend saving your profile name with the current format `Firstname Lastname (:username)` in order to be easily searchable and allow the Thunderbird team to offer better support.

## Find a Bug

Use the [Advanced Search](https://bugzilla.mozilla.org/query.cgi?format=advanced) section to find bugs you want to take care of, and be sure that the bug doesn't currently have any user listed as _Assignee_ and the _Status_ is set to `NEW`.

## Search for Code References

Making sense of the **Thunderbird** source code, and knowing where to look, will take some time. The code base is pretty big and if you never worked with `XBL` or `Custom Elements` it can be overwhelming at first. We recommend using our code search engine, [SearchFox](https://searchfox.org/comm-central/source/), to inspect the source code and find snippets and references to help you out while investigating a bug.

## Debugging Core Code

JavaScript code can be debugged using the built-in [developer tools toolbox](https://extensionworkshop.com/documentation/develop/debugging/). Debugging core C++ code requires [external tools](https://firefox-source-docs.mozilla.org/contributing/index.html).

## Creating Patches

### Configuring Mercurial

To ensure your work is correctly attributed to you, and to make the reviewer's task easier, these options should be set in your Mercurial configuration file (`Mecurial.ini` on Windows, `$HOME/.hgrc` elsewhere).

```
[ui]
username = Your Name <your@email.address>

[diff]
git = 1
showfunc = 1
unified = 8
```

### Mercurial Workflows

Mercurial is pretty flexible in terms of allowing writing your own code and keeping it separate from the main code base. See [the page on using Mercurial bookmarks](using-mercurial-bookmarks.md) for more information.

## Commit messages

Using standard forms for commit messages not only looks better when looking at the revision logs, it also helps various automation tools parse the messages.

Commit messages should be of the form:

```
Bug xxxx - Short description of your change. r=reviewer

Optionally, a longer description of the change.
This can span multiple lines.
```


For follow-up commits that fix a problem with a lint test or other failure, the suggested form is:

```text
Bug XXXX - Follow-up: Fixed lint failure. r?reviewer
```

When fixing a bug caused by a change made to mozilla-central, often referred to as "porting" a fix to Thunderbird, mention the upstream bug in the commit message like below. Doing so helps identify bugs that need uplifting to beta or release when the ported mozilla-central bug is uplifted.

```text
Bug NNNN - Port bug ZZZZ: Useful short description. r?reviewer
```

Prefixing the first line of the commit message with "`WIP:`" marks the patch as a work-in-progress. `moz-phab` \(see below\) will pick that up and mark it as "Changes Planned".


## Picking reviewers

All changes need to be reviewed before acceptance into the codebase. It can be pretty tricky to figure out who to ask for a review.

Thunderbird code is divided into modules, each with an owner and peers. Generally, these are the best people to review your changes. Here's [the list of module owners and peers for Thunderbird](https://wiki.mozilla.org/Modules/Thunderbird). [Calendar](https://wiki.mozilla.org/Modules/Calendar) and [MailNews Core](https://wiki.mozilla.org/Modules/MailNews\_Core) modules have separate lists.

Scanning through the recent commits in mercurial should also give you an idea of who is active in various areas of the code. Failing that you can always [ask around](https://developer.thunderbird.net/add-ons/community).

## Submitting a Patch

The way to submit a patch is via [Phabricator](https://moz-conduit.readthedocs.io/en/latest/phabricator-user.html).

There is a command line tool, `moz-phab`, which makes it easy to submit local changesets as patches.

See the [moz-phab setup and installation](https://moz-conduit.readthedocs.io/en/latest/phabricator-user.html#setting-up-mozphab) docs.

With `moz-phab` you can submit local mercurial changeset(s) like this:

```
$ moz-phab submit [start_changeset] [end_changeset]
```

The start/end changesets are optional. If omitted, `moz-phab` will guess which one(s) you mean.

It'll ask for confirmation before uploading, so don't worry too much about accidental submissions.

`moz-phab` will pick the bug number out of the commit message (`Bug xxxx`), and link back to the bugzilla bug. If there is a reviewer (`r=...`), it will automatically assign them and send them a notification. You can leave the reviewer out, but then one will have to be manually assigned via the phabricator web page. If the commit message starts with "`WIP:`", the patch will be marked "Changes Planned".

You can find more details in the `moz-phab` [README](https://github.com/mozilla-conduit/review/blob/master/README.md#submitting-commits-to-phabricator).

## Updating patches

It's very common for patches to require some updates before being accepted. Locally, you can use `hg commit --amend` to update a changeset.

Phabricator tracks uploaded patches by adding a line to the commit message:

```
Differential Revision: <url>
```

When you submit the patch again with `moz-phab`, it will see that line and realise that you're updating an existing revision rather than creating a brand new one.

{% hint style="warning" %}
If you're juggling and merging local changesets with `hg histedit`, make sure you preserve the `Differential Revision:` line in the commit message for any patches you're planning to resubmit!
{% endhint %}
