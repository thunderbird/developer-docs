---
description: Planned work for the 2024 release of Thunderbird.
---

# Roadmap

Below are the large projects planned for the 2024 release of Thunderbird.

## Primary Objectives

This is a list of all the primary objectives and efforts that we will try to complete for 2024. These are not currently tied to any particular beta release nor ESR release and they will be available when ready.

### Mozilla Account + Sync

[Bug 1833177 - \[meta\] Account Sync implementation](https://bugzilla.mozilla.org/show\_bug.cgi?id=1833177)

Implement Mozilla Account login and syncing server functionalities to synchronize accounts and settings across installations.

### **Exchange email support**

[Bug 1847846 - \[meta\] Implement Microsoft Exchange](https://bugzilla.mozilla.org/show\_bug.cgi?id=1847846)

Implement full support for Microsoft Exchange (EWS) into core for account create and email usage.

### **Global Message Database**

[Bug 1572000 - \[meta\] database backed global message index](https://bugzilla.mozilla.org/show\_bug.cgi?id=1572000)

Move away from the old Mork database and implement a real global message database, enabling **conversation view** and other modern paradigms.

### **Telemetry Heat Mapping**

Automatic generation of a visual heatmaps from telemetry clicks.

### **Cards View UI completion**

[Bug 1819710 - \[meta\] Implement a complete and usable cards message list for Vertical View layout](https://bugzilla.mozilla.org/show\_bug.cgi?id=1819710)

Finalize the cards view interface to implement all missing features and flexibility required.

### **Message list context menu**

[Bug 1875802 - Implement the new message context menu structure](https://bugzilla.mozilla.org/show\_bug.cgi?id=1875802)

Improve the context menu of the message list to make it more usable and digestible.

### **Dark reader email content**

[Bug 1715361 - Dark UI: automatically convert light message content to dark](https://bugzilla.mozilla.org/show\_bug.cgi?id=1715361)

Support full dark mode also for message content and properly handle images and graphics.

### **Folder Pane UI completion**

[Bug 1811366 - \[meta\] Supernova Folder Pane improvements](https://bugzilla.mozilla.org/show\_bug.cgi?id=1811366)

Complete the work of the new folder pane that was starting with the Supernova effort.

### **Account Hub**

[Bug 1789966 - \[meta\] Implement a new Account Hub in a dialog for all account creation workflows](https://bugzilla.mozilla.org/show\_bug.cgi?id=1789966)

Implement a single entry point for all account setups and offer a better guided setup experience for first time users.

### **Firefox Translate**

Opt in feature allowing users to translate email content.

### Notes

Standalone Rust component experiment of a notes implementation in Thunderbird.

## Maintenance Sprints

Alongside the primary objective for the year we will continue the effort of improving our codebase, keeping up with the changes upstream, as well as bug fixes and performance improvements.

Some of the efforts of this maintenance cycle are:

* **OpenPGP and S/MIME improvements and fixes**
* **Add-ons API Improvements**
* **Fluent Migration**
* **ESMification**

## Out of scope

As we focus on these primary objectives with the goal of bringing them to full completion, as well as making sure we have enough resources to tackle bug reports and regressions, we won't be dedicating any major commitment to the following areas of the application during this year.

* **Calendar**
* **Chat**
* **Tasks**
* **Settings**
* **Compose**
* **Search**
* **and everything else...**

We will always have dedicated resources allocated to tackle crashes or performance regressions caused by any of these components.
