---
description: How to add your own tests for Thunderbird.
---

# Adding Tests

Generally, tests live near the code they are testing, however Mozmill tests live in two particular directories.

This document doesn't cover actually writing tests, for that see this page for
Mochitests:

* [Writing Mochitest Tests](https://developer.thunderbird.net/testing/writing-mochitest-tests)

And also these pages on MDN:

* [Writing xpcshell-based unit tests](https://developer.mozilla.org/en-US/docs/Mozilla/QA/Writing_xpcshell-based_unit_tests)
* [Mochitest](https://developer.mozilla.org/en-US/docs/Mozilla/Projects/Mochitest)

\(Just note that the MDN pages are Firefox-centric and include some ancient
ideas and practices.\)

## XPCShell & Mochitest

Tests should be added to a directory near the code they are located. For example, code in `mail/components/extensions` is tested by tests in `mail/components/extensions/test`. Inside the `test` directory is a subdirectory named after the type of test: `browser` for Mozmill tests \(as in Firefox terms they are "browser-chrome" Mozmill tests\), and `xpcshell` or `unit` for XPCShell tests.

A new directory needs a test manifest:

### XPCShell test manifest \(xpcshell.ini\)

The default section isn't even necessary here, but you probably want to add a `head.js` file if you're going to have more than one test.

{% code title="xpcshell.ini" %}
```text
[default]

[test_firstTest.js]
```
{% endcode %}

### Mochitest manifest \(browser.ini\)

Mochitest needs some prefs set, or automated testing will fail.

{% code title="browser.ini" %}
```text
[default]
prefs =
  ldap_2.servers.osx.description=
  ldap_2.servers.osx.dirType=-1
  ldap_2.servers.osx.uri=
  mail.provider.suppress_dialog_on_startup=true
  mail.spotlight.firstRunDone=true
  mail.winsearch.firstRunDone=true
  mailnews.start_page.override_url=about:blank
  mailnews.start_page.url=about:blank
subsuite = thunderbird

[browser_firstTest.js]
```
{% endcode %}

For tests that need to load messages in the UI, add this pref:

```text
  browser.tabs.remote.autostart=false
```

For calendar mochitests, also add this pref so the tests don't fail on beta or ESR where they're packaged differently:

```text
  extensions.installDistroAddons=true
```

### Linking to manifests

The next thing you need to do is tell mach about your new test manifest. In the nearest `moz.build` file, add these lines as appropriate:

{% code title="moz.build" %}
```python
BROWSER_CHROME_MANIFESTS += [
    'test/browser/browser.ini',
]
XPCSHELL_TESTS_MANIFESTS += [
    'test/xpcshell/xpcshell.ini',
]
```
{% endcode %}

## Mozmill

It's not really ideal to be adding new Mozmill tests. The platform is obsolete and now that Mochitest runs on Thunderbird it's preferred over Mozmill. But sometimes, you've gotta do what you've gotta do.

All Mozmill tests live in `mail/test/mozmill`, except for calendar tests, which live in `calendar/test/mozmill`. The test files themselves general live one directory deeper than that and are named `test-something.js` \(or `testSomething.js`\). To add a new test, put it in one of the existing directories, or if you must, create a new directory and add its name to the file `mozmilltests.list`.

{% hint style="info" %}
If you add a calendar Mozmill test, you'll need to build again before you can use it.
{% endhint %}

