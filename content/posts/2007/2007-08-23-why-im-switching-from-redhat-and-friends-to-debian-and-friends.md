---
date: 2007-08-23
title: "Why Im switching from Red Hat (and friends) to Debian (and friends)"
---







categories:
- Debian/Ubuntu
- Linux
- Red Hat/CentOS


Yeah, I decided I'll be switching my servers to Debian.

It slowly started with my desktop OS, when I moved from Fedora to Ubuntu. (and CentOS before Fedora, until came the need for wireless with ipw3945 on my new laptop [[was using Orinoco Gold on my old CentOS 4 laptop](http://blog.wains.be/post/centos-42-orinoco-monitorscan/)]).

There was a reason why I chose to try something else. First, the (french) translation under Fedora was somewhat broken back then, sometimes resulting in frenglish sentences. But the most tedious problem in Fedora was (and still is) actually the point of the Fedora project : it is a testing field for Red Hat, meaning a lot of updates (I've had 100MB+ updates waiting when not booting for a few days), it also meant a lot of kernel updates, and at every kernel release, wireless support (provided by a 3rd party repo due to patent issue) and some other stuff would go kaboom. From there, you have two choices, sticking with the "old" kernel until support is brought up by some good fella out there, or rebuild/recompile that stuff that just broke.

In the end, you spend more time building/compiling/tweaking/figuring out/etc. than doing some actual work.

Then I moved to Ubuntu (since 6.06), it helped me discovering what Debian could be like (mainly the package management). Ubuntu particularly amazed me with the huge work brought at every new release. What struck me the most when I first tried Ubuntu was the availability of tools like ettercap and dsniff directly in the repositories... Indeed, anything depending on libnids/libnet/libpcap under CentOS/Fedora was a real pain (see [here](http://blog.wains.be/post/dsniff-working-under-centos-42/)). Under Ubuntu, it was just a few clicks away.

What I like is also the huge user base, which means a lot of bug reports (maybe too much ? duplicates must be counter-productive) for all kind of hardware. Anytime I'm finding a bug, a bug report is already filled.

So... Enjoying Ubuntu on the desktop... I decided to give Debian a try on the server, appealed by the **official** repositories providing just about anything.

Here's my feeling with Debian Etch after a few days...

Pros :

- Debian has a LOT of packages
- Debian really has a LOT of packages (Redhat based systems must have under 2000 packages available in the official repositories)
- I like the organization behind the project (http://en.wikipedia.org/wiki/Debian is an interesting read)
- I like the stable/testing/unstable/volatile repository scheme and the fact you can fetch some stuff from the testing repo or even unstable if you need the bleeding edge version of something
- apt-get must be 1000x faster than yum (**edit** : to make things clear, there's a tad of exaggeration in this statement)
- postfix mysql support brought in packages, always had to rebuild RPM under CentOS
- no need to rely on 3rd party repositories for a majority of packages (until now, I found everything I needed)
- minimal install really IS minimal (you don't even get telnet)(**edit** : to make things clear again, I know a minimal install holds on a few Mb, I'm comparing to CentOS 5 here), it takes minutes to install and is like 450 Mb big. That is nice, I like starting from scratch and only add what I really need. A minimal install of CentOS 5 can be tricky to achieve and require from 1 to 5 CD's, the packages being ridiculously spread out on the CD's. Also, you still get a truck of useless stuff like Bluetooth or CUPS on first boot. You have a few services listening by default too (netstat -nape is probably the first thing I check when trying a new distro). My first task when installing a new CentOS system was to remove the useless packages (see the list [here](http://blog.wains.be/post/my-installation-of-centos/))

Cons : 

- Debian using getty by default instead of mingetty ?
- no real "yum provides" function in dpkg or apt-get.. have to search on packages.debian.org to find which package provide a file.
- a minor one, but vim by default doesn't turn syntax coloring on.. Under RHEL, I just needed to install vim-enhanced and I was done. I'll need to make a universal vimrc config file and distribute it..
- I prefer "service postfix restart" than "/etc/init.d/postfix restart"
- I also liked chkconfig under Red Hat

Edit :

**My simple points of comparison between Debian Etch and CentOS 5**

**Debian Etch :**
Packages :
147

Netstat :
Active Internet connections (servers and established)
Proto Recv-Q Send-Q Local Address Foreign Address

**CentOS 5 :**
Packages :
340

Netstat :
Active Internet connections (servers and established)
Proto Recv-Q Send-Q Local Address Foreign Address State User Inode PID/Program name
tcp 0 0 0.0.0.0:1005 0.0.0.0:* LISTEN 0 5513 1671/rpc.statd
tcp 0 0 0.0.0.0:111 0.0.0.0:* LISTEN 0 5452 1646/portmap
tcp 0 0 127.0.0.1:631 0.0.0.0:* LISTEN 0 6076 1906/cupsd
tcp 0 0 127.0.0.1:25 0.0.0.0:* LISTEN 0 6227 1948/sendmail: acce
tcp 0 0 :::22 :::* LISTEN 0 6136 1924/sshd
tcp 0 0 ::ffff:192.168.254.207:22 ::ffff:192.168.254.20:36575 ESTABLISHED 0 8275 2200/0
udp 0 0 0.0.0.0:999 0.0.0.0:* 0 5495 1671/rpc.statd
udp 0 0 0.0.0.0:1002 0.0.0.0:* 0 5504 1671/rpc.statd
udp 0 0 0.0.0.0:111 0.0.0.0:* 0 5451 1646/portmap
udp 0 0 0.0.0.0:631 0.0.0.0:* 0 6079 1906/cupsd


**After using Debian for more than a month, here are my views :**

- iptables : no init script provided by default ? I liked the init script and /etc/sysconfig/iptables under Red Hat. Now I will need to put a memo on this site.
- libapache2-mod-auth-mysql not in the stable repository ? This is an essential component of any serious LAMP setup
- vim : by default, no "set paste" and "syntax on" option. This is a minor one, easily fixed, but I don't see the reason why I don't get colored syntax when I use VIM instead of VI. It's supposed to be "enhanced". About the "set paste" option, I may be totally wrong but who wants the default behaviour (tab at every line of a pasted block) ?
- I miss chkconfig !!!

As you can see only minor itches so far, except about the apache module...

Edit 2 :

I'm now used to update-rc.d but here's another tool to manage services and runlevel :
`apt-get install sysv-rc-conf`
