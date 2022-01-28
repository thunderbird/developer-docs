---
description: How to run Thunderbird's automated tests.
---

# Running Tests

## XPCShell

XPCShell tests test Thunderbird's components, without opening the user interface. Firefox also runs this type of test, and much of the information about Firefox's XPCShell tests also apply to Thunderbird.

To run an XPCShell test, or a directory of them, use mach:

```
mach xpcshell-test comm/mail/components/extensions/test/xpcshell
```

`mach` must point to mach. The path argument is always relative to the mozilla-central root, so include `comm/` at the start.

If more than one test runs, just a summary of the results will be displayed. You can pass `--verbose` to get the full output if necessary.

## Mochitest

Like XPCShell tests, mochitests are a type of test used on Firefox. The main difference is that mochitest runs with the full UI, in the context of the main mail window.

To run a mochitest, or a directory of them, use mach:

```
mach mochitest mail/components/extensions/test/browser
```

`mach` must point to mach. Any test path that matches the path given runs, for example instead of a path you could just put `browser_foo.js` and every test with that name in any directory would run.
