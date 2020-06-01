---
date: 2010-01-05
title: "Nagios + SMS notifications with Gammu and Siemens MC35i"
---







categories:
- High-Availability
- Howto


What you need :

- a working Nagios
- a GSM modem (this has been tested with a Siemens MC35i) or a cellphone with modem capabilities supported by Linux (I don't have that)
- Gammu


**Set up the modem :**

Connect the GSM modem to the Nagios machine through the serial port and make sure it receives signal from the carrier (LED blinking slowly means everything is OK, if not it blinks fast)


**Install and configure Gammu :**

`# apt-get install gammu`

The modem should be accessible through /dev/ttyS0

`# vim /etc/gammurc`

`[gammu]
port = /dev/ttyS0
connection = at19200
startinfo = no
name = Siemens
synchronizetime = no
use_locking = no`

Test the modem


    
    <code># gammu --identify
    Manufacturer         : Siemens
    Model                : unknown (MC35i)
    Firmware             : REVISION 02.00
    IMEI                 : 3515590XXXXXX
    Product code         : MC35i
    SIM IMSI             : 2061021XXXXXX</code>



`# gammu --networkinfo
Network state        : home network
Network              : 206 10 (Mobistar, Belgium), LAC 6B08, CID 19EC
Name in phone        : "B mobistar"`


    
    <code># gammu --monitor 1
    Press Ctrl+C to break...
    Entering monitor mode...
    
    Enabling info about incoming SMS    : No error.
    Enabling info about incoming CB     : No error.
    Enabling info about calls           : No error.
    Enabling info about USSD            : No error.
    SIM phonebook        :  59 used,  41 free
    Dialled numbers      :   7 used,   3 free
    Received numbers     :   0 used,  10 free
    Missed numbers       :   0 used,  10 free
    Own numbers          :   1 used,   2 free
    Phone phonebook      :   0 used, 250 free
    Leaving monitor mode...</code>



Everything looks fine !

Before we continue, let's check permissions on /dev/ttyS0

`# ls -l /dev/ttyS0
crw-rw---- 1 root dialout 4, 64 jan  5 16:14 /dev/ttyS0`

Nagios is usually running as user "nagios", so any notification command would be executed as "nagios". We can see user nagios wouldn't have permissions to access devices connected to /dev/ttyS0.

We'll apply the SUID bit (set-UID) on the command in charge of sending SMS notifications, this will execute gammu on behalf of user root.

`# chmod 4755 /usr/bin/gammu`

We could put nagios in the dialout group as well. I didn't as Nagios is running on an isolated box in the LAN.
If someone is able to mess up with gammu executed as SUID on that box, that would mean I'm already in a lot of troubles anyway :-)


Before we continue, we'll try to send a SMS to the belgian cell number 0475123456

`# echo "test" | gammu --sendsms TEXT 0475123456
If you want break, press Ctrl+C...
Sending SMS 1/1....waiting for network answer..OK, message reference=181`

It worked.


Now, an example of Nagios config for SMS notifications :


    
    <code>define command{
            command_name    host-notify-by-sms
            command_line    /usr/bin/printf "%b" "NAGIOS / Host: "$HOSTNAME$" / State: $HOSTSTATE$ / Info:$HOSTOUTPUT$ / Date:$SHORTDATETIME$" | /usr/bin/gammu --sendsms TEXT $CONTACTPAGER$
            }
    
    define command{
            command_name    notify-by-sms
            command_line    /usr/bin/printf "%b" "NAGIOS / Host: "$HOSTALIAS$" / State: $SERVICESTATE$ / Info:$SERVICEOUTPUT$ / Date:$SHORTDATETIME$" | /usr/bin/gammu --sendsms TEXT $CONTACTPAGER$
            }</code>





    
    <code>define contact{
            contact_name                    email
            alias                           email
            service_notification_period     24x7
            host_notification_period        24x7
            service_notification_options    c,r
            host_notification_options       d,r
            service_notification_commands   notify-by-email
            host_notification_commands      host-notify-by-email
            email                           nagios@example.org
     }
    
    define contact{
            contact_name                    sms
            alias                           sms
            service_notification_period     smshours
            host_notification_period        smshours
            service_notification_options    c,r
            host_notification_options       d,r
            service_notification_commands   notify-by-sms
            host_notification_commands      host-notify-by-sms
            pager                           0475123456
     }
    
    define contactgroup{
            contactgroup_name       admins
            alias                   Nagios Administrators
            members                 email, sms
     }</code>





    
    <code>define timeperiod{
            timeperiod_name smshours
            alias           SMS Hours
            sunday          00:00-24:00
            monday          00:00-07:00,18:00-24:00
            tuesday         00:00-07:00,18:00-24:00
            wednesday       00:00-07:00,18:00-24:00
            thursday        00:00-07:00,18:00-24:00
            friday          00:00-07:00,18:00-24:00
            saturday        00:00-24:00
    }</code>
