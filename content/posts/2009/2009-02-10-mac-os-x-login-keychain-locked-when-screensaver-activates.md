---
date: 2009-02-10
title: "Mac OS X - login keychain locked when screensaver activates ?"
---







categories:
- Apple/Mac OS


I've been running Mac OS X at home for the past two months.

I somewhat tightened Mac OS X security by disabling automatic logins, setting a password on the screensaver and such.. the usual steps.

Somehow I set the system in the way that whenever I log in, the keychain will be automatically unlocked in the process. I actually don't want to type two (identical) passwords in a row.. 

For some reason when the screensaver was starting, the login keychain was automatically locked again, despite the system settings. Tedious when you lock your session (by starting the screensaver by moving the cursor in the lower right corner of the screen) even when leaving your desk for 30 seconds..

After some googling, I finally stumbled upon the solution at this link 
[http://www.macosxhints.com/article.php?story=20070927204456413](http://www.macosxhints.com/article.php?story=20070927204456413)

SSHKeychain was guilty.

So if you don't want to lock your keychain when the screensaver starts :
- open SSHKeychain
- go in Preferences
- go under the Security tab
- select "Use custom security settings"
- Under "On screensaver" set the action you like. I set "No action".

Voilà ! :-)
