---
date: 2007-07-26
title: "Encrypted partition using LUKS under Debian"
---







categories:
- Howto
- Linux
- Security


**Install needed packages :**

`# apt-get install cryptsetup`

**Load modules (if needed) :**

`# modprobe aes

**LUKS on a free partition :**

`# cryptsetup luksFormat -c aes -h sha256 /dev/hda6`

This would erase any data on the partition !

THE PARTITION SHOULD NOT BE MOUNTED, if so "umount /dev/hda6"

**Formating the newly created partition :**

`# cryptsetup luksOpen /dev/hda6 secure

where "secure" is the name given to the encrypted partition.. **it would result in a device name like /dev/mapper/secure**

Mount manually into the /secure directory :

`mount -t ext3 /dev/mapper/secure /secure`

Unmount :

`# umount /secure

**Automatically mounting at boot :**

Edit /etc/crypttab like this : 

`secure /dev/hda6 none luks`

Edit /etc/fstab like this :

`/dev/mapper/secure /secure ext3 defaults 0 1`

You would be prompted for the LUKS password at each boot.

Using a LUKS partition may slow down your system. I haven't tested the impact of LUKS yet. You can always "renice" the daemon responsible for the encryption :

`$ sudo renice 10 `pgrep kcryptd``

**Links :**
[http://doc.ubuntu-fr.org/cryptsetup](http://doc.ubuntu-fr.org/cryptsetup)
[https://help.ubuntu.com/community/EncryptedFilesystemHowto](https://help.ubuntu.com/community/EncryptedFilesystemHowto)


