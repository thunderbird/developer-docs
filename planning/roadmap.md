---
description: Planned work for the 2024 release of Thunderbird.
---

# Roadmap

Below are the large projects planned for the 2025-2026 releases of Thunderbird.

This is a list of all the primary objectives and efforts that we will be working on.

> [!IMPORTANT]
> While we have provided our *targeted* delivery quarter at the time of writing (March 2025), this is no guarantee and each feature they will be available when ready.

## Front-end Priorities

### Conversation View

Priority: <span style="background-color: #FF8080">P1</span>
Delivery Quarter: Q1 2025
High level objectives (non-exhaustive):
* Full thread conversation in message pane.
* Collapsible/Expandable messages.
* Thread tree visualization.
* Reply inline (optional).

### Calendar UI/UX
Priority: <span style="background-color: #FF8080">P1</span>
Delivery Quarter: Q2 2025
High level objectives (non-exhaustive):
* Modern event modals with flexible UI.
* Video conferencing integration.
* Maps searching integration (Google, Apple, OpenStreetMap).
* Modern HTML, markdown, plaintext editor.
* Customizable UI with sensible defaults.
* Customizable agenda and reminders.
* Better handling of unprocessed invitations.

### First Time User Experience
Priority: <span style="background-color: #FF8080">P1</span>
Delivery Quarter: Q2 2025
High level objectives (non-exhaustive):
* Account Hub for email, calendar, address book, feeds, and newsgroups.
* Thunderbird Sync as the first option.
* Guided discoveries of features when first accessing (welcome wizard).
* Early customization options to set the desired defaults.
* Expose important entry points like encryption and other more hidden settings.

### Unified Toolbar Completion
Priority: <span style="background-color: #FFFF99">P2</span>
Delivery Quarter: Q3 2025
High level objectives (non-exhaustive):
* Remove old customizable UI toolbar.
* Unifying controls for spaces, standalone dialogs, and windows.
* Add overflow space.
* Add more buttons and elements (spin buttons, separators, button groups, etc).
* Menu position.
* Rows.
* Drop the App Menu!

### Tabs
Priority: <span style="background-color: #FFFF99">P2</span>
Delivery Quarter: Q3 2025
High level objectives (non-exhaustive):
* Spaces and tabs rebuild, with each space handling their own tabs.
* Standalone windows per space without the need of having the "mail" space.

### OS System Tray
Priority: <span style="background-color: #FFFF99">P2</span>
Delivery Quarter: Q4 2025
High level objectives (non-exhaustive):
* Same tray code for Linux, Windows, and macOS.
* Unread indicator and counter.
* Minimize to tray.
* Start minimized.
* Compose action per account.
* Open settings.
* Quit action.

### Native OS Notifications
Priority: <span style="background-color: #FFFF99">P2</span>
Delivery Quarter: Q4 2025
High level objectives (non-exhaustive):
* Quick actions to reply, delete, mark messages.
* Pause notifications for # time.
* Manage notifications per account.
* Focus modes.

### Compose Window
Priority: <span style="background-color: #B3FFB3">P3</span>
Delivery Quarter: Q1 2026
High level objectives (non-exhaustive):
* Drop the C++ editor code.
* Implement a modern JS editor component that can be reused in calendar, tasks, and any other text area field that needs it (signature, address book notes, etc).
* Link previews.
* Rich text with advanced features.
* Markdown support and preview.
* Native emojis support (unicode).
* Mentions support.

### Settings UI
Priority: <span style="background-color: #B3FFB3">P3</span>
Delivery Quarter: Q1 2026
High level objectives (non-exhaustive):
* Modal dialog, because a settings tab doesn't make sense.
* More sections and tabs for an intuitive discovery path, drop the never ending single pages.
* All "‚Appearanc" settings in one page, including native themes (follow system, light, dark)
* Application settings and Account settings should be in the same place.
* "‚Labs" section for listing experiments

### Tasks
Priority: <span style="background-color: #B3FFB3">P3</span>
Delivery Quarter: Q2 2026
High level objectives (non-exhaustive):
* Easier onboarding! Store them in the profile and sync them if there are no supported calendars.
* Use open specs to allow interoperability.
* Be opinionated with what we do here.
* Modern UI.
* Show tasks in the calendar.
* Kanban board.
* Integrate into emails, address books, and other areas.

### Home Space
Priority: <span style="background-color: #B3FFB3">P3</span>
Delivery Quarter: Q3 2026
High level objectives (non-exhaustive):
* Stats!
* Quick links to common actions.
* Summarization of accounts.
* Blog listing (feed).
* Suggested actions.

### Chat Design Kickoff
Priority: <span style="background-color: #B3FFB3">P3</span>
Delivery Quarter: Q4 2026
High level objectives (non-exhaustive):
* Create design plan
* Identify areas with technical debt
* Prioritize existing features
* Prioritize new features


## Back-end Priorities

### Exchange Email
Priority: <span style="background-color: #FF8080">P1</span>
Delivery Quarter: Q1 2025
High level objectives (non-exhaustive):
* Full support of email operations.
* Full support of account creation.

### Global Database
Priority: <span style="background-color: #FF8080">P1</span>
Delivery Quarter: Q2 2025
High level objectives (non-exhaustive):
* SQLite based.
* Rust interfaces.
* Migrations.
* Rollbacks.
* Build in parallel and populate it alongside the current database.

### A New Email Encryption
Priority: <span style="background-color: #FF8080">P1</span>
Delivery Quarter: Q1 2026
High level objectives (non-exhaustive):
* Thunderbird should be the champion of encryption and propose a new open path.
* Making it easier for non-technical users to use a reliable encrypted solution.

### Sentry
Priority: <span style="background-color: #FFFF99">P2</span>
Delivery Quarter: Q1 2025
High level objectives (non-exhaustive):
* Add Sentry SDK & Initialise with DSN
* Add channel/environment
* Leverage default error capturing mechanisms
* Add Custom Breadcrumb for one component
* Configure reporting
* Test
* Document for future consumption in other components

### Performance
Priority: <span style="background-color: #FFFF99">P2</span>
Delivery Quarter: Q2 2025
High level objectives (non-exhaustive):
* Audit current startup code.
* Slowly rebuild it.
* Create performance profiles to measure startup performance impacts.
* Implement the "‚start minimized" back-end.
* Take ownership of the "primary password"‚ process

### Exchange Calendar
Priority: <span style="background-color: #FFFF99">P2</span>
Delivery Quarter: Q3 2025
High level objectives (non-exhaustive):
* Integrate the exchange calendar protocol.
* Use it as a way to rebuild/improve the current calendar protocol.
* Support exchange-specific features.

### Exchange Address Book
Priority: <span style="background-color: #FFFF99">P2</span>
Delivery Quarter: Q3 2025
High level objectives (non-exhaustive):
* Integrate the exchange address book protocol (maybe it uses carddav?)
* Support exchange-specific features.
* Fetch users' profile pictures.

### Exchange Graph API
Priority: <span style="background-color: #B3FFB3">P3</span>
Delivery Quarter: Q1 2026
High level objectives (non-exhaustive):
* Audit what can be supported via GraphAPI.
* Build a parallel protocol to A/B test against EWS.

### Notes
Priority: <span style="background-color: #B3FFB3">P3</span>
Delivery Quarter: Q2 2026
High level objectives (non-exhaustive):
* Store notes in the profile and in Sync.
* HTML, markdown, plaintext editor.
* Share notes via Send.
* Copy the Google approach (create notes for this event).
* Potential Pro offering (cloud notes with shared access like Etherpad).
* Potential syncing through IMAP.

## Maintenance Priorities

### Support URLs
Priority: <span style="background-color: #FF8080">P1</span>
Delivery Quarter: Q1 2025
High level objectives (non-exhaustive):
* Plan and implement an efficient and standard approach to links within the application

### Better logging/diagnostics
Priority: <span style="background-color: #FF8080">P1</span>
Delivery Quarter: Q1 2025
High level objectives (non-exhaustive):
* Implement Sentry and telemetry to capture data that will help us prioritize response to issues based on frequency.
* Add or change logging tools to make it easier for the majority of users to provide logs.

### Fluent Migration
Priority: <span style="background-color: #FFFF99">P2</span>
Delivery Quarter: Q2 2025
High level objectives (non-exhaustive):
* Assign a small migration effort every 2 weeks to each developer.

### Complete Glean testing and uplift
Priority: <span style="background-color: #FFFF99">P2</span>
Delivery Quarter: Q2 2025
High level objectives (non-exhaustive):
* Complete the project.
* Validation that data is being captured as expected.
* Help services consume the data.

### Hardening
Priority: <span style="background-color: #FFFF99">P2</span>
Delivery Quarter: Q3 2025

### Fuzzing and Monkey testing
Priority: <span style="background-color: #FFFF99">P2</span>
Delivery Quarter: Q3 2025
High level objectives (non-exhaustive):
* Work with Rob to create scripts, VMs, checking tools etc that we can have running regularly to catch edge cases much sooner without users having to experience then report.

### Remove IMAP JS
Priority: <span style="background-color: #B3FFB3">P3</span>
Delivery Quarter: Q3 2025
High level objectives (non-exhaustive):
* Remove unused code.
* Remove preference.
* Close all related open bugzilla bugs.

### Re-implement Movemail
Priority: <span style="background-color: #B3FFB3">P3</span>
Delivery Quarter: Q3 2025
High level objectives (non-exhaustive):
* Reimplement the ability to use Movemail in Thunderbird.
* No account creation feature for now.

### Removal of the \*Overlay.js files
Priority: <span style="background-color: #B3FFB3">P3</span>
Delivery Quarter: Q4 2025
High level objectives (non-exhaustive):
* Replace overlay files with modules and system modules.
* Defer non-DOM operations to system modules.
* Lazy load modules when needed.

# Out of scope

As we focus on the P1s, we need to always account for the dozens of other features we currently ship in Thunderbird.

These features always require **maintenance, regressions and security fixes, and upstream porting**.

What we won't do during this cycle is adding new features or rebuilding big chunks of code outside of the prioritized components and efforts listed in the front-end and back-end tables.

These "outsiders"" listed here are a way to highlight low priority maintenance efforts that can happen during the year, but that won't (shouldn't) take us away from the top priorities.

**Feeds**
* Inheriting the email UI for now is acceptable.
* It needs a complete UI and UX overhaul.

**Newsgroups**
* Low user adoption.
* Inheriting the email UI for now is acceptable.

**Add-ons page**
* A very large effort that will happen in the future.
* It's tied to the website rebuild.

**Address Book space**
* More improvements are needed but the current state is superior to other areas.
* A substantial rebuild happened 2 years ago.

**OpenPGP and S/MIME  integration**
* Currently in a usable and enough stable state.
* Dedicating security fixes during this cycle.
* The UI saw some improvements in the past few years.

**Message Filters**
* They will need to be rebuilt from scratch.
* Tied to the new database implementation for easier message move/copy handling.

**Import/Export**
* Sync will make the need to import/export the whole profile hopefully uncommon.
* After that we should disable exporting the entire profile because it fails when dealing with large sizes.

**Account Settings**
* They should be integrated into the Settings space.
* It needs a lot of UI/UX rethinking.
