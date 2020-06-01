---
date: 2006-10-18
title: "Tool of the day - watch"
---







categories:
- Linux
- Tools


watch - execute a program periodically, showing output fullscreen

DESCRIPTION
       watch runs  command  repeatedly,  displaying  its  output  (the  first
       screenfull).   This allows you to watch the program output change over
       time.  By default, the program is run  every  2  seconds;  use  -n  or
       --interval to specify a different interval.

       The  -d  or  --differences flag will highlight the differences between
       successive  updates.   The  --cumulative  option  makes   highlighting
       "sticky", presenting a running display of all positions that have ever
       changed.  The -t or --no-title option turns off the header showing the
       interval, command, and current time at the top of the display, as well
       as the following blank line.

       watch will run until interrupted.


**watch comes with the package "procps" under CentOS**

It can be useful in debugging/testing stuff/...
