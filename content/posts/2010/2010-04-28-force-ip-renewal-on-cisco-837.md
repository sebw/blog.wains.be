---
date: 2010-04-28
title: "Force IP renewal on Cisco 837"
---







categories:
- Networks


Belgacom ISP force IP renewal for their residential ADSL customers every 36 hours for whatever stupid purpose (annoy us, sell their fixed IP option, etc.).

Here's how I force my Cisco 837 router to renew its WAN IP every night at 01:00am :


    
    <code>kron occurrence RENEW_IP at 1:00 recurring
     policy-list RENEW_IP
    
    kron policy-list RENEW_IP
     cli clear interface Dialer 1</code>



I'm less likely to lose my SSH/VPN connections now..
