---
date: 2006-11-14
title: "SpamAssassin - prefork- child states meaning"
---







categories:
- Linux


I start SpamAssassin with 2 children and set the maximum children to 10

This is how I start SpamAssassin :
`spamd -d -u spamassassin --min-children=2 --max-children=10 -x -r /var/run/spamd.pid --socketowner=filter --socketgroup=filter --socketmode=664 --socketpath=/var/run/spamd.sock`

spamd will send messages like this :
`spamd[16437]: prefork: child states: II`

II means the two children are idle

There are several status :
I : idle
S : starting
K : killed (you should not get that one I guess :))
B : busy

If the number of max children is too low, you'd get this message in the logs :
`spamd[13052]: prefork: server reached --max-children setting, consider raising it`

Well if you get that message, it's pretty self-explanatory.. raise the max-children value
