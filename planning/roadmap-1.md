---
description: Planning for Thunderbird 91 (2021) release and beyond.
---

# Roadmap

## Thunderbird 91+ Roadmap <a id="docs-internal-guid-eded1439-7fff-6f14-47e0-bc47fa85ed4f"></a>

This provides a listing of maintenance and feature work wanted for Thunderbird 91 \(2021\) release and beyond. How much of it we can do for 91 depends on how many contributors we have available.

{% hint style="warning" %}
**Note: This is a work in progress - everything is subject to change.**
{% endhint %}

### Address book changes \[TB78-TB91\]

The address book needs a big time refresh. We will design the address book so that it could work as a general utility, not only inside Thunderbird although Thunderbird is the primary consumer of it.

#### Frontend

* The new address book would live inside a tab, like calendar, tasks, and chat currently do. 
* UI changes to support vCard v4, e.g. specifying an arbitrary number of email addresses.
* Convergence to make the fields available match what can be stored in vCard v4 \(we have a few things that don’t; "ChatName", "preferDisplayName".\)

It should use web technologies and not rely on XUL widgets - the new front-end is intended to serve as a testing ground for finding solutions that we will need also elsewhere in the UI as we converge towards using HTML.

#### Backend

Finish CardDAV support \(tracker bug: [bug 546932](https://bugzilla.mozilla.org/show_bug.cgi?id=546932)\).

Get rid of the C++ address book providers.

* Create JavaScript replacement for LDAP, but only implement the few features we use. This would allow dropping significant amounts of old C++ code.
* Consider dropping the read-only OSX address book, and switch users over to using their read-write iCloud calendar through CardDAV.
* The Windows address book has only ever been available behind a pref. Consider dropping it completely.

New address book tracked in [bug 841598](https://bugzilla.mozilla.org/show_bug.cgi?id=841598).

### Fluent migration \[TB78 and beyond\]

For localization Firefox is moving \([https://arewefluentyet.com/](https://arewefluentyet.com/)\) to Fluent, and Thunderbird will follow. 

Firefox has officially deprecated the old style localization system since October 2019. Thunderbird should do the same: i.e. adding/changing strings only allowed for Fluent strings, with properties/dtd files basically freezed. 

This is tracked in [bug 1492751](https://bugzilla.mozilla.org/show_bug.cgi?id=1492751).

### de-XUL and web tech conversions \[TB91 and beyond\]

Thunderbird will move away from XUL documents and towards top-level \(X\)HTML windows. Thunderbird has over 200 top level documents, so this is a significant effort. Hopefully some scripting will help in pulling it through. 

In parallel, the conversion from individual XUL elements to corresponding HTML ones will continue.

Thunderbird will no longer add new files using top level XUL.

The messenger.xhtml effort is tracked in [bug 1570943](https://bugzilla.mozilla.org/show_bug.cgi?id=1570943).

### Protocols and accounts

#### Protocols in JavaScript \[TB91/TB2022\]

Implement one protocol in JavaScript, and make it an option to use that implementation. The prime candidate to start with is SMTP.

Tracked in bug [1661694](https://bugzilla.mozilla.org/show_bug.cgi?id=1661694).

#### Drop Movemail \[TB91\]

We should drop support for Movemail and clean up the account settings UI that is currently complicated by this. \(“Other accounts -&gt; Add News Account”\)

Tracked in [bug 1625741](https://bugzilla.mozilla.org/show_bug.cgi?id=1625741).

#### JMAP support \[TB2022\]

Implementing JMAP \(RFC 8620\) support at an early stage could be useful for getting experience in \(re-\)implementing protocols, without worrying too much about existing limitations, and at the same time obtain support for JMAP.  It would also help move JMAP forward as a first rate protocol, and perhaps give us bragging rights as the first [major email client to provide suppor](https://jmap.io/software.html)t.

Tracked in [bug 1322991](https://bugzilla.mozilla.org/show_bug.cgi?id=1322991).

### Backend

#### Kill Mork \[TB78-TB91-TB2022\]

The odd Mork file/”database” format is very unapproachable, and hard to understand. We should pay away this technical debt to finally get rid of it, paving the way for more modern approaches, and enabling consistent, proper async database operations. Mork is used for

* Address book data: tracked in [bug 382878](https://bugzilla.mozilla.org/show_bug.cgi?id=382876) \[DONE\]
  * Mork has been removed from the Address Book but while the code still exists it will be able to import Mork address book files \(see [bug 1664359](https://bugzilla.mozilla.org/show_bug.cgi?id=1664359)\).
* Panacea.dat: tracked in [bug 418551](https://bugzilla.mozilla.org/show_bug.cgi?id=418551)
* Folder message indexing data \(cache\): tracked in [bug 11050](https://bugzilla.mozilla.org/show_bug.cgi?id=11050)

To enable data migration, complete Mork removal would happen once a major Mork-free release is done.

Tracked in [bug 453975](https://bugzilla.mozilla.org/show_bug.cgi?id=453975).

#### Global Index for Messages \[TB91/TB2022\]

Currently the index of messages is per folder \(and based on Mork\). This causes severe limitations to implementing a proper conversation view of messages. Especially for Gmail, there is also message duplication on the file system since their All Mail folder has all mails, but the other folders do not know about this… For our special message views \(Unified Inbox etc.\) and search folders, lack of a cross folder database makes the implementations far more complicated than they should be.

This work is tracked in [bug 1572000](https://bugzilla.mozilla.org/show_bug.cgi?id=1572000).

#### Progress with libmime removal  \[TB91/TB2022\]

Libmime is the cruftiest piece of code we have: it is hard to understand and its maintenance is error prone. The library is used all over the place, so to the extent it’s possible we should start converting code over one part at a time. A number of years back the MIME header parsing was converted over to using the JSMime JavaScript library, but the message body is still using libmime for parsing.

Message sending is very entangled with libmime. For Thunderbird 90 we will finish the rewrite of sending in JavaScript. This work is tracked in [bug 1211292](https://bugzilla.mozilla.org/show_bug.cgi?id=1211292). 

After that, we should also get started with using JavaScript for the body parsing of MIME. Add an option to use it so dogfooding can find problems. This is tracked in [bug 1664150](https://bugzilla.mozilla.org/show_bug.cgi?id=1664150).

#### Investigate Microsoft TNEF support \[TB2022\]

Investigate if integrating TNEF support can be integrated cleanly enough. If it can, we could integrate it. If not, we should take the needed steps to make it possible.

Tracked in [bug 77811](https://bugzilla.mozilla.org/show_bug.cgi?id=77811).

#### Maildir \[TB78/TB91\]

Our maildir support is pretty good by now. Finish up the last things needed to have maildir \(our one file per message storage\) on by default for new accounts.

Tracked in bug [bug 845952](https://bugzilla.mozilla.org/show_bug.cgi?id=845952)

#### Typed Array adoption  \[TB78/TB91\]

Using Array&lt;T&gt; instead of nsIArray in .idl files allows using XPCOM arrays as native JavaScript arrays. This is very helpful when rewriting a C++ component in JavaScript. It also gives nicer, safer, more modern code on the C++ side.

There are similar adjustments needed to get rid of some enumerators that are really arrays.

Tracked in [bug 1583030](https://bugzilla.mozilla.org/show_bug.cgi?id=1583030)

### Thunderbird Fission \[TB91\]

Fission is the next step of multi-process support from Firefox, where even different origins are separated into their own processes. Thunderbird needs to adapt its code to Fission, since eventually Fission will not be optional anymore. These architectural changes should also help with boosting performance.

Tracked in [bug 1646648](https://bugzilla.mozilla.org/show_bug.cgi?id=1646648)

### Mail front-end \(-ish\) work

#### Better offline and slow network support \[TB2022\]

We should provide better offline and slow-network support \(use local data first; performance boost\). Leverage existing browser level caching and offline support mechanisms to achieve this. To be able to use those, this means data access through HTTP or pre-fetched into an IndexedDB database.

Requires investigation of feasibility.

#### Thread Pane improvements \[TB2022\]

* Implementing a better vertical layout by exploring the possibility of not using the &lt;tree&gt; XUL element and relying on a highly scalable and equally performant HTML component.
  * A first step before doing this will be to explore how to do it in the \(new\) address book view, which normally don’t have quite the same performance requirements.
* Implement a “density” toggle to allow users to switch between “relaxed”, “normal”, and “compact” mode.

#### Folder pane usefulness improvements \[TB91\]

The folder pane should allow mixing multiple folder modes like Outlook. Thunderbird  already supports different folder modes, but it’s currently one at a time. It would be much more useful to be able to show a section with \(e.g.\) Favorites on top, and All Folders beneath it. Allow the user to choose which modes to put where. Tracked in [bug 1163555](https://bugzilla.mozilla.org/show_bug.cgi?id=1163555).

Allow reordering of accounts. Tracked in [bug 418551](https://bugzilla.mozilla.org/show_bug.cgi?id=418551).

#### Moving chrome UI to content UI \[TB2022\]

Thunderbird top level UI is currently part of the chrome UI. With the target set on becoming a super fat privileged web application, this needs to be rethought: web applications live in content. It’s also become clear that the platform support for many features one can use in a web page using web technologies is implemented in a way essentially requiring the “page” to be located in a browser element \(e.g. form controls and validation\), and accessed through http \(localStorage etc.\). We should investigate to what degree we can move our chrome UI basically into a tabbrowser. Making sure we’re treated as an http accessed web page will enable many features without hacks: localStorage, IndexedDB, CSP etc. - even simple functionality such as opening new windows have caused breakage recently.   


For add-ons wanting good access to modifying the UI, this would be a significant win, since WebExtensions are primarily designed for modifying content. 

Requires investigation of feasibility.

#### Separation of chrome UI and content, redesigned content data access \[TB2022\]

Internally Thunderbird is using mailbox: and imap: etc. URI references to access the mail data. This area has proven to be very problematic. To eliminate much of these problems, we should separate out the data access providing that through IndexedDB database or over an HTTP internal API instead. This will make back-end/front-end separation much cleaner, and of course more webby.

Requires investigation of feasibility. But can fairly easily be done in pieces.

### Compose window modernization \[TB78/TB91/TB2022\]

The editor will move to using web compatible composition. In Thunderbird 78 some of the formatting actions already started using document.execCommand, but there are more to do to finish this work. The goal is that editor bugs affecting editor on the web should also reproduce in Thunderbird, and vice versa.

Other features:

* oEmbed/Twitter Card/Open Graph support: [bug 1572648](https://bugzilla.mozilla.org/show_bug.cgi?id=1572648)
* Support markdown - and extensions to it allowing nice graphs etc.: [bug 1280912](https://bugzilla.mozilla.org/show_bug.cgi?id=1280912)
* Refactor the text editor to remove as many dialogs as possible \(links, colours, tables, etc\) and offer a cleaner and inline editing experience.
* Attachment pane improvements and unification with compose: [bug 1640760](https://bugzilla.mozilla.org/show_bug.cgi?id=1640760)
* Make it easier to clear styles, so that user’s don’t need to worry about mixed font sizes etc.: [bug 1667372](https://bugzilla.mozilla.org/show_bug.cgi?id=1667372)

### Spaces toolbar \[TB91\]

Implement an easier more convenient way to move between the different parts of the application \(mail, calendar, tasks etc.\)

Tracked in [bug 1665511](https://bugzilla.mozilla.org/show_bug.cgi?id=1665511).

### OpenPGP support \[TB78/TB91 and beyond\]

Encryption is an increasingly important part of email communication.

Thunderbird 78 added basic OpenPGP support, but there is still much much to be improved to make it easy to use, and to handle more complex setups.

The OpenPGP code needs more automated tests. The code also needs cleanup, e.g. to remove old code that we don’t use or that is no-op for other reasons.

Like SSH, we should move more towards trust on first use \(TOFU\). This would be more feasible if all incoming keys were automatically collected for possible usage later - at the moment without chores for the user, it does not work since the keys are not “known”, even if they have been sent to you. We should allow users to publish their key on keyservers for others to discover.

We should also implement the “encrypt-if-possible” mode \([bug 135636](https://bugzilla.mozilla.org/show_bug.cgi?id=135636)\). If keys are \(thought to be\) available for all recipients, in this mode mails would be sent encrypted, but would be sent unencrypted if they are not.  A wizard type approach would guide the user to sending the email encrypted in case we have known keys collected, that would now be used for the first time. Should also consider “upselling” use of encryption where it would be possible.

Correctness fixes.

UX improvements to make it easier to use encryption. If keys weren’t already configured for the recipients users should be properly guided through the process. No internal knowledge of how OpenPGP works should be required.

Implement filter actions to permanently decrypt messages \([bug 1627962](https://bugzilla.mozilla.org/show_bug.cgi?id=1627962)\)

Redesign the OpenPGP key manager and make it live in a tab instead of a dialog.

Meta tracker: [bug 22687](https://bugzilla.mozilla.org/show_bug.cgi?id=22687).

### UTF-8 convergence \[TB91\]

Wherever possible we should use UTF-8. This ensures message data is maximally compatible over time. It also simplifies the code base. Further, native JavaScript can’t encode to random charsets. As we move more code to using JavaScript using other charsets causes needless complications. 

To support convergence server side a future with UTF-8 supporting servers should be encouraged.  


* Outgoing mail will be UTF-8 only \([bug 862292](https://bugzilla.mozilla.org/show_bug.cgi?id=862292)\)
* Remove needless view charset overrides. \([bug 1671880](https://bugzilla.mozilla.org/show_bug.cgi?id=1671880)\)
* Support for SMTPUTF8 - RFC 6531 - \([bug 1563891](https://bugzilla.mozilla.org/show_bug.cgi?id=1563891)\)
* Support for UTF-8 IMAP store - RFC 6855 - \([bug 1571672](https://bugzilla.mozilla.org/show_bug.cgi?id=1571672)\)

### Improved account setup flow \[TB91\]

Thunderbird 78 already took the first steps towards an account setup that lets users set up all their services in a central location. 

Going forwards Thunderbird should guide the user through an account setup process that sets up all the services they will need. The email address is often the connector that makes it possible to find mail + caldav calendars, + cardbook addresses, and more.

The account setup flow will move to a tab instead of a myriad of dialogs \(about:accounthub\).

Tracked in [bug 1664179](https://bugzilla.mozilla.org/show_bug.cgi?id=1664179) 

### Support for advanced addressing features \[TB91/TB2022\]

Now that the compose window is using pills for addressing, it’s now possible to implement some addressing features that would have been difficult earlier

* Mail Redirect support \(in the UI\) - backend support already exists. This is a fancy forward that puts you out of the loop.
* Consider Resend/bounce support
* Consider using RFC 5322 group addresses syntax support
  * Useful when sending to many people while wanting to keep the addresses private.

### Calendar

#### Libical removal \[TB91\]

Putting some effort into making ical.js performant enough will let us drop our very old fork of the C++ libical and focus on making the JavaScript implementation great. For Thunderbird 90 ical.js would be on by default, but the old libical could be enabled if necessary. After Thunderbird 90, libical will be removed completely.

Tracked in [bug 978570](https://bugzilla.mozilla.org/show_bug.cgi?id=978570).

#### Performance improvements \[TB2022 and beyond\]

The calendar is eating a lot of the Thunderbird resources. Move the calendar to its own process, and figure out how it would communicate with the rest of the application then. Fission?

There are also many individual cases that could be tracked down through performance profiling.

#### XPCOM removals \[TB91/TB2022\]

Remove use of all calendar XPCOM components and replace them with simple JavaScript classes where possible. Reduce or eliminate XPCOM dependencies.

#### Improved, prettier email based scheduling \(iMIP\) \[TB91\]

Event invite display will get an UX overhaul.

* Event descriptions to support HTML display \[DONE\]
* The current very technical table of event details will be replaced with a view that is more human oriented. 
* For the organizer, the data should become accessible \(e.g how many people \*are\* coming to my event in the end\)
* Fix display of what’s changed for incoming events \(current display is often confusing\)
* Reminders for RSVPs not replied to.
* Support for counters \(ability to suggest another meeting time\)

Adding events directly to emails will also be made possible, and the UI for inviting to events vs. sending mails should converge.

#### Soft auto-reminders for all events \[TB91\]

The reminder feature is very aggressive. While sitting in front of the computer, we should show a gentle system notification that a meeting is starting, and allow joining that meeting if it’s online.

Tracked in bug [1497434](https://bugzilla.mozilla.org/show_bug.cgi?id=1497434).

#### Improved collaboration across timezones \[TB91/TB2021\]

Setting up events across timezones should be easy. Seeing the local time for the other event attendees would make things easier. The data is already more or less available to Thunderbird. 

### Chat

#### Matrix support  \[TB78/TB91\]

[The Matrix network](https://matrix.org/) should be properly supported, allowing a Thunderbird teams type setup. 

Tracked in [bug 1572636](https://bugzilla.mozilla.org/show_bug.cgi?id=1572636).

#### Chat UI facelift \[TB91\]

The chat UI needs to be modernized. Compared to other parts, say calendar/tasks/addressbook it’s very “glued on”. 

A sidebar to have in any windows would be nice for 1:1 type of communication.

### Smart workflow widgets \[TB2022\]

Thunderbird should support smart widgets for voting, time scheduling. These could be built around micro-formats. For teams using Thunderbird only everything would work without problems \(all over email\). If someone uses another client, some manual actions would still be required. Fallback would still be pure text, so adding more structural data would give additional benefits only..

One way this would work for events is that we mark up the emails we send out with some structural data, say an hCalendar. When replying to a message, we check if the original mail had included an hCalendar, and can offer quick options to reply, and including that for that hCalendar event you are an attendee. It can also link to the UUID of an existing event in your calendar and offer response options related to that, e.g. check if you’d have conflicts in your schedule.

For things like simple votes, be smart and let a \[vote\]\(emoji\) in the subject mean there’s a vote going on. Upon request by the user, create a voting summary and clean it up into a nicely formatted output. Automate the things that take some time to copy-paste from many mails, but could easily be automated. Make the Thunderbird users look good.

### Mail data utilization and integration \[TB91/TB2022\]

* detect structured data \(JSON-LD\), allow acting on that data \(see schema.org, xref [KItinerary](https://www.volkerkrause.eu/2018/08/25/kde-itinerary-overview.html)\) — e.g. let you add flight data into your calendar directly
* detect micro-formats \[related to smart-widgets\]
* detect the most common free texts \(especially: times and dates\), and allow inline viewing of calendar schedules for that time, or contact information for a known contact in your address book etc.
* improved support for drag-n-drop as well as copy-paste of many data types: mail data, events, tasks, contacts

### Data fetcher \[TB???\]

Support the use case where a mail arrives only to give you a notification and a link. Allow Thunderbird to go grab the relevant data from that link and put it back into a message for convenient reading and archiving.

This is enabling the global data stream to be inside Thunderbird without much knowledge about the external forums etc. The feature will require some technical knowledge at least at first, but recipes on how to get data for various sources could later be created and shared by power users.

### Data access: import/export \[TB91\]

Add real support to import/export profiles, or single accounts. Look at the import/export tools add-on for inspiration.

* Export folder/account: tracked in [bug 752295](https://bugzilla.mozilla.org/show_bug.cgi?id=752295) 
* Import/restore folder/account: tracked in [bug 247312](https://bugzilla.mozilla.org/show_bug.cgi?id=247312)
* Import profile: tracked in [bug 63389](https://bugzilla.mozilla.org/show_bug.cgi?id=63389)
* Further email data exports through filter actions: tracked in [bug 1664171](https://bugzilla.mozilla.org/show_bug.cgi?id=1664171) 

### IMAP notes \[TB2022\]

Storing text notes on the IMAP server would be useful, and the feature is low hanging fruit not necessarily very hard to implement. 

Tracked in [bug 713843](https://bugzilla.mozilla.org/show_bug.cgi?id=713843).

### Improved mailing list handling \[TB91\]

Better support for mailing list handling: 

* Mailing list management UI
* filtering to folders, autodetect lists and offer to set up
* handle the case of 2 mailing lists being the recipient better
* How to handle list encryption?

### Filtering rewrite in JavaScript \[TB91\]

Rewrite into JavaScript. Move to async operations once that’s done.

Tracked in [bug 1574136](https://bugzilla.mozilla.org/show_bug.cgi?id=1574136).

### MailExtensions \[TB91 and beyond\]

Thunderbird should continue to add more MailExtension APIs. 

### Troubleshooter \[TB2022\]

One of the main problems users have is that they can’t send/receive emails. Add a trouble shooter that could help diagnose the problem.

### Performance improvements \[TB91 and beyond\]

Set up performance measuring. Investigate performance bottlenecks and improve UI responsiveness and application startup time. Concrete goals to be set up once we have more information.

### Printing UI facelift \[TB91\]

Firefox is currently reworking printing \(print in a tab\). This will cause some work to adapt. 

We should also be able to improve the looks of printed mails by some fairly simple css adjustments. 

### Improved system integration \[TB91\]

* system notifications \(windows\) - also improved notifications in general \(there are a number of ways they are not as useful as they could atm.
* mid: links to be fully supported and Thunderbird should register itself to open mid: links - would be super helpful for cross referencing of mails, events, and tasks, even from other applications. [Bug 264270](https://bugzilla.mozilla.org/show_bug.cgi?id=264270).
* calendar: Thunderbird 78 added the ability to handle adding and opening of .ics event attachments by double click: [bug 357480](https://bugzilla.mozilla.org/show_bug.cgi?id=357480).

### Enterprise features \[TB???\]

* XMPP/Jingle support. [Bug 1018060](https://bugzilla.mozilla.org/show_bug.cgi?id=1018060) \[If we can get sponsorship?\]

### TOR uplift for Thunderbird \[TB???\]

Consider an option to connect through Tor. Some mail providers support connecting through their onion services.

For the general Tor uplift effort at Mozilla, see [https://wiki.mozilla.org/Security/Tor\_Uplift/Tracking](https://wiki.mozilla.org/Security/Tor_Uplift/Tracking) 

### Releng

#### Single hg repo for Thunderbird \[TB91\]

Thunderbird will move to use a single repository for all the code to stop all the problems that have to do with the mozilla-central comm-central split. Instead Thunderbird code will live in a separate repository that is based on mozilla-central but has a comm branch carrying a comm/ subfolder.

Tracked in [bug 1666242](https://bugzilla.mozilla.org/show_bug.cgi?id=1666242).

#### Own l10n repository \[TB91\]

Thunderbird localization will start using its own localization repository. This will allow us to do string changes in point releases when needed. This is currently not possible due to technical constraints in how the localization system is set up. 

#### Minimize releng related differences compared to Firefox \[TB91\]

To make it easier to follow Firefox release engineering changes, we try to further align with the Firefox release setup. The single repository will help in that we don’t need to check out more than one repo. Ideally only the repo url and some config variables would have to be different, except for product specific build needs.

### Website

Make the web site more marketing focused with stories on how to best utilize various features in Thunderbird and highlight workflows.

Our current donation appeal shows up only after users’ download starts. We should consider a more aggressive up-front appeal that would happen before the download starts. \(Users could still choose to pay 0.\)

