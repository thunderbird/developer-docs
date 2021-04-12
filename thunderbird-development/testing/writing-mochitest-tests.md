---
description: Tips on writing Mochitest tests for Thunderbird.
---

# Writing Mochitest Tests

This document offers some basic tips for writing Mochitest tests. \(See also this [Mochitest](https://developer.mozilla.org/en-US/docs/Mozilla/Projects/Mochitest) page on MDN, which is Firefox-centric but may still be useful.\)

## Adding a New Test

You may be writing a new test in an existing test file, or you may have set up a new test file as described in [Adding Tests](https://developer.thunderbird.net/testing/adding-tests). Either way, add a new test with the `add_task` function:

```javascript
add_task(async () => {
  // The code for the test goes here.
});
```

## Helper Functions

Many essential functions live in these files.

* [EventUtils](https://searchfox.org/mozilla-central/source/testing/mochitest/tests/SimpleTest/EventUtils.js)
* [BrowserTestUtils](https://searchfox.org/mozilla-central/source/testing/mochitest/BrowserTestUtils/BrowserTestUtils.jsm)
* [MailTestUtils](https://searchfox.org/comm-central/source/mailnews/test/resources/MailTestUtils.jsm)

`EventUtils` and `BrowserTestUtils` do not need to be imported as they are already available in Mochitest files. `mailTestUtils` requires importing:

```javascript
const { mailTestUtils } = ChromeUtils.import(
  "resource://testing-common/mailnews/MailTestUtils.jsm"
);
```

This document is a basic introduction. To go further, explore these files \(particularly the docstrings of the various functions in them\) and look at existing tests.

## Assertion Functions

Use the `is` and `ok` functions to make test assertions. `is` compares two values \(using JavaScript's `===` equality comparison\). `ok` asserts that a single value is truthy \(in JavaScript's sense of truthy\).

The last argument to each is a message printed to the console to identify the assertion. It is optional but is a good practice for more understandable test logs.

```javascript
let sum = 3 * 4;
is(sum, 12, "multiplication appears to still be working");

let truthy = true;
ok(truthy, "thing is not falsy");
```

## Mouse Clicks

Do a single click on a DOM element:

```javascript
let element = document.getElementById("some-element-id");

EventUtils.synthesizeMouseAtCenter(element, { clickCount: 1 });
```

To double-click change the `clickCount` from 1 to 2. There is a shorthand for a single click:

```javascript
EventUtils.synthesizeMouseAtCenter(element, {});
```

If the test is interacting with a window that is not the main one, pass the relevant window as the \(optional\) third argument:

```javascript
EventUtils.synthesizeMouseAtCenter(element, {}, anotherWindow);
```

## Keyboard Keys

Type some text with the keyboard, or type a single key, even a non-character one like the tab key:

```javascript
EventUtils.sendString("some text");

EventUtils.sendKey("TAB");
```

Some other valid keys for `sendKey` include: RETURN, BACK\_SPACE, DELETE, HOME, END, UP, DOWN, LEFT, RIGHT, PAGE\_UP, PAGE\_DOWN, SHIFT, CONTROL, ALT, ESCAPE, F1, F2, etc. \(Not an exhaustive list.\)

If the test is interacting with a window that is not the main one, pass the relevant window as the \(optional\) second argument:

```javascript
EventUtils.sendString("some text", anotherWindow);

EventUtils.sendKey("TAB", anotherWindow);
```

## Modifier Keys \(Ctrl, Alt, Shift\)

To press a key along with one or more modifier keys:

```javascript
// Ctrl+A:
EventUtils.synthesizeKey("a", { accelKey: true });

// Ctrl+Alt+B (no shift key):
EventUtils.synthesizeKey("b", {
  accelKey: true,
  altKey: true,
  shiftKey: false
});
```

Some other options are `altKey`, `shiftKey`, and `ctrlKey` \(a non-exhaustive list\).

Similar to `sendString` and `sendKey`, there is an optional third `window` argument to use when interacting with a specific window.

## Waiting for Events

Tests move faster than users. Sometimes too fast. The test may need to wait for an event to occur before doing the next thing.

```javascript
let element = document.getElementById("some-element-id");

let event = await BrowserTestUtils.waitForEvent(element, "focus");

is(event.type, "focus", "element is focused");
```

## Letting Thunderbird Respond Before Proceeding

Sometimes the test needs to let the application respond to something the test did before moving on to the next step, and there is not an event to listen for. Here is a simple way to do this:

```javascript
// Do something here involving the UI.

// Let the application finish responding to what the test just did.
await new Promise(resolve => setTimeout(resolve));

// Do the next thing.
```

## Interacting with Regular Windows

Some tests will need to interact with windows that are not the main window. For exaple, below is a function that opens the address book window. It returns the `window` \(`nsIDOMWindow`\) object for the address book window, which can then be used when calling functions like `EventUtils.sendKey`.

The key point is the use of `BrowserTestUtils.domWindowOpened`, but this example also demonstrates some of the other tips found in this document. \(See below for dialog windows which are handled differently.\)

```javascript
async function openAddressBookWindow() {
  // Set up a watcher for "domwindowopened". When DOM windows are opened the
  // function supplied as a second argument is called. When it returns true the
  // promise is resolved as the nsIDOMWindow object for the window.
  let addressBookWindowPromise = BrowserTestUtils.domWindowOpened(
    null,
    async win => {
      // win is the nsIDOMWindow object for a window that is opening.

      // Wait until the "load" event has happened for the window.
      await BrowserTestUtils.waitForEvent(win, "load");

      // Return true when we have the right window.
      return (
        win.document.documentURI ==
        "chrome://messenger/content/addressbook/addressbook.xul"
      );
    }
  );

  // Open the address book window.
  const addressBookButton = document.getElementById("button-address");
  EventUtils.synthesizeMouseAtCenter(addressBookButton, { clickCount: 1 });

  // Wait for the promise to resolve. abWindow is a nsIDOMWindow object.
  let abWindow = await addressBookWindowPromise;

  // There is no event that fires when the JavaScript that is initially loaded
  // in the window has finished running, so let it finish by using a setTimeout.
  await new Promise(resolve => abWindow.setTimeout(resolve));

  // Assert that the window was opened successfully.
  ok(abWindow && abWindow instanceof Window, "address book window was opened");

  return abWindow;
}
```

## Interacting with Dialog Windows

Interact with dialog windows by using `BrowserTestUtils.promiseAlertDialog`.

```javascript
// The third argument is a function that interacts with the dialog window.
let dialogWindowPromise = BrowserTestUtils.promiseAlertDialog(
  null,
  "chrome://path/to/the/dialog.xul",
  async dialogWindow => {
    // dialogWindow is an nsIDOMWindow object.
    let doc = dialogWindow.document;
    let dialogElement = doc.querySelector("dialog");

    // More code to interact with the dialog goes here.

    // Usually click a button to close the dialog.
    dialogElement.getButton("accept").click();
  }
);

// Code to open the dialog window goes here.

// Wait for the dialog to close.
await dialogWindowPromise;
```

## Interacting with Trees

Trees are not like other DOM elements and require special handling.

```javascript
// Get the text from a given cell in a tree:

let tree = document.getElementById("some-tree");
let rowNumber = 1;
let columnNumber = 1;
let cellText = tree.view.getCellText(rowNumber, tree.columns[columnNumber]);

// Click on a given cell in a tree:

const { mailTestUtils } = ChromeUtils.import(
  "resource://testing-common/mailnews/mailTestUtils.jsm"
);

mailTestUtils.treeClick(EventUtils, window, tree, rowNumber, columnNumber, {
  clickCount: 1
});
```

