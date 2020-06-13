---
date: 2019-11-13
title: "i3 - making Firefox full screen inside its i3 container"
---

## Problem

So if you press F11 in Firefox, by default it goes 100% full screen.

If you want to put Firefox in an i3 workspace shared with other applications and make Firefox "full screen" inside its container, as presented below:

![Firefox](https://blog.wains.be/images/i3wm-firefox.png)

## Solution

In Firefox, in the URL bar type `about:config`.

Then change:

```bash
full-screen-api.ignore-widgets: true
```
