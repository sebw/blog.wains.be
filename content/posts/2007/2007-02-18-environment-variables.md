---
date: 2007-02-18
title: "Environment variables"
---







categories:
- RHCE


Displaying global variables --> "printenv" or "env"
Displaying local variables --> "set"

Set a local variable :
`VARNAME="value"`

Local vars only available in the current shell.

Set a global variable :
`export VARNAME="value"`

Useful special bash variables :
$?	Exit status of the most recently executed foreground pipeline.
$$	Process ID of the shell.
$!	Process ID of the most recently executed background command.
$0	Expands to the name of the shell or shell script.

Many more [here](http://tldp.org/LDP/Bash-Beginners-Guide/html/sect_03_02.html)
