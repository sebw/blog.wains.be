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

I use `nmtui` as an easy interface to NetworkManager. You can obviously use `nmcli`. [This blog post covers nmcli](https://medium.com/@kbidarkar/configuring-bridges-and-vlans-using-nmcli-8cb79f45d3a6).

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

## Regarding VMs in the native VLAN

For VMs that need to sit in the native VLAN (home VLAN ID 10 in my case), just use the host device `enp8s0` which will use macvtap (also known as direct interface).

Keep in mind that communication between host and guests using macvtap is not possible for technical reasons detailed [here](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/6/html/virtualization_host_configuration_and_guest_installation_guide/app_macvtap).

Guests to guests communication is possible.

I personally run a Pi-hole DNS server in a VM on my home network, you have two possible workarounds

### Second NAT NIC on the guest

You create a second NAT network interface, and the libvirt hosts resolves through the NAT IP

```
                  +----------------------------------------------+
                  |                                              |
                  |                libvirt host                  |
Switch trunk      +--------+            ^                        |
   +------------->+ enp8s0 +------------+-----------+            |
                  +----+---+                    +---v----------+ |
                  |    ^   ^-----------------+  |              | |
                  |    |                     |  |   VM home    | |
                  |    +----+                |  |              | |
                  |         |                |  +--------------+ |
                  +----------------------+-----------------------+
                  |         |            |   +------+            |
                  | bridge  |  enp8s0.20 | bridge   |  enp8s0.30 |
                  |         |            |          |            |
                  +----------------------------------------------+
                  |         |            |          |            |
                  |  +------+-------+    |     +----+---------+  |
                  |  |              |    |     |              |  |
                  |  |  VM guest    |    |     |   VM work    |  |
                  |  |              |    |     |              |  |
                  |  +--------------+    |     +--------------+  |
                  +----------------------+-----------------------+
```

### Second physical NIC on the host

You add a second physical NIC on the host and bridge VM on the second NIC. Host and guest will be able to communicate since they are no longer on the same NIC (this is what I have done)

```
                  +----------------------------------------------+
                  |                                    +---------|
                  |                libvirt host        | enp1s0 <---+switch untagged
Switch trunk      +--------+            ^              +---------+
   +------------->+ enp8s0 +------------+-----------+            |
                  +----+---+                    +---v----------+ |
                  |    ^   ^-----------------+  |              | |
                  |    |                     |  |   VM home    | |
                  |    +----+                |  |              | |
                  |         |                |  +--------------+ |
                  +----------------------+-----------------------+
                  |         |            |   +------+            |
                  | bridge  |  enp8s0.20 | bridge   |  enp8s0.30 |
                  |         |            |          |            |
                  +----------------------------------------------+
                  |         |            |          |            |
                  |  +------+-------+    |     +----+---------+  |
                  |  |              |    |     |              |  |
                  |  |  VM guest    |    |     |   VM work    |  |
                  |  |              |    |     |              |  |
                  |  +--------------+    |     +--------------+  |
                  +----------------------+-----------------------+
```

## Conclusion

By using native VLAN on the trunk port, I didn't have to modify anything on the libvirt host network interface so I didn't lose my SSH connection to the host.

By making VLAN aware bridges, I don't need to modify anything inside the guest VM. My VMs just fetch an IP from the DHCP server in the VLAN.

The configuration of VLAN aware bridges with `nmtui` was also possible remotely with no loss of connection.

Everything can be done over a SSH connection without any loss of communication.

Another way is to use one bridge and VLAN filtering, the procedure is detailed on this [Red Hat blog post](https://developers.redhat.com/blog/2017/09/14/vlan-filter-support-on-bridge/).