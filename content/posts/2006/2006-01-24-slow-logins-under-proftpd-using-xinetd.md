---
date: 2006-01-24
title: "Slow FTP logins under Proftpd using Xinetd"
---

I was able to connect from european machines to my local FTP server located in Belgium (running proftpd from xinetd) very fast but some machines in North Carolina had very sloooow login prompt...

I tried about anything from PAM to iptables, then i found out running proftpd as standalone fixed the problem. The issue was on the xinetd side.

Finally found this out when googling around :
    
    <code>The USERID option tells xinetd to query the remote host for a username.
    To do that, it needs a TCP connection.
    Many systems don't respond at all to this connection. For instance, it's
    common for firewalls to simply drop the traffic.
    In the absense of a specific rejection, the TCP will retry the connection
    until it times out.
    
    ----- Original Message -----
    From: "Jeff Bert" 
    To: "Wuftpd-Questions" 
    Sent: Monday, May 20, 2002 2:00 PM
    Subject: why does USERID cause slow login ?
    
    I was just curious to know the "why" behind this issue. Can someone
    please explain briefly why having USERID in the login_success/login_failure
    settings in /etc/xinetd.d/wu-ftpd causes a slow login process?
    thanks,
    Jeff </code>



I had to edit /etc/xinetd.d/xproftpd 

from :
`service ftp
{
        socket_type             = stream
        wait                    = no
        user                    = root
        server                  = /usr/sbin/in.proftpd
        log_on_success          += USERID DURATION
        log_on_failure          += USERID
        nice                    = 10
        disable                 = no
}`

to :
`service ftp
{
        socket_type             = stream
        wait                    = no
        user                    = root
        server                  = /usr/sbin/in.proftpd
        log_on_success          += DURATION
        log_on_failure          += DURATION
        nice                    = 10
        disable                 = no
}`
