---
date: 2015-09-18
title: "Firefox recommended about-config configuration"
---
The following is a recommended setup for Firefox as of September 2015 by [https://www.privacytools.io/#about_config](https://www.privacytools.io/#about_config) and [https://www.eff.org/deeplinks/2015/10/how-to-protect-yourself-from-nsa-attacks-1024-bit-DH](https://www.eff.org/deeplinks/2015/10/how-to-protect-yourself-from-nsa-attacks-1024-bit-DH)

[http://kb.mozillazine.org/Network.IDN_show_punycode](http://kb.mozillazine.org/Network.IDN_show_punycode)

```
network.IDN_show_punycode = true
	Display the “raw”, punycode version of internationalized domain names.
privacy.trackingprotection.enabled = true
    This is Mozilla’s new built in tracking protection.
geo.enabled = false
    Disables geolocation.
browser.safebrowsing.phishing.enabled = false
browser.safebrowsing.malware.enabled = false
browser.safebrowsing.downloads.enabled = false
browser.safebrowsing.blockedURIs.enabled = false
    Disable Google Safe Browsing malware and phishing checks. Security risk, but privacy improvement.
dom.event.clipboardevents.enabled = false
    Disable that websites can get notifications if you copy, paste, or cut something from a web page, and it lets them know which part of the page had been selected.
network.cookie.cookieBehavior = 1
    Disable cookies
    0 = accept all cookies by default
    1 = only accept from the originating site (block third party cookies)
    2 = block all cookies by default
network.cookie.lifetimePolicy = 2
    cookies are deleted at the end of the session
    0 = Accept cookies normally
    1 = Prompt for each cookie
    2 = Accept for current session only
    3 = Accept for N days
browser.cache.offline.enable = false
    Disables offline cache.
browser.send_pings = false
    The attribute would be useful for letting websites track visitors’ clicks.
webgl.disabled = true
    WebGL is a potential security risk. Source
dom.battery.enabled = false
    Website owners can track the battery status of your device. Source
browser.sessionstore.max_tabs_undo = 0
    Even with Firefox set to not remember history, your closed tabs are stored temporarily at Menu -> History -> Recently Closed Tabs.
security.ssl3.dhe_rsa_aes_128_sha = false
security.ssl3.dhe_rsa_aes_256_sha = false
    See https://www.eff.org/deeplinks/2015/10/how-to-protect-yourself-from-nsa-attacks-1024-bit-DH
media.autoplay.enabled = false
    Do not play video automatically
dom.webnotifications.enabled = false
    Disable web notifications
pdfjs.disabled = true
    https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2015-4495
```
