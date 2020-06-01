---
date: 2007-06-20
title: "Another rant against Linksys"
---







categories:
- Hardware
- Misc


As you may have read, I had some troubles with my Linksys ADSL Wireless router a while ago.. see [here for the story](http://blog.wains.be/post/experience-with-linksys-technical-support-and-their-rma-procedure/)

At the end of this journey, I was still believing Linksys was a good company making good products.
Read on.



My father in law switched from a DSL connection to cable lately. The cable company provided a cable modem (a Kathrein DCM 52i+, they have been distributing this model for years). As he needed a wireless connection, I went to the store with him to get the famous Linksys WRT54G wireless router (we got a V7 version, firmware 7.0.0.1).

Yesterday, I'm going to my in law's and try to set up the router. But it did not work. 
Hey, I'm starting to get used to that...

Though, I'm not used to dealing with cable connections, so I'm asking a friend about anything that could mess up.
He reminds me about turning off the modem for a while before connecting another device. I knew this and kept the modem off for minutes before plugging the router without success.

When connecting my laptop to the modem, I was surfing the web within seconds.
On the other hand, the router would just not grab the IP info from the DHCP server.

I'm checking out Linksys download site and see the latest version of the firmware (7.0.0.4) fixes an issue that the router may have with DHCP with some devices (no comprehensive list provided though). Bingo !

Grabbing the latest firmware.
Trying to upload the firmware through the web management interface. It starts upgrading, then after a while nothing is going on. No activity. After several minutes, the device reboots.. I'm kind of relieved at this point because it was taking so long to my taste. The device is not reachable after the reboot, I'm waiting for a minute and the device reboots again. I'm starting to get concerned as it's not the usual behavior I have noticed when upgrading other Linksys products.

After that last reboot, I can ping and connect to the router web interface.. at least I can authenticate.. then nothing... the page remains blank trying to load the page. And the device suddently reboots. The word BRICK starts echoing in my head.

I tried rebooting, resetting, hard resetting. Always the same frigging problem.

I give up and bring the unit back to my home where I decide to try the TFTP method Linksys told me about when I had my problems with my ADSL router.

This method worked. The firmware upgrade went through. And I could reach and set up the unit again. Woot !

Today, I went back to my in law's and plugged the revived router into the cable modem. I was on the internet within seconds...

Conclusion :
- Linksys is shipping some really broken units
- I don't know the impact of the DHCP issue and how many devices it affects (odd, it affected mine), but the local cable company has always been providing the same cable modem for years. This means that anyone buying a WRT54G at the store as of today will have the same issue. 
The bright people at that particular store (no, not everyone is dumb at computers) will believe it's some user error and won't exchange the unit or they will return the product in RMA (3 weeks or more with no router for the poor customer). This sucks.
- The upgrade process failing on this unit, the upgrade failing on my unit : coincidence ?
- Isn't Cisco ashamed of having their logo on the Linksys products ?

I know I should avoid Dlink products at all cost. Now I think I'm gonna avoid Linksys products.
Any advice on another good brand ? Netgear comes to mind ?

