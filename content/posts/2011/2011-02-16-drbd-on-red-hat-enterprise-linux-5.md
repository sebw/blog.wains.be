---
date: 2011-02-16
title: "DRBD on Red Hat Enterprise Linux 5"
---







categories:
- High-Availability
- Red Hat/CentOS


This is a rough guide and really for future personal references so I can rebuild a DRBD cluster in 3 minutes, without having to dig into DRBD docs again.
Please correct me if something is wrong (I'm thinking about DRBD gurus, [Arrfab](http://www.arrfab.net/blog/) coming to mind :-)) . 

This worked for me on RHEL5.6 64 bits and DRBD 8.2.


I'll call the DRBD resource "DRBDCluster"

Install drbd8X and kmod-drbd8X (grab the RPMS at your favorite RPM retailer.. Seems like [http://elrepo.org/linux/elrepo/el5/](http://elrepo.org/linux/elrepo/el5/) a good place to start, thought I haven't tried their RPMS)

On both node, create /etc/drbd.conf with something like this :


    
    <code>global {
        usage-count yes;
    }
    common {
        syncer {
            rate 1M;
        }
    }
    
    resource DRBDCluster {
        protocol C;
        net {
            cram-hmac-alg sha1;
            shared-secret "sdfdskljdfklfjsdkljfsdkljflk";
        }
        on srv1.intranet.example.org {
            device    /dev/drbd0;
            disk      /dev/sdb1;
            address   1.1.1.1:7789;
            meta-disk internal;
        }
        on srv2.intranet.example.org {
            device    /dev/drbd0;
            disk      /dev/sdb1;
            address   1.1.1.2:7789;
            meta-disk internal;
        }
    }</code>



**On the "master" (srv1) : **

- create the partition found in config (DO NOT FORMAT) :
`fdisk /dev/sdb1`
- Then create the actual DRBD device :
`drbdadm create-md DRBDCluster`
- if not already done : 
`modprobe drbd`
- start DRBD. It will complain that it can't "see" the other node, type "yes" at "To abort waiting enter 'yes'". :
`service drbd start`
- set the node as primary : 
`drbdadm -- --overwrite-data-of-peer primary DRBDCluster`
- format /dev/drbd0 : 
`mkfs.ext3 /dev/drbd0`

**On the slave node (srv2) : **
`service drbd start`


Now you can check the status of the DRBD cluster.

It's sync'ing.

`srv2# cat /proc/drbd
version: 8.2.6 (api:88/proto:86-88)
GIT-hash: 3e69822d3bb4920a8c1bfdf7d647169eba7d2eb4 build by buildsvn@c5-x8664-build, 2008-10-03 11:30:17
 0: cs:SyncTarget st:Secondary/Primary ds:Inconsistent/UpToDate C r---
    ns:0 nr:6016 dw:6016 dr:0 al:0 bm:0 lo:0 pe:0 ua:0 ap:0 oos:10476008
    [>....................] sync'ed:  0.2% (10230/10236)M
    finish: 1:49:07 speed: 1,504 (1,504) K/sec`

The "oos" flag expresses the amount of data to be synced, in KB. 

When the sync is done it will look like this :

`srv1# cat /proc/drbd
version: 8.2.6 (api:88/proto:86-88)
GIT-hash: 3e69822d3bb4920a8c1bfdf7d647169eba7d2eb4 build by buildsvn@c5-x8664-build, 2008-10-03 11:30:17
 0: cs:Connected st:Primary/Secondary ds:UpToDate/UpToDate C r---
    ns:10482024 nr:0 dw:298456 dr:10482072 al:121 bm:640 lo:0 pe:0 ua:0 ap:0 oos:0`


If for some reason the slave node becomes unavailable, it will resync what it has missed when it comes back (here 20 MB of data) :

`version: 8.2.6 (api:88/proto:86-88)
GIT-hash: 3e69822d3bb4920a8c1bfdf7d647169eba7d2eb4 build by buildsvn@c5-x8664-build, 2008-10-03 11:30:17
 0: cs:SyncSource st:Primary/Secondary ds:UpToDate/Inconsistent C r---
    ns:10486540 nr:0 dw:319096 dr:10486693 al:130 bm:642 lo:0 pe:0 ua:0 ap:0 oos:16124
    [=========>..........] sync'ed: 50.0% (16124/20640)K
    finish: 0:00:10 speed: 1,504 (1,504) K/sec`


**Useful commands :**

Set the sync speed on a running DRBD cluster : 
`drbdsetup /dev/drbd0 syncer -r 10M`

There's plenty of discussion on the right speed to set. It really all depends on your application, network, etc.

More useful commands to come later.


I have integrated DRBD into a RHCS Cluster (for Zarafa), check the doc here : [http://www.drbd.org/users-guide/ch-rhcs.html](http://www.drbd.org/users-guide/ch-rhcs.html)
