---
date: 2005-10-26
title: "disabling IRQ 5 error when installing Red Hat/CentOS on a ASUS P4P800"
---

When starting the Red Hat/CentOS installer, it kept giving me an error, over and over :
"disabling IRQ 5"

The installer was running very slow as well..
It seemed to me IRQ 5 was bound to the DVD drive



I had to disable the "enhanced mode" of the IDE drives under the (AMI) BIOS :
Steps :
- Boot the machine (no way !)
- Enter the BIOS
- Main menu
- IDE Configuration
- Onboard IDE Operate Mode
- Enhanced Mode --> Compatible Mode
- Save and reboot

The Red Hat/CentOS was no longer giving the error after changing this setting...
Hope it helps..
