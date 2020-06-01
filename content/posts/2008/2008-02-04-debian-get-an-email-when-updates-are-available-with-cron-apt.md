---
date: 2008-02-04
title: "Debian - get an email when updates are available with cron-apt"
---







categories:
- Debian/Ubuntu
- Linux


Install cron-apt :
`# apt-get install cron-apt`

Edit its config files :
`# vim /etc/cron-apt/config`

You basically just need this in the config :
`MAILTO="user@example.com"
MAILON="upgrade"`

cron-apt runs every night at 4AM (see /etc/cron.d/cron-apt)

Source : [http://www.debuntu.org/how-to-email-notification-upon-available-package-updates-with-cron-apt](http://www.debuntu.org/how-to-email-notification-upon-available-package-updates-with-cron-apt)
