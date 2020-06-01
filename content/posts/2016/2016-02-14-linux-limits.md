---
date: 2016-02-14
title: "Linux know the limits of one or several processes"
---

```
#!/bin/bash

for process in $@; do
     process_pids=`ps -C $process -o pid --no-headers | cut -d " " -f 2`

     if [ -z $process ]; then
        echo "[no $process running]"
     else
        for pid in $process_pids; do
              echo "[$process #$pid -- limits]"
              cat /proc/$pid/limits
        done
     fi

done
```
