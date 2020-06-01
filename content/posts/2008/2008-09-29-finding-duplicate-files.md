---
date: 2008-09-29
title: "Finding duplicate files"
---







categories:
- Linux
- Tools


**Under Debian : **

`apt-get install fdupes`

**Then :**

`fdupes -r /home/user > /home/user/duplicate.txt`

-r : recursive
Output of the command goes in duplicate.txt

fdupes will compare the size and MD5 hash of the files to find duplicates
