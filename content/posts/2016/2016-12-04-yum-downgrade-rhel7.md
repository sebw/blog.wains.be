---
date: 2016-12-04
title: "Downgrading packages with yum on RHEL7"
---

yum history list all

yum history undo $ID --exclude=kernel*

More info: [https://access.redhat.com/solutions/29617](https://access.redhat.com/solutions/29617)
