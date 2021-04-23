# Adapt to Changes in Thunderbird 79-91

## Changed API

### nsIArray and nsIMutableArray

Usage of nsIArray and `nsIMutableArray` is replaced by standard JavaScript Arrays. The following APIs have been updated \(links to actual patches, showing how core handled the change\):

* [nsIAbCard.properties](https://hg.mozilla.org/comm-central/diff/f7bc5c5ef5ebaec6fcd3394905b1190f7f9043e3/mailnews/addrbook/modules/AddrBookDirectory.jsm)
* [nsIAbDirectory.childCards](https://hg.mozilla.org/comm-central/diff/8d90e013f1e5ec392d10832272e622276010be05/mailnews/addrbook/public/nsIAbDirectory.idl)
* nsIAbDirectory.getCardsFromProperty \(not used in core, same change as with childCards\) 
* [nsIMsgAccountManager.accounts](https://hg.mozilla.org/comm-central/diff/1da9c365060718ffc88deb4d39b3930aeb02a1a7/calendar/base/modules/utils/calEmailUtils.jsm)
* [nsIMsgAccountManager.allFolders](https://hg.mozilla.org/comm-central/diff/4fd2ffb624922351c4c7ec6a775c78b436339fb3/mail/test/browser/folder-display/browser_recentMenu.js)
* [nsIMsgAccountManager.allIdentities](https://hg.mozilla.org/comm-central/diff/8d219926818378dda077442385c0d59650cc3ab6/calendar/base/modules/utils/calItipUtils.jsm)
* [nsIMsgAccountManager.allServers](https://hg.mozilla.org/comm-central/diff/16ca51abc6dda000296c7d7d082b9951ca04fd6d/mail/base/content/folderPane.js) 
* nsIMsgCopyService.CopyFolders -&gt; [nsIMsgCopyService.copyFolders](https://hg.mozilla.org/comm-central/diff/4f8d3dff196eba35f09bbbe4f6487f882c07840e/mailnews/imap/test/unit/test_localToImapFilter.js)
* [nsIMsgCopyService.CopyMessages](https://hg.mozilla.org/comm-central/diff/fe318ec033d42bd4342a63b86c320116bfa5c5bc/mail/test/browser/message-window/browser_commands.js) 
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

Renamed in Beta 86 to

```text
nsIMsgCompose.sendMsg
```

It also returns a Promise now. See [here](https://searchfox.org/comm-central/rev/bb3eb2b4cb4b17405e8efcc423563a7a07473292/mailnews/compose/public/nsIMsgCompose.idl#117) for more details.  


### nsISocketTransportService.createTransport

Changed in Beta 87. Needs a fifth parameter to specify a `nsIDNSRecord` value, but can be null if not needed:

```text
nsISocketTransport createTransport(in Array<ACString> aSocketTypes,
                                   in AUTF8String aHost,
                                   in long aPort,
                                   in nsIProxyInfo aProxyInfo,
                                   in nsIDNSRecord dnsRecord);
```

See [here](https://searchfox.org/mozilla-central/rev/15f6b60e343c536305a5aa81e2020d7b87f93158/netwerk/base/nsISocketTransportService.idl#62) for more details.

## folderPane.js

### ftvItem

The `ftvItem` object has been renamed to `FtvItem` in Beta 86.

## MsgComposeCommands.js

### GetMsgAttachmentElement\(\)

Has been replaced by`gAttachmentBucket`. More information can be found [here](https://hg.mozilla.org/comm-central/diff/b84ef4aee6c977f95fdf04d37f74791d3fecfbf4/mail/components/compose/content/MsgComposeCommands.js#l1.666).  


### attachmentsCount\(\) 

Has been replaced by `gAttachmentBucket.itemCount`.  


### attachmentsSelectedCount\(\) 

Has been replaced by `gAttachmentBucket.selectedCount`.



