---
date: 2007-05-21
title: "How to enable OpenSSH on a Synology DS-101 NAS"
---







categories:
- Howto
- Linux


I'll explain how I enabled SSH on my Synology DS-101 box.

![Synology DS-101](https://blog.wains.be/images/00-imageshack.jpg)

This guide is based on Nicolas' work : [http://www.dotmana.com/index.php/?p=91](http://www.dotmana.com/index.php/?p=91)



**1. FIRMWARE UPGRADE**

* Reboot the DS-101 (this is **absolutely** necessary in order to achieve the next step)

* Upgrade to the latest firmware available from Synology within 5 minutes after reboot. 
If you don't upgrade within 5 minutes after reboot you'll get an "error 24" message, and you'd need to reboot again. (this is a security function)
The version of the firmware the DS-101 was running was 2.0.1 1.0116.
After upgrade, the version was 2.0.1 3.0371 (you can download that version of the firmware [from here](http://blog.wains.be/pub/ds-101/synology_ixp420_1hd_0371.zip))
The upgrade brought PHP + MySQL + some other cool features.

Let the upgrade go until the POWER led remains solid (it took around around 4 minutes for me)

**2. ENABLING TELNET**

* When done with the upgrade, download [http://blog.wains.be/pub/ds-101/syno-telnet-r2.zip](http://blog.wains.be/pub/ds-101/syno-telnet-r2.zip) and unzip it.
Upgrade the box with the file enable-telnet.pat as if it was a regular firmware..
You'll get an error 42 message. This is normal.

This upgrade will ONLY enable telnet, it will take a few seconds.
The error 42 message is normal, it prevents the NAS from rebooting as it would do after any regular firmware upgrade.

* Download [http://blog.wains.be/pub/ds-101/ds101-bootstrap_1.0-1_armeb.xsh.gz](http://blog.wains.be/pub/ds-101/ds101-bootstrap_1.0-1_armeb.xsh.gz)
Unzip the file.
FTP into the NAS and upload ds101-bootstrap_1.0-1_armeb.xsh in the public directory.

* From here, it was a bit unclear, Nicolas' guide is mentionning the synopass php file. I didn't need it to enable SSH. Maybe something changed with the newer firmware.. ?

**Confirmed : the root account has the same password as user admin with never firmwares**

If you can't telnet into the NAS using your password..
- From the Web administration interface, enable the "Web station" feature.
- using FTP, upload synopass.php (found in syno-telnet-r2.zip) in the web directory
- Query the file : http://diskstation/synopass.php

You'll get a page where you'd find the root password you need to use to login as root in the box.

As soon as you're logged in, you can change the root password by using passwd.

**3. ENABLING SSH**

Telnet into the NAS, use root as user and your password

Go into the public directory (should be /volume1/public/)

Make the file we just uploaded executable :
`# chmod 755 ds101-bootstrap_1.0-1_armeb.xsh`

Execute the script, it will install the "ipkg" tool that will give us access to a repository full of great tools : 
`# ./ds101-bootstrap_1.0-1_armeb.xsh`

When done :
`# ipkg update`

Then : 
`# ipkg install openssh`

(the output for these commands is available at [http://blog.wains.be/pub/ds-101/syno.txt](http://blog.wains.be/pub/ds-101/syno.txt))

Reboot the box, you should now be able to SSH into the NAS.

If you want to see what you can install from the repo :
`# ipkg list`

If you want to see what you have installed :
`# ipkg list_installed`

**4. EXTRA**

*** Editing your SSH config :**

If, as me, you want to edit your SSH config (disabling password authentication and enable SSH key auth is a good idea) :

- Edit /volume1/opt/etc/openssh/sshd_config
- Restart openssh from "/opt/etc/init.d/S40sshd restart"

*** Enabling SSH key authentication :**

`# vi /volume1/opt/etc/openssh/sshd_config`

Edit these values :
`PermitRootLogin without-password
PasswordAuthentication no
RSAAuthentication yes
PubkeyAuthentication yes
AuthorizedKeysFile      .ssh/authorized_keys`

Then restart SSH :
`# /opt/etc/init.d/./S40sshd restart`

*** SSH key auth for root user :**
`# mkdir /root/.ssh`
Then put your authorized_keys file under /root/.ssh

*** SSH key auth for non-root users :**
For non-root users, you need to tweak the system a bit...

I'll consider you already created the user (named johndoe here) through the web interface, the user get a home directory normally located under /volume1/johndoe/..

You will need to create a "fake" home dir for the user as in the typical Linux/Unix directory tree  /home. Indeed, SSH will look for the user keys inside the regular user directory. There must be a way to tell SSH to look inside /volume1/username/.ssh instead of /home/username/.ssh though (recompiling ?).

`# mkdir -p /home/johndoe/.ssh
Put johndoe's authorized_keys file in his .ssh folder

EDIT : you can create a symlink from /volume1/username/ to /home then create the .ssh directory in the regular user home dir.

Then edit /etc/passwd :

Change the line : 
`johndoe:x:1026:100::/:/bin/csh`

By 
`johndoe:x:1026:100::/home/johndoe:/bin/sh`

**Pay great attention that /bin/csh became /bin/sh**

You should now be able to ssh into the NAS with the user johndoe.



**5. CONCLUSION**

Thanks to Synology for making devices based on Linux. I like how "hackable" and extendable Linux-based devices are.
I can now securely browse my files remotely from Nautilus using SSH. I can now get rid of my big old server.. I'm saving space and energy at the same time.

Thanks to Nicolas for his guide that greatly helped me :
Nicolas' page : [http://www.dotmana.com/index.php/?p=91](http://www.dotmana.com/index.php/?p=91)
