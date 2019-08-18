---
description: Tutorial on how to activate and use Mercurial Queues.
---

# Using Mercurial Queues

Mercurial Queues is the primary method used by many Thunderbird developers to create patches and work on multiple bugs without stumbling upon merge conflicts.

{% hint style="info" %}
If you're already familiar with Mercurial Queues but you need a quick overview of all the available commands, take a look at the [Mercurial Queues reference](http://hgbook.red-bean.com/read/mercurial-queues-reference.html)
{% endhint %}

## Before you start!

You can accidentally destroy work with MQ. MQ puts you in a position where you're doing fairly complicated stuff to your uncommitted work. Certain operations make it easy to lose work. Watch your step.

For instance, unless you're running the [mqext](https://bitbucket.org/sfink/mqext/), `hg qrefresh` is destructive. It replaces your previous version of the current patch with what's in your working directory. The previous version is lost.

Other things to keep in mind:

* **Don't use MQ in a repository anyone might pull from**. MQ creates temporary changesets in your repo. If someone pulls one of them, you'll never get rid of it.
* **Avoid the -f option**. It is sharp and can mess up your repository if used incorrectly.
* Ensure you use the latest stable release of Mercurial.
* Version your patch queue to save changes. The [mqext](https://bitbucket.org/sfink/mqext/) extension can make this much easier.

## Activate the Queues Extension

To enable MQ, put this in your `Mecurial.ini` file for Windows or the `$HOME/.hgrc` file for Linux and macOS:

```bash
[extensions]
mq =
[diff]
git = 1
unified = 8
showfunc = 1
```

Don't forget the `git` line. This allows changing binary files in your patches. The `unified` line give 8 lines patch.

## Create a new Patch

Create a new patch with the command `hg qnew -m "Bug ###### - fixing something amazing" patch-name`. Let's quickly analyze this command:

* `qnew` is the command to initial a new patch.
* `-m` is the command that allows you to write a commit message.
* `"Bug ###### - fixing something amazing"` is the format we recommend using for the commit message, specifying the number of the Bug you're working on and a small description stating what you fixed.
* `patch-name` is, obviously, the name of your patch, and that can be anything.

Each repository has its own queue of patches managed by MQ. They're just stored as files in the `.hg/patches` directory under the repository.

{% hint style="info" %}
The commit message is optional and you can add it at a later time with a `qrefresh`.
{% endhint %}

## Refresh the Code

Whenever you change something in your code, you need to trigger the `hg qrefresh` command in order to update your current patch with the latest changes. Do a `hg diff` before you issue `hg qefresh` to see which changes will be added to your patch. If you use multiple patches \(see section below\), it may be a good idea to do a `hg qseries` to make sure the right patch is on top. Otherwise the changes will be added to the wrong patch.

It's always good practice to check if the current changes have been properly saved in your patch by using the command `hg qdiff`. All the diffs will be listed in your terminal.

Note that both `hg diff` and `hg qdiff` take a `-w` argument to ignore white-space in case you reindented blocks and it's hard to see the net changes.

## Pop and Push

If you're working on a patch and you need to pull the updates from upstream, you need to _"disconnect"_ your pathes in order to prevent merge conflicts. Here's a standard workflow you should follow:

* `hg qpop -a` to _"pop"_ all your patches and revert the code base to its original status.
* `hg pull -u` to pull all the recent changes from upstream.
* `hg qpush` to apply once again your patch. Trigger this command as many times as you need in order to apply all the patches in sequence.

{% hint style="info" %}
Merge conflicts can happen when reapplying your patches after pulling updates from upstream. Simply fix the merging issues and `qrefresh` your patches.
{% endhint %}

## Managing multiple Patches

MQ allows you to work on multiple patches at once and keeping the code separate. No matter how many patches you have applied in your queue, the `qrefresh` command will only update the code to the patch at the top of you series.

Use the `hg qseries` command to visualize a list of currently applied patches and their order.

### Reordering the Patches

Sometimes the queue ends up not being in the order you want. For example, maybe you've been working on two patches, and the second one \(the topmost one in your queue\) is ready to be pushed before the first one is.

If you have Mercurial 1.6 or newer, the best way to reorder your queue is `hg qpush --move`. For example:

```bash
hg qpop -a                   # Unapply all patches
hg qpush --move patch-name   # Apply only one patch, reordering as needed
```

With older Mercurial versions, you can do this:

```bash
hg qpop -a                   # Unapply all patches
$EDITOR .hg/patches/series   # Manually rearrange the lines of the series file
hg qpush patch-name          # Reapply patches -- watch out for rejects!
```

{% hint style="warning" %}
Reordering patches that touch the same file can cause conflicts when you push! If this happens, `hg qpush` will tell you, and it will leave `.rej` files in your working directory. To avoid losing work, you must manually apply these rejected changes, then `hg qrefresh`.
{% endhint %}

## Import a Patch into your Queue

With MQ you can import a patch into your queue, e.g. from Bugzilla. It is unapplied by default and the `filename` is used as the patch-name. You can directly import a Bugzilla patch by using the Bugzilla attachment URL as the argument. In that case you may also want to use `-n patch-name` to specify the patch name.

```bash
hg qimport https://bugzilla.mozilla.org/attachment.cgi?id=9086264 -n patch-name
hg qimport ~/Your/Chosen/Directory/filename.patch -n patch-name
hg qimport https://hg.mozilla.org/comm-central/rev/0e7bfdf1b900
```

If you have the `qimportbz` extension installed, you can also import by specifying a bug number:

```bash
hg qimport bz:1574724
```

### Workflow Overview

Here's a quick overview of a standard workflow you will be using with MQ.

```bash
hg qnew bug-123456-fix
... change some files ...
hg qrefresh
... change some more files ...
hg qrefresh -m "Bug 123456: A brief summary of the changes you have made."
```

Note that `hg export qtip > ~/bug-123456-fix.patch` is not necessary since all the patches reside in the `.hg/patches` directory in your repository.

### Advanced usage

Commands mentioned so far can be abbreviated, so `hg qser`, `hg qref`, etc.

Of course you can delete patches from your queue using `hg qdelete` or rename them with `hg qrename`. If you decide to combine patches, there is `hg qfold`, that will merge the first unapplied patch into the patch at the tip of your apply queue. Use with care since the merged patch will be removed and the applied patch will be irreversibly changed.

Even if you're not the sheriff, it's sometimes handy to be able to backout one or more changesets. Use:

```bash
hg qbackout -r 0e7bfdf1b900
hg qbackout -r 0e7bfdf1b900:0e7bfdf1b901 (multiple consequtive changesets)
hg qbackout -r 0e7bfdf1b900 -r 0e7bfdf1b955 (non-consequtive)
hg qbackout -s -r 0e7bfdf1b900:0e7bfdf1b901 (merge into a single backout changeset)
```

There is also limited integration with Phabricator with `hg phabread`. That needs a special setup. Ask the resident Thunderbird sheriff for details or read [here](https://www.mercurial-scm.org/wiki/Phabricator#Setting_up_hg) \(but there's more to it\).



