---
description: Tutorial on how to push approved and reviewed patches to the Live Server
---

# Live Server

The Thunderbird live server works in exactly the same way to the [Firefox try server](https://wiki.mozilla.org/ReleaseEngineering/TryServer) with a few minor differences. The automation is based on the same hardware and tools, so there should be few differences.

{% hint style="info" %}
Thunderbird's Live server is often referred to as "comm-central", or "cc", but usually it's just known as c-c. 
{% endhint %}

## Getting access to the Live Server <a id="getting-access-to-the-try-server"></a>

To use the live server, you'll need [Level 3 Commit Access](http://www.mozilla.org/hacking/commit-access-policy/). You can learn more about Mozilla's commit access policies and start the process of signing up for an account here: [Becoming a Mozilla Contributor](http://www.mozilla.org/hacking/committer/)

## Adding Live to your Mercurial configuration

```text
[paths]
default = https://hg.mozilla.org/comm-central
live-cc = ssh://hg.mozilla.org/comm-central
```

You can of course [access the repository via HTTP](https://hg.mozilla.org/try-comm-central/), but not push to it, hence the ssh:// address.

## Before Pushing <a id="pushing-to-try"></a>

The first thing to check is it it's a good time to push. Mozilla usually updates the Firefox Live Server at **0400**, **1000**, **1600**, and **2200** **UTC**. Since Thunderbird builds on top of Firefox, pushing to Live after these time slots will ensure that the build will get the latest updates.

{% hint style="warning" %}
It's recommended to always wait for all the currently running builds on [TreeHerder](https://treeherder.mozilla.org/#/jobs?repo=comm-central) to complete before pushing a new patch to the Live Server.
{% endhint %}

## Pushing to Live <a id="pushing-to-try"></a>

Having gained level 3 access and configured Mercurial, you can push to Live. In general, it's just a matter of applying your patch\(es\) and running `hg push live-cc`, but let's not do that right now as a series of important checks need to happen before.

{% hint style="warning" %}
Pushing to comm-central will create builds using the **most recent** mozilla-central code, which may or may not be a good idea at the time. Generally it's okay, but there may be unresolved problems between the two repositories. If you strike a problem, ask on the \#maildev IRC channel.

Also, is recommended to ask on the \#maildev IRC channel if other developers are currently taking care of pushing patches to tree to avoid overlapping.
{% endhint %}

{% hint style="danger" %}
You should never push to the Live Server without having complete a successful push and build to the [Try Server](try-server.md).
{% endhint %}

### Steps before pushing to Live

These are a series of recommended steps to always go through before pushing to Live to ensure your Mercurial Queue is clean and you're pushing only what you need.

* Be sure your commit message is clear and has been approved during review. The standard syntax of a commit message is `Bug 000000 - Description of the patch and fix. r=reviewer`.
* Run `hg qpop -a` to clean your local queue.
* Run `hg qin` to check the live server for updates.
* Run `hg pull -u` to download and apply the most recent changes.
* Reorder your queue and run `hg qpush` to apply only the patches you want to push to Live.
* Run `hg qseries` to double check your applied patches.
* Run `hg qfinish --applied` to include all the currently applied patches in your local tree.
* Run `hg out` to see a list of patches that will be pushed to the Live server.
* Run `hg push live-cc` \(or any shorthand you used in your `.hgrc` file\) to push your applied patches to the Live Server.

Take a look at the [TreeHerder](https://treeherder.mozilla.org/#/jobs?repo=comm-central) to see your push show up at the top of the list.

