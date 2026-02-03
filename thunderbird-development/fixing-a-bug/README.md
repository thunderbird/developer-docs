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

Making sense of the **Thunderbird** source code, and knowing where to look, will take some time. The code base is pretty big and if you never worked with `XBL` or `Custom Elements` it can be overwhelming at first. We recommend using our code search engine, [SearchFox](https://searchfox.org/comm-central/source/), or the [GitHub Source](https://github.com/thunderbird/thunderbird-desktop) to inspect the source code and find snippets and references to help you out while investigating a bug.

## Debugging Core Code

JavaScript code can be debugged using the built-in [developer tools toolbox](https://extensionworkshop.com/documentation/develop/debugging/). Debugging core C++ code requires [external tools](https://firefox-source-docs.mozilla.org/contributing/index.html).

## Creating Patches

### Checkout the Source Code

If you don't need to build Thunderbird, you can just get the Thunderbird source:

```
git clone https://github.com/thunderbird/thunderbird-desktop
```

If you plan to build with your changes, you'll first need to get the latest Firefox source code, and check it out into a local directory `source` (or however you want to call it). Then, get the latest Thunderbird source code. It needs to be placed **inside** the Mozilla source code, in a directory named `comm/`:

```
git clone https://github.com/mozilla-firefox/firefox source/
cd source/
git clone https://github.com/thunderbird/thunderbird-desktop comm/
```

### Configuring Git

To ensure your work is correctly attributed to you, and to make the reviewer's task easier, these options should be set in your Git configuration.

```
git config --global user.name "Your Full Name"
git config --global user.email "you@example.com"
```

### Committing a Patch

Make the changes you need in the codebase.

{% hint style="note" %}
If you are unsure of what changes you need to make, or need help from the mentor of the bug, please don’t hesitate to use the needinfo feature (“Request information from”) on Bugzilla to get the attention of your mentor.
{% endhint %}

After making your changes, visualize your changes to ensure you’re including all the necessary work:

```
# For files changed/added/removed
$ git status

# For detailed line changes
$ git diff
```

Then commit your changes:

```
$ git commit
```

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

You can also run git log <modified-file>` on the relevant files, and look who usually is reviewing the actual changes (ie. not reformat, renaming of variables, etc).

## Reviewing before submitting

To review your commit, run:

```
$ git log
```

To review your patch, run:
```
$ git show
```

## Working with a stack of patches

[More information on how to work with stack of patches](https://firefox-source-docs.mozilla.org/contributing/stack_quickref.html#working-with-stack-of-patches-quick-reference)

## Submitting a Patch

The way to submit a patch is via [Phabricator](https://moz-conduit.readthedocs.io/en/latest/phabricator-user.html).

There is a command line tool, `moz-phab`, which makes it easy to submit local changesets as patches.

See the [moz-phab setup and installation](https://moz-conduit.readthedocs.io/en/latest/phabricator-user.html#setting-up-mozphab) docs.

Once you want to submit your patches (make sure you use the right commit message), run:

```
$ moz-phab
```

It will publish all the currently applied patches to Phabricator and inform the reviewer.

If you wrote several patches on top of each other, run:

```
$ moz-phab submit <first_revision>::<last_revision>
```

It'll ask for confirmation before uploading, so don't worry too much about accidental submissions.

`moz-phab` will pick the bug number out of the commit message (`Bug xxxx`), and link back to the bugzilla bug. If there is a reviewer (`r=...`), it will automatically assign them and send them a notification. You can leave the reviewer out, but then one will have to be manually assigned via the phabricator web page. If the commit message starts with "`WIP:`", the patch will be marked "Changes Planned".

You can find more details in the `moz-phab` [README](https://github.com/mozilla-conduit/review/blob/master/README.md#submitting-commits-to-phabricator).

## Updating a submitted patch

It is rare that a reviewer will accept the first version of patch. Moreover, as the code review bot might suggest some improvements, changes to your patch may be required.

If your patch is not loaded in your working directory, you first need to re-apply it:

```
$ moz-phab patch D<revision_id>

# Or you can use the URL of the revision on Phabricator
$ moz-phab patch https://phabricator.services.mozilla.com/D<revision_id>
```

Make your changes in the working folder and run:

```
$ git commit --amend
```

After amending the patch, you will need to submit it using moz-phab again.

{% hint style="warning" %}
Don’t use `git commit --amend -m`.

Phabricator tracks revision by editing the commit message when a revision is created to add a special `Differential Revision: <url>` line.

When `--amend -m` is used, that line will be lost, leading to the creation of a new revision when re-submitted, which isn’t the desired outcome.
{% endhint %}

If you wrote many changes, you can squash or edit commits with the command:

```
$ git rebase -i
```

The submission step is the same as for the initial patch.

[More information on how to work with stack of patches](https://firefox-source-docs.mozilla.org/contributing/stack_quickref.html#working-with-stack-of-patches-quick-reference)

## Update the working directory

If you’re finished with a patch and would like to return to the tip to make a new patch:

```
$ git pull --rebase
```
