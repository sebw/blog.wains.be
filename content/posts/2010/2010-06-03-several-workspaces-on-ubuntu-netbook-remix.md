---
date: 2010-06-03
title: "Several workspaces on Ubuntu Netbook Remix"
---







categories:
- Debian/Ubuntu


Workspaces are "disabled" by default in Ubuntu Netbook Remix (actually set to one workspace)

If you want 4 workspaces, in a terminal type :
`gconftool-2 -s /apps/metacity/general/num_workspaces 4 --type int`

You'll navigate in the workspaces with the usual keyboard shortcuts : ctrl + alt + left/right arrow keys
And : ctrl + alt + shift + left/right to move windows from one space to another.
