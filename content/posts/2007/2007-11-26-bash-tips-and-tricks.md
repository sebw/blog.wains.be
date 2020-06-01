---
date: 2007-11-26
title: "Bash tips and tricks"
---







categories:
- Bash
- Linux


[Richard posted some nice tips about bash history..](http://richbradshaw.wordpress.com/post/bash-tips-and-tricks/)

I'll post his tips here in a shorter version


The following snippets of code must be added to your .bashrc file.


**You open two terminals, when closing them, history of only one of both is saved ?
This is the fix, that will merge/append histories from both terminals in the history :**

`shopt -s histappend
PROMPT_COMMAND=’history -a’`


**Avoiding spelling mistakes (like /ect instead of /etc) :**

`shopt -s cdspell`


**Disable duplicate entries in history (this is the default under Ubuntu) :**

`export HISTCONTROL="ignoredups"`


**I'm adding a new one, same as above. ignorespace won't store in history every command started by a space. Nice when you need to type sensitive info at the CLI.**

`export HISTCONTROL=ignoredups:ignorespace`


**Remove ls, fg, bg and exit commands from history :**

`export HISTIGNORE="&:ls:[bf]g:exit"`


**Multiple line commands split up in history :**

`shopt -s cmdhist`
