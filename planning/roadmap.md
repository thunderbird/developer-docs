---
description: Planned work for the 102 (2022) release of Thunderbird.
---

# Roadmap

Below are the large projects planned for the 102 (2022) release of Thunderbird.

## Spaces Toolbar

![New Spaces Toolbar can be seen on the left-hand side of the Thunderbird window.](../.gitbook/assets/spaces\_toolbar.png)

The Spaces toolbar allows users an easier, more convenient way to move between the different parts of the application (such as mail, calendar, tasks and full-tab add-ons).

{% embed url="https://bugzilla.mozilla.org/show_bug.cgi?id=1665511" %}
Link to tracking bug for Spaces Toolbar
{% endembed %}

## New Address Book

![Mockup of the new address book](../.gitbook/assets/new\_address\_book.png)

A new address book (both backend and UX/UI) that makes it easier to navigate and provides a richer experience for interacting with contacts.

{% embed url="https://bugzilla.mozilla.org/show_bug.cgi?id=1705276" %}
Meta bug for the new address book
{% endembed %}

## Link Preview Card

![Link Preview Card (WIP)](../.gitbook/assets/link-preview.png)

When a link is added in the composer, you will have the option to convert it into a rich link preview with content displayed from the website.

{% embed url="https://bugzilla.mozilla.org/show_bug.cgi?id=1572648" %}
Link Preview Card tracking bug.
{% endembed %}

## Account Setup Hub

![The new Account Setup Hub](../.gitbook/assets/account-setup.png)

When setting up an email, autodiscovery of calendars and address books works really well! But managing accounts and setting up after the first time has lagged. We are updating that experience in 102 with a centralized place to set up accounts, with a consistent experience regardless of when you bring a new account into Thunderbird.

{% embed url="https://bugzilla.mozilla.org/show_bug.cgi?id=1664179" %}
Account setup hub tracking bug.
{% endembed %}

## Import/Export

![Import/Export UI Mockup](../.gitbook/assets/import\_export\_tool.png)

We have many users that have been using the Import/Export Tools add-on, we decided to bring that functionality into Thunderbird (and it has been requested for 18 years, check out the bug).  This will allow users more data portability and the ability to more easily bring their data into Thunderbird (as well as take it out).

{% embed url="https://bugzilla.mozilla.org/show_bug.cgi?id=271863" %}
Tracking bug for Import/Export.
{% endembed %}

## Message Header Redesign

![New Message Header](../.gitbook/assets/message-header.png)

Improved message header to better highlight important information and make it responsive, so it will look better at any screen size/window size.

{% embed url="https://bugzilla.mozilla.org/show_bug.cgi?id=1556261" %}
Message header tracking bug.
{% endembed %}

## Maildir

Maildir is a message storage format that should improve data safety, allow for incremental (delta) backups of messages and allow for antivirus to interact better with messages. Maildir will be vastly improved for 102, but may not be pref'd on by default.

{% embed url="https://bugzilla.mozilla.org/show_bug.cgi?id=845952" %}

## Protocol Rewrites

### POP3 in Javascript

{% embed url="https://bugzilla.mozilla.org/show_bug.cgi?id=1707548" %}
Meta bug for rewriting POP3 in Javascript
{% endembed %}

### NNTP in Javascript

{% embed url="https://bugzilla.mozilla.org/show_bug.cgi?id=1719413" %}

