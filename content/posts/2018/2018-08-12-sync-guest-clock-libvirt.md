---
date: 2018-08-12
title: "Sync guest clock on resume under Libvirt/KVM"
---

My host is RHEL7 running Gnome. Guest is Fedora 28 running i3 windows manager.

I use **virt-manager** and not virsh to manage my VM's.

TL;DR: it doesn't work for me, jump to **Conclusion** to read about my endeavour.

Edit: my host is now running Fedora 29 and the guest F29 too. The issue persists.

### On the host you need:

```
systemctl enable libvirt-guests
systemctl start libvirt-guests
```

/etc/sysconfig/libvirt-guests:

`SYNC_TIME=1`

Restart libvirt: `systemctl restart libvirtd`

This setting will **try** to sync the clock for you on resume. It never worked for me.

### On the guest:

You should add a channel gemu-ga to the guest hardware configuration.

Install and start the agent:

```
yum install qemu-guest-agent
systemctl enable qemu-guest-agent
systemctl start qemu-guest-agent
```

### Make sure the agent is working

We will "ping" the guest from the host to make sure the agent works.

From the host:

`virsh qemu-agent-command Fedora '{"execute":"guest-ping"}'`

The output should return `{"return":{}}`

### Sync the clock manually

Since automatic clock sync doesn't work for me, I have to force sync manually.

From the host:

`virsh qemu-agent-command Fedora '{"execute":"guest-set-time"}'`

Or from the guest:

`hwclock -s`

### Conclusion

At this day, automatic clock sync on resume doesn't work for me.

I either have to run `guest-set-time` from the host, or run `hwclock -s` from the guest.

I decided to run a cron on the guest every 5 minutes with the later command and get rid of chronyd/ntpd. I read other people did that as a workaround.

My host is synced with NTP, of course.

I have checked a lot of documentations, among them:

[https://www.redhat.com/archives/libvirt-users/2015-September/msg00099.html](https://www.redhat.com/archives/libvirt-users/2015-September/msg00099.html)

[https://serverfault.com/questions/334698/how-to-keep-time-on-resumed-kvm-guest-with-libvirt](https://serverfault.com/questions/334698/how-to-keep-time-on-resumed-kvm-guest-with-libvirt)

[https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/virtualization_deployment_and_administration_guide/chap-kvm_guest_timing_management](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/virtualization_deployment_and_administration_guide/chap-kvm_guest_timing_management)

That last link states:

```
When a guest is resumed after a pause or a restoration process, a command to synchronize the guest clock to a specified value should be issued by the management software (such as virt-manager). This synchronization works only if the QEMU guest agent is installed in the guest and supports the feature. The value to which the guest clock synchronizes is usually the host clock value. 
```

But that never worked for me, despite being able to contact the agent from the host.

If you have the solution to this problem, hit me up on Github!