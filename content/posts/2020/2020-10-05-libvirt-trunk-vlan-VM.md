---
date: 2020-10-05
title: Configuring VLANs for your Libvirt virtual machines
---

Previous title was "libvirtd with trunk port and VM in VLAN aware bridges".

I use my small home server as a virtualization host running Fedora 32.

It has only one network interface `enp8s0`.

I have 3 VLANs:

- 10 home
- 20 guest
- 30 work

My NAS sits in VLAN 10 and I wanted to be able to run virtual machines in VLAN 20 and 30 with no tagging done inside the guests VMs.

First of all, the host must be connected to a trunk port on your switch.

Then we must create VLAN aware bridges on the host for the guests VMs.

Let's see how to do it.

## Configure the trunk on the switch

My switch is a Ubiquiti EdgeSwitch 24 Lite. Adjust accordingly to your gear.

```python
enable
configure
interface 0/23
    description NAS
    switchport mode trunk
    switchport trunk allowed vlan 1,10,20,30
    switchport trunk native vlan 10
    exit
```

The native VLAN is my home network, so any traffic coming from my NAS or from a VM using the macvtap `enp8s0` device should fall within the home VLAN.

We need to configure the host with VLAN aware bridges for other VLANs.

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

There is a known limitation for VMs that need to use the same VLAN as the host.

In such a scenario the VM would be using the host device `enp8s0` which will use macvtap.

The communication between host and guests using macvtap is not possible for technical reasons detailed [here](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/6/html/virtualization_host_configuration_and_guest_installation_guide/app_macvtap).

Guests to guests communication is possible.

I personally run a Pi-hole DNS server in a VM on my home network, so it means the host can't use Pi-hole as the resolver.

There are two possible workarounds.

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

You can also add a second physical NIC on the host and bridge the VMs on the macvtap of the second NIC.

Host and guest will then be able to communicate since they are no longer sharing the same NIC (this is what I have done using a cheap 12 euros tplink Gigabit Ethernet card).



```
                  +----------------------------------------------+
                  |                                    +---------|
                  |                libvirt host     +--+ enp1s0 <---+switch untagged
Switch trunk      +--------+            ^           |  +---------+
   +------------->+ enp8s0 +------------+           |            |
                  +----+---+                    +---+----------+ |
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

Now that you have two network interface in the same subnet, you will need to configure metric priority like this:

```
[root@srv ~]# route -n
Kernel IP routing table
Destination     Gateway         Genmask         Flags Metric Ref    Use Iface
0.0.0.0         10.10.0.1       0.0.0.0         UG    10     0        0 enp8s0
0.0.0.0         10.10.0.1       0.0.0.0         UG    100    0        0 enp1s0
```

I give `enp8s0` (host traffic) priority over `enp1s0` (VM).

## Conclusion

Native VLAN on the trunk port means no tagging needed for the traffic of the host.

By making VLAN aware bridges, I don't have to configure VLAN tagging inside VMs. I just add a NIC to the VM that uses the appropriate bridge. The VM is able to communicate with the DHCP from then on.

Configuration of VLAN aware bridges with `nmtui` did not disrupt traffic. I didn't lose my SSH connection to the host while configuring them.

So all in all, everything can be performed remotely.

Besides the two options presented above, there's another option where you use one bridge and VLAN filtering. The procedure is detailed on this [Red Hat blog post](https://developers.redhat.com/blog/2017/09/14/vlan-filter-support-on-bridge/) (I have not tested it).