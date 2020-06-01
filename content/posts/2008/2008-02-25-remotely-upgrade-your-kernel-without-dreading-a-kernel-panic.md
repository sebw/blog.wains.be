---
date: 2008-02-25
title: "Remotely upgrade your kernel without dreading a kernel panic"
---







categories:
- Linux


If you're trying to upgrade a kernel remotely, there's always a risk of losing access to the machine because of a kernel panic when rebooting.

In the following we will tell grub to try to boot the new kernel at least once, if it panics the machine will reboot and boot with the working kernel instead.

**Edit /boot/grub/menu.lst :**

Add panic=5 to the end of the kernel line for the new kernel, this means the machine will reboot 5 seconds after kernel panic upon boot.


    
    <code>default         saved 
    timeout         5 
    
    # new kernel, not tested 
    title           Debian GNU/Linux, kernel 2.6.18-6-686 
    root            (hd0,0) 
    kernel          /boot/vmlinuz-2.6.18-6-686 root=/dev/sda1 ro panic=5 
    initrd          /boot/initrd.img-2.6.18-6-686 
    savedefault 1 
    
    # tested and working kernel 
    title           Debian GNU/Linux, kernel 2.6.18-5-686 
    root            (hd0,0) 
    kernel          /boot/vmlinuz-2.6.18-5-686 root=/dev/sda1 ro 
    initrd          /boot/initrd.img-2.6.18-5-686
    savedefault </code>




When you're done, type the following command, it will tell grub to boot the first kernel entry once :

`# grub-set-default 0`

Under CentOS you should type :

`echo "savedefault --default=0 --once" | grub --batch`

After rebooting, if you're running the new kernel succesfully, you can edit /boot/grub/grub.conf and set "default 0" instead.


**Rebooting the machine in case of kernel panic**

Kernel panic can happen in 2 situations :
- when booting
- while the system is running


WHEN BOOTING

As seen previously, you can add "panic=5" to the end of the kernel line in grub.conf, to make the system reboot if it kernel panics while booting. You should make sure grub will try another kernel in case of kernel panic, with the method seen above.


WHILE THE SYSTEM IS RUNNING

By default, if the kernel panics while the system is running, the system will wait forever for someone to physically act on the machine.

There are two ways to tell the system to reboot in case of kernel panic :

**1. Dynamically in /proc :**

Type the following in a terminal as root :
`echo "5" > /proc/sys/kernel/panic`
You can for instance put that line in /etc/rc.local to make the setting persistent.

**2. By editing /etc/sysctl.conf :**
`kernel.panic = 5`
Then type "sysctl -p" to enable the new setting.

http://www.gnu.org/software/grub/manual/html_node/savedefault.html
http://www.gnu.org/software/grub/manual/html_node/Invoking-grub_002dset_002ddefault.html
