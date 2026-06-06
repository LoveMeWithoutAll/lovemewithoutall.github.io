---
layout: single
title: Scrcpy - Android Mirroring
date: 2025-03-18 08:50:30.000000000 +09:00
type: post
header:
    teaser: "/assets/images/walking-android.jpg"
    image: "/assets/images/walking-android.jpg"
categories:
- IT
tags: [Android]
---

1. Installation
    1. macos: brew installl scrcpy
2. Connect the Android device
    1. Connect the Mac and the Android device via USB
    2. Turn on USB debugging on the Android device
    3. Verify the connection: adb devices
3.  Enter the command in the terminal: scrcpy

## Troubleshooting when adb devices fails to run (on MacOS)

1. Unplug the Android device from USB and plug it back in to grant device access permission
1. If that still doesn't work, go to System Settings > Privacy & Security > Full Disk Access and grant permission to the terminal app
1. Restart adb: adb kill-server && sudo adb start-server && adb devices
1. If that still doesn't work, reinstall the Android connection tools: brew install android-platform-tools android-file-transfer
1. Reinstall scrcpy as well: brew install scrcpy
1. Restart adb: adb kill-server && sudo adb start-server && adb devices
1. Run scrcpy

20250318

edit: 20250502
