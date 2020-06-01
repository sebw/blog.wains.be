---
date: 2008-01-10
title: "BIND - the $GENERATE Directive"
---







categories:
- DNS
- Linux


From : [http://www.bind9.net/manual/bind/9.3.2/Bv9ARM.ch06.html#id2566761](http://www.bind9.net/manual/bind/9.3.2/Bv9ARM.ch06.html#id2566761)

BIND Master File Extension: the $GENERATE Directive

Syntax: $GENERATE range lhs [ttl] [class] type rhs [ comment ]

$GENERATE is used to create a series of resource records that only differ from each other by an iterator. $GENERATE can be used to easily generate the sets of records required to support sub /24 reverse delegations described in RFC 2317: Classless IN-ADDR.ARPA delegation.

`$ORIGIN 0.0.192.IN-ADDR.ARPA.
$GENERATE 1-2 0 NS SERVER$.EXAMPLE.
$GENERATE 1-127 $ CNAME $.0`

is equivalent to

`0.0.0.192.IN-ADDR.ARPA NS SERVER1.EXAMPLE.
0.0.0.192.IN-ADDR.ARPA. NS SERVER2.EXAMPLE.
1.0.0.192.IN-ADDR.ARPA. CNAME 1.0.0.0.192.IN-ADDR.ARPA.
2.0.0.192.IN-ADDR.ARPA. CNAME 2.0.0.0.192.IN-ADDR.ARPA.
...
127.0.0.192.IN-ADDR.ARPA. CNAME 127.0.0.0.192.IN-ADDR.ARPA.`


More about the options on the site mentionned above

