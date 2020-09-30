---
date: 2020-09-30
title: "libvirtd won't start with --listen option on Fedora" 
---

If you have always passed the `--listen` option under `/etc/sysconfig/libvirtd` and you can't get libvirt to listen on a recent Fedora install, read on.

Fedora has changed the way to start libvirt in listen mode.

The new way is to not touch `/etc/sysconfig/libvirtd` at all, and use libvirt socket services: `systemctl enable libvirtd-tls.socket; systemctl start libvirtd-tls.socket`.

You can keep using the "old" way by setting `--listen` in `/etc/sysconfig/libvirtd` **but before running** `systemctl enable libvirtd.service; systemctl start libvirtd.service` you will need to mask socket services:

`systemctl mask libvirtd.socket libvirtd-ro.socket libvirtd-admin.socket libvirtd-tcp.socket libvirtd-tls.socket`

The new way currently has a bug and libvirt would only listen on the IPv6 interface: https://bugzilla.redhat.com/show_bug.cgi?id=1877243

I suggest you stick to the old way then :-)

Sources:

https://bugzilla.redhat.com/show_bug.cgi?id=1750340