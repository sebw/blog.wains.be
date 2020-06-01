---
date: 2019-04-17
title: "Block DNS ANY queries in Docker iptables"
---

You can use the `DOCKER-USER` chain to pass any custom iptables rules you want.

In my case, I run a DNS resolver publicly and want to prevent it from being abused in DNS amplification attacks.

I use the following:

```
iptables -I DOCKER-USER -p udp --dport 53 -m string --hex-string "|0000FF0001|" --algo bm --from 40 -j DROP
iptables -I DOCKER-USER -p tcp --dport 53 -m string --hex-string "|0000FF0001|" --algo bm --from 52 -j DROP
```