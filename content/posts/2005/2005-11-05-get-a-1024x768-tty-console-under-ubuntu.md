---
date: 2005-11-05
title: "Get a 1024x768 VESA tty console under Ubuntu"
---

Just add "vga=792" to the end of the line "kernel" under /boot/grub/menu.lst

**Example /boot/grub/menu.lst :**
`title           Ubuntu, kernel 2.6.12-9-686
root            (hd0,0)
kernel          /boot/vmlinuz-2.6.12-9-686 root=/dev/hda1 ro quiet splash vga=792
initrd          /boot/initrd.img-2.6.12-9-686
savedefault
boot`



Just in case, make a copy of the previous settings, so you would always be able to boot again if something had to go wrong

This may require packages like libdirectfb and such, i'm not too sure.. it worked with a regular install of ubuntu on my HP laptop

After the change, it should boot with a 1024x768 screen, and if you switch to a tty console, you should get a 1024x768 display as well..

VESA codes :
791: VESA framebuffer console @ 1024x768x64k
vga: normal
790: VESA framebuffer console @ 1024x768x32k
773: VESA framebuffer console @ 1024x768x256
788: VESA framebuffer console @ 800x600x64k
787: VESA framebuffer console @ 800x600x32k
771: VESA framebuffer console @ 800x600x256
785: VESA framebuffer console @ 640x480x64k
784: VESA framebuffer console @ 640x480x32k
769: VESA framebuffer console @ 640x480x256

It worked under Ubuntu 5.10 and CentOS 3.6, both with a cheap ATI card as far as I remember
