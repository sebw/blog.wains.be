---
date: 2008-01-18
title: "Set the default editor in Debian"
---







categories:
- Debian/Ubuntu
- Linux


I can never seem to remember that command.. always starting the command with alt.. then tab-tab-tab for autocompletion.. but nothing relevant shows up..

Here it is :
`sudo update-alternatives --config editor`

Under Red Hat, both update-alternatives and alternatives commands work.

Or one can always manually symlink editor to vim under /etc/alternatives
