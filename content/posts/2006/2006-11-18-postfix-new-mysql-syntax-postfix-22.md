---
date: 2006-11-18
title: "Postfix new MySQL syntax > Postfix 2.2"
---







categories:
- Linux
- Postfix
- SQL


I found out just yesterday that Postfix uses a new syntax for SQL lookups.
It seems like my old syntax was still compatible with Postfix 2.2.8.
I guess it will be backward compatible for a while.



**For Postfix 2.2 and later :**
`user = someone
password = password
dbname = database
query = SELECT alias FROM alias_table WHERE alias='%s'`

**For Postfix releases prior to 2.2 :**
`user = someone
password = password
dbname = database
select_field = alias
table = alias_table
where_field = alias`

Meaning of the '%' :
`%s     This is replaced  by  the  input  key.   SQL
                     quoting  is used to make sure that the input
                     key does not add unexpected  metacharacters.`

**A word on backward compatibility :**
`As of Postfix  2.2  this  interface  is  obsolete,  it  is
       replaced  by  the  more  general query interface described
       above.  If the query  parameter  is  defined,  the  legacy
       parameters  are  ignored. Please migrate to the new inter-
       face as the legacy interface may be removed  in  a  future
       release.`

[http://www.postfix.org/mysql_table.5.html](http://www.postfix.org/mysql_table.5.html)
