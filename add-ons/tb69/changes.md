# List of changes in core up to Thunderbird 69

This document tries to cover all the changes that may by needed to make add-ons compatible with Thunderbird 68. If you find stuff that is no longer working but is not yet on this list, ask for help and advice in the mozilla.dev.apps.thunderbird newsgroup or check our [communication channels](https://wiki.mozilla.org/Thunderbird/CommunicationChannels#If_you.27re_a_developer).

The changes are grouped by category and are listed in the order we became aware of them.

## nsISocketTransportService

Bug 1558726 (https://bugzilla.mozilla.org/show_bug.cgi?id=1558726) introduced a breaking change to the nsISocketTransportService interface.
  
Before the change, the first parameter used to be an array and the second one the arrays length. This strage set of parameters 
date back to the early days of xpcom. It had only very limited array support and you needed to pass an array it as you would 
have done it in c - as reference to the first element and a length. This was kept this way for more than a decade.

Now, after the change the first parameter is still an array, but the length parameter was removed as modern XPCOM does not need
this information. But this causes all subsequent arguments to shift by one. Also the default behaviour changed. To create a 
default socket you have to pass an empty array instead of passing null.

The workaround to be backward compatible is to simply check the argument count of "createTransport". In case it is 4 is is the new interface.
In case it is 5 you got the old interface.

```
   if (transportService.createTransport.length === 4)
      return transportService.createTransport(((secure) ? ["starttls"] : []), host, port, proxyInfo);

    if (transportService.createTransport.length === 5) {
      if (secure)
        return transportService.createTransport(["starttls"], 1, host, port, proxyInfo);

      return transportService.createTransport(null, 0, host, port, proxyInfo);
   }

   throw new Error("Unknown Create Transport signature");
      
```
