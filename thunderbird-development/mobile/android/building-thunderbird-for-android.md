---
description: How to build and run Thunderbird for Android.
---

# Building Thunderbird for Android

## Build Prerequisites

Depending on your Operating System you will need to carry out a different process to prepare your machine. So make sure you have first completed the steps for setting up a build environment for your OS.

{% content-ref url="setting-up-a-build-environment.md" %}
[setting-up-a-build-environment.md](setting-up-a-build-environment.md)
{% endcontent-ref %}

Then come back and continue following these build instructions.

## Building

#### Building from Android Studio

At the top of Android Studio, there should be a "Run/Debug Configurations" drop down menu that says  `app-k9mail` but you can choose to select `app-thunderbird` instead (see below).

<figure><img src="../../../.gitbook/assets/image (1).png" alt=""><figcaption></figcaption></figure>

Choose which app module you would like to build. For this example, we will select `app-thunderbird` .

Now choose which build variant you would like to work with from the Build Variants window. You should see the Build Variants tool in the left panel; it is the hammer with a waffle icon: <img src="../../../.gitbook/assets/image (5).png" alt="" data-size="line">

If you don't see it, you can add it to the panel in View > Tool Windows > Build Variants.

#### Building from command line
