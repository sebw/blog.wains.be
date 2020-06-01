---
date: 2006-11-11
title: "Backup partition to another machine over the network (CentOS/RHEL)"
---







categories:
- Howto
- Linux
- Security
- SSH


There are many ways to backup partitions between machines (CIFS, NFS, etc.)

I'll describe what seems to be the easiest and quickest way, you don't need to setup services like Samba or NFS here.



Machine A (source) : 192.168.0.1
/dev/hda1 /
/dev/hda2 /home
/dev/hda3 /tmp

Machine B (target) : 192.168.0.2

Tools needed : dd (diskdump), gzip, nc (netcat), pv (pipeview : optional)


**To save your home partition, on the source machine type this :**
`root@source# dd if=/dev/hda2 | gzip -9 | nc -l -p 9999`

**What it does : **it will diskdump /dev/hda2, compress the output and send it to the first machine connecting to nc running on port tcp/9999 (you must open the port in your firewall configuration)

**On the target machine, type this :**
`root@target# nc 192.168.0.1 9999 > hda2.gz`

**What it does : **it connects to the source machine and start receiving data from it.. the source machine start running the dd command, pipe it through gzip and send the dataflow to netcat. The target machine pipe it to a file hda2.gz


**What about security ?**
The data travels accross the network in the clear.
If you need to transfer file across the internet, you should use a VPN or SSH tunnel (see below)


**What's the pv tool you mentionned above ?**
pv (pipeview) is useful to display the progress of the data transfer.
You would need to pipe the data into pv.
pv is available at [http://dag.wieers.com/packages/pv/](http://dag.wieers.com/packages/pv/) or on the official homepage : [http://www.ivarch.com/programs/pv.shtml](http://www.ivarch.com/programs/pv.shtml)

Source :
`root@source# dd if=/dev/hda2 | gzip -9 | pv -b | nc -l -p 9999`

Target :
`root@target# nc 192.168.0.1 9999 | pv -b > hda2.gz`


**Using SSH tunnels :**

Source :
`root@source# dd if=/dev/hda2 | gzip -9 | pv -b | nc -l -p 9999`

Target :
`root@target# ssh -f -L 29999:127.0.0.1:9999 root@192.168.0.1 sleep 10;  
nc 127.0.0.1 29999 | pv -b > hda2.gz`


Thanks to the author : http://www.g-loaded.eu/post/netcat-a-couple-of-useful-examples/
Thanks to Philippe for pointing out a mistake in this article.
