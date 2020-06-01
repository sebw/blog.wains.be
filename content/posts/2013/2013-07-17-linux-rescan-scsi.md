---
date: 2013-07-17
title: "Rescan SCSI bus under Linux to add a drive without rebooting"
---
Date: 2013-07-17
Tags: Linux

Either:

	echo "- - -" > /sys/class/scsi_host/host0/scan

Or (usually comes with sg3-utils):

	rescan-scsi-bus.sh

If you want to hot extend a drive:

	echo 1 > /sys/class/scsi_device/device/rescan	

I discovered the extend trick [here](https://blogs.it.ox.ac.uk/oxcloud/post/rescanning-your-scsi-bus-to-see-new-storage/)
