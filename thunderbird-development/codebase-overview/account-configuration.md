# Account Configuration

This page describes the classes and interfaces involved in configuring mail accounts.

{% hint style="info" %}
Chat accounts use very similar mechanisms, but we won't go into that here to avoid confusion.
{% endhint %}

## The Account Manager

The account manager controls the objects described here. It is defined by [`nsIMsgAccountManager`](https://searchfox.org/comm-central/source/mailnews/base/public/nsIMsgAccountManager.idl) and implemented by [`nsMsgAccountManager`](https://searchfox.org/comm-central/source/mailnews/base/src/nsMsgAccountManager.cpp).

To get to the account manager from JS, use `MailServices.accounts`. To get to it from C++, use `mozilla::components::AccountManager::Service()`. (The rest of this page will describe things in JS terms only for ease of reading.)

## Accounts

Accounts are simple containers for incoming servers and identities. The are defined by [`nsIMsgAccount`](https://searchfox.org/comm-central/source/mailnews/base/public/nsIMsgAccount.idl) and implemented by [`nsMsgAccount`](https://searchfox.org/comm-central/source/mailnews/base/src/nsMsgAccount.cpp). If you're looking to use something in a mail account, you'll probably first get a reference to an `nsIMsgAccount`.

Accounts are identified by a `key` property, which is the word `account` and then a number. Preferences for an account have the prefix `mail.accounts.accountX` .

All accounts can be found at `MailServices.accounts.accounts`. To get a particular account, use `MailServices.accounts.getAccount(accountKey)`.

## Incoming Servers

Incoming Server objects describe a connection to a mail server, e.g. an IMAP or POP3 server, or for local mail. They are defined by [`nsIMsgIncomingServer`](https://searchfox.org/comm-central/source/mailnews/base/public/nsIMsgIncomingServer.idl) and a sub-interface and implementation exists for each type of server Thunderbird can connect to.

Incoming Servers are identified by a `key` property, which is the word `server` and then a number. Preferences for an account have the prefix `mail.servers.serverX` .

There is a 1:1 relationship between an account and an incoming server. To get to the server from an account, use the account's `incomingServer` property. To get the account for a server, use `MailServices.accounts.FindAccountForServer(server)`.

All incoming servers can be found at `MailServices.accounts.allServers`. To get a particular server, use `MailServices.accounts.getIncomingServer(serverKey)`.

## Identities

Identities describe everything about sending mail from an account, such as the user's name and email address, which SMTP server to use, and where to put sent mail. They are defined by [`nsIMsgIdentity`](https://searchfox.org/comm-central/source/mailnews/base/public/nsIMsgIdentity.idl) and implemented by [`nsMsgIdentity`](https://searchfox.org/comm-central/source/mailnews/base/src/nsMsgIdentity.cpp).

Identities are identified by a `key` property, which is the word `id` and then a number. Preferences for an account have the prefix `mail.identity.idX` .

Accounts can have any number of identities, although removing the last identity generally isn't allowed. Use the account's `identities` and `defaultIdentity` properties to access them.&#x20;

Typically an identity belongs to only one account, although it's technically possible for it to belong to multiple accounts. Use `MailServices.accounts.getServersForIdentity(identity)` to get the server(s) for an identity, and go from there to get the accounts. (Yes, this _is_ weird.)

All identities can be found at `MailServices.accounts.allIdentities`. To get a particular identity, use `MailServices.accounts.getIdentity(identityKey)`.

## SMTP

SMTP breaks some of the pattern you might've noticed in the previous classes. The SMTP service, `MailServices.smtp` or `mozilla::Components::Smtp::Service()`, implements [`nsISmtpService`](https://searchfox.org/comm-central/source/mailnews/compose/public/nsISmtpService.idl) as [`SmtpService`](https://searchfox.org/comm-central/source/mailnews/compose/src/SmtpService.jsm).

SMTP server configuration is kept by objects implementing [`nsISmtpServer`](https://searchfox.org/comm-central/source/mailnews/compose/public/nsISmtpServer.idl) as [`SmtpServer`](https://searchfox.org/comm-central/source/mailnews/compose/src/SmtpServer.jsm). They also are identified by a `key` property, which is the word `smtp` and then a number. Preferences for an account have the prefix `mail.smtpserver.smtpX` .

Identity objects reference SMTP servers in their `smtpServerKey` attribute.

All SMTP servers can be found at `MailServices.smtp.servers`. To get a particular server, use `MailServices.smtp.getServerByKey(smtpKey)`.
