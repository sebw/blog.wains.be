---
date: 2007-02-18
title: "Reading documentation under RHEL"
---







categories:
- RHCE


Several ways to get help quickly.
Need to get used to finding the missing info as fast as possible (I'm already pretty fast usually, but need to improve if possible)..



`# whatis named
named                (8)  - Internet domain name server
named-checkconf      (8)  - named configuration file syntax checking tool
named-checkzone      (8)  - zone file validity checking tool
named.conf [named]   (5)  - configuration file for named`

`# apropos named
Opcode               (3pm)  - Disable named opcodes when compiling perl code
XML::DOM::NamedNodeMap (3pm)  - A hash table interface for XML::DOM
charnames            (3pm)  - define character names for eN{named} string literal escapes
fifo                 (4)  - first-in first-out special file, named pipe
mkfifo               (1)  - make FIFOs (named pipes)
mkfifo               (3)  - make a FIFO special file (a named pipe)
named                (8)  - Internet domain name server
named-checkconf      (8)  - named configuration file syntax checking tool
named-checkzone      (8)  - zone file validity checking tool
named.conf [named]   (5)  - configuration file for named
named_selinux        (8)  - Security Enhanced Linux Policy for the Internet Name server (named) daemon
sched_rr_get_interval (2)  - get the SCHED_RR interval for the named process`

`man -k named` returns more results than the previous command, search for the string in every man pages available

`# man 8 named`

`# info named`

`Go under /usr/share/doc/package_name`

`command --help or command -h`

Man pages sections :
`man 1 user applications
man 2 system calls
man 3 library calls
man 4 special files
man 5 file format
man 6 games
man 7 misc
man 8 system administration`

