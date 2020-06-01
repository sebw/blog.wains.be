---
date: 2007-08-03
title: "MySQL - selecting duplicate entries"
---







categories:
- SQL


This is the correct query :
`SELECT field from `table` GROUP BY field HAVING COUNT(field) > 1;`

Here's an example where I have two duplicate entries (test and test3).


    
    <code># let's see the records
    mysql> SELECT field from `table`;
    +-------+
    | field |
    +-------+
    | test  | 
    | test  | 
    | test2 | 
    | test3 | 
    | test3 | 
    +-------+
    5 rows in set (0.00 sec)
    
    # THIS IS NOT WHAT WE WANT !!! THIS WILL ONLY RETURN THE FIRST ENTRY (duplicate or not)
    mysql> SELECT field from `table` HAVING COUNT(field) > 1;
    +-------+
    | field |
    +-------+
    | test  | 
    +-------+
    1 row in set (0.00 sec)
    
    <strong># This is what we want to do
    mysql> SELECT field from `table` GROUP BY field HAVING COUNT(field) > 1;
    +-------+
    | field |
    +-------+
    | test  | 
    | test3 | 
    +-------+
    2 rows in set (0.00 sec)</strong></code>



Thanks again to Marcel the [Admin Blogger](http://www.adminblogger.de/blog/) for some useful feedback on this article
