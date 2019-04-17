---
description: Tutorial on how to use Mercurial bookmarks and submit a patch to Bugzilla.
---

# Using Mercurial Bookmarks

This is a brief "quick start" guide to using Mercurial Bookmarks for Thunderbird development.  For more in-depth documentation see the [Using Bookmarks](https://mozilla-version-control-tools.readthedocs.io/en/latest/hgmozilla/bookmarks.html) section of Mercurial for Mozillians and the [Bookmarks](https://www.mercurial-scm.org/wiki/Bookmarks) page on the Mercurial Wiki.

{% hint style="info" %}
If you have used git, it will help to know that bookmarks in Mercurial are very similar to _branches_ in git and involve similar workflows.  (Note that Mercurial's _branches_ are very different from git branches.)  You may find it helpful to search for a "Mercurial for Git Users" tutorial.
{% endhint %}

Bookmarks are basically labels that point to a given changeset (or a given "commit" in git terminology).  They can point to different changesets at different times (more on this below).

For any of the commands below, you can learn more about them by running `hg commandname --help`.

## Working on a particular bug or feature

A common use case for bookmarks is for working on a given feature or bug.  A bookmark is a label you use to manage your work on one bug or feature, alongside other work on other bugs or features.  Here's an example.

0. Run `hg pull` to download the latest changes from the remote repository.
1. Run `hg update tip` to update your current working directory to the most recent changeset that you just downloaded.
1. Run the command `hg bookmark some-bookmark-name` to create a bookmark that points to the current changeset. Usually the name of the bookmark is related to the bug or feature you are working on.
2. Run `hg bookmarks` to list all of your bookmarks and see which one is currently active.  The one you just created should now be active.
2. You can run `hg log` to see which bookmarks point to which changesets.  Or you may prefer using a GUI tool like [TortoiseHG](https://tortoisehg.bitbucket.io/) that provides a more graphical overview.
2. Make changes to the code until you are ready to commit them.
3. Run `hg status` and `hg diff` to check what files and changes you are about to commit.  If you need to add a new file use `hg add some-filename` before committing.
3. Commit your changes with `hg commit -m "Bug ##### - fixing something amazing"`.  The `-m` is short for `--message` and lets you provide a commit message for your changeset.  "Bug ##### - fixing something amazing" is the format we recommend for the commit message, specifying the number of the Bug you're working on and a small description stating what you fixed.  Use `hg log` to see examples of commit messages.
4. Run `hg log` and note how the bookmark has moved to the changeset that you just committed.  The active bookmark will automatically move to the most recent changeset as you commit your changes.  That way you can make a series of commits and the bookmark will always point to the most recent one.
5. You can use `hg commit --amend` to change your previous commit rather than making a new commit.

## Creating a patch to upload to bugzilla

Let's say you now have a single changeset that fixes a bug (the simplest case), and you are ready to create a patch file to upload to the bug on bugzilla.  You have used a bookmark named "feature-A" and it points to this changeset.

Run `hg export --rev feature-A > my-feature-a-file.patch` to create a patch file.  The `--rev feature-A` part indicates which revision (changeset) you want, in this case the changeset that the bookmark `feature-A` points to.

## Working on more than one bug or feature

Being able to work on more than one bug or feature, and easily switch between working on one or the other, is a typical use case for bookmarks.

Say you have been working on feature A using the bookmark `feature-A` as described above, but now you want to switch to working on feature B.  Here is how that can work.

0. Run `hg log` and copy the identifying number of the first changeset that comes _before_ your changesets for feature A. For example if you see "changeset:   26858:4a2e39cfc820", you can copy either the "26858" or the "4a2e39cfc820" to identify this changeset.  Let's say we use 26858.
1. Run `hg update --rev 26858` to change the state of your current working directory to that changeset.
2. Run `hg bookmark feature-B` to create a new bookmark to work on feature B.  It will currently point to changeset 26858.
3. Make some changes and do one or more commits for feature B.
4. To go back to working on feature A, you run the command `hg update feature-A`.  That updates your working directory to the `feature-A` changeset and makes `feature-A` the active bookmark.
5. Once you've made some changes and done some commits for feature A, you may want to go back to working on feature B.  To do that you would run `hg update feature-B`.  That updates your working directory to the `feature-B` changeset and makes `feature-B` the active bookmark.
