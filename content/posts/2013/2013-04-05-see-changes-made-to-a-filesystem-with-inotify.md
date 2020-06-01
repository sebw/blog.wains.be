---
date: 2013-04-05
title: "See changes made to a filesystem with inotify"
---







categories:
- Linux
- Scripts


Install the package "inotify-tools" with your package manager (in EPEL for RHEL).

Then create and execute this script :

    
    inotifywait -m -r --format $'%T %e %w%f' --timefmt '%H:%M:%S' --exclude ~/'(\.mozilla|Documents/KeepNote)' -e modify -e move -e create -e delete ~ 2>&1 | awk '/^[0-9]/ {
    sub(/'"${HOME//\//\\/}"'/, "~", $0)
    split($0, a, " ")
    len=length(a[1])+length(a[2])+1
    printf "%-20s %s\n", substr($0, 0, len), substr($0, len+2)
    // flush stdout
    system("")
    next
    }
    {print ; system("")}
    ' | tee -a /tmp/home_monitor


Source : http://blog.yjl.im/2010/11/monitoring-file-system-changes-with.html


