---
date: 2007-07-23
title: "Put a password on your screen session"
---







categories:
- Linux


You can "protect" the access to a screen session.
Don't use your usual password, as the method used is pretty weak (standard DES)

Open a screen session :

`$ screen`

Inside the session press "ctrl + a" then type ":password"

If successfully set, you should see [ Password moved into copybuffer ] in the lower left corner of the terminal.

Now you can detach by pressing "ctrl + a" then type "d" 
Reattach with "screen -r" from the prompt.

You should be prompted for the session password.
