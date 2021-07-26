# Adapt to Changes in Thunderbird 79-91

This document tries to cover all the internal changes that may be needed to make add-ons compatible with Thunderbird 91. If you find changes which are not yet listed on this page, you can ask for help and advice in one of our [communication channels](https://developer.thunderbird.net/#getting-plugged-into-the-community).

## Changed API

### nsIArray and nsIMutableArray

Usage of nsIArray and `nsIMutableArray` is replaced by standard JavaScript arrays. The following APIs have been updated \(links to actual patches, showing how core handled the change\):

* [nsIAbCard.properties](https://hg.mozilla.org/comm-central/diff/f7bc5c5ef5ebaec6fcd3394905b1190f7f9043e3/mailnews/addrbook/modules/AddrBookDirectory.jsm)
* [nsIAbDirectory.childCards](https://hg.mozilla.org/comm-central/diff/8d90e013f1e5ec392d10832272e622276010be05/mailnews/addrbook/public/nsIAbDirectory.idl)
* nsIAbDirectory.getCardsFromProperty \(returns array instead of enumerator\)
* nsIAbManager.directories \(returns array instead of enumerator\) 
* [nsIMsgAccountManager.accounts](https://hg.mozilla.org/comm-central/diff/1da9c365060718ffc88deb4d39b3930aeb02a1a7/calendar/base/modules/utils/calEmailUtils.jsm)
* [nsIMsgAccountManager.allFolders](https://hg.mozilla.org/comm-central/diff/4fd2ffb624922351c4c7ec6a775c78b436339fb3/mail/test/browser/folder-display/browser_recentMenu.js)
* [nsIMsgAccountManager.allIdentities](https://hg.mozilla.org/comm-central/diff/8d219926818378dda077442385c0d59650cc3ab6/calendar/base/modules/utils/calItipUtils.jsm)
* [nsIMsgAccountManager.allServers](https://hg.mozilla.org/comm-central/diff/16ca51abc6dda000296c7d7d082b9951ca04fd6d/mail/base/content/folderPane.js) 
* nsIMsgCompFields.attachments \(returns array instead of enumerator\) 
* nsIMsgCopyService.CopyFolders -&gt; [nsIMsgCopyService.copyFolders](https://hg.mozilla.org/comm-central/diff/4f8d3dff196eba35f09bbbe4f6487f882c07840e/mailnews/imap/test/unit/test_localToImapFilter.js)
* [nsIMsgCopyService.CopyMessages](https://hg.mozilla.org/comm-central/diff/fe318ec033d42bd4342a63b86c320116bfa5c5bc/mail/test/browser/message-window/browser_commands.js) 
* nsIMsgFilterCustomAction.getCustomActions \(returns array instead of enumerator\)

* [nsIMsgFolder.addKeywordsToMessages](https://hg.mozilla.org/comm-central/diff/4f1fac782b47fc236f62a18e7a51a51c8982edfd/mail/base/content/mailWindowOverlay.js#l1.71)
* [nsIMsgFolder.deleteMessages](https://hg.mozilla.org/comm-central/diff/c2ddbcc4f19781c5c61f46918a94dd33fab2c8d9/mail/components/compose/content/MsgComposeCommands.js)
* nsIMsgFolder.deleteSubFolders -&gt; [nsIMsgFolder.deleteSelf](https://hg.mozilla.org/comm-central/diff/d4333da4784fc707a56a5bdda13392a94765c05b/mail/base/content/folderPane.js#l1.14)
* nsIMsgFolder.listFoldersWithFlags -&gt;  [nsIMsgFolder.getFoldersWithFlags](https://hg.mozilla.org/comm-central/diff/d146b4e2061c4ec55fce6ac0615c722fed19e756/mail/base/content/folderPane.js#l1.16)
* [nsIMsgFolder.markMessagesRead](https://hg.mozilla.org/comm-central/diff/93610e507d37b2da5e034536ba05f7d2679fcfac/mail/components/extensions/parent/ext-messages.js#l1.22) 
* [nsIMsgFolder.markMessagesFlagged](https://hg.mozilla.org/comm-central/diff/93610e507d37b2da5e034536ba05f7d2679fcfac/mail/components/extensions/parent/ext-messages.js#l1.25)
* [nsIMsgFolder.removeKeywordsFromMessages](https://hg.mozilla.org/comm-central/diff/4f1fac782b47fc236f62a18e7a51a51c8982edfd/mailnews/base/test/unit/test_bug428427.js#l1.40)
* [nsIMsgFolder.setJunkScoreForMessages](https://hg.mozilla.org/comm-central/diff/93610e507d37b2da5e034536ba05f7d2679fcfac/mail/components/extensions/parent/ext-messages.js#l1.29)
* [nsIMsgFolder.subFolders](https://hg.mozilla.org/comm-central/diff/d0c8f7c8585e0f485a80f67ccb483b329115eedd/mail/test/browser/folder-pane/browser_folderNamesInRecentMode.js) 
* [nsIMsgFolderListener.msgsClassified](https://hg.mozilla.org/comm-central/diff/e7ab0d2b72908c0a66347f188f8e00d3b87e7b11/mail/components/extensions/parent/ext-messages.js) 
* nsISmtpService.servers \(returns array instead of enumerator\)

  

### fixIterator\(\) & iteratorutils.jsm

The function `fixIterator()` is no longer needed by any core code and was subsequently removed together with `iteratorutils.jsm`. It was mostly used in the following way:

```text
for (let account of fixIterator(MailServices.accounts.accounts)) {
  ...
}
```

Since `MailServices.accounts.accounts` or `MailServices.accounts.allIdentities` return a simple array since Thunderbird 75, there is no need to pipe it through `fixIterator()` anymore. If your add-on is multi-version compatible and still supports Thunderbird 68 this has to be dealt with separately.  


### nsILoadInfo.SEC\_ALLOW\_CROSS\_ORIGIN\_DATA\_IS\_NULL

Renamed in Beta 80 to `SEC_ALLOW_CROSS_ORIGIN_SEC_CONTEXT_IS_NULL`. This is often used as the `aSecurityFlags` argument in calls to `Services.io.newChannelFromURI()`. 

```text
nsIChannel newChannelFromURI(
   in nsIURI aURI,
   in Node aLoadingNode,
   in nsIPrincipal aLoadingPrincipal,
   in nsIPrincipal aTriggeringPrincipal,
   in unsigned long aSecurityFlags,
   in nsContentPolicyType aContentPolicyType);
```

For example [here](https://searchfox.org/comm-central/rev/bb3eb2b4cb4b17405e8efcc423563a7a07473292/calendar/base/modules/utils/calProviderUtils.jsm#74).  


### nsIMsgCompose.SendMsg

Renamed in Beta 86 to `nsIMsgCompose.sendMsg`. It also returns a Promise now. More details can be found [here](https://searchfox.org/comm-central/rev/bb3eb2b4cb4b17405e8efcc423563a7a07473292/mailnews/compose/public/nsIMsgCompose.idl#117).  


### nsISocketTransportService.createTransport

Changed in Beta 87. Needs a fifth parameter to specify a `nsIDNSRecord` value, but can be `null` if not needed:

```text
nsISocketTransport createTransport(in Array<ACString> aSocketTypes,
                                   in AUTF8String aHost,
                                   in long aPort,
                                   in nsIProxyInfo aProxyInfo,
                                   in nsIDNSRecord dnsRecord);
```

See [here](https://searchfox.org/mozilla-central/rev/15f6b60e343c536305a5aa81e2020d7b87f93158/netwerk/base/nsISocketTransportService.idl#62) for more details.

### IOUtils.jsm

The Thunderbird-specific [IOUtils.jsm](https://hg.mozilla.org/comm-central/file/bec25a74919c93d954d48beeb617a1ecb92e55b8/mailnews/base/src/IOUtils.jsm) file was removed without a direct replacement. You need to alter your logic or copy code from the old JSM into your add-on. [Bug 1678109](https://bugzilla.mozilla.org/show_bug.cgi?id=1678109) might be a good inspiration, as it contains the changes that were necessary for core code.

## Removed API

### nsIAbListener

Interface has been dropped in favor of an observer based approach. See 

* [https://searchfox.org/comm-central/source/mail/base/content/msgHdrView.js\#379-429](https://searchfox.org/comm-central/source/mail/base/content/msgHdrView.js#379-429)
* [https://searchfox.org/comm-central/source/mail/base/content/msgHdrView.js\#277](https://searchfox.org/comm-central/source/mail/base/content/msgHdrView.js#277)
* [https://searchfox.org/comm-central/source/mail/base/content/msgHdrView.js\#335](https://searchfox.org/comm-central/source/mail/base/content/msgHdrView.js#335)

## Broken/Removed XUL Elements

### &lt;xul:grid&gt;

The grid element does not seem to work anymore. Alternative is to use &lt;html:table&gt;.

## Changes in commonly used Files

### folderPane.js

The `ftvItem` object has been renamed to `FtvItem` in Beta 86.

### MsgComposeCommands.js

#### GetMsgAttachmentElement\(\)

Has been replaced by`gAttachmentBucket`. More information can be found [here](https://hg.mozilla.org/comm-central/diff/b84ef4aee6c977f95fdf04d37f74791d3fecfbf4/mail/components/compose/content/MsgComposeCommands.js#l1.666).

#### attachmentsCount\(\) 

Has been replaced by `gAttachmentBucket.itemCount`.

#### attachmentsSelectedCount\(\) 

Has been replaced by `gAttachmentBucket.selectedCount`.

### chrome://messenger/content/newmailalert.xhtml

No longer supports the `gAlertListener`, `gUserInitiated` and `gOrigin` parameters when being opened \(argument 1-3\). Instead, the following parameters are are used now:

```text
+  // arguments[0] --> The real nsIMsgFolder with new mail.
+  // arguments[1] --> The keys of new messages.
+  // arguments[2] --> The nsIObserver to receive window closed event
```

## Low-level changes

### Thunderbird is now multi-process \(e10s\)

Thunderbird can now separate content into different processes, which can only communicate through limited channels. This change has advantages relative to security and performance, but restricts code from freely accessing data that belongs to a different process.

In Thunderbird 91, this is primarily affecting pages belonging to an Add-on, like the background page or frames injected with an experiment. Most other parts of Thunderbird did not \(yet!\) change as much.

In practical terms, this means that you may need to update your experiments:

{% hint style="info" %}
For most common scenarios, you can use the messaging system provided by the [notifyTools](https://github.com/thundernest/addon-developer-support/tree/master/auxiliary-apis/NotifyTools) to fix e10s related issues.
{% endhint %}

* **If you access your background page from an experiment** running in the main Thunderbird process \("parent"\), you need to switch to an e10s-compatible method to do so. For most common cases, you can use [notifyTools](https://github.com/thundernest/addon-developer-support/tree/master/auxiliary-apis/NotifyTools).
* **If you create frames in Thunderbird's UI**, these frames now need the appropriate attributes to load content in the right process. You can find an example of required changes in [the E10S compatibility commit for the CustomUI experiment](https://github.com/rsjtdrjgfuzkfg/thunderbird-experiments/commit/11232201ff437e7bb293efdcb93ecc3963a8328d#diff-41cf834ce8c3fae411d5f4c18abf8c024630074e616cf98307e582cea5362be7).
* **If you exchange raw objects between WebExtension scopes / "child" experiment code and Thunderbird / "parent" experiment code**, you need to migrate to an indirect approach, usually based on explicit message passing. Depending on the complexity of your task, this can either happen through notifyTools, a custom experiment API or through custom experiment code directly using cross-process APIs \(likely either [message managers](https://searchfox.org/mozilla-central/source/dom/chrome-webidl/MessageManager.webidl) or [actors](https://firefox-source-docs.mozilla.org/dom/ipc/jsactors.html)\).
* As a consequence, **if you load JavaScript modules in "child" experiment code**, you will now get separate instances of the JSM: each process has its own instance, and there could be multiple child processes. If you keep using JSMs from "child" code, you may furthermore need to manually unload these separate instances on API shutdown \(`ExtensionAPI.onShutdown`\), even if you use a catch-all unloading solution like CachingFix or the WindowListener API.

