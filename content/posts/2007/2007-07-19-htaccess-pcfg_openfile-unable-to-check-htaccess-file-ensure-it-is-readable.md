---
date: 2007-07-19
title: ".htaccess pcfg_openfile- unable to check htaccess file, ensure it is readable"
---







categories:
- Apache
- Linux


I got this error message in apache error logs :

`/some/path/to/.htaccess pcfg_openfile: unable to check htaccess file, ensure it is readable`

I was trying to access an image to something like http://www.domain.be/image/picture.jpg

The error message is very misleading because the real issue was just inappropriate rights on the folder "image". (I did not have anything involving htaccess around there).

I had the folder chmod'ed 644 for some reason.

A "chmod 755 image" fixed the problem..


