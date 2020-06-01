---
date: 2006-10-08
title: "Search in bash history"
---







categories:
- Bash
- Howto
- Linux


You typed a long command earlier and don't want to bother retyping it ?

At the prompt, press
Ctrl + r 
You'll be in "reverse-search-history" mode.

Type the beginning of the command, and type Ctrl + r again to browse through the history.

Sometimes I'm starting typing a command and realize I must already have that command in history.
I can't use Ctrl + r in the middle of the command and expect reverse-search-history to work.

For that, you need to edit either /etc/inputrc (as root) if you want to make the changes available system-wide or edit your $HOME/.inputrc, add :

`"C-f": history-search-backward
"C-g": history-search-forward`

Log off and log back in, now you can start typing a command, and press Ctrl + f to search the history for commands beginning by what you've just typed. Type Ctrl + g to search in reverse.

**More info ?**
`$ info rluserman`
