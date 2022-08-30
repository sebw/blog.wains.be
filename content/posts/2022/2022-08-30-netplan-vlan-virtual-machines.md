---
date: 2022-08-30
title: "Create VLANs for your libvirt virtual machines with Ubuntu's netplan"
---

I work at Red Hat but I happen to run some Ubuntu systems.

Ubuntu has `netplan` for network management. I have to admit I find it easier than the `nmcli` or `nmtui` of Network Manager.

In 2020, I wrote about how to configure [VLAN for libvirt virtual machines](https://blog.wains.be/2020/2020-10-05-libvirt-trunk-vlan-vm/).

I'll let you go through the 2020 article. The important thing to keep in mind is how a VM can't connect to the host in the same VLAN in this setup. My trick was to use a second NIC.

If you want to achieve the same setup on Ubuntu it's fairly simple(r).

Edit `/etc/netplan/config.yaml`:

```yaml
network:
  renderer: networkd
  version: 2
  ethernets:
    iftrunk:
      dhcp4: false
      match:
        macaddress: "aa:bb:cc:dd:ee:ff"
      set-name: iftrunk
      addresses:
      - 10.10.0.20/16
      routes:
        - to: default
          via: 10.10.0.1
          metric: 200
    ifmgmt:
      dhcp4: false
      match:
        macaddress: "gg:hh:ii:jj:kk:ll"
      set-name: ifmgmt
      addresses:
        - 10.10.0.2/16
      routes:
        - to: default
          via: 10.10.0.1
          metric: 100
      nameservers:
        addresses: [10.10.0.4]
  vlans:
    guest:
      id: 20
      link: iftrunk
      addresses: [10.20.0.2/16]
    device:
      id: 40
      link: iftrunk
      addresses: [10.40.0.2/16]
```

Run `netplay apply` and of you go.

The metric is needed to define which NIC gets priority to the outside world.