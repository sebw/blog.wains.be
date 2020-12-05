---
date: 2017-02-23
title: "Poor performances with Spice and QXL and mouse in KVM"
---

We entered the 21st century at work and got new workstations.

I decided to install Red Hat Enterprise 7 as a virtualization host, running my usual Linux Mint LMDE as guest.

I use virt-manager to connect to the guest over Spice.

It turns out, video performances were terrible. For some reason QXL drivers were not loaded on Linux Mint. It was impossible to load those drivers.

I could have been bothering recompiling those drivers and see if I could fix it that way, but I didn't have to run Mint for any specific reason.

I decided to check Fedora 25 workstation. Under Gnome 3 it was somewhat faster, but still not blazing fast, and displaying artifacts like bad jpeg compression. The problem there was Wayland.

I haven't been using Gnome in years anyway. I've been using [i3](https://i3wm.org/) for about a year now, it runs on top of X11.

Now everything is great and fast under Fedora 25 with i3.

For best performances on the guest, this is what you should do:

- install Spice Agent
- install X11 QXL drivers

On the guest, check those with:

- `lspci | grep -i vga` should return something like `QXL paravirtual graphic card`
- `ps aux | grep spice-vdagent` should output something
- `lsmod | grep -i qxl` too

### Mouse doesn't feel fast?

If you feel your mouse is laggish in your VM, make sure you have `EvTouch USB Graphics Tablet` as input device on your VM.

### Sources

[https://www.ovirt.org/develop/infra/testing/spice/](https://www.ovirt.org/develop/infra/testing/spice/)

[https://www.linuxquestions.org/questions/slackware-14/mouse-cursor-in-kvm-guest-4175575243/](https://www.linuxquestions.org/questions/slackware-14/mouse-cursor-in-kvm-guest-4175575243/)
