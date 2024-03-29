---
date: 2007-06-20
title: "Essential tools for Ubuntu (and others)"
---

I'm going to keep a list of tools I find essential under Linux. I'm using Ubuntu as my main desktop OS for now but it should apply to any other distro under Gnome.
The tools are available in the Ubuntu repositories unless otherwise stated.

**Security tools :**

Seahorse : encryption keys manager (SSH, GPG)
http://www.gnome.org/projects/seahorse/

Truecrypt : transparent volume encryption
http://www.truecrypt.org
Not available from the repositories

Wipe : Secure file deletion

Revelation : GNOME2 Password manager
Revelation is a password manager for the GNOME 2 desktop. It organizes
accounts in a tree structure, and stores them as AES-encrypted XML.



**GUI stuff :**

Glipper : clipboardmanager for GNOME
http://glipper.sourceforge.net/

tracker and tracker-search-tool : metadata database, indexer and search tool
http://www.gnome.org/projects/tracker/ (now installed by default since Ubuntu 7.10)

nautilus-open-terminal : nautilus plugin for opening terminals in arbitrary local paths

nautilus-image-converter : nautilus extension to mass resize images (this one is a life saver !)

nautilus-gksu : privilege granting extension for nautilus using gksu

nautilus-actions : nautilus extension to configure programs to launch (I personally create a "Securely erase" menu, working with wipe)

cGmail : email notification tool that integrates well with Gnome
http://cgmail.tuxfamily.org/index.html
Not available from the repositories

Intlclock : enhanced clock applet
http://live.gnome.org/GnomePanel/Intlclock
Soon to be included in Ubuntu by default

**CLI stuff :**

Screen : virtual terminal manager
http://www.gnu.org/software/screen/

vlock : allows to lock a command-line session (esp. useful for servers)


**Multimedia :**

EasyTag : viewing, editing and writing ID3 tags
http://easytag.sourceforge.net


**Documents :**

Evince : my favorite PDF reader (default under ubuntu)
http://www.gnome.org/projects/evince/

CUPS-PDF : make a virtual printer creating PDF of your documents (apparently installed by default now)
http://www.physik.uni-wuerzburg.de/~vrbehr/cups-pdf/

PDFedit : I don't need to describe this one right
http://pdfedit.petricek.net/

Inkscape : vector graphics editor, great for network diagrams
http://www.inkscape.org/


**Developement stuff :**

Vmware Server : virtual machine manager
http://www.vmware.com - See [this link to install under Ubuntu](http://blog.wains.be/post/vmware-server-on-ubuntu-704-the-easy-way/)
Ubuntu package name : vmware-server

Internet Explorer : yeah.. need it to see how its funky CSS rendering can destroy a website
http://www.tatanka.com.br/ies4linux/
Not in the Ubuntu repositories

Meld : graphical tool to diff and merge files
http://meld.sourceforge.net

Colordiff : color your diff outputs

GColor2 : Simple GTK2 color selector and picke
http://gcolor2.sourceforge.net/

mysql-query-browser : Official GUI tool to query MySQL database


**System stuff :**

Filezilla : Port of the famous Win32 graphical FTP client (still in beta, great gftp replacement)
http://filezilla.sourceforge.net

ntp support is not installed by default !

tsocks : transparent network access through a SOCKS 4 or 5 proxy for any app
http://tsocks.sourceforge.net

sysv-rc-conf : SysV init runlevel configuration tool for the terminal

gstm : Gnome SSH tunnel manager
Gnome Secure shell Tunnel Manager is a front-end to manage secure shell
tunneled port redirects


Any suggestion welcomed !
