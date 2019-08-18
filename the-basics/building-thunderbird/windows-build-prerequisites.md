---
description: >-
  This page has all the information you need to get your Windows development
  environment set up and ready to hack on Thunderbird.
---

# Windows Build Prerequisites

## The Basics

### 64-bit Windows

You will need to be running a 64-bit version of Windows in order to build Thunderbird. To check this in Windows 10, open the start menu and click on the gear icon on the left-hand side of the menu. This will open up the "settings" window. Click on the "System" option and then scroll down to "About". Click on the "About" option and on the new screen next to "System Type" you should see: "**64-bit operating system"**

![How System type appears in the About Settings page.](../../.gitbook/assets/windows-version-screen.png)

### MAPI Headers

Messaging Application Programming Interface \(**MAPI**\) is an API for Microsoft Windows ****which allows programs to become email-aware and the libraries that enable this are required to build Thunderbird on Windows. 

{% hint style="info" %}
You can get the MAPI header files from: [https://www.microsoft.com/en-us/download/details.aspx?id=12905](https://www.microsoft.com/en-us/download/details.aspx?id=12905) 
{% endhint %}

Copy 17 of the 18 header files to a Windows SDK include directory so that the build process will find the files, that is `C:\Program Files (x86)\Windows Kits\10\Include\10.0.nnnnn.0\shared`, where `nnnnn` is the highest number present on the system. Note that the downloaded Outlook 2010 MAPI Header Files contain 18 fies, of which only 17 are needed. Do **not** copy MAPI.h, it is already in C:\Program Files \(x86\)\Windows Kits\10\Include\10.0.17134.0\um\MAPI.h.

As of April 2019, 10.0.17134.0 is needed to compile Thunderbird. Assuming standard installation locations, copy these 17 files to `C:\Program Files (x86)\Windows Kits\10\Include\10.0.17134.0\shared`.

```text
18/10/2010  16:11             7,334 MAPIAux.h
02/06/2009  17:02             7,938 MAPICode.h
02/06/2009  17:02            22,960 MAPIDbg.h
02/06/2009  17:02            84,644 MAPIDefS.h
02/06/2009  17:02            27,840 MAPIForm.h
02/06/2009  17:02            11,880 MAPIGuid.h
02/06/2009  17:02             2,648 MAPIHook.h
02/06/2009  17:02             5,359 MAPINls.h
02/06/2009  17:02             2,743 MAPIOID.h
02/06/2009  17:02            32,978 MAPISPI.h
02/06/2009  17:02            54,395 MAPITags.h
02/06/2009  17:02            26,467 MAPIUtil.h
02/06/2009  17:02            97,301 MAPIVal.h
02/06/2009  17:02             9,334 MAPIWin.h
02/06/2009  17:02             1,906 MAPIWz.h
02/06/2009  17:02            18,277 MAPIX.h
02/06/2009  17:02             5,012 MSPST.h
```

### **Visual Studio**

In order to get the necessary libraries in order to build Thunderbird, you will need to install Visual Studio - an IDE from Microsoft. [Download the free community edition here](https://visualstudio.microsoft.com/downloads/).

During installation make sure the following workloads are checked:

* "Desktop development with C++"
* "Game development with C++"

### MozillaBuild Package

 Finally, download the [MozillaBuild Package](https://ftp.mozilla.org/pub/mozilla.org/mozilla/libraries/win32/MozillaBuildSetup-Latest.exe) from Mozilla. Accept the default settings, in particular the default installation directory: `c:\mozilla-build\`. On some versions of Windows an error dialog will give you the option to ‘reinstall with the correct settings’ - you should agree and proceed.

{% hint style="info" %}
 Once this is done, creating a shortcut to `c:\mozilla-build\start-shell.bat` on your desktop will make your life easier.
{% endhint %}

{% hint style="success" %}
**NOTE: You will need to run the start-shell.bat to open up the shell and perform the commands listed in other parts of this guide.**
{% endhint %}

### Getting the Code

Once you have run start-shell.bat, you will need to grab the source code if you haven't already.

Get the latest Mozilla source code from Mozilla's `mozilla-central` Mercurial code repository, and check it out into a local directory `source` \(or however you want to call it\). Then, get the latest Thunderbird source code from Mozilla's `comm-central` Mercurial code repository. It now needs to be placed **inside** the Mozilla source code, in a directory named `comm/` \(this is inverse from Thunderbird 59 and earlier\):

```bash
hg clone https://hg.mozilla.org/mozilla-central source/
cd source/
hg clone https://hg.mozilla.org/comm-central comm/
```

### Mach Bootstrap

In the `source` directory run the following command to get additional dependencies needed to install Thunderbird:

```bash
./mach bootstrap
```

You will be presented with the following options:

```text
Please choose the version of Firefox you want to build:
  1. Firefox for Desktop Artifact Mode
  2. Firefox for Desktop
  3. GeckoView/Firefox for Android Artifact Mode
  4. GeckoView/Firefox for Android
```

Please choose option 2 to proceed with a successful build.

This action will install all the remaining libraries and dependencies necessary to build Thunderbird locally.

{% hint style="danger" %}
**Make sure to restart after installing all the requirements, or Thunderbird might encounter a build error.**
{% endhint %}

### Building Thunderbird

Now that you have the prerequisites for Windows, make sure you have the source code via the commands on the Getting Started page:

{% page-ref page="../getting-started.md" %}

Then you can follow the instructions on the Building Thunderbird page:

{% page-ref page="./" %}

