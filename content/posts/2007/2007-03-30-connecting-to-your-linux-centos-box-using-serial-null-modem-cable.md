---
date: 2007-03-30
title: "Connecting to your Linux CentOS box using serial null-modem cable"
---







categories:
- Howto
- Linux


This has been tested under CentOS 4.4.

**Why would I need that ?**
This is useful if you set up a server that will be located in a place where there's low chance to have a keyboard and a monitor around. Or useful, when, like me, you're given like 2 squared meters to store your servers.



**What you need :**
Server : At least one serial port available on the linux box and the "util-linux" package installed.
Client : At least one serial port on a laptop with at least Windows and Hyperterminal (installed by default I guess).

**Make sure the serial ports are recognized on the server :**
`# dmesg |grep tty
ttyS0 at I/O 0x3f8 (irq = 4) is a 16550A
ttyS1 at I/O 0x2f8 (irq = 3) is a 16550A`

If the system doesn't detect serial ports, check the relevant settings in the BIOS.

**Edit /etc/inittab and change this :**
`# Run gettys in standard runlevels
1:2345:respawn:/sbin/mingetty tty1
2:2345:respawn:/sbin/mingetty tty2
3:2345:respawn:/sbin/mingetty tty3
4:2345:respawn:/sbin/mingetty tty4
5:2345:respawn:/sbin/mingetty tty5
6:2345:respawn:/sbin/mingetty tty6`

To :

`# Run gettys in standard runlevels
1:2345:respawn:/sbin/mingetty tty1
2:2345:respawn:/sbin/mingetty tty2
S0:235:respawn:/sbin/agetty -L 9600 ttyS0 vt100
#3:2345:respawn:/sbin/mingetty tty3
#4:2345:respawn:/sbin/mingetty tty4
#5:2345:respawn:/sbin/mingetty tty5
#6:2345:respawn:/sbin/mingetty tty6`

**Then, run :**
`# init q
or shutdown -r now`

This would keep 2 mingetty's running (that would save a few Kb of memory, and who need 6 tty prompts on a machine that wouldn't have a monitor and keyboard attached ?).

Before we continue, a bit of explanations on the options used on the agetty line.
-L : force the line to be a local line with no need for carrier detect (needed in this case)
9600 : serial line rate in bps. Set this in your terminal configuration. You can use 38400, I've tried it and it works, but I can't swear it would always work.
ttyS0 : port COM1
vt100 : this is the standard terminal emulation. I also had success with vt102.

**Now, let's make sure agetty is running :**
`# ps ef |grep agetty
17277 ttyS0    Ss+    0:00 /sbin/agetty -L 9600 ttyS0 vt100 HOME=/ TERM=linux vga=771 SELINUX_INIT=YES PATH=/usr/local/sbin:/sbin:/bin:/usr/sbin:/usr/bin RUNLEVEL=3 PREVLEVEL=N CONSOLE=/dev/console INIT_VERSION=sysvinit-2.85`

Then you're ready to connect your dumb terminal (like Hyperterminal.. *really* dumb) to your linux box.
You should set the rate to 9600 bps, data bits to 8, parity to null and stop bits to 1. Set the flow control to off.
Connect your null-modem cable to each computer.

You can find info about null-modem cables at [http://www.nullmodem.com/NullModem.htm](http://www.nullmodem.com/NullModem.htm).
Mine was a DB9-DB9 female-female.

**Important : 
Do not forget to explicitly log out from the session on the serial console. If you close the dumb terminal, the session would remain opened and could be resumed !**
