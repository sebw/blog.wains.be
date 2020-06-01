---
date: 2007-10-28
title: "Belgian eID under Ubuntu 7.10"
---







categories:
- Firefox
- Hardware
- Linux


I had almost forgotten that I was offered an [eID card](http://eid.belgium.be) reader by our Federal Public Service FINANCE, a few months ago when I went up to their offices to get some info.

I decided to give it a try tonight.

The sticker on the back of the reader says "ACR38U-SPC-FDT (FW104)".

![Free Image Hosting at www.ImageShack.us](https://blog.wains.be/images/00-imageshack.jpg)

I first blindly tried to install some packages (beidgui to read my card info) but ran into several problems.

**beid :**

When running beidgui, it was not even listing the card reader in the options tab.

Running from the CLI, I was getting the following error message :
Error: can't open /var/run/openct/status: No such file or directory

If I was starting beidgui using sudo, the card reader was listed correctly.

At some point I also got the following error in beidgui :
"Error : Wrong Root Certificate"

The card reader was also detected as another reader for a while.

I removed every packages and was about to give up when I finally found the answer that made things work out of the box at 
[http://doc.ubuntu-fr.org/materiel/acr38](http://doc.ubuntu-fr.org/materiel/acr38)

Packages to install :

`sudo apt-get install libacr38u libacr38ucontrol0 beid-tools pcscd libpcsclite-dev beidgui libbeid2 libbeidlibopensc2`

After installing these, I was able to read my card under beid.


**Firefox : **

In order to access websites working with the eID, you need to load the PKCS#11 module.

Edit > Preferences > Advanced > Encryption > Security Devices > Load

Module name : "Belgium Identity Card PKCS#11"
Module filename : "/usr/lib/libbeidpkcs11.so.2"

Restart Firefox when done.

The card reader MUST be plugged before starting Firefox if you intend to access a website requiring access to your eID.


**Interesting links :**

[http://users.pandora.be/mydotcom/howto/linux/ubuntubelpic.htm](http://users.pandora.be/mydotcom/howto/linux/ubuntubelpic.htm)
[https://www.mijndossier.rrn.fgov.be/](https://www.mijndossier.rrn.fgov.be/)

This last link is interesting and requires your eID, you can see a lot of stuff the government stores about you (driving licence, passport delivery, etc.).

I even found a mistake in my file, I'm living in an apartment and the previous tenant is listed in my family description... Our public services are not at the edge in every fields.
