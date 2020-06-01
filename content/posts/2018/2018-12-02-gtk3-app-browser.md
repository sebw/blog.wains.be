---
date: 2018-12-02
title: "Access GTK3 application from a browser"
---

Start broadwayd

```
broadwayd :5 &
```

Start a GTK3 application (for example `gtk3-demo`)

```
GDK_BACKEND=broadway BROADWAY_DISPLAY=:5 gtk3-demo
```

Application can now be reached from [http://hostname:8085](http://hostname:8085).