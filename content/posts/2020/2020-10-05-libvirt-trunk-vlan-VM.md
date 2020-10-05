---
date: 2020-10-05
title: "libvirtd with trunk port and VM in VLAN aware bridges"
---

I use my small home server as a virtualization host running Fedora 32.

It has only one network interface `enp8s0`.

I have 3 VLANs:

- 10 home
- 20 guest
- 30 work

My NAS sits in VLAN 10 and I wanted to be able to run virtual machines in VLAN 20 and 30 with no tagging done inside the guests VMs.

First of all, the host must be placed behind a trunk port.

Then we must create VLAN aware bridges for the guests VMs.

Let's see how to do it.

## Configure the trunk on the switch

My switch is a Ubiquiti EdgeSwitch 24 Lite. As a result the following might not work for your gear.

```
enable
configure
interface 0/23
    description NAS
    switchport mode trunk
    switchport trunk allowed vlan 1,10,20,30
    switchport trunk native vlan 10
    exit
```

The native VLAN is my home network, so anything coming from my NAS or from a VM using the macvtap device should fall within the home VLAN.

We need to configure VLAN aware bridges for other VLANs.

## Configure the bridge for the guest VLAN

I use `nmtui` as an easy interface to NetworkManager. You can obviously use `nmcli`.

Install the package if you want to use the TUI `dnf install NetworkManager-tui`.

Start `nmtui`.

Create a new connection of type bridge:

![](https://blog.wains.be/images/libvirt/libvirt1.png)

Give it a name (`bridge-guest` here) and disable STP (unless you really need it in your setup):

![](https://blog.wains.be/images/libvirt/libvirt2.png)

When done add a bridge slave of type VLAN:

![](https://blog.wains.be/images/libvirt/libvirt3.png)

Give the new VLAN a name (`vlan-guest` here) and specify the device name.

The device name is the original network interface followed by a dot and the VLAN id:

`enp8s0.20`

The TUI will figure out the parent and VLAN ID automatically based on the device name:

![](https://blog.wains.be/images/libvirt/libvirt4.png)

The resulting bridge:

![](https://blog.wains.be/images/libvirt/libvirt5.png)

Save the new bridge, activate it and quit `nmtui`.

Now when you configure your VM, do not choose a predefined libvirt network.

Instead just select "specify shared device name" and type the name of your new bridge `bridge-guest`:

![](https://blog.wains.be/images/libvirt/libvirt6.png)

After starting the VM it should look like this:

![](https://blog.wains.be/images/libvirt/libvirt7.png)

Your guest VM should now be sitting in the guest VLAN.

You can now redo the procedure for any other VLANs.

## Conclusion

By making VLAN aware bridges, you don't need to modify anything inside the guest VM.

By using native VLAN on the trunk port, I didn't have to modify anything on the libvirt host network interface (and losing remote connectivity to the host, even if it's not so far away [garage]).

The configuration of VLAN aware bridges with `nmtui` was also possible remotely with no loss of connection.
