---
description: Planned work for the 115 (2023) release of Thunderbird.
---

# Roadmap

Below are the large projects planned for the 115 (2023) release of Thunderbird.

## Firefox Sync

![Accounts and syncing options in 115.](<../.gitbook/assets/User recognition - Donor care.png>)

#### Expected Delivery: Q4 2022

Syncing between Thunderbird installations and the Android app should work seamlessly. Tags, filters, email accounts and relevants settings should be synced.

## Unified Toolbar

**Expected Delivery: Q4 2022**

A single, reusable, global toolbar that is present throughout the app and acts as a simple interaction point for searching, interacting with the app menu and tab management.

****

## **App Menu**

![The expanded app menu in 115.](../.gitbook/assets/account\_mail\_tab.png)

**Expected Delivery: Q3 2022**

A single menu for the app that replaces the current hamburger menu, but also handles accounts and syncing.

## Mail Tab Redesign

#### Expected Delivery: Q4 2022/Q1 2023

Update the Mail space with improved UX and UI, some of these enhancements include multi-line message list and "conversational" threads. It will be possible to reply to messages in the conversations area.

#### Depends On:

### "Conversational" View

**Expected Delivery: Q4 2022**

&#x20;By default, Thunderbird users should be able to see all messages in a thread as a "conversation". With each message in that thread collapsible and expandable, with all messages showing up in that thread regardless of what folder they exist within.

{% embed url="https://bugzilla.mozilla.org/show_bug.cgi?id=1686504" %}
Bug for conversations in Thunderbird
{% endembed %}

### Global Message Index

**Expected Delivery: Q4 2022**

In order to do Conversations correctly, we need to be able to keep track of what folders messages in a thread are in, so we can display them in a conversation. This means we need to have a database that keeps track of the location of all emails.

### 3Pane (replacing XUL tree)

**Expected Delivery: Q4 2022**

### Drop Window



## Link Preview Card Improvements

![Link Preview Card (WIP)](../.gitbook/assets/link-preview.png)

Link previews should be improved to be more useful and visually appealing.

{% embed url="https://bugzilla.mozilla.org/show_bug.cgi?id=1572648" %}
Link Preview Card tracking bug.
{% endembed %}

## Account Setup Hub

**Expected Delivery Q4 2022**

![The new Account Setup Hub](../.gitbook/assets/account-setup.png)

When setting up an email, autodiscovery of calendars and address books works really well! But managing accounts and setting up after the first time has lagged. We are updating that experience in 115 with a centralized place to set up accounts, with a consistent experience regardless of when you bring a new account into Thunderbird.

{% embed url="https://bugzilla.mozilla.org/show_bug.cgi?id=1664179" %}
Account setup hub tracking bug.
{% endembed %}

## Customizable Shortcuts

**Expected  Delivery: Q4 2022**

## Calendar UI

**Expected Delivery: Q1 2023**
