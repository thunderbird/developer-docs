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

Open the Device Manager by clicking the Device Manager icon (<img src="../../../.gitbook/assets/Screenshot 2026-10-13 at 2.23.25 PM (1).png" alt="" data-size="line">). This will open a third panel on the right side of Android Studio.

Click the "+" icon to "Create Virtual Device" and pick a phone profile (e.g. Pixel 10).

<figure><img src="../../../.gitbook/assets/Screenshot 2026-10-13 at 2.33.09 PM.png" alt=""><figcaption></figcaption></figure>

After you click Next, you can choose a system image. Note that the "Services" defaults to "Google Play Store" but you may want to change this to "Google APIs" if you want root/adb flexibility. Google Play images work too but are more locked down.

<figure><img src="../../../.gitbook/assets/Screenshot 2026-10-13 at 2.35.39 PM.png" alt=""><figcaption></figcaption></figure>

Click "Finish" and confirm the download. When the image finished downloading, you can click the play button next to the new device (the above example shows the new device is named "Pixel 10") to run it within Android Studio.

<figure><img src="../../../.gitbook/assets/Screenshot 2026-10-13 at 2.44.24 PM.png" alt=""><figcaption></figcaption></figure>

To install your debug build in the emulator running in Android Studio, you will need to go to the terminal to find which emulator device this is:

```
adb devices
```

In this example, the only one listed is `emulator-5554` and we will install our debug apk to this emulator with the following command (run from the root project of the git repository):

```
adb -s emulator-5554 install app-thunderbird/build/outputs/apk/foss/debug/app-thunderbird-foss-debug.apk
```

Now you see the debug build of Thunderbird for Android daily has replaced the GMail application.

<figure><img src="../../../.gitbook/assets/Screenshot 2026-10-13 at 2.50.45 PM.png" alt=""><figcaption></figcaption></figure>

#### Command Line

Make sure `ANDROID_HOME` is set (see [this step in the Setting Up a Build Environment page](setting-up-a-build-environment.md#set-android_home)) and update your `PATH`:

```
echo 'export PATH=$PATH:$ANDROID_HOME/emulator:$ANDROID_HOME/platform-tools:$ANDROID_HOME/cmdline-tools/latest/bin' >> ~/.zshrc
```

Source this file so that it takes effect:

```
source ~/.zshrc
```

Install "Android SDK Command-line Tools (latest)" from Android Studio’s Settings > Languages & Frameworks > Android SDK > SDK Tools.

Install the emulator and a system image (use `x86_64` instead of `arm64-v8a` on Intel):

```
sdkmanager "emulator" "platform-tools" "system-images;android-35;google_apis;arm64-v8a"
```

Create the AVD:

```
avdmanager create avd -n tb_test -k "system-images;android-35;google_apis;arm64-v8a" -d pixel_10
```

Start it:

```
emulator -avd tb_test
```

{% columns %}
{% column %}
<figure><img src="../../../.gitbook/assets/Screenshot 2026-10-13 at 2.08.32 PM.png" alt=""><figcaption></figcaption></figure>
{% endcolumn %}

{% column %}
Once your emulator is running, you can tell from the port which adb device this is.
{% endcolumn %}
{% endcolumns %}

Install your debug build in the emulator with:

```
adb -s emulator-5556 install app-thunderbird/build/outputs/apk/foss/debug/app-thunderbird-foss-debug.apk
```

{% columns %}
{% column %}
<figure><img src="../../../.gitbook/assets/Screenshot 2026-10-13 at 2.16.00 PM.png" alt=""><figcaption></figcaption></figure>
{% endcolumn %}

{% column %}
Now you see the debug build of Thunderbird for Android daily has replaced the GMail application.
{% endcolumn %}
{% endcolumns %}
