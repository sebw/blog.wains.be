---
date: 2021-10-10
title: "Spin up a Kubernetes cluster in a couple of minutes on Libvirt using Fedora Cloud images"
---

There are many lightweight or light enough Kubernetes distributions out there: k0s, k3s, Microk8s, OpenShift CodeReady Containers, etc.

Those lightweight distributions are a very inexpensive way to learn Kubernetes or OpenShift, and is a good approach to eventually embrace Enterprise Kubernetes distributions.

In this post I will show how to quickly spin up a virtual machine based on Fedora Cloud Image and install k3s on it. Using cloud images is the fastest way to provision and deprovision virtual machines, which allows you to create, play, break, destroy and respin very quickly.

The "in a couple of minutes" from the title is kinda click baity, I will admit.

If you're starting from scratch and need to install the Fedora host and Libvirt, you're looking at having your lightweight Kubernetes running within an hour.

But if you already have your Linux distribution with Libvirt running, I swear, it's gonna take only a few minutes :D

# optional: Enable virtualization and install Fedora

I use Fedora Server and install Libvirt.

Make sure you have virtualization extension enabled on your machine:

```bash
egrep '^flags.*(vmx|svm)' /proc/cpuinfo
```

This should throw out some output. If not, go to your BIOS and find the relevant option.

# optional: Install Fedora and Libvirt

Install Libvirt, as root:

```bash
dnf install @virtualization
systemctl start libvirtd
systemctl enable libvirtd
```

# Download Fedora Cloud Image

Start here if you already had Fedora and Libvirt installed.

From your libvirt host, download the Fedora Cloud base image:

```bash
cd /mnt/DATA/libvirtd/CLOUDIMAGES
wget https://ftp.fau.de/fedora/linux/releases/34/Cloud/x86_64/images/Fedora-Cloud-Base-34-1.2.x86_64.qcow2
```

# Prepare your cloud-init configuration files

Still on the libvirt host..

Create `/var/lib/libvirt/boot/cloud-init/k3s-meta-data` with this content:

```yaml
instance-id: k3s01
```

Then create `/var/lib/libvirt/boot/cloud-init/k3s-user-data` with this content (adjust where marked TODO):

```yaml
#cloud-config
preserve_hostname: False
hostname: k3s01
fqdn: k3s01.TODOexample.org

users:
    - name: root
      ssh-authorized-keys:
      - ssh-rsa AAAAB3Nz TODO add your public SSH key here
    - name: TODOyouruser
      groups: users,wheel
      ssh_pwauth: True
      sudo: ALL=(ALL) NOPASSWD:ALL
      ssh-authorized-keys:
      - ssh-rsa AAAAB3Nz TODO add your public SSH key here

chpasswd:
  list: |
    root:V3ryC0mpl3x!
    TODOyouruser:V3ryC0mpl3x!
  expire: False

packages:
  - firewalld
  - vim
  - container-selinux
  - selinux-policy-base
  - https://rpm.rancher.io/k3s/stable/common/centos/8/noarch/k3s-selinux-0.3-0.el8.noarch.rpm

package_upgrade: false

runcmd:
  - nmcli con mod path 1 con-name eth0  connection.zone trusted
  - nmcli con mod path 1 con-name 'System eth0' ipv6.method disabled
  - nmcli con up  path 1
  - systemctl enable firewalld  --now
  - echo "================ INSTALLING K3S ================"
  - curl -sfL https://get.k3s.io | sh -
  - systemctl  disable  cloud-init
  - reboot
```

The `container-selinux`, `selinux-policy-base` and `k3s-selinux` packages are needed for k3s to install on Fedora.

# Spin up your first VM

Always from the libvirt host terminal, run the next few commands.

I'm going to name the new VM `k3s01`:

```bash
NEWIMG=k3s01
```

Now copy the Cloud image to the VM disk destination:

```bash
cd /mnt/DATA/libvirtd/CLOUDIMAGES
cp Fedora-Cloud-Base-34-1.2.x86_64.qcow2 /mnt/libvirtd/VM/$NEWIMG.qcow2
```

You can now spin up your new VM based on the cloud image and the cloud-init files you have created in the previous steps:

```bash
virt-install --name $NEWIMG \
    --memory 4096 \
    --cpu host \
    --vcpus 2 \
    --graphics none \
    --os-type linux \
    --os-variant fedora34 \
    --import \
    --disk path=/mnt/libvirtd/VM/$NEWIMG.qcow2,format=qcow2,bus=virtio \
    --network type=direct,source=enp1s0,source_mode=bridge,model=virtio \
    --cloud-init meta-data=/var/lib/libvirt/boot/cloud-init/$NEWIMG-meta-data,user-data=/var/lib/libvirt/boot/cloud-init/$NEWIMG-user-data
```

In my case I use macvtap network so I use `--network type=direct,source=enp1s0,source_mode=bridge,model=virtio`.

If you want to use a bridge, replace with `--network bridge=bridge_name`.

The cloud image will boot, your terminal will attach itself to the new VM, you will see some output from cloud-init.

The IP address of your new VM will be displayed at the login prompt.

You can detach from the VM you can do `ctrl + ]`.

# Have fun

From this point on you should be able to SSH with your unprivileged user into your new VM and validate your Kubernetes is running:

```bash
$ sudo kubectl get nodes
NAME              STATUS   ROLES                  AGE   VERSION
k3s01             Ready    control-plane,master   17h   v1.21.5+k3s2
```

Enjoy!
