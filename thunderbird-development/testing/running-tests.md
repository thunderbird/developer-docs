---
description: How to run Thunderbird's automated tests.
---

# Running Tests

## XPCShell

XPCShell tests test Thunderbird's components, without opening the user interface. Firefox also runs this type of test, and much of the [information about Firefox's XPCShell tests](https://firefox-source-docs.mozilla.org/testing/xpcshell/index.html) also apply to Thunderbird.

To run an XPCShell test, or a directory of them, use mach:

```shell
mach xpcshell-test comm/mail/components/extensions/test/xpcshell
```

`mach` must point to mach. The path argument is always relative to the mozilla-central root, so include `comm/` at the start.

If more than one test runs, just a summary of the results will be displayed. You can pass `--verbose` to get the full output if necessary.

## Mochitest

Like XPCShell tests, [mochitests are a type of test used on Firefox](https://firefox-source-docs.mozilla.org/testing/mochitest-plain/index.html). The main difference is that mochitest runs with the full UI, in the context of the main mail window.

To run a mochitest, or a directory of them, use mach:

```shell
mach mochitest mail/components/extensions/test/browser
```

`mach` must point to mach. Any test path that matches the path given runs, for example instead of a path you could just put `browser_foo.js` and every test with that name in any directory would run.

By default, these tests will launch Thunderbird windows, making your computer largely unusable while they're running. If you are running a lot of tests, you can pass `--headless` to prevent this behavior.

## Marionette

Marionette is another Firefox testing tool, specifically intended for remote control of the UI or internal JS. See [Firefox's Marionette documentation](https://firefox-source-docs.mozilla.org/testing/marionette/Intro.html) for full details. Its mostly useful for any tests where the behavior needs to be controlled externally.

To run Marionette tests, use mach:

```shell
mach marionette-test mail/test/marionette/test_commandline.py
```

`mach` must point to mach. The path argument is relative to the current working directory. Like mochitests, you can pass `--headless` to prevent actually launching windows.

