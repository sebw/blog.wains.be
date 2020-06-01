---
date: 2008-05-23
title: "Sharing your bash session using screen"
---







categories:
- Bash
- Linux


[In this post](http://blog.wains.be/post/share-your-bash-session/) I was describing how one can share a bash session using a named pipe.

This is a great way if you can't install anything on the machine. If you are able to get "screen" installed, screen provides a much easier way, which allows all connected users to interact on the shared session. The named pipe method only allowed one user to watch what the other user was doing.

So here it goes...

1. user 1 connects to the machine and type the command "screen"
2. inside the screen terminal, user 1 hits ctrl + a and then type ":multiuser on"
3. user 2 joins in by typing "screen -x"
4. other users can join as described at point 3

Both users are now sharing the session.

Ctrl + a then d will close the screen session


**Links : **

Source : [http://linuxhelp.blogspot.com/2005/01/screen-window-manager-for-console.html](http://linuxhelp.blogspot.com/2005/01/screen-window-manager-for-console.html)

Putting a password on a screen session : [http://blog.wains.be/post/put-a-password-on-your-screen-session/](http://blog.wains.be/post/put-a-password-on-your-screen-session/)

