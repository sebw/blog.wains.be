---
date: 2007-02-17
title: "Input/output redirections and pipes"
---







categories:
- RHCE


Today is the day I'm diving into the docs getting ready for the RHCE exam (exam on April 20)
I'm adding a RHCE category to the site. I'll put all my study notes in there.

Feel free to drop a comment if you have any useful tip or trick !

Let's begin with the basics..



Standard input (stdin) : 0 
Standard output (stdout) : 1 
Error output (stderr) : 2

> : output redirection
> : append output
| : pass the output to the next utility
|| : execute the next command if the previous failed 
&& : execute the next command only if the previous has succeded

Examples : 
1. `ls -l test.txt > /tmp/output.txt`

If test.txt exists, the output will be written in /tmp/output.txt
If test.txt doesn't exist, the error message will be output to the screen while nothing will be written to /tmp/output.txt (but the file will be created if not existing)

2. `ls -l test.txt 2> /tmp/error_output.txt > /tmp/output.txt`
Now, the error message would be output to /tmp/error_output.txt if the file doesn't exist

3. `ls -l test.txt 2> /tmp/error_output.txt >> /tmp/output.txt`
The error output would still go in error_output.txt while the standard output would be APPENDED to output.txt

4. `cat file1 > file2`
Will copy the content of file1 to file2

5. `cat > file3
Hello world
^D`

Will write "Hello world" to file3

6. `wc  count_test.txt`
This would count the number of lines, words and bytes in test.txt ("wc < test.txt") and output the result to count_test.txt

Several ways of counting lines etc. :
$ wc test.txt
 7  2 17 test.txt
$ wc < test.txt
 7  2 17
$ cat test.txt | wc
      7       2      17

7.
`ls -l test10 > output.txt 2>&1
This would output anything under output.txt

`ls -l test10 2>&1 | mail -s "output in a mail" address@domain.be`
Any output in a mail
