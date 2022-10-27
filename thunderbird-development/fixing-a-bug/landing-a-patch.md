---
description: Tutorial on how to push approved and reviewed patches to the production server
---

# Landing a Patch

## Helpful Mercurial extensions

If you ran `mach bootstrap`, you were given the option to configure Mercurial as well. If you didn't do so at the time, you can run `mach vcs-setup` at any time. This will clone the [version-control-tools](https://hg.mozilla.org/hgcustom/version-control-tools) repository to your `.mozbuild` directory and configure your `.hgrc` file.

`mach vcs-setup` will offer to enable the [`firefoxtree`](https://mozilla-version-control-tools.readthedocs.io/en/latest/hgmozilla/firefoxtree.html) extension. Despite its name, it is helpful for Thunderbird work as well.

Firefoxtree's main feature is automatic configuration of remote repository paths. The documentation doesn't mention them, but the Thunderbird repositories are mapped as well:

| Repository                                  | FXTree aliases     |
|---------------------------------------------|--------------------|
| https://hg.mozilla.org/comm-central         | comm, c-c, cc      |
| https://hg.mozilla.org/releases/comm-beta   | comm-beta, c-b, cb |
| https://hg.mozilla.org/releases/comm-esr102 | comm-esr102        |
| https://hg.mozilla.org/projects/ash         | ash                |
| https://hg.mozilla.org/projects/jamun       | jamun              |
| https://hg.mozilla.org/try-comm-central     | try-comm           |

Another helpful extension that `mach vcs-setup` does not configure for you is [mozext](https://mozilla-version-control-tools.readthedocs.io/en/latest/hgmods/extensions.html#mozext). It adds several revision set specifiers and templates to Mercurial. Run `hg help mozext` after enabling it to see what it offers. To enable, add to you `~/.hgrc` file's `[extensions]` section (replace `<home_dir>` with your actual home directory):

```ini
mozext = <home_dir>/.mozbuild/version-control-tools/hgext/mozext
```

## Getting access to comm-central <a href="#getting-access-to-the-try-server" id="getting-access-to-the-try-server"></a>

To push a patch to comm-central, you'll need [Level 3 Commit Access](http://www.mozilla.org/hacking/commit-access-policy/). You can learn more about Mozilla's commit access policies and start the process of signing up for an account here: [Becoming a Mozilla Contributor](http://www.mozilla.org/hacking/committer/)

## Adding the live server to your Mercurial configuration

You'll need to add the address to your Mercurial configuration file at `path/to/comm-central/.hg/hgrc`:

```ini
[paths]
default = https://hg.mozilla.org/comm-central
live-cc = ssh://hg.mozilla.org/comm-central
```

In this example we used the label `live-cc` but you can use whatever you like. Using the standard label `default-push` is not recommended – having to type the name of the repository helps prevent mistakes.

You can of course [access the repository via HTTP](https://hg.mozilla.org/try-comm-central/), but not push to it, hence the ssh:// address.

## Before pushing <a href="#pushing-to-try" id="pushing-to-try"></a>

**Is the tree open?** Check [TreeHerder](https://treeherder.mozilla.org/#/jobs?repo=comm-central) – the name of the tree in the top-left corner shows you the status of the tree. Usually it's "open" (a green circle is displayed), which means you can push. Other statuses are "approval required" (yellow padlock) and "closed" (red X) which mean you can't push without permission, and in fact the server will prevent you from doing so.

**Is it a good time to push?** The best time to push is shortly after Mozilla updates the Firefox live server. Since Thunderbird builds on top of Firefox, pushing to live then will ensure that the build will get the latest changes.

Mozilla usually updates the Firefox Live Server around **0400**, **1000**, **1600**, and **2200** **UTC** on weekdays and **1000 and 2200 UTC** on weekends (give or take an hour). If one of these times is approaching, it's probably not a good time to push. You can check [mozilla-central on TreeHerder](https://treeherder.mozilla.org/#/jobs?repo=mozilla-central) to see when they last pushed.

{% hint style="warning" %}
Coordinate with others in the [#maildev Matrix chat room](https://chat.mozilla.org/#/room/#maildev:mozilla.org) as they may already be planning to push. A Firefox push is usually followed by an assigned person landing something to check the build is not broken.
{% endhint %}

**Is there a build in progress already?** If there is, please wait until you're reasonably sure the first build is not broken. In most cases this means that the Linux and OS X builds (B) are complete and tests (bct, X) are starting to turn green (free from major failures).

**Is the tree green?** If it isn't, do not push. Pushing something on top of an already broken build wastes resources (both computing and human).

{% hint style="warning" %}
Pushing to comm-central will create builds using the **most recent** mozilla-central code, which may or may not be a good idea at the time. Generally it's okay, but there may be unresolved problems between the two repositories. If you strike a problem, ask for help in the [#maildev Matrix chat room](https://chat.mozilla.org/#/room/#maildev:mozilla.org).
{% endhint %}

## Pushing to comm-central <a href="#pushing-to-try" id="pushing-to-try"></a>

Having gained level 3 access and configured Mercurial, you can push to comm-central. In general, it's just a matter of applying your patch(es) and running `hg push`, but let's not do that right now as a series of important checks need to happen before.

{% hint style="danger" %}
You should not push without having complete a successful push and build to the [Try Server](try-server.md).
{% endhint %}

### Steps before pushing

These are a series of recommended steps to always go through before pushing to Live to ensure you're pushing only what you need.

Be sure your commit message is clear and has been approved during review. The standard syntax of a commit message is `Bug 000000 - Description of the patch and fix. r=reviewer`.

#### Mercurial Queues

* Run `hg qpop -a` to clean your local queue.
* Run `hg in` to check if there are updates on the live server. (This isn't strictly necessary if you always do the next step.)
* Run `hg pull -u` to download and apply the most recent changes.
* Reorder your queue and run `hg qpush` to apply only the patches you want to push to Live.
* Run `hg qseries` to double check your have the right patches applied.
* Run `hg qfinish --applied` to include all the currently applied patches in your local tree.
* Run `hg out` to see a list of patches that will be pushed to the Live server. **Check your commit message(s) again.**
* Run `hg push live-cc` (or any shorthand you used in your `hgrc` file) to push your applied patches to comm-central.

#### Mercurial Bookmarks

* Run `hg pull` to download and apply the most recent changes.
* Run `hg rebase -b my-bookmark-name -d XXX` to rebase your patches. Replace the XXX with the latest public revision.
* Run `hg out -r my-bookmark-name` to see a list of patches that will be pushed to the Live server. **Check only the patches you intend to send are listed. Check your commit message(s) again.**
* Run `hg push live-cc -r my-bookmark-name`to push your applied patches to comm-central. Always specify a bookmark or revision to avoid sending more than one branch.

Take a look at the [TreeHerder](https://treeherder.mozilla.org/#/jobs?repo=comm-central) to see your push show up at the top of the list.

{% hint style="warning" %}
If your patch is faulty (i.e. it breaks the build or fails tests), it may be backed out without any warning. It's up to you to fix it.

The tree is monitored for failures by a team of people and although they are nice people they are not expected to tolerate your broken patch. A tree without failures is much easier to work with for everybody concerned.
{% endhint %}

### Commit message magic words

Adding some magic words to the commit message of the tip-most revision will cause the build system to do different things. Getting it wrong or making a typo will not get the desired result.

* `DONTBUILD` tells the build system not to build on this push. Only the decision and linting tasks will happen, unless another process comes along and starts a build, such as the Daily automatic build.
* `CLOSED TREE` allows you to push to a closed tree. I hope you have permission!
* `a=approver` You must specify who approved the changes on some trees (not comm-central).

### Landing somebody else's patch

#### From Bugzillla

To land a patch you didn't write, e.g. from Bugzilla, you'll need to import it into Mercurial: `hg import -e https://bugzilla.mozilla.org/attachment.cgi?id=0000000`

Use the `-e` flag as above, or `hg commit --amend` to edit the commit message as necessary.

#### From Phabricator

To import a patch or patches from Phabricator, use `moz-phab patch`: `moz-phab patch --apply-to tip --no-bookmark --skip-dependencies D000000`

* The `--apply-to` argument adds the patch to a specific parent revision, in this case the tip revision.
* The `--no-bookmark` argument prevents a Mercurial bookmark from being created automatically. If you're just importing to land a patch, creating and then deleting a bookmark is just wasting your time.
* The `--skip-dependencies` argument imports _only_ the patch in question. Otherwise `moz-phab` will attempt to import all parent and child revisions in the Phabricator stack, including revisions that may already exist on your tree (and in this case fail miserably). You may want this to happen, in which case don't use this argument.

Use `hg commit --amend` or `hg histedit` to adjust commit messages as necessary.

#### Using Lando

Our Phabricator installation has a system for automatically landing patches: Lando. To use it click "View stack in Lando" and follow the instructions. A few minutes may pass before an actual landing attempt happens. If it fails (usually because the patches do not apply cleanly) you'll be notified by email and will have to find a solution.

To land several patches together, create a "stack" of patches by using the "Edit related revisions…" and section in Phabricator. This can get messy so plan in advance. Check the current stack of any revision in the "Revision Contents" section of Phabricator.

## After landing

If you worded your commit message correctly, a bot will post a message in your bug with a link to the changes you made, and close the bug. To prevent the bot from closing a bug, add the `leave-open` keyword to the bug before landing. The bot will automatically remove the `checkin-needed-tb` flag if it is set.

At this point you should set the Target Milestone field in the bug to the current version, which is generally, but not always, the last option for that field.

## Landing a patch on beta or ESR

You do not need to land patches on beta or ESR. We have authorised people to do that for you.

Request approval on Bugzilla in the same way you request review, using the `approval-comm-beta` and `approval-comm-esrXX` flags. At an appropriate point approval will be granted \(or denied!\) and your patch will be landed for you. Filling out the request form, especially the "Regression bug" and "Risk" fields, will help the approver prioritize the patch.

When requesting uplift approval for a bug with multiple patches, a single request is sufficient. It's helpful to specify in the request that multiple patches are to be uplifted. Likewise, if a bug has dependencies on another bug, specifying those dependencies and what order they should be uplifted can save a lot of time.

Uplifting patches to earlier versions is for fixes to major bugs, and regressions that break the user interface. It should not be used as a shortcut to get new features to users earlier \(some exceptions apply\). The release channels ensure that changes are exposed to a test audience for a period of time before being shipped to all users.

The comm-esrXX repositories in particular can be difficult to uplift patches to because of code-churn since the repository was created. Sometimes it's necessary to create a patch specifically for comm-esrXX. In these cases, attach the patch in Bugzilla rather than Phabricator. The fix still needs to be applied to comm-central and comm-beta first unless the bug really only affects comm-esrXX. (Bugs that only affect comm-esrXX are actually quite rare. At some point the bug most likely was present on comm-central and was fixed.)
