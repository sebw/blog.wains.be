---
date: 2020-01-12
title: "Monitor logs live and take actions with swatchdog"
---

swatchdog was originally called swatch. I'll call it swatch in this article.

So, swatch is a very simple process that can monitor a log file live, and take actions if a string is found.

It's an ideal situation if you don't have the resources to run a full fledged monitoring solution such as Graylog.

On Fedora, install the package:

```
sudo dnf install swatch
```

Create a configuration file `.swatchrc`, for example:

```
watchfor /success/
        echo green
        exec bash /opt/swatch/notify.sh $_

watchfor /failure/
        echo red
        exec bash /opt/swatch/notify.sh $_
```

Between slashes is the regexp you want to match.

The `echo` line will output the matched log line and display it in the specified color.

The exec line contains `$_`. `$_` contains the log line, so as you can see you can execute binaries or scripts and pass the log line as an argument to it.

Now start `swatch`:

```
swatch -c ~/.swatchrc -t /var/log/myapp.log
```

This is the expected output as soon as something gets matched:

```
*** swatch version 3.2.3 (pid:1) started at Sun Jan 12 18:59:57 UTC 2020

[2020-01-12 19:00:09] blah is a success
```

The color of the output would be green.

You can run `swatch` as a daemon with the option `--daemon`.

**Conclusion:**

swatch is a fairly simple and lightweight tool if you need to monitor events without installing a resource hungry monitoring solution.