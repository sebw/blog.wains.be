---
date: 2006-11-08
title: "Subversion - svnserve over xinetd"
---







categories:
- Howto
- Linux
- Security
- Versioning


If you want to run svnserve through xinetd (documentation still refers to inetd) :



1. Create file /etc/xinetd.d/svnserve :

`# default: on
service svnserve
{
        socket_type     = stream
        protocol        = tcp
        user            = root
        wait            = no
        disable         = no
        server          = /usr/bin/svnserve
        server_args     = -i -r /path/to/svn/projects
        port            = 3690
}`

/path/to/svn/projects is the repository you want to share, if you don't use -r, you'd need to specify the full path to the svn repository, which should be avoided.

2. type the following, it will add subversion ports to the list of services.
`echo "svnserve        3690/tcp" >> /etc/services
echo "svnserve        3690/udp" >> /etc/services`

3. Open tcp/3690 in your firewall

4. Restart xinetd

5. try telnet localhost 3690

6. if succesful, try "svn list svn://localhost/"

7. Try from a remote location

8. If not working, set up tcp_wrappers

`svnserve : ALL : allow`

This would allow anyone to connect to svnserve
