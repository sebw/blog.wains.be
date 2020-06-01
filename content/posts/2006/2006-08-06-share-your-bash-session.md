---
date: 2006-08-06
title: "Share your bash session"
---







categories:
- Bash
- Howto
- Linux


From a cool blog : [Yannick's blog](http://blog.uggy.org/index.php?post/101-faire-partager-sa-session-avec-un-utilisateur-distant)

You may know "screen", tool that can help you put your session on hold and and get back to it whenever you want.
Described below is a way to share your session with someone... this can come in handy while doing support or if you just want to share your session for some reason

You should follow the following steps carefully

User 1 will work in the terminal
User 2 will watch what user 1 is doing

1. User 1 : 
`$ mkfifo /tmp/file`

2. User 2 : 
`$ cat /tmp/file`

3. User 1 : 
`$ script -f /tmp/file`

User 1 can start working, user 2 will be able to follow his work, user 1 should type ctrl + D if wanting to stop sharing the session...

If user 2 tries to input something, the connection to the pipe will be lost and you should get back to step 2.

Thanks to Yannick !

Edit :

The screen method is described at this link : [http://blog.wains.be/post/share-your-bash-session/](http://blog.wains.be/post/share-your-bash-session/)


