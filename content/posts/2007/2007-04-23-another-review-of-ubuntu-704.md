---
date: 2007-04-23
title: "Another review of Ubuntu 7.04"
---







categories:
- Linux


I freshly installed Ubuntu 7.04 on my Toshiba laptop.
I first tried to update it but I was getting some slowliness issues for some unknown reason.

**Let's start with the cons :**



- Graphic installation anyone ? You can have all those fancy CD sleeves and a nice logo reworked at every release but that text installer would scare anyone used to GUI environments. (this comment apparently applies to the alternate version of Ubuntu, the desktop one has a graphical installer available within the live cd)

- I've downloaded the alternate version of Ubuntu, because I wanted to use LVM. For some reason, the system was never getting through the LVM thing. I finally went with a regular ext3 system with no LVM.

- The partionning tool is confusing. I mean, when you're used to Anaconda from RedHat, it is just confusing and somewhat counter-intuitive.

- I live in Belgium, set my keymap to belgian keyboard, and the installer was trying to download language packages from a french repository which was overwhelmed at the time. The first install was then incomplete because it gave up on some packages. How about probing for the closest mirror available, Belnet in this case.

- Compiz still a bit unstable. I switched to the good old Beryl.

- Beryl still having some issues when playing videos, the video would sometimes goes blank and you have to move the window around to get the image back.

- Installing Ubuntu is dead sloooow. It takes ages, really.

- Updating Ubuntu is dead sloooow. It took more than 2 hours when I upgraded my Edgy install on my desktop PC (P4 2.6 Ghz, 1 Gb RAM). (OK, this comment still applies to Edgy).

- Some internationalization/translation issues here and there. (like the tooltip announcing the computer has some hardware that requires proprietary drivers to function properly, Ubuntu is mispelled to Ubtunu. I've tried to find where I could report that typo but it is a maze to know what package this tooltip belongs to).

- Anti-aliased window borders on default Human theme anytime soon ?

**The pros now :**

- Seahorse integration !!! Best. Stuff. Ever. Managing your SSH/GPG keys is a breeze. I could go in Nautilus and type Ctrl + L then ssh://server and Seahorse would prompt me for my SSH key password.

- They fixed the SMB access problems I was having. When trying to open the SMB share on my server, Nautilus would prompt me for the credentials but would NEVER save them. After a while, the connection would kinda drop and I'd have to kill Nautilus to be prompted AGAIN for the password. Now, it works. Always.

- Better Gnome-VFS integration. I can now play my mp3's right from the SMB shares. Not sure if it's fixed in Nautilus or in Totem though.

- Codecs installation is a breeze. Trying to play an MP3 for the first time. The system warns you you don't have the codecs installed, you install them and the mp3 starts playing. Same with divx or quicktime movies (even within Firefox). Every media I've tried to play so far just played. Flawlessly. Amazing.

- Beryl in the repository ! Install packages. Start beryl-manager at session start. Done. 

- Beautiful font rendering in 2 minutes, as described [here](http://ubuntuguide.org/wiki/Ubuntu:Feisty#How_to_improve_sub-pixel_font_rendering_for_Feisty)

- WPA2 working out of the box with the Intel wireless ipw3945. I think it was already working in Ubuntu 6.10 but it's just nice to see it working perfectly when you know how it used to be in the not-so-far past.

- OpenOffice now comes with the iconset of Ubuntu. Just a question of aesthetic again, but the old iconset was really so 90's.

**Conclusion :**

The most usable Linux desktop I had to use so far. Things just work. They fixed the most annoying bugs I was encountering.

Seahorse is to me the nicest addition to the system.

There's a foreever bug in Gnome ([Bug 310809](http://bugzilla.gnome.org/show_bug.cgi?id=310809)) that I'd like to see fixed soon.

An annoying forever bug in tsclient ([Bug 8422](https://bugs.launchpad.net/ubuntu/+source/tsclient/+bug/8422))

I'd love to see a better Bluetooth integration. Mac OS X is above anything else when it comes to Bluetooth stuff (at least with Sony Ericsson phones). iSync and Bluetooth manager are doing such a great job in sending/receiving SMS, transfering files, syncing address book, etc.
I'd just love to receive SMS notification popups on my screen and be able to reply right from that notification window, just as if it was an email. Bluetooth under Windows XP is still lame. It is nowhere under Linux.

Some interesting Bluetooth links explaining how easy it is under Mac OS X and how I'd like to see it running under Linux : 
[http://www.oreillynet.com/mac/blog/2004/03/bluetooth_phones_and_mac_os_x.html](http://www.oreillynet.com/mac/blog/2004/03/bluetooth_phones_and_mac_os_x.html)
[http://www.pcworld.com/article/id,115279/article.html](http://www.pcworld.com/article/id,115279/article.html)

My laptop now only has the Ubuntu system installed. Windows partition gone :)
