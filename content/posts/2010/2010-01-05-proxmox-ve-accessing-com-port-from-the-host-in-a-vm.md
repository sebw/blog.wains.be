---
date: 2010-01-05
title: "Proxmox VE - accessing COM port from the host in a VM"
---







categories:
- Virtualization


If you want to access the COM/serial port of your host machine from a KVM virtual machine in Proxmox VE, simply do the following :

`vim /etc/qemu-server/104.conf`
where 104 is the ID of the VM

add **"args: -serial /dev/ttyS0"** to the end of the file

It should look like this :

`name: testVM
ide2: debian-500-i386-netinst.iso,media=cdrom
smp: 1
vlan0: rtl8139=XX:XX:XX:XX:XX:XX
bootdisk: ide0
ide0: vm-104-disk.qcow2
ostype: other
memory: 256
args: -serial /dev/ttyS0`

If the VM is already running, you may have to stop the VM completely and start it again for it to see the COM port.
If you simply reboot the machine, it may not see it.

Proxmox VE : [http://pve.proxmox.com/wiki/Main_Page](http://pve.proxmox.com/wiki/Main_Page)
