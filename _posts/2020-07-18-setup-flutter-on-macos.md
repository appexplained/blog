---
layout: post
title: "Quickstart Setup of Flutter on macOS"
date: 2020-07-18
categories: flutter
author: nathan
tags: flutter
image: https://raw.githubusercontent.com/appexplained/blog/gh-pages/assets/post-images/flutter.png
---

> Quickstart Setup of Flutter on macOS

Flutter is an exciting new UI SDK created by Google. With Flutter you can create an app for both Android and iOS from a single codebase. In this post, I'll walkthrough the setup process for getting started on macOS.

### Downloading the Flutter SDK

Clone the latest stable release of the Flutter SDK to a directory on your local machine:
{% highlight bash %}
git clone https://github.com/flutter/flutter.git -b stable --depth 1
{% endhighlight %}

### Adding the Flutter SDK to your PATH

For most developers who use macOS, `/usr/local/bin/` is already in your `PATH` variable. As a result, we can create a symbolic link to our flutter directory. For example:
{% highlight bash %}
ln -s /Users/name/flutter/bin/flutter /usr/local/bin
{% endhighlight %}

Alternatively, you can elect to clone the latest stable release of the Flutter SDK directly to your `/usr/local/bin/` in the previous step.

### Run flutter doctor

You are now ready to run the Flutter CLI commands! There's a convenient built-in CLI command called `flutter doctor` that will check to ensure you have all the required dependencies. Run:
{% highlight bash %}
flutter doctor
{% endhighlight %}

The output of the command will inform you on the next tasks you need to perform. For example:
{% highlight bash %}
Doctor summary (to see all details, run flutter doctor -v):
[✓] Flutter (Channel stable, v1.17.5, on Mac OS X 10.14.6 18G2022, locale en-US)

[✓] Android toolchain - develop for Android devices (Android SDK version 28.0.3)
[✓] Xcode - develop for iOS and macOS (Xcode 11.3.1)
[✓] Android Studio (version 3.6)
[!] IntelliJ IDEA Community Edition (version 2020.1.1)
✗ Flutter plugin not installed; this adds Flutter specific functionality.
✗ Dart plugin not installed; this adds Dart specific functionality.
[✓] VS Code (version 1.47.0)
{% endhighlight %}

### iOS Simulator Setup

Install the latest stable version of Xcode from the macOS App Store. You can then install and configure the Xcode command-line tools by running the following CLI commands:
{% highlight bash %}
sudo xcode-select --switch /Applications/Xcode.app/Contents/Developer
sudo xcodebuild -runFirstLaunch
{% endhighlight %}

One additional item to check is if the Xcode license agreement has been accepted. You can verify this by launching Xcode after you have installed it or running the following CLI command:
{% highlight bash %}
sudo xcodebuild -license
{% endhighlight %}

With Xcode now installed, you can utlize the iOS Simulator. You can either launch it from Spotlight by typing in "Simulator" or from the CLI:
{% highlight bash %}
open -a Simulator
{% endhighlight %}

### Android Emulator Setup

Install the latest stable version of Android Studio, either through _homebrew_ or from the [Android Developer website](https://developer.android.com/studio). Launch Android Studio and go through the Android Studio Setup Wizard. This installs the latest Android SDK, Android SDK CLI Tools, and Android SDK build tools, which are required by Flutter when developing for Android.

Once that has completed, in Android Studio navigate to _Tools > Android > AVD Manager_ and select _Create Virtual Device_. From here, you can select a device specification. For improved emulator performance select _Hardware - GLES 2.0_ before finishing the AVD configuration. Now in the Android Virtual Device Manager, click Run in the toolbar to launch your emulator.
