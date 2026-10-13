# Running your build

You can either test drive your new build on a physical Android device or in an emulator on your computer.

## Android device

If you have a physical Android device that you would like to use to try out your new build, then you need to first make sure the device has the [developer options and USB debugging enabled](https://developer.android.com/studio/debug/dev-options).

If you built the package from the command line, then the resulting APK file should be in `app-thunderbird/build/outputs/apk/<flavor>/<build type>/` and for this example, we'll test `app-thunderbird/build/outputs/apk/foss/daily/app-thunderbird-foss-daily-unsigned.apk` on a Google pixel device.

### Windows

lala

### Linux

lala

### MacOS

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

## Emulator

### Windows

lala

### Linux

lala

### MacOS

Since you already have the Android studio SDK, you only need an emulator package, a system image, and a virtual device (AVD). You can do it in Android Studio or entirely from the command line.

#### Android Studio

lala

#### Command Line

Make sure `ANDROID_HOME` is set (see [this step in the Setting Up a Build Environment page](setting-up-a-build-environment.md#set-android_home)) and update your `PATH`:

```
echo 'export PATH=$PATH:$ANDROID_HOME/emulator:$ANDROID_HOME/platform-tools:$ANDROID_HOME/cmdline-tools/latest/bin' >> ~/.zshrc
```

If you are unsure that your `PATH` environment variable has all the right pieces, you can open `~/.zshrc` with your favorite editor and add the above export line

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
