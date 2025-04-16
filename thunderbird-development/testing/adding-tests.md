---
description: How to add your own tests for Thunderbird.
---

# Adding Tests

Generally, tests live near the code they are testing, however some old tests are in a separate test directory.

This document doesn't cover actually writing tests, for that see this page for Mochitests:

* [Writing Mochitest Tests](https://developer.thunderbird.net/testing/writing-mochitest-tests)

And also these pages:

* [Writing xpcshell-based unit tests](https://firefox-source-docs.mozilla.org/testing/xpcshell/index.html)
* [Mochitest](http://devdoc.net/web/developer.mozilla.org/en-US/docs/Mochitest.html) (archived MDN content)

(Just note that these pages are Firefox-centric and include some ancient ideas and practices.)

## XPCShell & Mochitest

Tests should be added to a directory near the code they are located. For example, code in `mail/components/extensions` is tested by tests in `mail/components/extensions/test`. Inside the `test` directory is a subdirectory named after the type of test: `browser` for mochitests (as in Firefox terms they are "browser-chrome" mochitests), and `xpcshell` or `unit` for XPCShell tests.

A new directory needs a test manifest:

### XPCShell test manifest (xpcshell.toml)

The default section isn't even necessary here, but you probably want to add a `head.js` file if you're going to have more than one test.

{% code title="xpcshell.toml" %}
```
[default]
prefs = [
  "calendar.timezone.local=UTC",
]

["test_firstTest.js"]
```
{% endcode %}

The calendar preferences in line 3 is unnecessary outside of the calendar tests. Calendar tests always run in UTC.

### Mochitest manifest (browser.toml)

Mochitest needs some prefs set, or automated testing will fail.

{% code title="browser.toml" %}
```
[default]
prefs = [
  "calendar.timezone.local=UTC",
  "calendar.week.start=0",
  "mail.spotlight.firstRunDone=true",
  "mail.winsearch.firstRunDone=true",
  "mailnews.start_page.override_url=about:blank",
  "mailnews.start_page.url=about:blank",
]
subsuite = "thunderbird"

["browser_firstTest.js"]
```
{% endcode %}

The calendar preferences in lines 3-4 are unnecessary outside of the calendar tests. Calendar tests always run in UTC with the week starting on Sunday.

### Linking to manifests

The next thing you need to do is tell mach about your new test manifest. In the nearest `moz.build` file, add these lines as appropriate:

{% code title="moz.build" %}
```python
BROWSER_CHROME_MANIFESTS += [
    'test/browser/browser.toml',
]
XPCSHELL_TESTS_MANIFESTS += [
    'test/xpcshell/xpcshell.toml',
]
```
{% endcode %}
