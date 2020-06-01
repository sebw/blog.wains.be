---
date: 2005-10-29
title: "sh/bin bad interpreter- No such file or directory"
---

If a script you are trying to run returns :

`sh/bin bad interpreter: No such file or directory`

Try this :

`dos2unix filename`

**Before dos2unix :**

```
head -1 filename | od -c
should return :
0000000   #!   /   b   i   n   /   s   h  r  n
0000013
```

**After dos2unix :**

```
head -1 filename | od -c
should return :
0000000   #!   /   b   i   n   /   s   h  n
0000013
```

**Noticed the r character ? That's what turned out to be the problem..**

You can find more info about the "newline" character at [Wikipedia](http://en.wikipedia.org/wiki/%5Cr%5Cn).
To make a long story short, Windows systems use "rn" to create a new line while Unix systems use "n" alone.

Files edited under Windows systems could be "corrupted" by this small issue, reason of the dos2unix tool.

You may need to convert a Unix file into a Windows file with the unix2dos command.
