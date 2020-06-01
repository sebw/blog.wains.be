---
date: 2006-10-03
title: "Enabling remote desktop under Linux (XDMCP)"
---







categories:
- Howto
- Linux


Under CentOS 4, 

edit **/etc/X11/gdm/gdm.conf**

Search for the section **[XDMCP]**
Change the line **"Enable=false"** to **"Enable=true"**

Restart X

You should now be able to remotely connect using X-Win32 or such

You may need to configure iptables to allow connections :
`iptables -A INPUT -p udp -m udp --dport 177 -j ACCEPT
iptables -A OUTPUT -p udp -m udp -m state --state RELATED,ESTABLISHED -j ACCEPT`

Pay close attention to the security risks, don't open it wide to the internet



Under Red Hat 5 : [http://www.redhatmagazine.com/post/tips-and-tricks-how-do-i-enable-xdmcp-in-red-hat-enterprise-linux-5/](http://www.redhatmagazine.com/post/tips-and-tricks-how-do-i-enable-xdmcp-in-red-hat-enterprise-linux-5/)
