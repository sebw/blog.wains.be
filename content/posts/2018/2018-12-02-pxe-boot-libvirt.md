---
date: 2018-12-02
title: "PXE boot on Libvirt"
---

Allow TFTP for the libvirt network:

```
virsh net-destroy lab
virsh net-edit lab
```

```
 <name>lab</name>
  <uuid>7c0e845a-a3fa-4403-8fb4-2373dfcb416c</uuid>
  <forward mode='nat'/>
  <bridge name='virbr1' stp='on' delay='0'/>
  <mac address='52:54:00:aa:5f:89'/>
  <domain name='lab' localOnly='yes'/>
  <ip address='192.168.123.1' netmask='255.255.255.0'>
    <tftp root='/var/lib/tftpboot'/>
    <dhcp>
      <range start='192.168.123.2' end='192.168.123.254'/>
      <bootp file='pxelinux.0'/>
    </dhcp>
  </ip>
</network>
```

Install packages:

```
sudo dnf install tftp vsftpd syslinux
```

Change VSFTPD config in `/etc/vsftpd/vsftpd.conf`:

```
anonymous_enable=yes
```

Create TFTP config:

```
cp -r /usr/share/syslinux/* /var/lib/tftpboot
mkdir /var/lib/tftpboot/pxelinux.cfg
touch /var/lib/tftpboot/pxelinux.cfg/default
```

Mount ISO and copy pxeboot files to TFTP and ISO to FTP:

```
mount -o loop rhel75.iso /mnt

mkdir /var/ftp/pub/rhel75/
cp -r /mnt * /var/ftp/pub/rhel75/

mkdir /var/lib/tftpboot/rhel75/
cp /mnt/images/pxeboot/* /var/lib/tftpboot/rhel75/

umount /mnt
```

Create the PXE boot menu:

Edit `/var/lib/tftpboot/pxelinux.cfg/default`

```
default menu.c32
prompt 0
timeout 300
ONTIMEOUT local

menu title ########## PXE Boot Menu ##########

label 1
menu label ^1) Install RHEL 7.5 x64 manual
kernel rhel75/vmlinuz
append initrd=rhel75/initrd.img method=ftp://192.168.123.1/pub/rhel75 devfs=nomount

label 2
menu label ^2) Install RHEL 7.5 x64 kickstart
kernel rhel75/vmlinuz
append initrd=rhel75/initrd.img inst.ks=ftp://192.168.123.1/pub/kickstart/rhel75.cfg
```

Create kickstart file `/var/ftp/pub/kickstart/rhel75.cfg`:

```
#version=DEVEL
auth --enableshadow --passalgo=sha512
url --url="ftp://192.168.123.1/pub/rhel75"
graphical
firstboot --enable
ignoredisk --only-use=vda
keyboard --vckeymap=be --xlayouts='be'
lang en_US.UTF-8

network  --bootproto=dhcp --device=eno16777736 --ipv6=auto --activate
network  --bootproto=dhcp --hostname=localhost.localdomain

rootpw --plaintext redhat
services --enabled="chronyd"
timezone Europe/Brussels --isUtc
bootloader --append=" crashkernel=auto" --location=mbr --boot-drive=vda
clearpart --none --initlabel
part /boot --fstype="xfs" --ondisk=vda --size=1024
part pv.202 --fstype="lvmpv" --ondisk=vda --size=9215
volgroup rhel --pesize=4096 pv.202
logvol swap  --fstype="swap" --size=1023 --name=swap --vgname=rhel
logvol /  --fstype="xfs" --grow --maxsize=51200 --size=1024 --name=root --vgname=rhel

%packages
@compat-libraries
@core
chrony
kexec-tools
net-tools
wget

%end

%addon com_redhat_kdump --enable --reserve-mb='auto'

%end

reboot
```

Restore SELinux context:

```
restorecon -RFv /var/lib/tftpboot/
restorecon -RFv /var/ftp/pub/
```

Add firewall rules:

```
firewall-cmd --permanent --add-service=ftp
firewall-cmd --permanent --add-service=tftp
firewall-cmd --reload
```

Start and enable services:

```
systemctl enable vsftpd tftp
systemctl start vsftpd tftp
```

Start a new VM from NIC.

/!\ RHEL7/CentOS7 need at least 2GB of RAM or installer can fail with “wrong fs type” error.