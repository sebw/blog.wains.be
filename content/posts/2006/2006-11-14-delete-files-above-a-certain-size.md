---
date: 2006-11-14
title: "Delete files above a certain size with xargs + other find recipes"
---







categories:
- Bash
- Linux


Delete files in the current directory above 500Kb
`find -size +500 | xargs rm`

This may not work on files with spaces in their filename. Use the solution recommended in the comments.

**Other recipes :**

Change the permissions and set 750 on dirs and 0640 on files:

`find -type d -exec chmod 0750 '{}' ';'
find -type f -exec chmod 0640 '{}' ';'`
