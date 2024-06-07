---
description: This list collects standards that the Thunderbird family of applications currently at least partially implements or supports (in our code base, ignoring things like TCP which we inherit from upstream components). It reflects the state of the current latest main repo code.
---

## Mail

| Spec Name                           | Spec URLs                             | Desktop Status | Android Status | Standards Involvement        | Comment |
|:------------------------------------|:-------------------------------------:|:--------------:|:--------------:|:---------------------------- |:------- |
| POP3                                | [^rfc1939]                            |  &#10004;      |   &#10004;     |                              |         |
| IMAP 4r1                            | [^rfc3501]                            |  &#10004;      |   &#10004;     |                              |         |
| IMAP IDLE                           | [^rfc2177]                            |  &#10004;      |   &#10004;     |                              |         |
| SMTP                                | [^rfc821]                             |  &#10004;      |   &#10004;     |                              |         |
| NNTP                                | [^rfc977]                             |  &#10004;      |   &#10004;     |                              |         |

[^rfc1939]: https://datatracker.ietf.org/doc/html/rfc1939
[^rfc3501]: https://datatracker.ietf.org/doc/html/rfc3501
[^rfc2177]: https://datatracker.ietf.org/doc/html/rfc2177
[^rfc821]: https://datatracker.ietf.org/doc/html/rfc821
[^rfc977]: https://datatracker.ietf.org/doc/html/rfc977 

## RSS Feeder

| Spec Name                           | Spec URLs                             | Desktop Status | Android Status | Standards Involvement        | Comment | 
|:------------------------------------|:-------------------------------------:|:--------------:|:--------------:|:---------------------------- |:------- |
| RSS 0.9                             | [^rss-0-9-0]                          |  &#10004;      |      N/A       |                              |         |
| RSS 2.0                             | [^rss-spec]                           |  &#10004;      |      N/A       |                              |         |
| Atom 0.3                            | [^atom-0-3]                           |  &#10004;      |      N/A       |                              |         |
| Atom 1.0                            | [^atom-1-0]                           |  &#10004;      |      N/A       |                              |         |

[^rss-0-9-0]: https://www.rssboard.org/rss-0-9-0
[^rss-spec]: https://www.rssboard.org/rss-specification
[^atom-0-3]: https://datatracker.ietf.org/doc/html/draft-ietf-atompub-format-00
[^atom-1-0]: https://datatracker.ietf.org/doc/html/rfc4287
 
## Security and Authentication

| Spec Name                           | Spec URLs                             | Desktop Status | Android Status | Standards Involvement        | Comment |
|:------------------------------------|:-------------------------------------:|:--------------:|:--------------:|:---------------------------- |:------- |
| MIME                                | [^rfc2045], [^rfc2046], [^rfc2047]    |  &#10004;      |   &#10004;     |                              | All 3 are supported for both Desktop and Android platforms. |
| List Management Headers             | [^rfc2369], [^rfc5064]                |  &#10004;      |                |                              |         |
| OpenPGP                             |                                       |  &#10004;      |   &#10004;     | Kai Engert actively involved |         |
| S/MIME version?                     |                                       |  &#10004;      |                |                              |         |
| oAuth 2.0                           | [^rfc6749]                            |  &#10004;      |   &#10004;     |                              | See also https://oauth.net/2/  |

[^rfc2045]: https://datatracker.ietf.org/doc/html/rfc2045
[^rfc2046]: https://datatracker.ietf.org/doc/html/rfc2046
[^rfc2047]: https://datatracker.ietf.org/doc/html/rfc2047
[^rfc2369]: https://datatracker.ietf.org/doc/html/rfc2369
[^rfc5064]: https://datatracker.ietf.org/doc/html/rfc5064
[^rfc6749]: https://datatracker.ietf.org/doc/html/rfc6749

## Address Book

| Spec Name                           | Spec URLs                             | Desktop Status | Android Status | Standards Involvement        | Comment |
|:------------------------------------|:-------------------------------------:|:--------------:|:--------------:|:---------------------------- |:------- |
| vCard                               | [^rfc6350]                            |  &#10004;      |                |                              |         |
| CardDAV                             | [^rfc6352]                            |  &#10004;      |      N/A       |                              |         |
| LDAP                                | [^rfc4511]                            |  &#10004;      |      N/A       |                              |         |

[^rfc6350]: https://datatracker.ietf.org/doc/html/rfc6350
[^rfc6352]: https://datatracker.ietf.org/doc/html/rfc6352
[^rfc4511]: https://datatracker.ietf.org/doc/html/rfc4511

## Calendar

| Spec Name                           | Spec URLs                             | Desktop Status | Android Status | Standards Involvement        | Comment |
|:------------------------------------|:-------------------------------------:|:--------------:|:--------------:|:---------------------------- |:------- |
| iCalendar                           | [^rfc5545]                            |  &#10004;      |                |                              |         |
| CalDAV                              | [^rfc4791]                            |  &#10004;      |      N/A       |                              |         |
| CalDAV Scheduling                   | [^rfc6638]                            |                |      N/A       |                              |         |
| iTIP                                | [^rfc2446]                            |  &#10004;      |                |                              |         |

[^rfc5545]: https://datatracker.ietf.org/doc/html/rfc5545
[^rfc4791]: https://datatracker.ietf.org/doc/html/rfc4791
[^rfc6638]: https://datatracker.ietf.org/doc/html/rfc6638
[^rfc2446]: https://datatracker.ietf.org/doc/html/rfc2446

## Instant Messaging

| Spec Name                           | Spec URLs                                      | Desktop Status | Android Status | Standards Involvement        | Comment |
|:------------------------------------|:----------------------------------------------:|:--------------:|:--------------:|:---------------------------- |:------- |
| IRC                                 | [^rfc1459], [^rfc2812]                         |  &#10004;      |      N/A       |                              |         |
| IRC TLS Port                        | [^rfc7194]                                     |  &#10004;      |      N/A       |                              |         |
| IRC CTCP                            | [^irc-ctcp]                                    |  &#10004;      |      N/A       |                              |         |
| IRC DCC                             | [^irc-dcc]                                     |  &#10004;      |      N/A       |                              |         |
| IRC SUPPORT                         | [^irc-00], [^irc-03], [^irc-05]                |  &#10004;      |      N/A       |                              | **TODO**: What about [ISUPPORT 01](https://datatracker.ietf.org/doc/html/draft-brocklesby-irc-isupport-01) and [ISUPPORT 02](https://datatracker.ietf.org/doc/html/draft-brocklesby-irc-isupport-02)? |
| IRC NAMESX                          | [^irc-namesx]                                  |  &#10004;      |      N/A       |                              |         |
| IRC MONITOR                         | [^irc-monitor]                                 |  &#10004;      |      N/A       |                              |         |
| IRC WATCH                           | [^irc-watch]                                   |  &#10004;      |      N/A       |                              |         |
| IRCv3 Capability Negotiation        | [^ircv3-cap-neg]                               |  &#10004;      |      N/A       |                              | See also the [IRCv3 Support Table](https://ircv3.net/software/clients#desktop-clients) |
| IRCv3 SASL 3.2                      | [^ircv3-sasl]                                  |  &#10004;      |      N/A       |                              |         |
| IRCv3 Message Tags                  | [^ircv3-message-tags]                          |  &#10004;      |      N/A       |                              |         |
| IRCv3 Echo Message                  | [^ircv3-echo-message]                          |  &#10004;      |      N/A       |                              |         |
| IRCv3 Multi-Prefix                  | [^ircv3-multi-prefix]                          |  &#10004;      |      N/A       |                              |         |
| IRCv3 Server Time                   | [^ircv3-server-time]                           |  &#10004;      |      N/A       |                              |         |
| XMPP                                | [^rfc3920], [^rfc3921], [^rfc6120], [^rfc6121] |  &#10004;      |      N/A       |                              |         |
| XEP-0030                            | [^xep-0030]                                    |  &#10004;      |      N/A       |                              |         |
| XEP-0045                            | [^xep-0045]                                    |  &#10004;      |      N/A       |                              |         |
| XEP-0054                            | [^xep-0054]                                    |  &#10004;      |      N/A       |                              |         |
| XEP-0059                            | [^xep-0059]                                    |  &#10004;      |      N/A       |                              |         |
| XEP-0078                            | [^xep-0078]                                    |  &#10004;      |      N/A       |                              |         |
| XEP-0085                            | [^xep-0085]                                    |  &#10004;      |      N/A       |                              | Typing notifications |
| XEP-0092                            | [^xep-0092]                                    |  &#10004;      |      N/A       |                              |         |
| XEP-0199                            | [^xep-0199]                                    |  &#10004;      |      N/A       |                              |         |
| XEP-0203                            | [^xep-0203]                                    |  &#10004;      |      N/A       |                              |         |
| XEP-0245                            | [^xep-0245]                                    |  &#10004;      |      N/A       |                              |         |
| XEP-0249                            | [^xep-0249]                                    |  &#10004;      |      N/A       |                              |         |
| XEP-0280                            | [^xep-0280]                                    |  &#10004;      |      N/A       |                              |         |
| XEP-xxxx MUC Avatars                | [^muc-avatars]                                 |  &#10004;      |      N/A       |                              |         |
| Matrix                              | [^matrix]                                      |  &#10004;      |      N/A       | Thunderbird is an [Ecosystem Member](https://matrix.org/support/) of the Matrix Foundation | UI only partially supports the full body of the specificaiton, not tracking on an MSC basis at this time |
| Olm/Megolm                          | [^olm], [^megolm]                              |  &#10004;      |      N/A       |                              |         |
| OTR                                 | [^otr-3-1-0], [^otr-4-1-1]                     |  &#10004;      |      N/A       |                              |         |

[^rfc1459]: https://datatracker.ietf.org/doc/html/rfc1459
[^rfc2812]: https://datatracker.ietf.org/doc/html/rfc2812
[^rfc7194]: https://datatracker.ietf.org/doc/html/rfc7194
[^irc-ctcp]: http://www.alien.net.au/irc/ctcp.txt
[^irc-dcc]: http://www.irchelp.org/irchelp/rfc/dccspec.html
[^irc-00]: https://datatracker.ietf.org/doc/html/draft-hardy-irc-isupport-00
[^irc-03]: https://datatracker.ietf.org/doc/html/draft-brocklesby-irc-isupport-03
[^irc-05]: http://www.irc.org/tech\_docs/005.html
[^irc-namesx]: https://docs.inspircd.org/2/modules/namesx/
[^irc-monitor]: https://github.com/atheme/charybdis/blob/master/doc/monitor.txt
[^irc-watch]: http://www.stack.nl/~jilles/cgi-bin/hgwebdir.cgi/irc-documentation-jilles/raw-file/tip/reference/draft-meglio-irc-watch-00.txt
[^ircv3-cap-neg]: https://ircv3.net/specs/extensions/capability-negotiation
[^ircv3-sasl]: https://ircv3.net/specs/extensions/sasl-3.2
[^ircv3-message-tags]: https://ircv3.net/specs/extensions/message-tags
[^ircv3-echo-message]: https://ircv3.net/specs/extensions/echo-message-3.2
[^ircv3-multi-prefix]: https://ircv3.net/specs/extensions/multi-prefix-3.1
[^ircv3-server-time]: https://ircv3.net/specs/extensions/server-time-3.2
[^rfc3920]: https://datatracker.ietf.org/doc/rfc3920
[^rfc3921]: https://datatracker.ietf.org/doc/rfc3921
[^rfc6120]: https://datatracker.ietf.org/doc/rfc6120
[^rfc6121]: https://datatracker.ietf.org/doc/rfc6121
[^xep-0030]: https://xmpp.org/extensions/xep-0030.html
[^xep-0045]: https://xmpp.org/extensions/xep-0045.html
[^xep-0054]: https://xmpp.org/extensions/xep-0054.html
[^xep-0059]: https://xmpp.org/extensions/xep-0059.html
[^xep-0078]: https://xmpp.org/extensions/xep-0078.html
[^xep-0085]: https://xmpp.org/extensions/xep-0085.html
[^xep-0092]: https://xmpp.org/extensions/xep-0092.html
[^xep-0199]: https://xmpp.org/extensions/xep-0199.html
[^xep-0203]: https://xmpp.org/extensions/xep-0203.html
[^xep-0245]: https://xmpp.org/extensions/xep-0245.html
[^xep-0249]: https://xmpp.org/extensions/xep-0249.html
[^xep-0280]: https://xmpp.org/extensions/xep-0280.html
[^muc-avatars]: https://xmpp.org/extensions/inbox/muc-avatars.html
[^matrix]: https://spec.matrix.org/latest/client-server-api/
[^olm]: https://gitlab.matrix.org/matrix-org/olm/-/blob/master/docs/olm.md
[^megolm]: https://gitlab.matrix.org/matrix-org/olm/-/blob/master/docs/megolm.md
[^otr-3-1-0]: https://otr.cypherpunks.ca/Protocol-v2-3.1.0.html
[^otr-4-1-1]: https://otr.cypherpunks.ca/Protocol-v3-4.1.1.html
