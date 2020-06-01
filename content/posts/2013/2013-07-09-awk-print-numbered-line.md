---
date: 2013-07-09
title: "Print every X line in a file"
---
Date: 2013-07-09

In this example, I'll print line 20, 40, 60, 80, etc. of data.txt:

    awk '(NR%20==0)' data.txt

NF The number of fields in the current input record.
NR The total number of input records seen so far.
