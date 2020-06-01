---
date: 2010-03-24
title: "Adding a new disk drive to a Linux VMware host without rebooting"
---







categories:
- Linux
- Virtualization


After adding a new drive to a VMware (and probably other virtualization systems) host, it is possible to make the Linux OS rescan the SCSI bus.
The new drive would appear without the need to reboot the host.

Use the following command :

`echo "- - -" > /sys/class/scsi_host/hostX/scan`

hostX is usually host0.

If you check the output of dmesg, you should see the new drive detected.
You can then proceed and partition and format your new drive.

Source (and comprehensive howto) : [http://www.cyberciti.biz/tips/vmware-add-a-new-hard-disk-without-rebooting-guest.html](http://www.cyberciti.biz/tips/vmware-add-a-new-hard-disk-without-rebooting-guest.html)

More info : [http://kbase.redhat.com/faq/docs/DOC-3942](http://kbase.redhat.com/faq/docs/DOC-3942)
