---
date: 2007-02-18
title: "Keep your machine synchronized using NTP"
---







categories:
- Howto
- Linux
- Security


Install the package :
`yum install ntp`

Make a backup of the default config
`cp /etc/ntp.conf /etc/ntp.conf.default`



**Minimal and pretty safe /etc/ntp.conf :**
`server 0.europe.pool.ntp.org
server 1.europe.pool.ntp.org
server 2.europe.pool.ntp.org
server 0.north-america.pool.ntp.org
server 1.north-america.pool.ntp.org
server 2.north-america.pool.ntp.org
driftfile /var/lib/ntp/drift
restrict default nopeer nomodify
restrict 127.0.0.1`

**Make sure NTP daemon will start at boot :**
`chkconfig ntpd on`

**Start the NTP service :**
`service ntpd on`

**Verify if your server can reach the peers :**
`# ntpq -pn
     remote           refid      st t when poll reach   delay   offset  jitter
==============================================================================
 195.190.190.13  129.132.97.15    3 u   33   64    3   31.031  -46784.   0.540
 204.152.189.171 216.218.254.202  2 u   33   64    3  160.121  -46786.   0.490
 213.144.132.251 192.36.134.17    2 u   31   64    3   46.861  -46782.   3.868
 127.127.1.0     LOCAL(0)        10 l   30   64    3    0.000    0.000   0.001`

The port udp/123 should be opened in the router routing to the internet.
Logs can be found under /var/log/messages


