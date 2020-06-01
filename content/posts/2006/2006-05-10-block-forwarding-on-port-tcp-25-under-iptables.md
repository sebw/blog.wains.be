---
date: 2006-05-10
title: "Block viruses and protect yourself from spammers by blocking port 25 under Linux + iptables "
---
  
Just like ISP's do!

**Find out viruses on your network and prevent spammers from abusing your wireless network ! With simple iptables rulesets...**

OK, let's calm down, this needs a bit of explanation before proceeding.

**ISP's usually block port 25 :**
Unlike many ISP's, mine doesn't ! They still allow customers to send emails directly through and to any SMTP servers (tcp/25).

The goal in blocking port 25 is to block viruses from spreading around by sending emails using their own SMTP daemon.
At work, by just reading our email server logs, I know which ISP's aren't blocking port TCP/25  (damn Wanadoo and Road Runner).

If your ISP blocks port TCP/25, you need to send emails through their own (usually overwhelmed) SMTP server.

**Worst case scenario :**
Let's say someone breaks into my wireless network with a linux laptop (pretty unlikely with WPA2 security but who knows :) ), the attacker would be able to send as much spam as one would like using a local sendmail or postfix server.

To fill that breach, we need to block port tcp/25 for wireless clients.



**The iptables answer :**

Basically my linux gateway does this :

    
    <code>eth0 --- WAN
    eth1 --- LAN (192.168.1.0/24)
    eth2 --- WLAN (192.168.2.0/24)
    
    Iptables default filter stance :
    INPUT DROP
    FORWARD DROP
    OUTPUT ACCEPT
    </code>



The rules to add to the gateway (must come first before any other FORWARD rule) :
`iptables -A FORWARD -i eth2 -o eth0 -p tcp -m tcp --destination-port 25 -j DROP
iptables -A FORWARD -i eth0 -o eth2 -p tcp -m tcp --source-port 25 -j DROP`

If you want to still allow access to a specific server (your ISP ?), you'd just need to do this : 
`iptables -A FORWARD -i eth2 -o eth0 -p tcp -m tcp -d ! ALLOWED_IP --destination-port 25 -j DROP
iptables -A FORWARD -i eth0 -o eth2 -p tcp -m tcp -s ! ALLOWED_IP --source-port 25 -j DROP`

Personally, I filter MAC addresses and only allow one IP for wifi under iptables :
`-A FORWARD -i eth2 -o eth0 -p tcp -m tcp --destination-port 25 -j DROP
-A FORWARD -i eth0 -o eth2 -p tcp -m tcp --source-port 25 -j DROP
-A FORWARD -i eth2 -o eth0 -s 192.168.2.xx -m mac --mac-source 00:02:2D:xx:xx:xx -m state --state NEW,ESTABLISHED,RELATED -j ACCEPT
-A FORWARD -i eth2 -o eth0 -s 192.168.2.xx -m mac --mac-source 00:06:25:xx:xx:xx -m state --state NEW,ESTABLISHED,RELATED -j ACCEPT
-A FORWARD -i eth0 -o eth2 -d 192.168.2.xx -m state --state ESTABLISHED,RELATED -j ACCEPT`

For MAC filtering to be really useful, you should run arpwatch along which would send you an alert if the system finds out weird activity going on with MAC addresses : [http://ee.lbl.gov/](http://ee.lbl.gov/)

As a security measure, you would also prohibit access to the linux gateway SMTP server (and trying to set it up properly as well) :
`iptables -A INPUT -i eth2 -p tcp -m tcp --dport 25 -j DROP`

Blocking port 25 data forward under a wired business network could be useful, it could block potential viruses on client machines (who said PC's running Windows ?) from sending emails out, you can even log the illegal activity on port 25 so you can detect any virus presence on the network.

To enable activity logging, use this ruleset :
`-A FORWARD -i eth2 -o eth0 -p tcp -m tcp --destination-port 25 -j DROP
-A FORWARD -i eth0 -o eth2 -p tcp -m tcp --source-port 25 -j LOG --log-prefix "Illegal port 25 > "
-A FORWARD -i eth0 -o eth2 -p tcp -m tcp --source-port 25 -j DROP`

Log output :
`May 11 11:29:51 localhost kernel: Illegal port 25 > IN=eth2 OUT=eth0 SRC=192.168.2.xx DST=195.238.5.128 LEN=60 TOS=0x00 PREC=0x00 TTL=63 ID=29159 DF PROTO=TCP SPT=53827 DPT=25 WINDOW=5840 RES=0x00 SYN URGP=0 
May 11 11:30:10 localhost kernel: Illegal port 25 > IN=eth2 OUT=eth0 SRC=192.168.2.xx DST=195.238.5.128 LEN=60 TOS=0x00 PREC=0x00 TTL=63 ID=41524 DF PROTO=TCP SPT=53829 DPT=25 WINDOW=5840 RES=0x00 SYN URGP=0 
May 11 11:30:13 localhost kernel: Illegal port 25 > IN=eth2 OUT=eth0 SRC=192.168.2.xx DST=195.238.5.128 LEN=60 TOS=0x00 PREC=0x00 TTL=63 ID=41526 DF PROTO=TCP SPT=53829 DPT=25 WINDOW=5840 RES=0x00 SYN URGP=0 `

If you want to generate a daily report of port 25 (ab)use, this is a quick bash script : 

`#!/bin/sh
DATE="$(date --date "1 day ago" +"%b %e")"
echo "Illegal traffic accross the network"
echo "Date : $DATE"
echo "==========================================="
echo " "
echo "Port 25 (virus, local smtp server,...)"
/bin/cat /var/log/messages | grep "Illegal port 25" | grep "${DATE}" | 
awk '{print $1" "$2" "$3" > "$12" - "$13}'
echo " "
`

The report looks like this :



    
    <code>Illegal traffic accross the network
    Date : May 11
    ===========================================
     
    Port 25 (virus, local smtp server,...)
    May 11 11:29:51 > SRC=192.168.254.xx - DST=195.238.5.128
    May 11 11:30:10 > SRC=192.168.254.xx - DST=195.238.5.128
    May 11 11:30:13 > SRC=192.168.254.xx - DST=195.238.5.128
    </code>



As a security measure, please consider switching your wireless network to WPA or WPA2.
Indeed, it is so easy to crack a WEP network and circumvent MAC filters and absence of a DHCP server.

Setting up a transparent web proxy can also help finding out weird activity coming from computers on the network. Some viruses attempt to download stuff from the web at specific time of the day. That's how I noticed one computer on a network was always trying to access some weird site, every night at 3am. 

For that matter, I recommend using Squid + DansGuardian + ClamAV :
[http://blog.wains.be/post/transparent-squid/](http://blog.wains.be/post/transparent-squid/)
[http://blog.wains.be/post/centosrhelfedora-web-proxy-antivirus-clamav/](http://blog.wains.be/post/centosrhelfedora-web-proxy-antivirus-clamav/)

I hope you'll find this guide useful.
