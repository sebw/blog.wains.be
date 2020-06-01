---
date: 2007-07-12
title: "Ubuntu - connect to your secure wireless network without authenticating against"
---






  keyring '

categories:
- Howto
- Linux
- Wifi


From : [original link](http://ubuntuforums.org/showpost.php?s=f5de5634f4448d733a599c23e4bb0257&p=2776815&postcount=1)

By default, if your wireless network is secured by WPA or such, you have to save the info in your keyring manager, which is protected by a password.

I personally have the same password for my Ubuntu session but also for the keyring manager.

Whenever I log in, I have to authenticate with my user and password, then Gnome tries to connect to my wireless network and prompts me to unlock the keyring. This is tedious since I have the same password for both the session and keyring manager.

Now, you can avoid having to unlock the keyring, read on...



**1. Install libpam-keyring package :**
`$ sudo apt-get install libpam-keyring`

**2. Then tweak the GDM PAM (plugable authentication module) security **
`$ sudo gedit /etc/pam.d/gdm`

Add the following line at the very end of that file, then save :
`@include common-pamkeyring`

**3. Reboot and authenticate into your session, you should now be connected directly !**

**Both SESSION and KEYRING passwords must MATCH, if they don't match, you would be prompted to unlock the keyring.**

**4. Optional : change your keyring password**

`$ /usr/lib/libpam-keyring/pam-keyring-tool -c`

Also see https://help.ubuntu.com/community/WifiDocs/NetworkManager under section "Automatic keyring"


