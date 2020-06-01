---
date: 2008-10-29
title: "Tool of the day - pipeview (aka pv)"
---







categories:
- Linux
- Tools


<code>NAME
           pv - monitor the progress of data through a pipe
    
    SYNOPSIS
           pv [OPTION] [FILE]...
           pv [-h|-V]
    
    DESCRIPTION
           pv  allows  a  user  to see the progress of data through a pipeline, by
           giving information such as time  elapsed,  percentage  completed  (with
           progress  bar),  current  throughput  rate, total data transferred, and
           ETA.
    
    ...</code>



**Some examples :**

pv file | nc -w 1 somewhere.com 3000

cat file | pv -s 12345 | nc -w 1 somewhere.com 3000

**Installation :**

Under Debian :

`apt-get install pv`
