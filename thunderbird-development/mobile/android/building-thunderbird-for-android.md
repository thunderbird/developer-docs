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

### Building from Android Studio

At the top of Android Studio, there should be a "Run/Debug Configurations" drop down menu that says `app-k9mail` but you can choose to select `app-thunderbird` instead (see below).

<figure><img src="../../../.gitbook/assets/image (1).png" alt=""><figcaption></figcaption></figure>

Choose which app module you would like to build. For this example, we will select `app-thunderbird` .

Now choose which build variant you would like to work with from the Build Variants window. You should see the Build Variants tool in the left panel; it is the hammer with a waffle icon: <img src="../../../.gitbook/assets/image (5).png" alt="" data-size="line">

If you don't see it, you can add it to the panel in View > Tool Windows > Build Variants.

### Building from command line

A Gradle wrapper is included in the project, so you can build the project from the command line without installing Gradle globally. Run the following commands from the root of the project, where `./gradlew` is the Gradle wrapper script:

* `assemble` - only does the build. It compiles the code and generates the APK artifact.
* `build` - does everything the `assemble` command does, but also runs tests and other checks.

{% hint style="info" %}
Note that the `build` command takes much longer than the `assemble` command. If the objective is to only build the artifact, `assemble` is recommended.
{% endhint %}

```
# Build all variants
./gradlew assemble
./gradlew build

# Build debug or release variant
./gradlew assembleDebug
./gradlew assembleRelease

#Build Foss or Full variants
./gradlew assembleFossDebug
./gradlew assembleFullDebug

# Build a specific app module
./gradlew :app-thunderbird:assembleDebug
./gradlew :app-k9mail:assembleDebug

# Build a specific library/feature module
./gradlew :module-name:build
```

Replace `module-name` with the actual name of the module you want to build. For example, the command to build the `app-thunderbird` module is:

```
./gradlew :app-thunderbird:build
```

The resulting APK file should be in `app-thunderbird/build/outputs/apk/<flavor>/<build type>/`

There are 2 flavors you will see:

* _full_ - This variant includes the Google Play Services we use (Billing, for example). This variant is the APK that goes to Google Play Store.
* _foss_ - This variant does not include the Google Play Services. This variant is the one available on GitHub and F-Droid.

Under each full/foss flavor, you will find 4 different build types:

* _daily_ - The bleeding edge that includes the most recent commit pulled from the [thunderbird-andriod main branch](https://github.com/thunderbird/thunderbird-android)
* _beta_ - The most recent beta release.
* _release_ - The most recent stable release.
* _debug_ - The most recent daily build but with debug symbols.

{% hint style="info" %}
Note that the debug build is the only one that is signed and is therefore the easiest to install and Android won’t install an unsigned APK.
{% endhint %}
