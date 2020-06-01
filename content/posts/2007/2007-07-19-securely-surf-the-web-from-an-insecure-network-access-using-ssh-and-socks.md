---
date: 2007-07-19
title: "Securely surf the web from an insecure network access using SSH and SOCKS"
---







categories:
- Linux
- Security
- SSH


Posted by JoshTriplett on Mon 23 Oct 2006 at 12:35
From : [http://www.debian-administration.org/articles/449](http://www.debian-administration.org/articles/449)

SSH has numerous uses beyond just logging into a remote system. In particular, SSH allows you to forward ports from one machine to another, tunnelling traffic through the secure SSH connection. This provides a convenient means of accessing a service hosted behind a firewall, or one blocked by an outgoing firewall.



However, forwarding an individual port still requires you to change where your program connects, telling it to use a non-standard port on localhost rather than the standard port on the remote machine, and it requires a separate port forward for each machine you want to access. Dynamic port forwarding via SOCKS provides a more convenient alternative.

The examples in this article assume that you reside behind a restrictive firewall which does not allow outgoing SMTP connections except to a designated mail server. You want to connect to a different mail server, mail.example.net, on port 25. You have an SSH account on a machine shell.example.org, which does not reside within the restrictive firewall and can thus access port 25 on mail.example.net.

With standard SSH port forwarding, you could enter the command:

`ssh -L 2525:mail.example.net:25 shell.example.org`

This will forward port 2525 on your machine to port 25 on mail.example.net, by way of shell.example.org. You will then need to configure your mailer to send mail to localhost, port 2525, and use the authentication information for your mail account on mail.example.net. For example, in Thunderbird, you could add an additional outgoing mail server via Edit->Preferences, "Outgoing Mail Server (SMTP)", "Add...", and either set it as the default or explicitly set your mail account to use that server. You can then send your mail, which will potentially (if you use secure authentication with mail.example.net) give you a security warning about localhost presenting a certificate for mail.example.net, and then prompt you for your account password. After you have finished sending all the mails you want to send, you can then change your outgoing mail server back to the previous setting, and exit SSH.

To avoid all this hassle, SSH also supports dynamic port forwarding via SOCKS. SOCKS defines a standard mechanism for a client to connect to a server by way of a proxy. SSH can serve as the proxy, allowing you to connect to shell.example.org and make connections from there to an arbitrary server such as mail.example.net. Simply run:

`$ ssh -D 1080 shell.example.org`

to make the connection to shell.example.org and start a SOCKS proxy on localhost port 1080.

In order to make use of the SOCKS proxy, you can either use applications which can speak SOCKS natively, or you can use a socksifier program like tsocks. tsocks provides a library used with LD_PRELOAD, which replaces the standard sockets functions like socket, connect, and sendto with functions that make use of a designated SOCKS proxy. The tsocks script runs a program with this library loaded. The library will read /etc/tsocks.conf to find out what SOCKS proxy to use. To configure tsocks to work with an SSH SOCKS proxy on localhost, edit the default /etc/tsocks.conf, change the server variable to 127.0.0.1, and comment out the path example.

Now that you have tsocks configured, you can run the following whenever you want to send mail via mail.example.net:

`$ ssh -D 1080 shell.example.org
$ tsocks thunderbird`

This will open the SSH-tunnelled SOCKS proxy to shell.example.org and run thunderbird. You can then send mail normally, without changing the outgoing server configuration, and without seeing any authentication mismatch warnings.

**Notes :**

You can use this instead of just -D
`$ ssh -fND 3125 host.example.com`

-f : Requests ssh to go to background just before command execution.
-N : Do not execute a remote command. This is useful for just forwarding ports (protocol version 2 only).

**Links :**
 An Illustrated Guide to SSH Agent Forwarding : [http://www.unixwiz.net/techtips/ssh-agent-forwarding.html](http://http://www.unixwiz.net/techtips/ssh-agent-forwarding.html)
SSH and port forwarding explained and illustrated : [http://www.debuntu.org/comment/reply/17](http://www.debuntu.org/comment/reply/17)
