---
date: 2020-10-05
title: "Replacing Synology DS213 with a custom build running Fedora"
---

Ever since purchasing a Synology NAS, the Linux admin in me have been frustrated with the limitations and sometimes weird implentations of Synology DSM.

I decided to go back to a fully open and much more manageable system for the sysadmin that I am.

I have a small rack in the garage so I was looking for a small form factor.

## Mainboard: Gigabyte B450 I AORUS PRO WIFI ITX

![](https://blog.wains.be/images/nas2020/mainboard.png)

## PSU: Be quiet! Power 9 ATX 400W

![](https://blog.wains.be/images/nas2020/psu.png)

## SSD: Crucial P2 NVMe 500GB

![](https://blog.wains.be/images/nas2020/ssd.png)

## HDD: 

![](https://blog.wains.be/images/nas2020/hdd.png)

## RAM: Corsair DDR4 32GB XMP 3000 MHz

![](https://blog.wains.be/images/nas2020/ram.png)

## CPU: AMD Ryzen 5 3400G

![](https://blog.wains.be/images/nas2020/cpu.png)

## Case: Silverstone SST-SG13B-Q

![](https://blog.wains.be/images/nas2020/case.png)

Total came out to be 640.39 EUR including shipping.

Everything besides the hard drive was purchased on Amazon.de.

After a terrible experience purchasing hard drives on Amazon, I purchased the Ironwolf on Alternate.be which uses appropriate packaging.

On top of that I purchased a TPLink PCIe gigabyte network interface (12 EUR) to optimize throughput on SPICE connections to my VM.

Everything is supported out of the box on Fedora 32 and very stable.

Don't forget to [turn on virtualization](https://blog.wains.be/2020/2020-09-30-enable-virtualization-amd-ryzen/) in the BIOS if you want to run VMs.