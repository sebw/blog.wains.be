---
date: 2015-08-15
title: "Migrating my website (again)"
---
Date: 2015-08-15

In the first half of this year, I migrated this site from Wordpress to [Scriptogram].

Scriptogr.am is tightly integrated to Dropbox, which means if you want to have two blogs running the same platform, you should have two Dropbox accounts.

It was my case, and I never really got it to work properly on two Dropbox accounts, let alone one. Scriptogr.am had many sync hiccups. The project felt pretty dormant actually.

I wanted to achieve something that would be as easy as possible, I'm often on the road and I want to publish things as soon as I think of them.

My requirements were:

```
- articles should be plaintext
  - stored on my private cloud
  - synced with my devices (phone, computers, tablet)
  - language should be markdown (many editors available on different platforms)
- website should be in Python or PHP
- preferably, as soon as I save the file in the editor, it would automagically appear online.
```

I've been thinking about coding something myself based on [PHP Markdown].

Then I found [Wikitten]. Installation took less than 2 minutes.

The whole workflow goes like this:
- Create or edit file on the client:
  - Macbook: [Atom]
  - Android: [Writeily Pro]

- As I save the document, it is saved automatically in my private cloud.
- My phone picks up the file as soon as it connects to a Wi-Fi network.
- [Foldersync] detects the change and sends the change over SSH to my VPS.
- The page is online.

As you will probably notice, they are a few formatting issues for now (especially with lists). I use the [Tidy markdown](https://github.com/slang800/atom-tidy-markdown/) package with Atom, it is supposed to fix markdown properly as soon as you save the file, but there is a [bug](https://github.com/slang800/tidy-markdown/issues/15) for now.

[scriptogram]: http://scriptogr.am
[php markdown]: https://michelf.ca/projects/php-markdown/
[wikitten]: https://github.com/victorstanciu/Wikitten
[atom]: https://atom.io
[foldersync]: https://play.google.com/store/apps/details?id=dk.tacit.android.foldersync.full
[writeily pro]: https://play.google.com/store/apps/details?id=me.writeily
