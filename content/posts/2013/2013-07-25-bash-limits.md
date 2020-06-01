---
date: 2013-07-25
title: "Bash easily consult a process limits"
---
Date: 2013-07-25
Tags: Bash, Linux

Create this script, I'll call it limits.sh:

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

Then:

	bash limits.sh 27193

	
