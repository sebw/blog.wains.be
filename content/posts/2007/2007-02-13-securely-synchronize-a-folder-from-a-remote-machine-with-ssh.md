---
date: 2007-02-13
title: "Securely synchronize a folder from a remote machine with SSH"
---







categories:
- Howto
- Linux
- Security
- SSH


`rsync -vaz -e ssh user@server.domain.be:/home/user/ /home/user/`

Source : user@server.domain.be:/home/user/
Target : /home/user/

-a : archive mode, preserve owner/group and permissions
-v : verbose
-z : compress data during transfer 
-e : the remote shell to use

Output : 
`receiving file list ... done
created directory /home/user
./
28403/
BEXX0014/
28403.xml
error.xml
.
sent 876 bytes  received 1294165 bytes  2590082.00 bytes/sec
total size is 1644823  speedup is 1.27`

