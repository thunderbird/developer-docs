---
description: Tutorial on how to use Mercurial bookmarks and submit a patch to Bugzilla.
---

# Using Mercurial Bookmarks

This is a brief "quick start" guide to using Mercurial Bookmarks for Thunderbird development. For more in-depth documentation see the [Using Bookmarks](https://mozilla-version-control-tools.readthedocs.io/en/latest/hgmozilla/bookmarks.html) section of Mercurial for Mozillians and the [Bookmarks](https://www.mercurial-scm.org/wiki/Bookmarks) page on the Mercurial Wiki.

{% hint style="info" %}
If you have used git, it will help to know that bookmarks in Mercurial are very similar to _branches_ in git and involve similar workflows. \(Note that Mercurial's _branches_ are very different from git branches.\) You may find it helpful learn more about the similarities and differences between Mercurial and Git. For example, see the in-depth discussion in [Mercurial for Git Users \(and vice versa\)](https://www.rath.org/mercurial-for-git-users-and-vice-versa.html).
{% endhint %}

Bookmarks are basically labels that point to a given changeset \(or a given "commit" in git terminology\). They can point to different changesets at different times \(more on this below\).

For any of the commands below, you can learn more about them by running `hg commandname --help`.

## Working on a particular Bug or Feature

A common use case for bookmarks is for working on a given feature or bug. A bookmark is a label you use to manage your work on one bug or feature, alongside other work on other bugs or features. Here's an example.

1. Run `hg pull` to download the latest changes from the remote repository.
2. Run `hg update tip` to update your current working directory

   to the most recent changeset that you just downloaded.

3. Run the command `hg bookmark some-bookmark-name` to create a bookmark

   that points to the current changeset.

   Usually the name of the bookmark is related to the bug or feature you are working on.

4. Run `hg bookmarks` to list all of your bookmarks and see which one is currently active.

   The one you just created should now be active.

5. You can run `hg log --graph` or `hg wip` to see which bookmarks point to which changesets.

   Or you may prefer using a GUI tool like

   [TortoiseHG](https://tortoisehg.bitbucket.io/)

   that provides a more graphical overview.

6. Make changes to the code until you are ready to commit them.
7. Run `hg status` and `hg diff` to check what files and changes you are about to commit.

   If you need to add a new file use `hg add some-filename` before committing.

8. Commit your changes with `hg commit -m "Bug ##### - fixing something amazing"`.

   The `-m` is short for `--message` and lets you provide a commit message for your changeset.

   "Bug \#\#\#\#\# - fixing something amazing" is the format we recommend for the commit message,

   specifying the number of the Bug you're working on and a small description stating what you fixed.

   Use `hg log --graph` to see examples of commit messages.

9. Run `hg log --graph` or `hg wip` and note how the bookmark has moved to the changeset that you

   just committed.

   The active bookmark will automatically move to the most recent changeset as you commit your changes.

   That way you can make a series of commits and the bookmark will always point to the most recent one.

10. You can use `hg commit --amend` to modify the current changeset rather than making a new one.

## Creating a Patch to upload to Bugzilla

Let's say you now have a single changeset that fixes a bug \(the simplest case\), and you are ready to create a patch file to upload to the bug on bugzilla. You have used a bookmark named "feature-A" and it points to this changeset.

Run `hg export --rev feature-A > my-feature-a-file.patch` to create a patch file. The `--rev feature-A` part indicates which revision \(changeset\) you want, in this case the changeset that the bookmark `feature-A` points to.

## Working on more than one Bug or Feature

Being able to work on more than one bug or feature, and easily switch between working on one or the other, is a typical use case for bookmarks.

Say you have been working on feature A using the bookmark `feature-A` as described above, but now you want to switch to working on feature B. Here is how that can work.

1. Run `hg log --graph` or `hg wip` and copy the identifying number of the first changeset

   that comes _before_ your changesets for feature A.

   For example if you see "changeset:   26858:4a2e39cfc820",

   you can copy either the "26858" or the "4a2e39cfc820" to identify this changeset.

   Let's say we use 26858.

2. Run `hg update --rev 26858` to change the state of your current working directory to that changeset.
3. Run `hg bookmark feature-B` to create a new bookmark to work on feature B.

   It will currently point to changeset 26858.

4. Make some changes and do one or more commits for feature B.
5. To go back to working on feature A, you run the command `hg update feature-A`.

   That updates your working directory to the `feature-A` changeset

   and makes `feature-A` the active bookmark.

6. Once you've made some changes and done some commits for feature A,

   you may want to go back to working on feature B.

   To do that you would run `hg update feature-B`.

   That updates your working directory to the `feature-B` changeset

   and makes `feature-B` the active bookmark.

## Rebasing Before Uploading to Bugzilla

Say you are ready to export one or more patches to upload to Bugzilla. Often other changes will have landed in the Thunderbird code base since you started your work. In that case it is best to make sure that your changes will still apply to the current state of comm-central. We can do that using `hg rebase`.

1. Pull down any new changesets with `hg pull`.
2. Use `hg log --graph` or `hg wip` to see if there are new changesets and

   get the number \(or the hash\) of the first changeset

   from the newly pulled changesets.

   Let's say its number is 54321.

3. Rebase your branch on to the newest changeset

   with the command `hg rebase -b my-bookmark-name -d 54321`.

   The `-b` is for bookmark and the `-d` is for destination.

   We are rebasing the changesets from the bookmark `my-bookmark-name`

   onto the destination changeset `54321`.

For example, you have a series of changesets like so:

```text
  A -> B -> C -> D -> E
```

Where C, D, and E are new changesets you have created, and your bookmark "my-bookmark-name" points to changeset E.

Then, after you do `hg pull` you have this:

```text
  A -> B -> F -> G -> H
        \
         C -> D -> E
```

Where F, G, and H are changesets that have been added to comm-central since you started working on your changes in C, D, and E.

Then, after you do `hg rebase -b my-bookmark-name -d tip` you have:

```text
  A -> B -> F -> G -> H -> C2 -> D2 -> E2
```

Where Mercurial has re-applied C on top of H, then D on top of C2, then E on top of D2.

Now you know your changesets will apply to the current state of comm-central and you can export them by doing `hg export --rev NNN > my-patch-file.patch` for each of C2, D2, and E2, where NNN is the number \(or hash\) for the changeset.

Rebasing can be useful in other situations too. Another variation is `hg rebase -s 44444 -d 54321`. Where `-s` indicates a source changset. That will apply the source changeset on top of the destination changeset.

So, in the example above, if changeset C had the number 44444, then `hg rebase -s 44444 -d 54321` would do the same thing as `hg rebase -b my-bookmark-name -d 54321`.

### Merge Conflicts When Rebasing

What if there are "merge conflicts"? Say, in the example above, what if the same lines of a given file were changed in different ways by both changesets H and D, and Mercurial couldn't figure out what to do?

In that case, Mercurial will pause the rebase operation and guide you through the process of resolving the conflicts.

First Mercurial will tell you which files have conflicts. Open those files in your editor to fix the conflicts manually. There you will find something like the following, where you can see the two versions of the code that conflict:

```text
<<<<<<< local
        // These are lines that were changed by changeset H!
        anAwesomeFunction(foo);
=======
        // But in changeset D they were changed in a different way!
        anEndearingFunction(bar);
>>>>>>> other
```

Edit those lines to resolve the conflict, so that the code is what it should be after changeset D. Once all such conflicts are fixed, save the file\(s\).

Then back in the terminal run the command `hg resolve --mark` to mark the conflicts as resolved.

And then do `hg rebase --continue` and Mercurial will continue the rebase operation.

To get back to the state of things before the rebase operation started, do `hg rebase --abort`.

