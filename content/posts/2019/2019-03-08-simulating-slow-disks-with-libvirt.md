---
date: 2019-03-08
title: "Simulating slow disks with Libvirt"
---

I once wrote about how to [simulate latency on Linux](https://blog.wains.be/2016/2016-03-23-simulating-latency-linux/).

The article has been useful for myself recently, as I was at a customer who was having all kind of weird issues, probably caused by unreliable network, and slow performing disks.

I recreated their environment on my Libvirt lab.

You can throttle the I/O of a Libvirt disk, as documented [here](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/virtualization_deployment_and_administration_guide/sect-managing_guest_virtual_machines_with_virsh-disk_io_throttling) and [here](https://fedoraproject.org/wiki/QA:Testcase_Virtualization_IO_Throttling).

```
```

Example:

```
```