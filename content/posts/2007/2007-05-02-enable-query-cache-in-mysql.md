---
date: 2007-05-02
title: "How to enable query caching in MySQL"
---







categories:
- Linux


Query caching should improve MySQL performances..



**Edit /etc/my.cnf :**

Under the mysqld section add the following lines

`[mysqld]
query-cache-type = 1
query-cache-size = 10M`

**Restart MySQL :**
`# service mysqld restart`

**Make sure query caching is enabled :**


    
    <code>[root@local](1008)# mysql -p
    Enter password: 
    Welcome to the MySQL monitor.  Commands end with ; or g.
    Your MySQL connection id is 9 to server version: 4.1.20
    
    Type 'help;' or 'h' for help. Type 'c' to clear the buffer.
    
    mysql> SHOW VARIABLES LIKE '%query_cache%';
    +------------------------------+----------+
    | Variable_name                | Value    |
    +------------------------------+----------+
    | have_query_cache             | YES      |
    | query_cache_limit            | 1048576  |
    | query_cache_min_res_unit     | 4096     |
    | query_cache_size             | 10485760 |
    | query_cache_type             | ON       |
    | query_cache_wlock_invalidate | OFF      |
    +------------------------------+----------+
    6 rows in set (0.00 sec)</code>



