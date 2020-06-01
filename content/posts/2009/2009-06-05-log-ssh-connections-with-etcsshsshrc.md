---
date: 2009-06-05
title: "Log SSH connections with /etc/ssh/sshrc"
---







categories:
- Linux
- SSH


Yannick over at [http://blog.uggy.org](http://blog.uggy.org) always comes up with interesting and valuable tips.

Following [his latest post](http://blog.uggy.org/post/post/Execution-de-commande-lors-d-une-connexion-SSH) regarding sshrc, I made my own sshrc script.

Unlike his example, I didn't make SSH email me whenever a connection is made. That would be overkill given the number of connections I can make on a single day.
Instead I'm just logging dates and IP in a log file of its own, which I plan on keeping forever.

My /etc/ssh/sshrc :

`DATE=`date "+%d.%m.%Y %Hh%Mm"`
IP=`echo $SSH_CONNECTION | awk '{print $1}'`
echo "$DATE - $IP" >> /var/log/ssh_connections.log`
