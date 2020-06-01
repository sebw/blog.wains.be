---
date: 2006-10-18
title: "A simple TCP proxy using rinetd"
---







categories:
- Howto
- Linux
- Security


Rinetd redirects TCP connections from one IP address and port to another. rinetd is a single-process server which handles any number of connections to the address/port pairs specified in the file /etc/rinetd.conf. Since rinetd runs as a single process using nonblocking I/O, it is able to redirect a large number of connections without a severe impact on the machine. This makes it practical to run TCP services on machines inside an IP masquerading firewall. rinetd does not redirect FTP, because FTP requires more than one socket.

I used to use it at work, to simply bypass some issue we were having with a single computer on the network.
Back then, our emails were stored on an external webhost. We had a computer on the network that could not get access to the external network for security reasons (kinda public computer aimed for email use only).

The fastest and most secure way I found back then was to use rinetd on a local machine having web access and make it redirect to the pop3 server at on the webhost machine...

Schema :
Email client -----> 192.168.1.100:110  200.100.100.100:110 -----> POP3 server


[http://www.boutell.com/rinetd/](http://www.boutell.com/rinetd/)
