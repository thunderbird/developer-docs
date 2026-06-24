---
description: Tutorial on how to land approved and reviewed patches to Thunderbird
---

# Landing Patches

## Getting access to land patches

To land patches to Thunderbird, you'll need [Level 3 Commit Access](http://www.mozilla.org/hacking/commit-access-policy/). You can learn more about Mozilla's commit access policies and start the process of signing up for an account here: [Becoming a Mozilla Contributor](http://www.mozilla.org/hacking/committer/)

## Before landing patches

**Is the tree open?** Thunderbird main is synced to comm-central where CI is run. Check [TreeHerder](https://treeherder.mozilla.org/#/jobs?repo=comm-central) – the name of the tree in the top-left corner shows you the status of the tree. Usually it's "open" (a green circle is displayed), which means you can land. Other statuses are "approval required" (yellow padlock) and "closed" (red X) which mean you can't land without permission, and in fact the server will prevent you from doing so.

**Is it a good time to land patches?** The best time to land patches is shortly after Mozilla updates the Firefox main branch. Since Thunderbird builds on top of Firefox, landing at this time will ensure that the build will get the latest changes.

Mozilla usually updates the Firefox main branch around **0400**, **1000**, **1600**, and **2200** **UTC** on weekdays and **1000 and 2200 UTC** on weekends (give or take an hour). If one of these times is approaching, it's probably not a good time to land. You can check the [Firefox main branch on GitHub](https://github.com/mozilla-firefox/firefox/commits/main/) or [mozilla-central on TreeHerder](https://treeherder.mozilla.org/#/jobs?repo=mozilla-central) to see when they last pushed.

{% hint style="warning" %}
Coordinate with others in the [#maildev Matrix chat room](https://chat.mozilla.org/#/room/#maildev:mozilla.org) as they may already be planning to land. A Firefox push is usually followed by an assigned person landing something to check the build is not broken.
{% endhint %}

**Is there a build in progress already?** If there is, please wait until you're reasonably sure the first build is not broken. In most cases this means that the Linux and OS X builds (B) are complete and tests (bct, X) are starting to turn green (free from major failures).

**Is the tree green?** If it isn't, do not land. Landing patches on top of an already broken build wastes resources (both computing and human).

{% hint style="warning" %}
Landing patches to Thunderbird will create builds using the **most recent** Firefox main code, which may or may not be a good idea at the time. Generally it's okay, but there may be unresolved problems between the two repositories. If you strike a problem, ask for help in the [#maildev Matrix chat room](https://chat.mozilla.org/#/room/#maildev:mozilla.org).
{% endhint %}

## Landing patches to Thunderbird main

Lando is used to land phabricator patches into the Thunderbird main branch. All patches must land via Lando.

The [Lando User Guide](https://moz-conduit.readthedocs.io/en/latest/lando-user.html) has details on how to land patches.

Having gained level 3 access, you can land code to the Thunderbird main branch.

{% hint style="danger" %}
You should not land patches without having completed a successful try build [Try Server](try-server.md).
{% endhint %}

### Steps before landing patches

These are a series of recommended steps to always go through before landing patches.

Be sure your commit message is clear and has been approved during review. The standard syntax of a commit message is `Bug 000000 - Description of the patch and fix. r=reviewer`.

{% hint style="warning" %}
Always check the reviewer in a commit message matches the person who actually reviewed the patch. A patch could be reviewed by someone other than the originally intended person, or it could have been sent to a group of reviewers.
{% endhint %}

After landing to the Thunderbird main branch, the code will sync to Mercurial where CI will run. You can take a look at [TreeHerder](https://treeherder.mozilla.org/#/jobs?repo=comm-central) to see the changes show up at the top of the list.

{% hint style="warning" %}
If your patch is faulty (i.e. it breaks the build or fails tests), it may be backed out without any warning. It's up to you to fix it.

The tree is monitored for failures by a team of people and although they are nice people they are not expected to tolerate your broken patch. A tree without failures is much easier to work with for everybody concerned.
{% endhint %}

### Commit message magic words

Adding some magic words to the commit message of the tip-most commit will cause the build system to do different things. Getting it wrong or making a typo will not get the desired result.

* `DONTBUILD` tells the build system not to build on this patch. Only the decision and linting tasks will happen, unless another process comes along and starts a build, such as the Daily automatic build.
* `CLOSED TREE` allows you to land to a closed tree. I hope you have permission!
* `a=approver` You must specify who approved the changes on some trees (not Thunderbird main).

## After landing

If you worded your commit message correctly, a bot will post a message in your bug with a link to the changes you made, and close the bug. To prevent the bot from closing a bug, add the `leave-open` keyword to the bug before landing. The bot will automatically remove the `checkin-needed-tb` flag if it is set.

At this point you should set the Target Milestone field in the bug to the current version, which is generally, but not always, the last option for that field.

## Landing patches to beta, release, or ESR

You do not need to land patches on beta, release, or ESR. We have authorised people to do that for you.

Request approval on Bugzilla in the same way you request review, using the `approval-comm-beta` and `approval-comm-esrXX` flags. At an appropriate point approval will be granted \(or denied!\) and your patch will be landed for you. Filling out the request form, especially the "Regression bug" and "Risk" fields, will help the approver prioritize the patch.

When requesting uplift approval for a bug with multiple patches, a single request is sufficient. It's helpful to specify in the request that multiple patches are to be uplifted. Likewise, if a bug has dependencies on another bug, specifying those dependencies and what order they should be uplifted can save a lot of time.

Uplifting patches to earlier versions is for fixes to major bugs, and regressions that break the user interface. It should not be used as a shortcut to get new features to users earlier \(some exceptions apply\). The release channels ensure that changes are exposed to a test audience for a period of time before being shipped to all users.

The esr branch in particular can be difficult to uplift patches to because of code-churn since the repository was created. Sometimes it's necessary to create a patch specifically for esr. In these cases, attach the patch in Bugzilla rather than Phabricator. The fix still needs to be applied to main and beta branches first unless the bug really only affects esr. (Bugs that only affect esr are actually quite rare. At some point the bug most likely was present on main and was fixed.)
