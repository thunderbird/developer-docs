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

At the top of Android Studio, there should be a "Run/Debug Configurations" drop down menu that says  `app-k9mail` but you can choose to select `app-thunderbird` instead (see below).

<figure><img src="../../../.gitbook/assets/image (1).png" alt=""><figcaption></figcaption></figure>

Choose which app module you would like to build. For this example, we will select `app-thunderbird` .

Now choose which build variant you would like to work with from the Build Variants window. You should see the Build Variants tool in the left panel; it is the hammer with a waffle icon: <img src="../../../.gitbook/assets/image (5).png" alt="" data-size="line">

If you don't see it, you can add it to the panel in View > Tool Windows > Build Variants.

### Building from command line

A Gradle wrapper is included in the project, so you can build the project from the command line without installing Gradle globally. Run the following commands from the root of the project, where `./gradlew` is the Gradle wrapper script and the command `build` runs tests and other checks, while `assemble` only compiles the code and packages the APK.

```
# Build all variants
./gradlew assemble
./gradlew build

# Build debug or release variant
./gradlew assembleDebug
./gradlew assembleRelease

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

## Running your build

You can either test drive your new build on a physical Android device or in an emulator on your computer.

### Android device

If you have a physical Android device that you would like to use to try out your new build, then you need to first make sure the device has the [developer options and USB debugging enabled](https://developer.android.com/studio/debug/dev-options).

If you built the package from the command line, then the resulting APK file should be in `app-thunderbird/build/outputs/apk/<flavor>/<build type>/` and for this example, we'll test `app-thunderbird/build/outputs/apk/foss/daily/app-thunderbird-foss-daily-unsigned.apk` on a Google pixel device.

#### Windows

lala

#### Linux

lala

#### MacOS

If you are on MacOS, then you will need `android-platform-tools` to be able to transfer the apk file. You can install it with:

```
brew install android-platform-tool
```

Connect the phone with a USB cable and authorize it's usage. Then run:

```
adb devices
```

The phone should show a prompt asking whether to allow USB debugging from this computer. Accept it, and run `adb devices` again. Your device should be listed as `device` (not `unauthorized`). If you have multiple devices, specify your phone with the `-s` flag.

Install the APK file:

```
adb -s 65150DLCR0022B install app-thunderbird/build/outputs/apk/foss/debug/app-thunderbird-foss-debug.apk
```

Then you will see the Thunderbird for Android app icon with the "Debug" label in the phone that you can run and test.

### Emulator

#### Windows

lala

#### Linux

lala

#### MacOS

Since you already have the Android studio SDK, you only need an emulator package, a system image, and a virtual device (AVD). You can do it in Android Studio or entirely from the command line.

**Android Studio**

lala

**Command Line**

Make sure `ANDROID_HOME` is set (usually `~/Library/Android/sdk`) and these are on your `PATH`:

bash

```bash
export ANDROID_HOME=$HOME/Library/Android/sdk
export PATH=$PATH:$ANDROID_HOME/emulator:$ANDROID_HOME/platform-tools:$ANDROID_HOME/cmdline-tools/latest/bin
```

Install the emulator and a system image (use `x86_64` instead of `arm64-v8a` on Intel):

bash

```bash
sdkmanager "emulator" "platform-tools" "system-images;android-35;google_apis;arm64-v8a"
```

Create the AVD:

bash

```bash
avdmanager create avd -n tb_test -k "system-images;android-35;google_apis;arm64-v8a" -d pixel_8
```

Start it:

bash

```bash
emulator -avd tb_test
```

If `avdmanager` or `sdkmanager` isn’t found, install **Android SDK Command-line Tools (latest)** from Android Studio’s **Settings > Languages & Frameworks > Android SDK > SDK Tools**.
