---
date: 2015-02-17
title: "Migrating from Wordpress to Scriptogr.am"
---
Date: 2015-02-17

I just migrated this blog from Wordpress to Scriptogr.am.

Mainly because this blog isn't so much active anymore, those SQL IOPS were useless for something that had become so static (I disabled the comments many years ago, tired of spam).

I missed Posterous and started looking for something similar, until I found about Calepin.co and Scriptogr.am.

For those of you who don't know, Scriptogr.am will fetch markdown text files from your Dropbox and turn them into a website.

What I like about this approach is that all my articles are stored locally (in a readable format) on my computer, and I can grep, sed and awk the hell out of them.

On the Mac, I use [Mou](http://25.io/mou/) and on Android [Draft](https://play.google.com/store/apps/details?id=com.mvilla.draft). I still have to find a good editor on Linux.

Draft can connect to your Dropbox and synchronise with any folder, so you can pick your posts folder. Most similar apps don't give you a choice.

Mou has many interesting features and has integrated with Scriptogram, so you can push your articles straight from the editor.

The downside of Scriptogr.am is probably the lack of comments (which I don't care about, and some people came up with the code to have Disqus anyway), the inability to search inside your blog (you have to Google it), and the fact that if you need several blogs on the platform, you have to have as many Dropbox accounts. I can deal with that, I have a personal and work Dropbox account, I both use them for this blog and my [travel blog](http://voyage.wains.be).

I used [exitwp](https://github.com/thomasf/exitwp) to convert the XML export of Wordpress into markdown.

Now, you might actually start seeing new content now all over again, given how easy it is to publish stuff :-)
