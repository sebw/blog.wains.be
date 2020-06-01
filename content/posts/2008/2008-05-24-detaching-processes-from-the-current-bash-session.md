---
date: 2008-05-24
title: "Detaching processes from the current bash session"
---







categories:
- Bash
- Linux


**A bit of explanations :**

`$ help disown
disown: disown [-h] [-ar] [jobspec ...]
    By default, removes each JOBSPEC argument from the table of active jobs.
    If the -h option is given, the job is not removed from the table, but is
    marked so that SIGHUP is not sent to the job if the shell receives a
    SIGHUP.  The -a option, when JOBSPEC is not supplied, means to remove all
    jobs from the job table; the -r option means to remove only running jobs.`


**And a slice of example :**

1. Let's open a terminal
2. We launch a very long process in background : ./myprocess.sh &
3. We type : disown XXXX where XXXX is the PID of the bash command running myprocess.sh
4. We close the terminal
5. We open a new terminal, we type "ps faux" and notice myprocess.sh is still running

It's possible to start the command like this : "./myprocess.sh & disown"

disown allows you to detach a process from the currently running bash session, this can come in handy if you forgot to launch the process in a screen session or with nohup and you need to close the current session.

If you want to make some tests, myprocess.sh could look like this :

`#!/bin/bash
until [ ! blah ]; do sleep 1; done`


**Links :**

nohup : [http://blog.wains.be/post/nohup/](http://blog.wains.be/post/nohup/)

screen : [http://www.gnu.org/software/screen/](http://www.gnu.org/software/screen/)
