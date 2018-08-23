---
author: simon.jonassen@gmail.com
comments: true
date: 2018-06-01 00:00:00+01:00
layout: post
title: Get started with Flutter in 30 minutes
tags: [today_i_learned, programming, android, osx]
share: true
---

[Flutter](https://flutter.io/) is a great Google SDK allowing effortless creation of mobile apps with native interfaces
on iOS and Android. To install it follow [the official installation instructions](https://flutter.io/get-started/install/). Here are a few additional tips for Mac and Windows:

* If you are using [fish](https://fishshell.com/), add `set PATH /Users/simonj/flutter/bin $PATH` to your `.config/fish/config.fish`
* For android emulation support install Androind Studio and [create a new emulated device](https://developer.android.com/studio/run/managing-avds), lets call it Pixel_2_API_26. To launch the emulator, run `~/Library/Android/sdk/tools/emulator -avd Pixel_2_API_26` on Mac or `C:\Users\<name>\AppData\Local\Android\Sdk\emulator\emulator.exe -avd Pixel_2_API_26` on Windows.
* Disabling Hyper-V may help if you experience Windows crashes when running Android emulator.
* Some useful commands:
  * flutter doctor # helps to diagnose problems, install missing components, etc.
  * flutter build apk # builds apk
  * flutter install -d <deviceid> # installs apk to a device. to use your actual phone, mount the phone with usb debug enabled
  * flutter devices # to list devices
* To use visual studio code, follow [these instructions](https://www.youtube.com/watch?v=hhP1tE-IHos) or just run `code .` from your flutter terminal/project and install Flutter and Dart plugins.
* Check out [awesome-flutter](https://github.com/Solido/awesome-flutter) for example and inspiration!
