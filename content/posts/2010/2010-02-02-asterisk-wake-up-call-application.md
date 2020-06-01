---
date: 2010-02-02
title: "Asterisk Wake Up call application"
---







categories:
- Asterisk
- VoIP


If you want to be awaken by your Asterisk PBX, here's a simple bit of code to add in your dial plan.

Basically, you would call 9253 followed by the time the phone should ring, for example if you want to wake up at 06:30am you would call 92530630 before going to bed (on your dialpad WAKE0630).

If you want to delete the 0630am alarm, call 6692530630 (on dialpad NOWAKE0630).

How does it all work ?
Asterisk will create a call file and put it under /var/spool/asterisk/outgoing/
How does Asterisk know when to call you ? It will check the timestamp of the call files.
So basically the following script will create the call file, and set its timestamp in the future.

Make sure you enable func_strings.so module, it is required for STRFTIME.


    
    
    ; wake up call programmation
    ; condition, si on encode 06h30, avant minuit il faut faire un touch sur le fichier le jour suivant (voir la condition)
    ; WAKE + hour + minute
    exten => _9253XXXX,1,Answer()
    exten => _9253XXXX,n,Set(wakeuptime=${EXTEN:4:4})
    exten => _9253XXXX,n,Set(today=${STRFTIME(${EPOCH},,%Y%m%d)})
    exten => _9253XXXX,n,Set(tomorrow=${STRFTIME($[${EPOCH} + 86400],,%Y%m%d)})
    exten => _9253XXXX,n,Set(now=${STRFTIME(${EPOCH},,%Y%m%d%H%M)})
    exten => _9253XXXX,n,System(echo "Channel: SIP/${CALLERID(num)}\\nContext: WakeUp\\nExtension: 92531" > /tmp/${UNIQUEID}.call)
    exten => _9253XXXX,n,GotoIf($["${today}${wakeuptime}" < "${now}"]?tomorrow:today)
    exten => _9253XXXX,n(today),NoOp(Scheduling wake up call for ${CALLERID(num)} today at ${wakeuptime} / )
    exten => _9253XXXX,n,System(touch -t ${today}${wakeuptime} /tmp/${UNIQUEID}.call)
    exten => _9253XXXX,n,Goto(move)
    exten => _9253XXXX,n(tomorrow),NoOp(Scheduling wake up call for ${CALLERID(num)} tomorrow at ${wakeuptime} / )
    exten => _9253XXXX,n,System(touch -t ${tomorrow}${wakeuptime} /tmp/${UNIQUEID}.call)
    exten => _9253XXXX,n(move),System(mv /tmp/${UNIQUEID}.call /var/spool/asterisk/outgoing/${wakeuptime}.${UNIQUEID}.call)
    exten => _9253XXXX,n,Wait(1)
    exten => _9253XXXX,n,SayNumber(${wakeuptime})
    exten => _9253XXXX,n,Hangup()
    
    ; NOWAKE + hour + minute : delete a wake up call
    exten => _669253XXXX,1,Answer()
    exten => _669253XXXX,n,Set(wakeuptime=${EXTEN:6:4})
    exten => _669253XXXX,n,NoOp(Deleting alarm set at ${wakeuptime})
    exten => _669253XXXX,n,System(rm -f /var/spool/asterisk/outgoing/${wakeuptime}*)
    exten => _669253XXXX,n,Wait(1)
    exten => _669253XXXX,n,Background(auth-thankyou)
    exten => _669253XXXX,n,Hangup()
    
    [WakeUp]
    ;;; Context used for generating wake up calls only
    exten => 92531,1,Answer()
    exten => 92531,n,Wait(1)
    exten => 92531,n,Background(hello-world)
    exten => 92531,n,Wait(1)
    exten => 92531,n,Hangup()
    



