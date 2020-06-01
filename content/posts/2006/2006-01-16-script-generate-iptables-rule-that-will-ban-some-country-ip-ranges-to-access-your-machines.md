---
date: 2006-01-16
title: "Ban a whole country with an iptables script"
---

Let's say you want to completely ban a country from accessing your servers..
E.g. : countries that have very shallow internet laws

**Note : in regards to Epe's comment, this article has been updated with a newer script, which should be doing a better job. Please drop me a comment, I'd love to hear feedback !**

This script will parse the RIPE database and generate the iptables rules automatically..

Download the script here : [https://www.wains.be/pub/update_country_block_list](https://www.wains.be/pub/update_country_block_list)

The output would look like this :

`-A INPUT -s 62.217.192.0/18 -m state --state NEW -j DROP
-A INPUT -s 62.231.64.0/18 -m state --state NEW -j DROP
-A INPUT -s 80.74.48.0/20 -m state --state NEW -j DROP`

Or like this if you just want blocks :

`62.217.192.0/18
62.231.64.0/18
80.74.48.0/20`

You can use the output with iptables or any other firewall
