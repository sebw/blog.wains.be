---
date: 2006-07-26
title: "Screen help"
---

Based on http://www.rackaid.com/resources/tips/linux-screen.cfm

A good reminder :)

At the prompt, type "screen", then...

Ctrl + A ? : help
Ctrl + A C : create a screen session
Ctrl + A N : switch to the next session 
Ctrl + A P : switch to the previous session
Ctrl + A K or exit : exit session
Ctrl + A D : detach session
screen -r session : re-attach to a detached session
Ctrl + A M : look for activity within the screen session
Ctrl + A _ : look for silence within the screen session
Ctrl + A Esc : allows to scroll back in the buffer

Edit ~/.screenrc :

`caption always "Screen %n - %d/%m/%y (%c)"`

It will add a status line showing in which screen session you're working plus the date and time 
