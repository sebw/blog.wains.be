---
date: 2007-06-13
title: "Experience with Linksys technical support and their RMA procedure"
---







categories:
- Hardware
- Misc


I bought a Linksys wireless DSL gateway on Ebay in May. It was still under warranty as Linksys products are covered for 3 years.
According to the seller, the unit was brand new and had never been used, as it was a unit that was sent to him from a RMA procedure. In the meantime, he had bought another router.

The exact model I got was a WAG54G-E2 (hardware version 2) with firmware 1.00.43.

[![](https://blog.wains.be/images/00-imageshack.jpg)
_The WAG54G-E2 v2 looked like this_

**The problems**

When I tried to change the DDNS settings (for DynDNS), I was getting an error "Strange server response, are you connecting to the right server?"

After googling the error out, I found out this error was fixed in a later release of the firmware.



Going to the download section of linksys.com, grabbing what seems to be the latest (version 1.01.15) firmware for my model (kind of a maze on their FTP by the way).

I'm trying to upgrade the firmware from the web administration interface and get an error again "Upgrade has failed".

This is where the troubles begin.

**Contacts with the tech support**

22/05 : contacting Linksys support by email about the 2 issues
23/05 : getting an answer from the tech support asking for more details about my setup, in order to make sure the basic configuration is fine.
23/05 : sending them the details
24/05 : getting an answer with a firmware in attachment. They give several solutions in order to upgrade the firmware : the Web GUI, or a Linksys TFTP tool or as last resort the TFTP command line utility. None of them worked.
24/05 : replying it's not working
26/05 : getting an answer inviting me to call the tech support
26/05 : calling the tech support which send me two other firmware files
26/05 : calling again because these 2 are not working either
26/05 : I'm getting *another* firmware from them, it's friday 6pm, the guy asks me to call on Monday if it does not work.
26/05 6.02pm : It does not work...
29/05 : Calling again, we are starting the RMA procedure, I'll get an email asking for details. As I'm concerned about the firmware in that particular version, I'm granted a different model as replacement unit.. I should get a WAG200G instead (which is kind of the newer WAG54G).
29/05 : sending the invoice and necessary details by mail
29/05 : invoice accepted, RMA number created
30/05 : sending them the RMA number, I'll get the UPS details a bit later. (UPS is picking up the faulty unit at your convenience, all this is free of charge for the customer).
30/05 : as soon as I got the UPS sticker by mail, I call UPS to arrange the pick up
31/05 : UPS picking up the unit
04/06 : Linksys in Czech Republic received the unit
11/06 10am : Checking out the status of my RMA, I notice they changed the replacement unit to WAG54G-EU. I'm calling the RMA support. Changed back to WAG200G.
11/06 5pm : Checking out again as the unit should be shipped anytime soon. The replacement unit has been changed to WAG54G-E2 now ! Calling the RMA support but the guy tells me the unit has already been shipped, he tells me that if the unit should have the same I should send the unit again. I'm starting to slighty be pissed off at them now.
11/06 8pm : Checking the RMA status again, the unit appears as shipped, but the UPS tracking number appears to not be valid.
13/06 : unit received. I'm relieved as I got a WAG54G-E2 (hardware version 3) with firmware 1.00.24 but the unit has smear and you can notice wear.

**The new unit**

I can't find any info on the WAG54G version 3, it looks like a WAG200G but not exactly similar.
Anyhow, it looks like this model is doing a great job. **(edit 14 June : the DDNS feature is still messing up... It would update the IP when the unit is booting, but if the IP changes it says it has updated successfully, but actually it hasn't, I have to either reboot or click "update" in the web GUI...I'm giving up on it and I'm now running a DynDNS client on my NAS server instead)**

[![Free Image Hosting at www.ImageShack.us](https://blog.wains.be/images/00-imageshack.jpg)
_The WAG54G-E2 v3 looks like this but has an antenna and the power button on the front, the model on the pic is a WAG200G.. confusing_

It appears the WAG54G-E2 with firmware 1.00.43 was a model sent by Linksys in RMA only.
That particular firmware version was kinda problematic : the DDNS and firmware upgrade problems but also the unit had to be rebooted everytime you were changing a setting. This was tedious.

They probably had a pallet of bricks they needed to get rid of somewhere in a chinese warehouse or something.. 
I've seen reports from several people on the web describing the same issues with the exactly same model, each of them coming from RMA.

**Conclusion**

I'd buy from Linksys again for the 3 years warranty and relatively good support (at least the belgian one) but I would keep away from lesser known models now.
I liked the fact that everything is free of charge for the customer when sending a product in RMA.
And of course, many Linksys products run Linux :)

By the way, the belgian Linksys tech support has two phone numbers : 02-6277077 which is a national call, but they also have the toll free 0800-58028.. both numbers are actually leading to the same place so it's always good to know :)
