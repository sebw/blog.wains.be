---
date: 2014-10-10
title: "PreserveFQDN and EscapeControlCharactersOnReceive with rsyslog"
---







categories:
- Linux


In legacy versions of rsyslog, if you want to use the option PreserveFQDN, you have to set the option before anything else, or it wouldn't work.

If you are having issues sending logs from nxlog on Windows to rsyslog legacy, you might want to have a look at EscapeControlCharactersOnReceive.

http://www.rsyslog.com/doc/rsconf1_escapecontrolcharactersonreceive.html
