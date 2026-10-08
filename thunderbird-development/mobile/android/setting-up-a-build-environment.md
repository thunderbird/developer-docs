---
description: >-
  This page has all the information you need to get your development environment
  set up and ready to hack on Thunderbird for Android.
---

# Setting Up a Build Environment

Before you can build Thunderbird for Android, please follow your platform's build prerequisites steps.

## Windows

lala

## Linux

lala

## MacOS

### Git

The Thunderbird for Android source code is stored in a Git repository. This means you will need to install Git if it is not already available. Here is a quick command to install it:

```
brew install git
```

You are ready to proceed to the next section to grab the source code.

### Get the source code

Go to the [Thunderbird for Android repository](https://github.com/thunderbird/thunderbird-android).

Click the **Fork** button in the top-right corner.

Create a fork under your GitHub account.

We will import this forked project in Android Studio a bit later, once that is set up.

### JDK

You will need Java Development Kit (JDK) version 21 or higher, which can be installed from homebrew. If you have not yet setup homebrew, please see [the homebrew installation instructions](https://brew.sh/).

```
brew install --cask temurin
```

Then you should be able to verify the install with:

```
java -version
```

You should see something like `openjdk version "27" 2026-09-15` with "Temurin" in the runtime line. If you see a different version, macOS can have multiple versions of JDK installed and you can list them with:

```
/usr/libexec/java_home -V
```

You can point to the JDK version you want to use ("27" in this example) by adding this to your `~/.zshrc` file:

```
export JAVA_HOME=$(/usr/libexec/java_home -v 27)
```

Then reload with `source ~/.zshrc` and check `java -version` again.

### Android Studio - Install

The latest stable version of Android studio is recommended. Install it with:

```
brew install --cask android-studio
```

Once it's installed, run it and go through the wizard. Following the "standard" install type is sufficient for this project. (The only difference the "custom" install type offers is choosing the UI theme and selecting which additional SDK components to download.)

After this standard install completes, you'll be presented a "Welcome to Android Studio" window. Before we import the project, quit Android Studio and set your `ANDROID_HOME` environment variable.

### Set ANDROID\_HOME

You will need to set the `ANDROID_HOME` environment variable or else the sync and build in Android Studio will not work.

Find the path in the Android Studio settings (gear icon in the top right) > SDK Manager. In the Settings Manager window that opens, you should be in the Android SDK panel, and you should see the "Android SDK Location". It will probably be something similar to `/Users/<user>/Library/Android/sdk` .

In your terminal, add this environment variable to the end of your `~/.zshrc`:

```
echo 'export ANDROID_HOME=/Users/<user>/Library/Android/sdk' >> ~/.zshrc
```

Source this file so that it takes effect:

```
source ~/.zshrc
```

and restart Android Studio.

### Android Studio - Setup

Now that we have Android Studio installed and our `ANDROID_HOME` environment variable set, we need to import the project repository and install a plugin before we can successfully build.

#### **Import the project**

Start Android Studio and on the "Welcome to Android Studio" screen, select "Open" to import our existing project. Navigate to the cloned repo and open it. You will see in the bottom of the app window that an import has been started. Wait for this to complete before moving on.

#### **Kotlin Multiplatform plugin**

For the best development experience, we recommend you add the Kotlin Multiplatform plugin. To add this plugin, open the settings (top right gear icon) > Plugins and search for "Kotlin Multiplatform" and install it. Then restart Android Studio.

Now you are ready to proceed to the next step: Building Thunderbird for Android!
