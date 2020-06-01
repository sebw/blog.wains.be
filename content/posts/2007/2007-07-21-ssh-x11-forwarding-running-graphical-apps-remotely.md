---
date: 2007-07-21
title: "SSH X11 forwarding - running graphical apps remotely"
---







categories:
- Linux
- SSH


polishlinux.com has a great article about SSH and its powerful functions.

Original link : [http://polishlinux.org/apps/ssh-tricks/#](http://polishlinux.org/apps/ssh-tricks/#)

Here's an excerpt about X forwarding...



One of the least known functions of SSH is X protocol forwarding. This enables us to run almost every X application remotely! It’s enough to connect to the remote server using the -X option:

`ssh -X user1@remote_serwer 'application'`

and the display of every X application executed from now on will be forwarded to our local X server. We can configure the X11 Forwarding permanently by editing the /etc/ssh/ssh_config file (relevant option is ForwardX11 yes). Of course for the option to work, the remote SSH server needs to support X11 forwarding as well. The /etc/ssh/sshd_config file is responsible for that. This option is however configured by default in most of the Linux distros.

If we just need to execute one single command, we can use the syntax we learned before:

`ssh -X user1@remote_serwer 'psi'`

- this will execute PSI instant messenger on the remote server, passing the display to the local screen.

Of course the speed of applications executed remotely depends mostly on the network connection speed. It works almost flawlessly in local networks (even things like forwarding Totem playing a DivX movie). In case of Internet connection, a DSL seems reasonable to get apps like Skype or Thunderbird work quite well with a remote call.

Notice that it’s also possible to connect to the remote server without the X11 forwarding enabled, export the DISPLAY variable to point to the local machine and then run the X application. This way, the application would be executed with a remote display, using the generic X server functionality. SSH security would not be applied in such case since this kind of configuration has nothing to do with SSH. Depending on the configuration of the local X server, it may be that the authorization of the remote X applications needs to be turned on in such case. This is usually done by the command xhost. For example, xhost + hostname accepts all the remote applications from the specified hostname for a while. If we plan to use this option regularly, a more secure X server configuration is recommended. 
