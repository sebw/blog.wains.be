---
date: 2016-11-04
title: "MySQL 5.6 quick and dirty replication"
---

```
Some assumptions:  
master> = mysql commands on master  
slave> = mysql commands on slave  
root@master = bash commands on master  
root@slave = bash commands on slave  
/var/lib/mysql/master01-bin.* = name of the replication logs, depending on your config
```

master> STOP MASTERslave> STOP SLAVEroot@master: service mysqld stop
root@slave: service mysqld stop
Take a snapshot of servers if possibleroot@master: service mysqld startmaster> RESET MASTER (this deletes binary files under /var/lib/mysql/master01-bin.XXXXXX and reset the position, be careful this might lock the DB for a couple of seconds or minutes)
root@master: service mysqld stopRsync /var/lib/mysql from master to slaveOn the slave remove:- /var/lib/mysql/auto.cnf (contains node UUID, file will be regenerated upon mysql start)- /var/lib/mysql/master01-bin.*- /var/lib/mysql/mysqld-relay.*Start both master and slave
master> START MASTERslave> CHANGE MASTER TO MASTER_HOST="172.24.252.137", MASTER_USER="replication_account", MASTER_PASSWORD="replication_password", MASTER_PORT=3306, MASTER_AUTO_POSITION = 1;slave> START SLAVEslave> SHOW SLAVE STATUS\GNow you should see this:             Slave_IO_Running: Yes            Slave_SQL_Running: YesNo error should be reported.*_Gtid_Set info should match closely between master and slave:```mysql> show master status\G*************************** 1. row ***************************             File: master01-bin.000003         Position: 41308364     Binlog_Do_DB: Binlog_Ignore_DB:Executed_Gtid_Set: 0fa3a859-0c73-11e6-a40a-005056bb5198:1-1006431 row in set (0,00 sec)
``````mysql> show slave status\G[...]           Retrieved_Gtid_Set: 0fa3a859-0c73-11e6-a40a-005056bb5198:1-100671            Executed_Gtid_Set: 0fa3a859-0c73-11e6-a40a-005056bb5198:1-100671[...]
```
