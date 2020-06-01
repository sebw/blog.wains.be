---
date: 2010-01-20
title: "Asterisk - XMPP notifications for missed calls"
---







categories:
- Asterisk
- Howto
- VoIP


Tester under Asterisk 1.4.21.

If someone calls and hangs up before leaving a voicemail (that means while the phone is ringing or during voicemail message), Asterisk will send a "missed call" notification by XMPP/Jabber.

**/etc/asterisk/jabber.conf :**

This file contains the info for Asterisk to connect to the Jabber server.
When restarting Asterisk, it will connect automatically and add contacts specified under buddy fields to its contact list.
You can specify several accounts in this file, and use different accounts for different notifications, for example.
From Asterisk CLI, there's a command "jabber test" which would display the status of your contacts, this command only works with the account specified in the [asterisk] context.


    
    <code>[general]
    debug=no
    autoprune=no ; this is important to set this to no, if set to yes and you don't specify any "buddy=" it will delete contacts from your buddy list
    autoregister=yes        
    
    [asterisk] ; must be called "asterisk" if we want the command "jabber test" to work
    type=client
    serverhost=jabber.example.org
    username=pbx@example.org/pbx
    secret=PASSWORD
    port=5222       
    usetls=yes
    usesasl=yes
    buddy=youraccountreceivingnotifications@gmail.com
    buddy=anotheraccountthatmayreceivenotifications@gmail.com
    statusmessage=Asterisk XMPP bot. Don't talk to me, your messages would be lost forever.
    timeout=100
    
    [account2]
    type=client
    serverhost=jabber.example.org
    username=anotheraccount@example.org/pbx
    secret=PASSWORD
    port=5222       
    usetls=yes
    usesasl=yes
    buddy=someoneelse@gmail.com
    statusmessage=Asterisk XMPP bot. Don't talk to me, your messages would be lost forever.
    timeout=100</code>




**/etc/asterisk/extensions.conf :**

When you pass the option "g" to the Dial() command, when the user hangs up, Asterisk exits the Dial() command and continue by jumping to the special "h" extension in the current context. From the console you should expect something like "Spawn extension (macro-DialVM, h, 5) exited" when the whole thing has been processed.

If you don't specify the option, Asterisk will exit at the Dial() command. You would then see "Spawn extension (macro-DialVM, s, 1) exited" right after the user hangs up.

In this bit of dialplan, we enable XMPP notifications for calls made on extension 555 in the context named Local.
Dialing is made through a macro called macro-DialVM.
XMPP notifications are sent through macro-XMPPSend.


    
    <code>[macro-XMPPSend]
    ;;; Description : sends XMPP messages only if user is online and not away
    ;;; ARG1 = Jabber ID to be notified
    ;;; ARG2 = Message
    ;;; Jabberstatus and Jabbersend take the account name to user to send notifications as first argument ([asterisk] or [account2] under jabber.conf)
    
    ; getting user's status
    ; Status can be in order : 1)Online, 2)Chatty, 3)Away, 4)XAway, 5)DND, 6)Offline, 7)Not in roster
    exten => s,1,Jabberstatus(asterisk,${ARG1},STATUS)
    ; If the value of STATUS is anything under 3 (or Away), in other words if user is Online or in Chatty mode
    exten => s,n,GotoIf($["${STATUS}"  s,n(available),NoOp(${ARG1} is available)
    exten => s,n,Jabbersend(asterisk,${ARG1},${ARG2})
    exten => s,n,MacroExit()
    ; if the user is not available, we don't send anything
    exten => s,n(unavailable),NoOp(${ARG1} is not available in at least one location.. Do not send notification)
    exten => s,n,MacroExit()
    
    [macro-DialVM]
    ;;; Description : dials (option g enabled, jumps to h extension) and goes to voicemail if reaching timeout.
    ;;; ARG1 = extension to be dialed
    ;;; ARG2 = timeout
    ;;; XMPP notification if call missed
    
    exten => s,1,Dial(SIP/${ARG1},${ARG2},wg)
    exten => s,n,Voicemail(${ARG1})
    
    ; option g must be passed to Dial() to jump to h extension or it would spawn at "macro-DialVM,s,1"
    ; if user doesn't leave a voicemail, VMSTATUS = FAILED
    ; if user hangs up before reaching the voicemail app, DIALSTATUS = CANCEL
    exten => h,1,NoOp(Did user hang up before leaving a voicemail ?)
    exten => h,n,GotoIf($["${VMSTATUS}" = "FAILED"]?missed:nextcheck)
    exten => h,n(nextcheck),GotoIf($["${DIALSTATUS}" = "CANCEL"]?missed:notmissed)
    exten => h,n(missed),Macro(XMPPSend,youraccountreceivingnotifications@gmail.com,${CALLERID(all)} just tried to call ${ARG1})
    exten => h,n(notmissed),Hangup()
    
    
    [Local]
    ;;; Description : Local calls context
    
    ; My extension is 555, with a timeout of 30 seconds
    exten => 555,1,Macro(DialVM,${EXTEN},30)</code>
