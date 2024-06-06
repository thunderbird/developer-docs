---
description: This list collects standards that the Thunderbird family of applications currently at least partially implements or supports (in our code base, ignoring things like TCP which we inherit from upstream components). It reflects the state of the current latest main repo code.
---

## Mail

| Spec Name                                                          | Desktop Status | Android Status | Standards Involvement      | Comment |
|:------------------------------------------------------------------ |:--------------:|:--------------:|:-------------------------- |:------- |
| [POP3](https://datatracker.ietf.org/doc/html/rfc1939)              |  &#10004;      |   &#10004;     |                            |         |
| [IMAP 4r1](https://datatracker.ietf.org/doc/html/rfc3501)          |  &#10004;      |   &#10004;     |                            |         |
| [IMAP IDLE](https://datatracker.ietf.org/doc/html/rfc2177)         |  &#10004;      |   &#10004;     |                            |         |
| [SMTP](https://datatracker.ietf.org/doc/html/rfc821)               |  &#10004;      |   &#10004;     |                            |         |
| [NNTP](https://datatracker.ietf.org/doc/html/rfc977)               |  &#10004;      |   &#10004;     |                            |         |

## RSS Feeder

| Spec Name                                                          | Desktop Status | Android Status | Standards Involvement      | Comment |
|:------------------------------------------------------------------ |:--------------:|:--------------:|:-------------------------- |:------- |
| [RSS 0.9](https://www.rssboard.org/rss-0-9-0)                      |  &#10004;      |      N/A       |                            |         |
| [RSS 2.0](https://www.rssboard.org/rss-specification)              |  &#10004;      |      N/A       |                            |         |
| [Atom 0.3](https://datatracker.ietf.org/doc/html/draft-ietf-atompub-format-00) |  &#10004; |  N/A    |                            |         |
| [Atom 1.0](https://datatracker.ietf.org/doc/html/rfc4287)          |  &#10004;      |      N/A       |                            |         |

## Security and Authentication

| Spec Name                                                          | Desktop Status | Android Status | Standards Involvement      | Comment |
|:------------------------------------------------------------------ |:--------------:|:--------------:|:-------------------------- |:------- |
| [MIME](https://datatracker.ietf.org/doc/html/rfc2045)              |  &#10004;      |   &#10004;     |                            | MIME is actually over 3 RFCs: [RFC2045](https://datatracker.ietf.org/doc/html/rfc2045), [RFC2046](https://datatracker.ietf.org/doc/html/rfc2046), and [RFC2047](https://datatracker.ietf.org/doc/html/rfc2047). All 3 are supported for both Desktop and Android platforms. |
| [List Management Headers](https://datatracker.ietf.org/doc/html/rfc2369) | &#10004; |                |                            | List Management Headers is over 2 RFCs: [RFC2369](https://datatracker.ietf.org/doc/html/rfc2369) and [RFC5064](https://datatracker.ietf.org/doc/html/rfc5064)  |
| OpenPGP                                                            |  &#10004;      |   &#10004;     | Kai Engert actively involved |        |
| S/MIME version?                                                    |  &#10004;      |                |                            |         |
| [oAuth 2.0](https://datatracker.ietf.org/doc/html/rfc6749)         |  &#10004;      |   &#10004;     |                            | See https://oauth.net/2/  |

## Address Book

| Spec Name                                                          | Desktop Status | Android Status | Standards Involvement      | Comment |
|:------------------------------------------------------------------ |:--------------:|:--------------:|:-------------------------- |:------- |
| [vCard](https://datatracker.ietf.org/doc/html/rfc6350)             |  &#10004;      |                |                            |         |
| [CardDAV](https://datatracker.ietf.org/doc/html/rfc4511)           |  &#10004;      |      N/A       |                            |         |
| [LDAP](https://datatracker.ietf.org/doc/html/rfc4511)              |  &#10004;      |      N/A       |                            |         |

## Calendar

| Spec Name                                                          | Desktop Status | Android Status | Standards Involvement      | Comment |
|:------------------------------------------------------------------ |:--------------:|:--------------:|:-------------------------- |:------- |
| [iCalendar](https://datatracker.ietf.org/doc/html/rfc5545)         |  &#10004;      |                |                            |         |
| [CalDAV](https://datatracker.ietf.org/doc/html/rfc4791)            |  &#10004;      |      N/A       |                            |         |
| [CalDAV Scheduling](https://datatracker.ietf.org/doc/html/rfc6638) |                |      N/A       |                            |         |
| [iTIP](https://datatracker.ietf.org/doc/html/rfc2446)              |  &#10004;      |                |                            |         |

## Instant Messaging

| Spec Name                                                          | Desktop Status | Android Status | Standards Involvement      | Comment |
|:------------------------------------------------------------------ |:--------------:|:--------------:|:-------------------------- |:------- |
| [IRC](https://datatracker.ietf.org/doc/html/rfc1459)               |  &#10004;      |      N/A       |                            | IRC is actually over 2 RFCs: [RFC1459](https://datatracker.ietf.org/doc/html/rfc1459) and [RFC2812](https://datatracker.ietf.org/doc/html/rfc2812)  |
| [IRC TLS Port](https://datatracker.ietf.org/doc/html/rfc7194)      |  &#10004;      |      N/A       |                            |         |
| [IRC CTCP](http://www.alien.net.au/irc/ctcp.txt)                   |  &#10004;      |      N/A       |                            |         |
| [IRC DCC](http://www.irchelp.org/irchelp/rfc/dccspec.html)         |  &#10004;      |      N/A       |                            |         |
| [IRC SUPPORT](http://www.irc.org/tech_docs/005.html)               |  &#10004;      |      N/A       |                            | IRC SUPPORT is actually over 3 links: [ISUPPORT 00](https://datatracker.ietf.org/doc/html/draft-hardy-irc-isupport-00), [ISUPPORT 03](https://datatracker.ietf.org/doc/html/draft-brocklesby-irc-isupport-03), and [ISUPPORT 05](http://www.irc.org/tech_docs/005.html). (**TODO**: What about [ISUPPORT 01](https://datatracker.ietf.org/doc/html/draft-brocklesby-irc-isupport-01) and [ISUPPORT 02](https://datatracker.ietf.org/doc/html/draft-brocklesby-irc-isupport-02)?) |
| [IRC NAMESX](https://docs.inspircd.org/2/modules/namesx/)          |  &#10004;      |      N/A       |                            |         |
| [IRC MONITOR](https://github.com/atheme/charybdis/blob/master/doc/monitor.txt) | &#10004;  | N/A     |                            |         |
| [IRC WATCH](http://www.stack.nl/~jilles/cgi-bin/hgwebdir.cgi/irc-documentation-jilles/raw-file/tip/reference/draft-meglio-irc-watch-00.txt) | &#10004; | N/A |   |   |
| [IRCv3 Capability Negotiation](https://ircv3.net/specs/extensions/capability-negotiation) | &#10004; | N/A |                      | See also [IRCv3 Support Table](https://ircv3.net/software/clients#desktop-clients) |
| [IRCv3 SASL 3.2](https://ircv3.net/specs/extensions/sasl-3.2)      |  &#10004;      |      N/A       |                            |         |
| [IRCv3 Message Tags](https://ircv3.net/specs/extensions/message-tags) | &#10004;    |      N/A       |                            |         |
| [IRCv3 Echo Message](https://ircv3.net/specs/extensions/echo-message-3.2) | &#10004; |     N/A       |                            |         |
| [IRCv3 Multi-Prefix](https://ircv3.net/specs/extensions/multi-prefix-3.1) | &#10004; |     N/A       |                            |         |
| [IRCv3 Server Time](https://ircv3.net/specs/extensions/server-time-3.2)  |  &#10004; |     N/A       |                            |         |
| [XMPP](https://datatracker.ietf.org/doc/rfc3920)                   | &#10004;       |      N/A       |                            | XMPP is actually over 4 RFCs: [RFC3920](https://datatracker.ietf.org/doc/rfc3920), [RFC3921](https://datatracker.ietf.org/doc/rfc3921), [RFC6120](https://datatracker.ietf.org/doc/rfc6120), and [RFC6121](https://datatracker.ietf.org/doc/rfc6121) |
| [XEP-0030](https://xmpp.org/extensions/xep-0030.html)              |  &#10004;      |      N/A       |                            |         |
| [XEP-0045](https://xmpp.org/extensions/xep-0045.html)              |  &#10004;      |      N/A       |                            |         |
| [XEP-0054](https://xmpp.org/extensions/xep-0054.html)              |  &#10004;      |      N/A       |                            |         |
| [XEP-0059](https://xmpp.org/extensions/xep-0059.html)              |  &#10004;      |      N/A       |                            |         |
| [XEP-0078](https://xmpp.org/extensions/xep-0078.html)              |  &#10004;      |      N/A       |                            |         |
| [XEP-0085](https://xmpp.org/extensions/xep-0085.html)              |  &#10004;      |      N/A       |                            | Typing notifications |
| [XEP-0092](https://xmpp.org/extensions/xep-0092.html)              |  &#10004;      |      N/A       |                            |         |
| [XEP-0199](https://xmpp.org/extensions/xep-0199.html)              |  &#10004;      |      N/A       |                            |         |
| [XEP-0203](https://xmpp.org/extensions/xep-0203.html)              |  &#10004;      |      N/A       |                            |         |
| [XEP-0245](https://xmpp.org/extensions/xep-0245.html)              |  &#10004;      |      N/A       |                            |         |
| [XEP-0249](https://xmpp.org/extensions/xep-0249.html)              |  &#10004;      |      N/A       |                            |         |
| [XEP-0280](https://xmpp.org/extensions/xep-0280.html)              |  &#10004;      |      N/A       |                            |         |
| [XEP-xxxx MUC Avatars](https://xmpp.org/extensions/inbox/muc-avatars.html) | &#10004; |    N/A       |                            |         |
| [Matrix](https://spec.matrix.org/latest/client-server-api/)        |  &#10004;      |      N/A       | Thunderbird is an [Ecosystem Member](https://matrix.org/support/) of the Matrix Foundation | UI only partially supports the full body of the specificaiton, not tracking on an MSC basis at this time |
| [Olm/Megolm](https://gitlab.matrix.org/matrix-org/olm/-/blob/master/docs/olm.md) | &#10004; | N/A    |                            | Encryption used by Matrix. It is actually over 2 links: [Olm]((https://gitlab.matrix.org/matrix-org/olm/-/blob/master/docs/olm.md) and [Megolm](https://gitlab.matrix.org/matrix-org/olm/-/blob/master/docs/megolm.md) |
| [OTR](https://otr.cypherpunks.ca/Protocol-v3-4.1.1.html)           |  &#10004;      |      N/A       |                            | Protocol agnostic encryption applies to IRC and XMPP. It is actually over 2 links: [Protocol v3 4.1.1](https://otr.cypherpunks.ca/Protocol-v3-4.1.1.html) and [Protocol v2 3.1.0](https://otr.cypherpunks.ca/Protocol-v2-3.1.0.html) |
