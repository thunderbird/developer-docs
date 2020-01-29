---
description: Tutorial on how to push approved and reviewed patches to the production server
---

# Landing a Patch

## Getting access to comm-central <a id="getting-access-to-the-try-server"></a>

To push a patch to comm-central, you'll need [Level 3 Commit Access](http://www.mozilla.org/hacking/commit-access-policy/). You can learn more about Mozilla's commit access policies and start the process of signing up for an account here: [Becoming a Mozilla Contributor](http://www.mozilla.org/hacking/committer/)

## Adding the live server to your Mercurial configuration

You'll need to add the address to your Mercurial configuration file at `path/to/comm-central/.hg/hgrc`:

```text
[paths]
default = https://hg.mozilla.org/comm-central
live-cc = ssh://hg.mozilla.org/comm-central
```

In this example we used the label `live-cc` but you can use whatever you like. Using the standard label `default-push` is not recommended – having to type the name of the repository helps prevent mistakes.

You can of course [access the repository via HTTP](https://hg.mozilla.org/try-comm-central/), but not push to it, hence the ssh:// address.

## Before pushing <a id="pushing-to-try"></a>

**Is the tree open?** Check [TreeHerder](https://treeherder.mozilla.org/#/jobs?repo=comm-central) – the name of the tree in the top-left corner shows you the status of the tree. Usually it's "open" \(a green circle is displayed\), which means you can push. Other statuses are "approval required" \(yellow padlock\) and "closed" \(red X\) which mean you can't push without permission, and in fact the server will prevent you from doing so.

**Is it a good time to push?** The best time to push is shortly after Mozilla updates the Firefox live server. Since Thunderbird builds on top of Firefox, pushing to live then will ensure that the build will get the latest changes.

Mozilla usually updates the Firefox Live Server around **0400**, **1000**, **1600**, and **2200** **UTC** on weekdays and **1000 and 2200 UTC** on weekends \(give or take an hour\). If one of these times is approaching, it's probably not a good time to push. You can check [mozilla-central on TreeHerder](https://treeherder.mozilla.org/#/jobs?repo=mozilla-central) to see when they last pushed.

{% hint style="warning" %}
Coordinate with others on IRC \(\#maildev\) as they may already be planning to push. A Firefox push is usually followed by an assigned person landing something to check the build is not broken.
{% endhint %}

**Is there a build in progress already?** If there is, please wait until you're reasonably sure the first build is not broken. In most cases this means that the Linux and OS X builds \(B\) are complete and tests \(bct, X\) are starting to turn green \(free from major failures\).

**Is the tree green?** If it isn't, do not push. Pushing something on top of an already broken build wastes resources \(both computing and human\).

{% hint style="warning" %}
Pushing to comm-central will create builds using the **most recent** mozilla-central code, which may or may not be a good idea at the time. Generally it's okay, but there may be unresolved problems between the two repositories. If you strike a problem, ask on the \#maildev IRC channel.
{% endhint %}

## Pushing to comm-central <a id="pushing-to-try"></a>

Having gained level 3 access and configured Mercurial, you can push to comm-central. In general, it's just a matter of applying your patch\(es\) and running `hg push`, but let's not do that right now as a series of important checks need to happen before.

{% hint style="danger" %}
You should not push without having complete a successful push and build to the [Try Server](try-server.md).
{% endhint %}

### Steps before pushing to Live

These are a series of recommended steps to always go through before pushing to Live to ensure you're pushing only what you need.

Be sure your commit message is clear and has been approved during review. The standard syntax of a commit message is `Bug 000000 - Description of the patch and fix. r=reviewer`.

#### Mercurial Queues

* Run `hg qpop -a` to clean your local queue.
* Run `hg in` to check if there are updates on the live server. \(This isn't strictly necessary if you always do the next step.\)
* Run `hg pull -u` to download and apply the most recent changes.
* Reorder your queue and run `hg qpush` to apply only the patches you want to push to Live.
* Run `hg qseries` to double check your have the right patches applied.
* Run `hg qfinish --applied` to include all the currently applied patches in your local tree.
* Run `hg out` to see a list of patches that will be pushed to the Live server. **Check your commit message\(s\) again.**
* Run `hg push live-cc` \(or any shorthand you used in your `hgrc` file\) to push your applied patches to comm-central.

#### Mercurial Bookmarks

* Run `hg pull` to download and apply the most recent changes.
* Run `hg rebase -b my-bookmark-name -d XXX` to rebase your patches. Replace the XXX with the latest public revision.
* Run `hg out -r my-bookmark-name` to see a list of patches that will be pushed to the Live server. **Check only the patches you intend to send are listed. Check your commit message\(s\) again.**
* Run `hg push live-cc -r my-bookmark-name`to push your applied patches to comm-central. Always specify a bookmark or revision to avoid sending more than one branch.

Take a look at the [TreeHerder](https://treeherder.mozilla.org/#/jobs?repo=comm-central) to see your push show up at the top of the list.

## After landing

If you worded your commit message correctly, a bot will post a message in your bug with a link to the changes you made, and close the bug. At this point you should set the Target Milestone field in the bug to the current version, which is generally, but not always, the last option for that field.

## Landing a patch on beta or ESR

You do not need to land patches on beta or ESR. We have authorised people to do that for you.

Request approval on Bugzilla in the same way you request review, using the `approval-comm-beta` and `approval-comm-esrXX` flags. Filling out the request form is not required. At an appropriate point approval will be granted \(or denied!\) and your patch will be landed for you.

Uplifting patches to earlier versions is for fixes to major bugs, and regressions that break the user interface. It should not be used as a shortcut to get new features to users earlier \(some exceptions apply\). The release channels ensure that changes are exposed to a test audience for a period of time before being shipped to all users.

