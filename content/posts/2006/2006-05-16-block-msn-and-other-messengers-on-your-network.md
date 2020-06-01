---
date: 2006-05-16
title: "Block MSN and other messengers on your network"
---

**1. Iptables**

This is my iptables config stored under /etc/sysconfig/iptables :
(eth0 = WAN interface, eth1 = LAN interface)

You'll notice 192.168.1.16 is allowed to connect to any services

You'll also notice that the default stance for output traffic is ACCEPT.
You can of course set it to DROP and only accept what you specifically define.


    
    <code>*filter
    :INPUT DROP [0:0]
    :FORWARD DROP [0:0]
    :OUTPUT ACCEPT [0:0]
    
    # Basic protections against syn floods and other stuff
    -A FORWARD -p tcp --syn -m limit --limit 1/s -j ACCEPT
    -A FORWARD -p tcp --tcp-flags SYN,ACK,FIN,RST RST -m limit --limit 1/s -j ACCEPT
    -A FORWARD -p icmp --icmp-type echo-request -m limit --limit 1/s -j ACCEPT
    
    # Block MSN
    -I FORWARD -s 192.168.1.0/24 -p tcp -m tcp --dport 1863 -j DROP
    -I FORWARD -s 192.168.1.0/24 -p tcp -m tcp --dport 1863 -j LOG --log-prefix "MESSENGER MSN > "
    -I FORWARD -s 192.168.1.16 -p tcp -m tcp --dport 1863 -j ACCEPT
    
    # Block AIM/ICQ
    -I FORWARD -s 192.168.1.0/24 -d 64.12.25.0/22 -j DROP
    -I FORWARD -s 192.168.1.0/24 -d 64.12.25.0/22 -j LOG --log-prefix "MESSENGER ICQ/AIM > "
    -I FORWARD -s 192.168.1.16 -d 64.12.25.0/22 -j ACCEPT
    
    # Block Yahoo IM
    -I FORWARD -s 192.168.1.0/24 -d 216.155.193.0/22 -j DROP
    -I FORWARD -s 192.168.1.0/24 -d 216.155.193.0/22 -j LOG --log-prefix "MESSENGER YIM > "
    -I FORWARD -s 192.168.1.16 -d 216.155.193.0/22 -j ACCEPT
    
    # Allowing anything else
    -A FORWARD -i eth1 -o eth0 -m state --state NEW,ESTABLISHED,RELATED -j ACCEPT
    -A FORWARD -i eth0 -o eth1 -m state --state ESTABLISHED,RELATED -j ACCEPT</code>



As soon as the MSN client is not able to connect to the server on port tcp 1863, it'll try to connect using port tcp 80, which is probably allowed :

Web activity upon connection :
`1.10         gateway.messenger.hotmail.com/gateway/gateway.dll?Action=open&Server=NS&IP=messenger.hotmail.com
1.10         207.46.25.15/gateway/gateway.dll?SessionID=1047611159.2422
1.10         207.46.25.15/gateway/gateway.dll?SessionID=1047611159.1885
1.10         207.46.25.15/gateway/gateway.dll?Action=poll&SessionID=1047611159.24447
1.10         207.46.25.15/gateway/gateway.dll?SessionID=1047611159.7573`

**2. More firewall rules or Squid web proxy **

Now you have two choices :

- making an ACL blocking the microsoft IP ranges... if new ranges are made available, MSN clients would be able to connect again.. this is not an ideal stance, unless you enjoy tracking the IP of MSN servers.

- blocking Internet Explorer (and MSN which uses the Internet Explorer engine) in your web proxy : you'll need to run a transparent web proxy (Squid does the job pretty well) to block Internet Explorer, so MSN won't be able to connect to port 80... See [here for a short howto](http://blog.wains.be/post/blocking-internet-explorer-with-the-squid-web-proxy/)

Of course, you'd need something like [Firefox](http://www.mozilla.com) installed on your client PC's if you decide to block IE... you can always make an ACL in Squid allowing safe websites under Internet Explorer... This is not a bad stance as IE is known to have many unfixed security flaws.

`Edit june 2007 : I'm blocking the Internet Explorer User Agent which apparently blocks the MSN client as well, but I noticed this [page](http://www.hypothetic.org/docs/msn/general/http_connections.php) mentions the user agent for MSN is "MSMSGS". Please let me know if the described solution does not work for you.`

I've not put much efforts into blocking AIM/ICQ/YIM since 99 % of people use MSN in Belgium
The MSN blocking is working well for me, I'm not sure about the other IM's (the IP ranges can change from times to times)

**3. Additional notes**

It is reported at many places that the following squid rules are working.. **I have tried them and they do NOT work for me..** If they do for you, let me know :)
`acl mi_intranet src 192.168.1.0/255.255.255.0
acl msn req_mime_type -i ^application/x-msn-messenger
http_access deny mi_intranet msn
http_access allow mi_intranet `

This is a working Squid ACL blocking a bunch of web messenger :
`http://.*e-messenger.net/.*
http://193.238.160.*
http://.*meebo.com/.*
http://.*messenger.msn.com/.*
http://.*clientless.net/.*
http://.*wbmsn.net/.*
http://.*msn2go.com/.*
http://64.92.173.*
http://.*iloveim.com/.*
http://info.sytes.net/.*
http://chatenabled.mail.google.com/.*`
