---
date: 2020-03-10
title: "How to fix the Synology Cloud Sync Unknown error occurs non sense"
---

I sync my data from my Seafile instance back to my NAS. Well, when it works.

In the most random fashion, my Synology NAS "Cloud Sync" enjoys to take a break. And up until now, it seemed impossible to get it back to sync.

![](https://blog.wains.be/images/synologysucks1.png)

![](https://blog.wains.be/images/synologysucks2.png)

You would expect that restarting the app would trigger a resync, but alas, no, it doesn't. We're talking about Synology here, right.

The only option was to unlinking and relinking stuff, which meant reconfiguring. A massive pain when Cloud Sync fails every couple of weeks.

After an hour of digging in the logs, searching the dark corner of the internet, I finally found the solution by one brave man called @mback2k on Github.

[https://github.com/nextcloud/server/issues/10123#issuecomment-412640597](https://github.com/nextcloud/server/issues/10123#issuecomment-412640597)

So yeah, there it is. When Cloud Sync gets stuck, Cloud Sync will NEVER try to sync again.

Procedure to fix this non sense:

- SSH as root on your NAS.
- Then open up the DB for Cloud Sync and update the session_table table:

```
root@crappynas:/volume1/@cloudsync/db# sqlite3 
SQLite version 3.10.2 2016-01-20 15:27:19
Enter ".help" for usage hints.
Connected to a transient in-memory database.
Use ".open FILENAME" to reopen on a persistent database.
sqlite> .open config.sqlite
sqlite> SELECT sync_folder,error FROM session_table;
/@Snapshot/@PushServ|0
/ANDROID|-12
/DATA|-12
sqlite> UPDATE session_table SET error = 0 WHERE error = -12;
sqlite> SELECT sync_folder,error FROM session_table;
/@Snapshot/@PushServ|0
/ANDROID|0
/DATA|0
```

- Restart the Cloud Sync app by navigating to the Package Center, click stop:

![](https://blog.wains.be/images/synologysucks3.png)

- Then when it's stopped, click "Run".

Syncing should resume.

Thank you Synology, you never disappoint.