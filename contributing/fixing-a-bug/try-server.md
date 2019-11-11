# Try Server

The Thunderbird try server works in exactly the same way to the [Firefox try server](https://wiki.mozilla.org/ReleaseEngineering/TryServer) with a few minor differences. The automation is based on the same hardware and tools, so there should be few differences.

{% hint style="info" %}
Thunderbird's Try server is often referred to as "try-comm-central", or "try-cc", but usually it's just known as Try. Use an upper-case T if it makes things clearer.
{% endhint %}

## Getting access to the Try Server

To use the try server, you'll need [Level 1 Commit Access](http://www.mozilla.org/hacking/commit-access-policy/). You can learn more about Mozilla's commit access policies and start the process of signing up for an account here: [Becoming a Mozilla Contributor](http://www.mozilla.org/hacking/committer/)

## Adding Try to your Mercurial configuration

Try server has a separate repository based upon comm-central. You'll need to add the address to your Mercurial configuration file at `path/to/comm-central/.hg/hgrc`:

```text
[paths]
default = https://hg.mozilla.org/comm-central
try = ssh://hg.mozilla.org/try-comm-central
```

You can of course [access the repository via HTTP](https://hg.mozilla.org/try-comm-central/), but not push to it, hence the ssh:// address.

## Pushing to Try

Having gained level 1 access and configured Mercurial, you can push to Try. In general, it's just a matter of applying your patch\(es\) and running `hg push try`.

{% hint style="warning" %}
Pushing to try-comm-central will create builds using the **most recent** mozilla-central code, which may or may not be a good idea at the time. Generally it's okay, but there may be unresolved problems between the two repositories. If you strike a problem, ask on the \#maildev IRC channel.

You can also work with a **specific** mozilla-central revision, see "Testing mozilla-central patches" below.
{% endhint %}

You can \(and should\) control what testing jobs you want to run on your push. This is usually done by using special code \(Try syntax\) in the commit message of the tip-most revision being pushed, for example `try: -b o -p linux64 -u all` creates only an "opt" build on 64-bit Linux, and runs all of the tests on that build.

## Try Syntax

This is the Try syntax try-comm-central understands:

* `-b` Build type. Use `o` for an opt build \(most common\), `d` for a debug build, or `do` for both.
* `-p` Platform. There are five platforms. Each also has a `-shippable` variant, which is a complication you probably don't need to think about.
  * `linux` 32-bit Linux
  * `linux64` 64-bit Linux
  * `macosx64` Mac OS \(if you don't need a debug build, specify `macox64-shippable` instead\)
  * `win32` 32-bit Windows
  * `win64` 64-bit Windows
  * `all` for all platforms
* `-u` Unit test suites.
  * `marionette` and `marionette-headless`
  * `mochitest-thunderbird`
  * `mozmill`
  * `xpcshell`
  * `all`
* `--artifact` Artifact builds. See the [Artifact Builds page](../../the-basics/building-thunderbird/artifact-builds.md) for more information.

## Testing mozilla-central patches

If you have changes that affect mozilla-central, you may wish to do a Try run to check Thunderbird isn't broken. Here's how:

1. In your mozilla-central directory, apply your patch. Then run `./mach try empty` to push to the mozilla-central Try repository. You'll need to know the revision number of your push, which will be in the message printed to the console.
2. Move to your comm-central directory.
3. Modify the file `.gecko_rev.yml` – change `GECKO_HEAD_REPOSITORY` to [`https://hg.mozilla.org/try`](https://hg.mozilla.org/try), and `GECKO_HEAD_REF` to point to the revision you previously pushed to M-C's try with `mach try empty`.
4. Now push to try-comm-central as per usual.

You can change `.gecko_rev.yml` to point to any revision on the mozilla-\* trees to test your comm-central patch against them.

## Testing comm-beta and comm-esr patches

When doing a Try run for patches to `comm-beta` or `comm-esr##`, the steps are the same as when doing a Try run for `comm-central`. \(For example, you do not need to change anything in your `hgrc` file.\) The try server is smart enough to automatically detect which one to build and test. This works because of the `.gecko_rev.yml` file. Note that some things might not work the same way as on `comm-central` \(e.g. the `--artifact` option only works on `comm-central`\).

