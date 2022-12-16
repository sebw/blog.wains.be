---
date: 2006-12-13
title: "RRDWeather 0.40 released"
---

Download here: [https://github.com/sebw/rrdweather](https://github.com/sebw/rrdweather)

Project page: [https://www.wains.be/projects/rrdweather/](https://www.wains.be/projects/rrdweather/)

Changelog since version 0.36
- Grégory Bittan reported an issue when the web connection is down.
  The script would still feed the RRD databases with a value of 0, messing up the average figures.
- The update script can now handle the monitoring of several cities
- Lionel Porcheron made RRDweather available to the Ubuntu Community trough the Universe repository
- To achieve the goal of monitoring several cities from a single script, RRDWeather had to use a new directory structure in order to store database files
- Better indenting
- Improved bash code
- Improved debugging mode
- ...
