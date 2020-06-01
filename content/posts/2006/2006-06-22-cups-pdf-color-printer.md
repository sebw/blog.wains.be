---
date: 2006-06-22
title: "A Cups PDF color printer for your Windows clients"
---

We suppose you already have a properly configured and running CUPS system.

Grab the RPM or SRPM from http://www.physik.uni-wuerzburg.de/~vrbehr/cups-pdf/

rpm -ihv cups-pdf-something.rpm (unless you want to go through the whole rebuild process which I won't describe here)

service cups restart

You should see the new PDF printer under http://yourserver:631

Now let's share the PDF printer for your Windows clients :

**Edit /etc/samba/smb.conf and add this section**

`[printers]
   path = /some/path/to/spool
   browsable = yes
   guest ok = no
   writable = no
   valid users = +print_users
   printable = yes
   create mask = 0770`

**Path to the spool should be at least :**
drwxrwxr-x  2 root print_users  4096 Jun 23 01:27 spool

**Add the group and users allowed to print in this group :**
- groupadd print_users
- gpasswd -a user print_users

**Restart samba : **
service smb restart

Windows clients should now see the new PDF printer when browsing the server in the network neighborhood.
Right click the PDF printer and select "Connect...". The system may warn you of potential viruses (if XP), then tell you no drivers have been installed for that printers. Click yes to install the drivers. 

To get a color-able PDF printer, pick up **"HP DeskJet 1200/C PS"** which is quite mainstream (drivers included in Windows 2000/XP by default). You won't need to share the drivers on the samba server if you go with this one.

Edit /etc/cups/cups-pdf.conf to your needs. I've personally set the printer to put the PDF's under my home directory, into a "PDF" directory.
