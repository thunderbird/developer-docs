---
description: >-
  Thunderbird has three test suites, that each test different aspects of the
  program.
---

# Running Tests

## XPCShell

XPCShell tests test Thunderbird's components, without opening the user interface. Firefox also runs this type of test, and much of the information about Firefox's XPCShell tests also apply to Thunderbird.

To run an XPCShell test, or a directory of them, use mach:

```shell
mach xpcshell-test comm/mail/components/extensions/test/xpcshell
```

`mach` must point to mach. The path argument is always relative to the mozilla-central root, so include `comm/` at the start.

If more than one test runs, just a summary of the results will be displayed. You can pass `--verbose` to get the full output if necessary.

{% hint style="info" %}
You may notice a long pause when running tests for the first time after building. Mach is reindexing the tests.
{% endhint %}

## Mochitest

Like XPCShell tests, mochitests are a type of test used on Firefox. The main difference is that mochitest runs with the full UI, in the context of the main mail window.

To run a mochitest, or a directory of them, use mach:

```shell
mach mochitest mail/components/extensions/test/browser
```

`mach` must point to mach. The path argument is **not** relative to the mozilla-central root, it points directly at the test in the source directory.

{% hint style="info" %}
You may notice a long pause when running tests for the first time after building. Mach is reindexing the tests.
{% endhint %}

## Mozmill

Mozmill is a test suite for the Thunderbird user interface. It simulates a user's actions, in a "click this button", or "enter this text" kind of way, rather than interacting directly with the program's code.

Mozmill tests are located in `mail/test/mozmill`, except for calendar tests \(see below\). You can run one test or a whole directory.

```shell
# Runs a single test.
make -C objdir/ SOLO_TEST=pref-window/test-attachments-pane.js mozmill-one

# Runs a directory of tests.
make -C objdir/ SOLO_TEST=pref-window mozmill-one
```

You can run these commands from anywhere, as long as the `-C` argument points to your objdir.

### Calendar Mozmill Tests

The mozmill tests for the Lightning extension are located in `calendar/test/mozmill`. Running them is the same as above, but the `-C` argument is different.

```shell
make -C objdir/comm/calendar/test/mozmill SOLO_TEST=testAlarmDefaultValue.js mozmill-one
```

{% hint style="info" %}
You may prefer to run tests using a profile stored in memory, rather than on the disk. Tests do a lot of file I/O, so running them with an in-memory profile can be faster.

On Linux you can do this by setting the TEMP environment variable to `/dev/shm`, a filesystem stored in memory: run `export TEMP=/dev/shm` before running tests. All three test suites support this, except XPCShell when running more than one test for some reason.
{% endhint %}

