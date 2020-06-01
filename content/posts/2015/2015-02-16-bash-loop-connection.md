---
date: 2015-02-16
title: "Bash- loop until a connection is successful"
---
Date: 2015-02-16
Tags: linux, bash

I use Terminator as my terminal app, and use the "watch for activity" feature a lot. With the following command, I'd get notified as soon as the connection is opened.

    while ! nc -vz localhost 3306 2> /dev/null; do sleep 1; done && echo "Available!"
