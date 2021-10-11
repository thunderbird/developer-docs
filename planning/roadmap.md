---
description: Planning for Thunderbird 103 (2022) release and beyond.
---

# Roadmap

## Thunderbird 103+ Roadmap <a href="docs-internal-guid-eded1439-7fff-6f14-47e0-bc47fa85ed4f" id="docs-internal-guid-eded1439-7fff-6f14-47e0-bc47fa85ed4f"></a>

This provides a listing of maintenance and feature work wanted for Thunderbird 103 (2022) release and beyond. How much of it we can do for 103 depends on how many contributors we have available.

{% hint style="warning" %}
**Note: This is a work in progress - everything is subject to change.**
{% endhint %}

## Account setup

1. Account provisioner in a tab ([Bug 1700487](https://bugzilla.mozilla.org/show_bug.cgi?id=1700487))
2. Populate address book from mail history after setup ([Bug 1725007](https://bugzilla.mozilla.org/show_bug.cgi?id=1725007))
3. Move remaining setup dialogs (chat/filelink/nntp) to a tab. [Bug 1731221](https://bugzilla.mozilla.org/show_bug.cgi?id=1731221), [Bug 1731224](https://bugzilla.mozilla.org/show_bug.cgi?id=1731224) , [Bug 1731222](https://bugzilla.mozilla.org/show_bug.cgi?id=1731222) 
4. Expose option to import profile from backup. ([Bug 1731064](https://bugzilla.mozilla.org/show_bug.cgi?id=1731064))

## Address book

1. New address book UI: During the 91 cycle we already implemented the start of a new UI, in [about:addressbook](about:addressbook). The new address book lives in a tab. During the next cycle we will bring this implementation up to par with the old address book, and remove the old one. ([Bug 1705276](https://bugzilla.mozilla.org/show_bug.cgi?id=1705276))
2. Improved storage: The current backend needs to be changed to support vCard data properly; e.g. arbitrary number of email addresses. [Bug 469209](https://bugzilla.mozilla.org/show_bug.cgi?id=469209) 
3. Non-standard data fields like “preferDisplayName” and “ChatName” should be dropped or moved out of the address book. [Bug 1727298](https://bugzilla.mozilla.org/show_bug.cgi?id=1727298) 
4. Consider dropping the read-only OSX address book, and switch users over to using their read-write iCloud calendar through CardDAV. 
5. “Meet with this person”/”Show conversations with this person” support ([Bug 1638282](https://bugzilla.mozilla.org/show_bug.cgi?id=1638282), bug [Bug 1731065](https://bugzilla.mozilla.org/show_bug.cgi?id=1731065))
6. Contact consolidation: allow finding/merging duplicates ([Bug 1331249](https://bugzilla.mozilla.org/show_bug.cgi?id=1331249))

## Account manager

1. Fix NNTP setup: “Other accounts” is now only NNTP. [Bug 1727304](https://bugzilla.mozilla.org/show_bug.cgi?id=1727304) 
2. UI work towards per-account SMTP - global SMTP (use the ISP SMTP for all accounts) is an abnormality nowadays. [Bug 1731206](https://bugzilla.mozilla.org/show_bug.cgi?id=1731206) 

## Back-end

1. NNTP in JavaScript ([bug 1719413](https://bugzilla.mozilla.org/show_bug.cgi?id=1719413))
2. POP3 in JavaScript ([bug 1707548](https://bugzilla.mozilla.org/show_bug.cgi?id=1707548))
3. IMAP in JavaScript ([bug 1707547](https://bugzilla.mozilla.org/show_bug.cgi?id=1707547)); might still be preffed off for version 103
4. Message body parsing in JavaScript: Libmime is the cruftiest piece of code we have: it is hard to understand and its maintenance is error prone. The library is used all over the place, so to the extent it’s possible we should start converting code over one part at a time. A number of years back the MIME header parsing was converted over to using the JSMime JavaScript library The message body is still using libmime for parsing. We should get started with using JavaScript for the body parsing of MIME. Add an option to use it so dogfooding can find problems. This is tracked in [bug 1664150](https://bugzilla.mozilla.org/show_bug.cgi?id=1664150).
5. Replace Mork in panacea.dat (folder metadata) ([bug 418551](https://bugzilla.mozilla.org/show_bug.cgi?id=418551))
6. Replace Mork for message index ([bug 11050](https://bugzilla.mozilla.org/show_bug.cgi?id=11050)): create a global message index backed by a real database. Currently the index of messages is per folder (and based on Mork). This causes severe limitations to implementing a proper conversation view of messages. This work is tracked in [bug 1572000](https://bugzilla.mozilla.org/show_bug.cgi?id=1572000).
7. Finish Maildir support ([bug 845952](https://bugzilla.mozilla.org/show_bug.cgi?id=845952))
8. Compose back-end in JavaScript
9. Coverity fixes. [Bug 1668926](https://bugzilla.mozilla.org/show_bug.cgi?id=1668926) and others.
10. Interface improvements (use concrete types, e.g. not nsISupports in idls) ([Bug 1683236](https://bugzilla.mozilla.org/show_bug.cgi?id=1683236))

## Calendar

1. Redo the today pane agenda panel. Tracked in [bug 1728743](https://bugzilla.mozilla.org/show_bug.cgi?id=1728743)
2. Fix how the calendar widgets interact with the backend: this will facilitate code sharing. [bug 1727711](https://bugzilla.mozilla.org/show_bug.cgi?id=1727711)
3. Consider only having cached calendars - for performance. [Bug 1731115](https://bugzilla.mozilla.org/show_bug.cgi?id=1731115) 
4. ical.js: Putting effort into making ical.js performant enough will let us drop our very old fork of the C++ libical and focus on making the JavaScript implementation great. Tracked in [bug 978570](https://bugzilla.mozilla.org/show_bug.cgi?id=978570).
5. Support hierarchical tasks. Tracked in [bug 194863](https://bugzilla.mozilla.org/show_bug.cgi?id=194863).
6. Column for week numbers ([Bug 1705041](https://bugzilla.mozilla.org/show_bug.cgi?id=1705041))
7. Use system timezone. ([Bug 1609219](https://bugzilla.mozilla.org/show_bug.cgi?id=1609219))
8. Integrate something like the birthday calendar provider, linking contact birthdays to the calendar view. ([Bug 151994](https://bugzilla.mozilla.org/show_bug.cgi?id=151994))
9. Holiday calendar auto-setup. Allow users easy subscription to holiday calendar for their region. [Bug 1731117](https://bugzilla.mozilla.org/show_bug.cgi?id=1731117) 
10. iMIP event invitations improvements
    1. what changed is confusing
    2. make it more human, not so techy and formal
    3. allow setting RSVP status manually (of others), 
    4. For the organizer, the data should become accessible (e.g how many people \*are\* coming to my event in the end)
    5. add UI reminders when not responded
    6. See timezone for attendees when trying to schedule meetings. Timezone to be added to the contact information.
    7. check status on server \*before\* updating
11. Support “this and future” for recurring event editing. [Bug 320178](https://bugzilla.mozilla.org/show_bug.cgi?id=320178) 
12. Full support for counters (=invitee can suggest another time). [Bug 426532](https://bugzilla.mozilla.org/show_bug.cgi?id=426532).
13. Improve usability when interacting and viewing events: easier to see and interact with the data, e.g. tojoin a video call link. [Bug 1685007](https://bugzilla.mozilla.org/show_bug.cgi?id=1685007) 

![](https://lh4.googleusercontent.com/VIip5uEhdZ_VUU0-\_42pWuSZO3h6cxPCAZ8KAfqma-H\_\_ZQM0DrxqRAU1d6OL8wJMeVjhaohqG8dqK9m2CIckFwHLEnqVvVEb82M0w9q_Od3BpB7e9\_r3E-WrMo8jbX5lv5S1oFM=s0)

  14\. Improve the keyboard accessibility of the entire Calendar tab: [bug 1717692](https://bugzilla.mozilla.org/show_bug.cgi?id=1717692)\
  15\. Implement a date picker when calendar sidebar is collapsed (no mini month quick access):\
 [        bug 1717692](https://bugzilla.mozilla.org/show_bug.cgi?id=1717692)

## Composition

1. Investigate inlining styles of replies so that styling is contained within the quoted text. [Bug 1731198](https://bugzilla.mozilla.org/show_bug.cgi?id=1731198) 
2. Groups support ([Bug 83521](https://bugzilla.mozilla.org/show_bug.cgi?id=83521), [Bug 110605](https://bugzilla.mozilla.org/show_bug.cgi?id=110605))
3. Smart templates/snippets/pre-canned responses ([Bug 271553](https://bugzilla.mozilla.org/show_bug.cgi?id=271553))
4. Break thread on new subject. [Bug 369068](https://bugzilla.mozilla.org/show_bug.cgi?id=369068)  
5. Hook up language detection. [Bug 1731203](https://bugzilla.mozilla.org/show_bug.cgi?id=1731203) 
6. oEmbed/Twitter Card/Open Graph support: [bug 1572648](https://bugzilla.mozilla.org/show_bug.cgi?id=1572648)
7. Support markdown - and extensions to it allowing nice graphs etc.: [bug 1280912](https://bugzilla.mozilla.org/show_bug.cgi?id=1280912)
8. Editor dialog removal - we have too many “web page element editing” type dialogs which make very little sense when editing mails. [Bug 1731213](https://bugzilla.mozilla.org/show_bug.cgi?id=1731213) 
9. Support more emoticons/emojis. [Bug 1727498](https://bugzilla.mozilla.org/show_bug.cgi?id=1727498) 
10. Implement undo/redo button for compose toolbar: [Bug 1490397](https://bugzilla.mozilla.org/show_bug.cgi?id=1490397)

## Chat

1. Matrix support ([Bug 1712710](https://bugzilla.mozilla.org/show_bug.cgi?id=1712710))
2. Matrix provisioning - allow people to sign up for hosted Matrix from within Thunderbird.
3. UI renewal
4. Remove/rework the special chat accounts dialog. [Bug 1731225](https://bugzilla.mozilla.org/show_bug.cgi?id=1731225) 

## Filelink

1. Support WebDAV ([Bug 1532957](https://bugzilla.mozilla.org/show_bug.cgi?id=1532957))
2. Make filelinks work more like normal attachments: adjust the inserted “ad”, and have the fake file be something useful. [Bug 1670791](https://bugzilla.mozilla.org/show_bug.cgi?id=1670791)

## Filtering

1. Rewrite filtering in JavaScript. Tracked in [bug 1574136](https://bugzilla.mozilla.org/show_bug.cgi?id=1574136).
2. Support “to filesystem” actions ([Bug 1664171](https://bugzilla.mozilla.org/show_bug.cgi?id=1664171))

## Fission phase 2

1. Multiprocess utilization for calendar. [Bug 1731227](https://bugzilla.mozilla.org/show_bug.cgi?id=1731227) 

## Front-end

1. Replace the 3pane implementation. Instead of one global data view we should have one view per tab. [bug 1729379](https://bugzilla.mozilla.org/show_bug.cgi?id=1729379)
2. Convert xul tree usage to html widgets. [bug 1724841](https://bugzilla.mozilla.org/show_bug.cgi?id=1724841)
   1. folder pane
   2. thread pane
3. XUL replacements ([Bug 1570943](https://bugzilla.mozilla.org/show_bug.cgi?id=1570943))
   1. Top level HTML. Convert \<window> over to \<html> everywhere. [Bug 1703164](https://bugzilla.mozilla.org/show_bug.cgi?id=1703164) 
   2. Replace elements and attributes that can’t be web compatible. Aim to have documents that would gracefully degrade if viewed without XUL.
   3. Continue replacing elements that have good HTML replacements (exactly which to take on first needs investigation)
4. Threaded (conversational) view of messages
5. Consider changing default thread/message order - newest on top? Threaded view by default.
6. Parking of messages (prevent marking read or deleting until unparked) [Bug 503064](https://bugzilla.mozilla.org/show_bug.cgi?id=503064)  
7. Firefox Monitor integration - let people know if their account was exposed in a data breach. [Bug 1731228](https://bugzilla.mozilla.org/show_bug.cgi?id=1731228) 
8. Mail meta-data exposure (micro-data, JSON-LD)
9. TODO tag -> Task integration. [Bug 380814](https://bugzilla.mozilla.org/show_bug.cgi?id=380814) 
10. IMAP notes? Storing text notes on the IMAP server would be useful, and the feature is low hanging fruit not necessarily very hard to implement. Tracked in [bug 713843](https://bugzilla.mozilla.org/show_bug.cgi?id=713843).
11. Printing CSS (mostly) improvements: make the printed mails/calendars/address books look nice.
12. Spaces toolbar: Implement an easier and more convenient way to move between the different parts of the application (mail, calendar, tasks etc.) Tracked in [bug 1665511](https://bugzilla.mozilla.org/show_bug.cgi?id=1665511).
13. Rebuild the message header. The message header has a bit of messy source code, and with the removal of the customizable toolbar, it seems necessary to do a good clean up and rethink the UI to better highlight sections and make the whole area properly responsive. Mock-up: ![](https://lh6.googleusercontent.com/cYI0A2UKQD-QoJRVoQ0LifcOnzYw4PKMkSBwmK1FXPMU1iwAuHRrygcO7VUbH9WUN8IQUvvLJlBoqF7nQQa4C55Uk7Lh-yQ66dlbYGbZffL_B7RktcnV2AyuQ0F-XEN2GNS7JVpw=s0)
14. Mail data utilization and integration
    1. detect structured data (JSON-LD), allow acting on that data (see schema.org, xref KItinerary) — e.g. let you add flight data into your calendar directly
    2. detect micro-formats \[related to smart-widgets]
    3. detect the most common free texts (especially: times and dates), and allow inline viewing of calendar schedules for that time, or contact information for a known contact in your address book etc.
    4. improved support for drag-n-drop as well as copy-paste of many data types: mail data, events, contacts
15. Implement a new icon theme with symbolic and hicolor variation, and predefined color schemes for the folder pane icons users can select.
16. Make more areas of the UI be sensitive to the Density pref.
17. Implement a UI pref to change the font size of the entire app.
18. Implement a more solid design system with consistent colors, spacings, font-sizings, etc., in order to be more independent from the variable changes coming from m-c and reduce the amount of visual regressions and upstream syncs.
19. Support mailing list management headers (List-Unsubscribe, List-Archive, List-Post, List-Archive). [Bug 29041](https://bugzilla.mozilla.org/show_bug.cgi?id=29041).

## Import/Export

1. Import/export of profiles ([Bug 271863](https://bugzilla.mozilla.org/show_bug.cgi?id=271863))
2. Import/export account ([Bug 599631](https://bugzilla.mozilla.org/show_bug.cgi?id=599631))
3. Improved UI: [Bug 1720042](https://bugzilla.mozilla.org/show_bug.cgi?id=1720042) 
4. Import/export folder ([Bug 752295](https://bugzilla.mozilla.org/show_bug.cgi?id=752295))
5. Exports through filters ([Bug 1664171](https://bugzilla.mozilla.org/show_bug.cgi?id=1664171))

## Localization

For localization Firefox is moving ([https://arewefluentyet.com/](https://arewefluentyet.com)) to Fluent, and Thunderbird should follow.\


Firefox has officially deprecated the old style localization system since October 2019. Thunderbird should do the same: i.e. adding/changing strings only allowed for Fluent strings, with properties/dtd files basically freezed.\


This is tracked in [Bug 1492751](https://bugzilla.mozilla.org/show_bug.cgi?id=1492751).

1. Fluent-migrations for the main windows

## MailExtensions

1. Add iframes to the Thunderbird UI where WebExtensions need to load custom content, similar to the sidebar of Firefox. This could be added as a dedicated API or per use-case to the individual APIs (calendar, addressBook, mailTabs), see [bug 1693543](https://bugzilla.mozilla.org/show_bug.cgi?id=1693543).
2. Add a DNS API for advanced DNS requests (not just A records).
3. Add a Sessions API to allow WebExtensions to store/access session data (similar to what is provided by Firefox).
4. Work on internal message handling, so WebExtension can identify messages across sessions with the same id.
5. Allow for additional customization and add an overflow container for action buttons, if UI space is exhausted.
6. Support better theming/UI integration of WebExtension option tabs
7. Add an API for calendar access and providing new calendars to Thunderbird ([bug 1627205](https://bugzilla.mozilla.org/show_bug.cgi?id=1627205))
8. Rework the contacts APIs to fix existing gaps like missing photo access, align with the new address book and permit add-ons to provide their own address books.
9. Figure out a way forwards for file system access (Chrome FileSystem API?) together with Mozilla core.

## OpenPGP

1. Encrypt if possible (guided TOFU): Like SSH, we should move more towards trust on first use (TOFU). This would be more feasible if all incoming keys were automatically collected for possible usage later ([Bug 1667564](https://bugzilla.mozilla.org/show_bug.cgi?id=1667564)) - at the moment without chores for the user, it does not work since the keys are not “known”, even if they have been sent to you. [Bug 135636](https://bugzilla.mozilla.org/show_bug.cgi?id=135636)/ [bug 1627956](https://bugzilla.mozilla.org/show_bug.cgi?id=1627956).
2. Publish to keyserver: We should allow users to publish their key on the openpgp keyserver for others to discover. [Bug 1731232](https://bugzilla.mozilla.org/show_bug.cgi?id=1731232) 
3. Filters actions to decrypt/encrypt. ([Bug 1627962](https://bugzilla.mozilla.org/show_bug.cgi?id=1627962))
4. More automated tests
5. Code cleanup: The code needs cleanup, e.g. to remove old code that we don’t use or that is no-op for other reasons.
6. Redesign the OpenPGP key manager and make it live in a tab instead of a dialog. [Bug 1731231](https://bugzilla.mozilla.org/show_bug.cgi?id=1731231) 
7. UX improvements to make it easier to use encryption: No internal knowledge of how OpenPGP works should be required to use encryption (setup should educate what’s needed).

## Engineering

1. One repo - Thunderbird code to be in a single repository on a comm branch. [Bug 1666242](https://bugzilla.mozilla.org/show_bug.cgi?id=1666242) 
2. Track files that are largely copies of mozilla-central files ([bug 1666672](https://bugzilla.mozilla.org/show_bug.cgi?id=1666672))
3. Phabricator - let’s try to get everybody using this...
