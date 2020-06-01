---
date: 2018-08-19
title: "Odd upload problems behind Ubiquiti router to Scaleway VPS"
---

I just spent a couple of hours troubleshooting what was for me an odd network connectivity issue.

I started my new job at Red Hat and received a Nokia 7 Plus smartphone, running Android 8.1.

I reinstalled all my apps and moved all my data from my Moto G5 to the Nokia from my hotel room in Amsterdam, and all seemed fine.

After coming back home from my business trip, the Nextcloud app started complaining about "SSL initialization problem", or "connection time out" while trying to auto upload my pictures. My phone was connected to my Wi-Fi provided by Ubiquiti UAP antennas and could not test upload over 4G as my number was being ported on Red Hat account, I had no data plan.

Access to the internet is provided by a Ubiquiti ERX router, bridged with the ISP VDSL2 modem.

My Nextcloud instance runs in a container on a Scaleway VPS, exposed to the internet behind an Apache reverse proxy running on the VPS (not container), taking care of SSL.

Also, I couldn't "git pull" from inside Pass application (password store app, Git repository stored in Gogs in another container on the same VPS), the app was giving me an "auth failure" after 2 minutes.

ConnectBot (SSH client) could not achieve a SSH connection to the VPS. The connection was initiated after typing my key password but then hung and I didn't get any prompt. It would time out eventually.

FolderSync could not do his daily backup from SD card to Nextcloud either.

My Macbook connected to the same Wi-Fi antenna could upload to Nextcloud (via brower or Mac app), SSH to the VPS, do git pulls from Gogs.

Last night, I went to the restaurant and my phone connected to the local Wi-Fi. Nextcloud started uploading pictures that were pending. I could do "git pulls" and SSH to my VPS.

I came home and started comparing tcpdump traces (taken on my Ubiquiti ER-X router and the VPS) from the conversations between my phone and the VPS, and the Macbook and the VPS. It was just showing that at some point the VPS stopped answering and the phone was just sending the same frame over and over, until time out. The dump from the Macbook didn't show anything striking.

With those observations, I ruled out a specific app being the problem, and thought more about crypto or network issues.

I started digging further and realized that I could SSH to my VPS with a password (which I enabled for my tests) or with a smaller 2048 key, so the problem existed only with my SSH key.

My SSH key was RSA 8192, I regenerated a 4096 key and the SSH problem (Pass app and ConnectBot) was considered fixed. I thought it was some weird implementation of a library on Android having a hard time with RSA 8192 and didn't think the Nextcloud problem as related.

The Nextcloud problem was still present, and interestingly, it was limited to uploads. Downloading 4-10MB pictures was fine, uploading a 1MB file was not.

Digging deeper, I realized that the problem was not limited to Nextcloud. I could not upload pictures to my Lychee instance (photo platform running in a container on the very same VPS) from Chrome. The upload was always getting stuck around 10-11%. Again the Macbook had no problem with that.

I tested upload from my old Motorola G5 and it was able to upload to Nextcloud or Lychee from Chrome!

I wanted to rule out the Wi-Fi antennas (Ubiquiti Unifi UAP Lite and Pro controled by Unifi Controller running on a different VPS (not container)), so I started my old Linksys AP: same problem, Ubiquiti antennas not at fault. From that point on, I was pretty convinced that either the router was the issue or Scaleway doing some weird IDS stuff.

I still wanted to rule out Docker from the equation, so I made a small PHP upload form (running directly on Apache on the VPS) and tested some more. I could upload a tiny file (751 bytes) but a 1.54 kB file would not upload and I would get a timeout.

For many hours, I had been thinking it was some crypto related problems (because SSH problem with 2 apps and Nextcloud being over SSL), so I wanted to rule out SSL (I have HSTS enabled for my domains). I made the form available on HTTP only on the VPS IP: same problem. SSL not at fault.

I removed all my iptables rules to rule out the firewall: same problem. Firewall not at fault.

It seemed like a weird QoS problem, but didn't have any rule applied.

### The solution

After looking up for a couple hours including keywords like timeout, hang, upload stuck, etc., I stumbled upon a [forum post from 2017](https://community.ubnt.com/t5/EdgeRouter/UAP-Pro-can-t-reach-remote-controller-behind-ER-X-works-fine/m-p/2142678/highlight/true#M186119). I was the author of the post! At the time I was complaining how my Android phone (Moto G 1st gen at the time) could not upload to Nextcloud on my Scaleway VPS... Someone asked me if I had mss clamping set to 1452 on the pppoe interface. I applied the recommended setting to no luck and the exchange stopped there.

I somewhat forgot that I already had upload issues with Nextcloud, but I figured it was a bug in the app at the time (there were many issues opened regarding uploads and developers were working on fixes)

Investigating this MSS clamping thing deeper on Ubiquiti forums, I find a post where someone recommended to apply the setting to ALL interfaces. Which I did:

```
set firewall options mss-clamp interface-type all
set firewall options mss-clamp mss 1452
commit
```

**Problem gone!** I can now upload to Nextcloud, I can upload through the PHP form (SSL or not), I can SSH to the VPS.

Even a speed test looks better.

Interestingly, it seems only this specific Android device was concerned by this misconfiguration. If anyone has a clue, hit me up on Github!

### Some other things I have investigated: 

- Apache reqtimeout module and timeout settings
- Tried Nginx as SSL reverse proxy instead of Apache
- Could upload to Nextcloud or Lychee from my old Motorola G5 via browser
- recreated my Let's encrypt certs to a single wildcard one
- used my Synology NAS as SSL reverse proxy (on a Comodo cert) in front of the VPS: worked
- bought Comodo cert to put on Nextcloud: didn't help