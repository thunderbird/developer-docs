# Try Server

The Thunderbird try server works in exactly the same way to the [Firefox try server](https://wiki.mozilla.org/ReleaseEngineering/TryServer) with a few minor differences. The automation is based on the same hardware and tools, so there should be few differences.

{% hint style="info" %}
Thunderbird's Try server is often referred to as "try-comm-central", or "try-cc", but usually it's just known as Try. Use an upper-case T if it makes things clearer.
{% endhint %}

## Getting access to the Try Server

To use the try server, you'll need [Level 1 Commit Access](http://www.mozilla.org/hacking/commit-access-policy/). You can learn more about Mozilla's commit access policies and start the process of signing up for an account here: [Becoming a Mozilla Contributor](http://www.mozilla.org/hacking/committer/).

### Configure your SSH Host

You need to make sure your `~/.ssh/config` is properly configured to use the correct SSH username for the Mozilla Mercurial repository.

Your SSH config file should have something like this:

```bash
Host hg.mozilla.org
  User nemo@thunderbird.net
```

Your **User** should match the SSH username that has been granted [Level 1 Commit Access](http://www.mozilla.org/hacking/commit-access-policy/).

## Adding Try to your Mercurial configuration

Try server has a separate repository based upon comm-central. You'll need to add the address to your Mercurial configuration file at `path/to/comm-central/.hg/hgrc`:

```
[paths]
default = https://hg.mozilla.org/comm-central
try-cc = ssh://hg.mozilla.org/try-comm-central
```

You can of course [access the repository via HTTP](https://hg.mozilla.org/try-comm-central/), but not push to it, hence the ssh:// address.

{% hint style="info" %}
The name of the comm-central try server must be different from `try`. There's
an alias in the Mozilla Mercurial configuration that will override this
configuration if the server is named `try`. The instructions below assume the
name `try-cc`.
{% endhint %}

## Pushing to Try

Having gained level 1 access and configured Mercurial, you can push to Try. In general, it's just a matter of applying your patch(es) and running `hg push -r . try-cc` if you're planning to manually trigger tasks from the **Taskcluster** web interface.

For pushes via the command line, we recommend using the `push-to-try` extensions in order to simplify the commands required to automatically trigger jobs and tasks on the try server.

An example of a command to trigger all _mochitest_ jobs for an artifact build will look like this:

{% code overflow="wrap" lineNumbers="true" %}
```bash
hg push-to-try -s ssh://hg.mozilla.org/try-comm-central -m "try: -b o -p all -u mochitest --artifact"
```
{% endcode %}

Read the full list of [try syntax commands](try-server.md#try-syntax) to write in your commit message.

{% hint style="warning" %}
Pushing to try-comm-central will create builds using the **most recent** mozilla-central code, which may or may not be a good idea at the time. Generally it's okay, but there may be unresolved problems between the two repositories. If you strike a problem, ask in the [#maildev Matrix chat room](https://chat.mozilla.org/#/room/#maildev:mozilla.org).\
\
You can also work with a **specific** mozilla-central revision, see "Testing mozilla-central patches" below.
{% endhint %}



## Choosing what tasks to run

You can (and should) control what testing tasks you want to run on your push. There are several methods to do so:

### Try syntax

This is the easiest and most common way. A special code (known as Try syntax) is put in the commit message of the tip-most revision being pushed, for example `try: -b o -p linux64 -u all` creates only an "opt" build on 64-bit Linux, and runs all of the tests on that build.

Here is the Try syntax try-comm-central understands:

* `-b` Build type. Use `o` for an opt build (most common), `d` for a debug build, or `do` for both.
* `-p` Platform. There are five platforms. Each also has a `-shippable` variant, which is a complication you probably don't need to think about.
  * `linux` 32-bit Linux
  * `linux64` 64-bit Linux
  * `macosx64` Mac OS (if you don't need a debug build, specify `macosx64-shippable` instead)
  * `win32` 32-bit Windows
  * `win64` 64-bit Windows
  * `all` for all platforms
* `-u` Unit test suites.
  * `mochitest-thunderbird`
  * `xpcshell`
  * `marionette`
  * `all`
* `--artifact` Artifact builds. See the [Artifact Builds page](../building-thunderbird/artifact-builds.md) for more information.

### Try task configuration

For more control, a special file named `try_task_config.json` and containing a list of the tasks to run is included in one of the pushed revisions.

The contents of the file look like this:

```
{
  "version": 1,
  "use-artifact-builds": true,
  "tasks": [
    "test-linux1804-64-qr/opt-mochitest-thunderbird-e10s-1",
    "test-linux1804-64-qr/opt-mochitest-thunderbird-e10s-2",
    "test-linux1804-64-qr/opt-mochitest-thunderbird-e10s-3",
    "test-linux1804-64-qr/opt-mochitest-thunderbird-e10s-4",
    "test-linux1804-64-qr/opt-mochitest-thunderbird-e10s-5",
    "test-linux1804-64-qr/opt-mochitest-thunderbird-e10s-6",
    "test-linux1804-64-qr/opt-mochitest-thunderbird-e10s-7",
    "test-linux1804-64-qr/opt-xpcshell-e10s-1",
    "test-linux1804-64-qr/opt-xpcshell-e10s-2",
    "test-linux1804-64-qr/opt-xpcshell-e10s-3",
    "test-linux1804-64-qr/opt-xpcshell-e10s-4"
  ]
}
```

`use-artifact-builds` tells the Try server to do an artifact build. Set to false or remove it for a full build. See the [Artifact Builds page](../building-thunderbird/artifact-builds.md) for more information.

`tasks` is a list of tasks to run. In this example it's all of the 64-bit Linux tests. A 64-bit Linux build will also run, because it is required by the tasks specified.

A copy of the file with all available tests is maintained [here](https://github.com/darktrojan/mozconfigs/blob/master/try\_task\_config.json). A typical workflow would be to copy the file into your working directory, remove the tasks you don't want to run, and commit it. For efficiency you could export the commit as a patch and import it again when needed.

{% hint style="info" %}
Task configurations and names change over time. If you're not getting the tasks you requested, this may be why.
{% endhint %}

To find the name of any particular task, click on existing instance in Treeherder, then look for the "job name" in the lower-left corner of the page.

### Adding tasks to an empty Try run

If you commit with neither Try syntax nor a `try_task_config.json` file (or you want to add to an existing run), you can one or more tasks using Treeherder. Once the decision (D) task has completed, click the drop-down arrow to the right of it, and choose "Add new jobs".

### Get an installable build from a Try run

When the build at `https://treeherder.mozilla.org/jobs?repo=try-comm-central` is complete (normally takes 1-2 hours):

* Click the green "B" (for binary) next to one of the following: "Windows 2012 x64 \[shippable] opt", "Linux x64 \[shippable] opt", "OS X Cross Compiled \[shippable] opt" (unless instructed to use a debug build).\
  ![](<../../.gitbook/assets/trybuilds (3).jpeg>)\

* In the black header below click "Artifacts and Debugging Tools".
* In the Artifacts section, to download the install file click on `target.installer.exe` (Windows), `target.tar.bz2` (Linux), or `target.dmg` (Mac).\
  \
  ![](<../../.gitbook/assets/try-target (1).jpeg>)\
  \

* Install the downloaded file.

## Testing mozilla-central patches

If you have changes that affect mozilla-central, you may wish to do a Try run to check Thunderbird isn't broken. Here's how:

1. In your mozilla-central directory, apply your patch. Then run `./mach try empty` to push to the mozilla-central Try repository. You'll need to know the revision number of your push, which will be in the message printed to the console.
2. Move to your comm-central directory.
3. Modify the file `.gecko_rev.yml` – change `GECKO_HEAD_REPOSITORY` to [`https://hg.mozilla.org/try`](https://hg.mozilla.org/try), and `GECKO_HEAD_REV` to point to the revision you previously pushed to M-C's try with `mach try empty`.
4. Now push to try-comm-central as per usual.

You can change `.gecko_rev.yml` to point to any revision on the mozilla-\* trees to test your comm-central patch against them.

{% hint style="info" %}
It's not required, but you _should_ base your comm-central patch on a known good revision of comm-central (probably the tip), and your mozilla-central patch on the matching mozilla-central revision (also probably the tip). Otherwise changes made to one tree but not the other (such as build configuration changes) can cause problems.

To find the matching revision, open the log of the comm-central decision (D) task and search for "built from mozilla-central revision".
{% endhint %}

## Testing comm-beta and comm-esr patches

When doing a Try run for patches to `comm-beta` or `comm-esr##`, the steps are the same as when doing a Try run for `comm-central`. (For example, you do not need to change anything in your `hgrc` file.) The try server is smart enough to automatically detect which one to build and test. This works because of the `.gecko_rev.yml` file. Note that some things might not work the same way as on `comm-central` (e.g. the `--artifact` option only works on `comm-central`).
