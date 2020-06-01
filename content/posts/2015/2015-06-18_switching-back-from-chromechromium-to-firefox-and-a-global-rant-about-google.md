---
date: 2015-06-18
title: "Switching back from Chrome/Chromium to Firefox and a global rant about Google"
---


Originally published 2015-06-18

I have used Chrome (and Chromium, depending on the platform I'm working on, from now on I'll refer to it as Chrome) pretty much since it went out of beta.

I started getting annoyed with the last few versions though.

It all started when I installed a random application on a Windows 7 machine as a regular user. The app contained malware that didn't get detected by the antivirus and it installed a couple of nasty Chrome extensions in the user profile. The extensions were redirecting traffic to ads and opening popups when I was surfing.

Trying to get rid of them has been challenging. If you removed the extensions in the extension manager, upon next start up they would appear again. I tried Shield for Chrome but it wasn't helpful, it would just uninstall the extension but it would reappear on the next restart. It is actually because Chrome was starting with some flags that were calling for extensions located on the filesystem. Run `chrome://version` and see the command line, you would see the directories called. The extension manager is pretty useless as it gives you the idea that you have control, but you actually don't.

On the other hand, Chrome has been very good at silently removing an extension that Google considered "harmful". The extension is called "Download Youtube Chrome" (DYC), and you can only install it by downloading it from the web and installing it as an unpacked extension. The extension allows downloading Youtube videos, so it seems copyrights are more harmful than actual malwares potentially spreading on thousands of machines. Once again, you have no control over extensions, Google does.

Before the DYC episode, I was using a similar extension: "Video Downloader Professional". It was working great until Google decided to remove offending extensions from their store and by offending, they probably meant copyright offending (I commute a lot with poor reception, so I download videos for offline viewing).

And even before that, Google started to implement many new features that makes Chrome today twice as big as Firefox as (my comparison is based on OS X): profile management (`chrome://flags/#enable-new-profile-management`), OK Google voice search (this thing is listening to you constantly if enabled, and any OK Google record is saved in Google Cloud), desktop notifications.

You used to be able to specify where desktop notifications were supposed to appear on your screen, but Google decided to remove the feature, so now you have to stick with notifications in the upper right corner, an area that you are very likely to click on Mac (Wi-Fi picker for example). I can't count how many times I have clicked on the stupid notification that just poped up in my face, triggering an action I didn't intend to do.

This goes along with default settings enabling translation and location, and those tend to slow down the browser considerably (I'm talking about a late 2008 Macbook here).

I had a couple of bookmarks synced in their "cloud" by signing in. I protected that with my Google password from the time, but today I have a new password and I still need to type my old password whenever I install Chrome on a new machine. I haven't found how to manage that password (didn't search so much though, but it should be an obvious option).

Lastly, it has been reported that Chromium silently downloads binaries related to voice upon every start on Linux Debian. See [http://www.theregister.co.uk/2015/06/17/debian_chromium_hubbub/](http://www.theregister.co.uk/2015/06/17/debian_chromium_hubbub/) and [https://bugs.debian.org/cgi-bin/bugreport.cgi?bug=786909](https://bugs.debian.org/cgi-bin/bugreport.cgi?bug=786909)

So all in all, Chrome has become a memory hog, and Google is controlling your browser in a unprecedented way. The will push or remove whatever features or design, whenever they wish and don't care about what you think. 

And if you believe that the supposedly open source project gives you a voice in the design and implementation, see this link talking about the decision to hide http:// from the URL bar when it's not secured by SSL: [https://code.google.com/p/chromium/issues/detail?id=41467](https://code.google.com/p/chromium/issues/detail?id=41467)

See this comment particularly: [https://code.google.com/p/chromium/issues/detail?id=41467#c19](https://code.google.com/p/chromium/issues/detail?id=41467#c19)

	Chromium UI design is not a democracy and is not based on users' votes ~ Peter Kasting
	
The problem with Chrome design became so important that some people even petionned against Kasting and the UI team: [https://www.change.org/p/lawrence-page-replace-peter-kasting-and-the-google-chrome-ui-team](https://www.change.org/p/lawrence-page-replace-peter-kasting-and-the-google-chrome-ui-team)

I'm now switching back to Firefox. The last  version I used was 3 or so. What is it now? Version 38. Holy cow, time flew.

If only Chrome was the only problem with Google.

Their are trying to force Google+ down our throats in the nastiest ways (you have to have a G+ account to upload Youtube videos, you can't comment Android applications without G+, etc.).

They shut down major applications like Reader probably in a way to increase traffic to G+, but it failed big time. They should have integrated Reader in G+, but instead they decided to lose users (and trust from them).

iGoogle was neat too. Too good probably (my dad loved it), they had to kill it.

They are forcing Hangouts for people who still use Google Talk and stopped XMPP support. Every once in a while, Gmail will switch to Hangouts without notice. Thanks, I'm now using competing products.

I'm done with the force-feeding attitude of Google that got so powerful that they think their vision of the web should be pushed (silently) to users with not a single care of what users think. They know it, a vast majority of users have no clue about privacy and don't question what they are given. They also have the right to pull the plug on popular services to serve their purpose of winning the social network business. Give up Google, 90% of users never posted anything on your G+ joke: [https://www.stonetemple.com/real-numbers-for-the-activity-on-google-plus/](https://www.stonetemple.com/real-numbers-for-the-activity-on-google-plus/)

Update 2016-07-18: Interesting blog post by Maxime Kjaer regarding vulnerabilities in Chrome extensions (confirming Google can remove extensions as they want): [link](https://kjaer.io/extension-malware/)

Update 2019-02-28: https://redalemeden.com/blog/2019/we-need-chrome-no-more
