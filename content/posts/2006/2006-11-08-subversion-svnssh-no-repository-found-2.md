---
date: 2006-11-08
title: "Subversion - svn+ssh-// - No repository found"
---







categories:
- Linux
- Versioning


You can use "svn checkout svn://hostname.tld/project/" but you can't check out your projects using svn+ssh:// ?

Let's clear things, svn+ssh:// doesn't require svnserve to run on the server as a daemon or through (x)inetd to work.

svn+ssh:// is only doing some kind of scp connection to the server.
That's why you need to USE ABSOLUTE PATH WHEN USING svn+ssh

**Usage :**

**svn only**
svn checkout svn://hostname.tld/project/

**svn+ssh**
svn checkout svn+ssh://hostname.told/full/path/to/project/
