---
date: 2007-02-18
title: "grep, sed, awk"
---







categories:
- RHCE


Let's continue with the basics... Reviewing only the really useful, and not going into the deep

If you have some useful tips, please share ! :)



**Grep**

grep : print lines matching a pattern (equals "grep -G" which is the default)
egrep : equals grep -E (interpret extended regexp)

grep -n : line numbered
grep -i : ignore case
grep -c : count matches
grep -v : print non-matching lines
grep -r : recursivity, read all files under each directory

`grep pattern file.txt`
Will display the lines containing the pattern

`grep -c pattern file.txt`
Will display how many lines contain the pattern

`grep -i pattern file.txt`
Will display the lines containing the pattern regardless of the case

`grep -A 1 -B 1 pattern output.txt`
Will print the one line before (-B) and one line after (-A) the matching pattern
 
**Awk**

`awk '{ print $0 }' file`
Output the content of the file

`awk '{ print $2 }' file`
Output the second field of data of the file, space is the default separator

`awk -F ':' '{ print $2 }' file`
Same but separator is ":"

**Sed**
`cat file | sed -e 's/old_pattern/new_pattern/g'`
sed would replace old_pattern by new_pattern in the output

`cat file | sed -e '4,10s/old_pattern/new_pattern/g'`
sed would replace old_pattern by new_pattern in the output between line 4 and 10

`cat file | sed '/pattern/d'`
Delete a pattern

`cat file | sed '/pattern/!d'`
Delete everything but the pattern (this equals grep "string_to_remove")
