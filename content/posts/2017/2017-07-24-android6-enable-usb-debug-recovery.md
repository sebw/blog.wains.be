---
date: 2017-07-24
title: "Enabling USB debug from recovery on Android 6.0.1"
---

Tested on a Moto G 1st gen XT1032 running Cyanogen 13

Enabling USB debug allowed me to get logcat working at boot.

Android SDK tools should be installed on your computer (adb).

Boot into recovery (power + volume down on Moto G)

Plug your phone to the computer.

Run "./adb shell mount data" from your computer shell.

Run "./adb shell mount system"

./adb pull /data/property/persist.sys.usb.config /tmp

Edit it to "mtp,adb" and save.

./adb push /tmp/persist.sys.usb.config /data/property

./adb pull /system/build.prop /tmp

Add these lines and save:

        persist.service.adb.enable=1 
        persist.service.debuggable=1
        persist.sys.usb.config=mtp,adb

./adb push /your-dir/build.prop /system/

Make sure your device is authorized:

adb push ~/.android/adbkey.pub /data/misc/adb/adb_keys
