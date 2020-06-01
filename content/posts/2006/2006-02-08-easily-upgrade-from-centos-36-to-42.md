---
date: 2006-02-08
title: "Easily upgrade from CentOS 3.6 to 4.2"
---

To counter-strike all the rants and posts I have seen on the web about how difficult it was to upgrade from CentOS 3 to 4, this post is here to testify it was actually easy -at least for a friend and myself-.

I've been wandering around trying to find people experiences about such an upgrade and all i found was complains, failures, rants and so. Until my friend said the night before, he had upgraded without any problem

Ok, here it goes, my little guide on how I proceeded : 


    
    <code>- Grab the server version of CentOS 4.2 at ftp://ftp.belnet.be/mirror/ftp.centos.org/4.2/isos/i386/CentOS-4.2.ServerCD-i386.iso
    - Burn it ! :)
    - Boot your machine with your brand new CD
    - At the boot prompt, type "linux askmethod upgradeany"
    - The installer will request you to set up the network..
    - When asking which method to use to install packages, choose FTP :
    Server : ftp.belnet.be
    Directory : /mirror/ftp.centos.org/4.2/os/i386/
    - Let the installer proceed
    - When required, reboot</code>



Then you just need to update any other package not originally from CentOS repositories, and you are done !

It worked great for my server (no windows manager or such useless stuff for a server), I can't swear it would work fine on a desktop system though :)

More info : [http://mirror.centos.org/centos/4/docs/html/rhel-ig-x8664-multi-en-4/ap-upgrade.html](http://mirror.centos.org/centos/4/docs/html/rhel-ig-x8664-multi-en-4/ap-upgrade.html)
