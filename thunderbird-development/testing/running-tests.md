---
description: >-
  Thunderbird has two test suites, that each test different aspects of the
  program.
---

# Running Tests

## XPCShell

XPCShell tests test Thunderbird's components, without opening the user interface. Firefox also runs this type of test, and much of the information about Firefox's XPCShell tests also apply to Thunderbird.

To run an XPCShell test, or a directory of them, use mach:

```text
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

```text
mach mochitest mail/components/extensions/test/browser
```

`mach` must point to mach. The path argument is **not** relative to the mozilla-central root, it points directly at the test in the source directory.

{% hint style="info" %}
You may notice a long pause when running tests for the first time after building. Mach is reindexing the tests.
{% endhint %}
