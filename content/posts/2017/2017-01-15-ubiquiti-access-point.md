---
date: 2017-01-15
title: "Ubiquiti Unifi WiFi access point UAP LR vs UAP AC Pro"
---

My old Linksys access-point was showing signs of weaknesses and I decided to change for something a bit more professionnal.

I went (by mistake) with an Ubiquiti "UAP LR". LR stands for "Long Range".

In reality, Ubiquiti don't advertise UAP access-points so much anymore. Those are limited to 2.4GHz range. The one I really needed was the "UAP AC" model (dual band 2.4GHz and 5GHz) that is almost twice the price.

First of all with Ubiquiti AP's, you need a "controller" to install the AP. There's no embedded HTTP server or whatever on those AP's. You can use your mobile phone to first setup the antenna but it comes with fewer options than a fully fledged controller.

Also, with a 24/7 controller you get interesting features like roaming when you own multiple antennas and statistics.

First word of caution: disable automatic upgrade! It appears the last two firmwares for the non-AC model was making the connection pretty unreliable.

Another word of caution: you SHOULD NOT buy the LR (long range) model for residential or even enterprise use. LR models are dedicated to very specific use (think industrial applications).

Let me explain why below.

When you get an LR AP, it is set by default to auto transmit power, which is basically very high transmit power. It is likely that none of your devices will be able to keep a stable connection with that. The AP constantly "yells" while your devices "whisper". Such a way of communicating does not even work in real life, right?

In order to get a reliable connection with my devices I had to lower the transmit power to low. This defeats the purpose of long range, and you actually get a "non Long Range" AP by doing so.

The AP accepts a maximum of 4 SSID.

The network configuration is a bit different to Linksys regarding VLAN's. On my older Linksys AP I had the native VLAN set to 1 and I was tagging each SSID.

With Ubiquiti you can't specify a native VLAN on the antenna. You should define the native VLAN on the trunk. SSID's that are not tagged on the antenna will run on the native VLAN, otherwise you specify the VLAN for the SSID and off you go.

Interestingly you can't define the same VLAN for two SSID's. If you want two SSID's running on the same VLAN, it should be on the native VLAN only.

For a setup with VLAN's, Radius and multiple SSID's, it can be tricky, so check this:

[https://community.ubnt.com/t5/UniFi-Wireless/UniFi-multiple-SSID-s-VLAN-s-RADIUS-servers-and-subnets/td-p/703809](https://community.ubnt.com/t5/UniFi-Wireless/UniFi-multiple-SSID-s-VLAN-s-RADIUS-servers-and-subnets/td-p/703809)

After going through all those troubles I switched the "UAP LR" for an "UAP AC Pro". Please note that only Pro models come with **two Gigabit Ethernet ports**.

Out of the box, the Pro model is reliable without tweaking transmit power. Also it got provisionned with the configuration immediately, thanks to the controller already being in place.

I will test it some more in the next couple of weeks and report back in this post.

----

Update 2017-04-21

No worries with the AP since the installation a couple of months ago. Highly recommended!
