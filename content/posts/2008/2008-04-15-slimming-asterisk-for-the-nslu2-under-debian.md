---
date: 2008-04-15
title: "Slimming Asterisk for the NSLU2 under Debian"
---



categories:  
- Asterisk  
- NSLU2  
- VoIP


**A FULLY WORKING ASTERISK SIP PBX RUNNING WITH ONLY 6 MODULES LOADED ! READ ON...**

This howto is based on Asterisk 1.2 under Debian Etch. Please let me know through the comments if it works for you under other versions (and if it doesn't, please provide the steps to get a working system). Thanks.

**My needs :**

I had to slim Asterisk down to the most minimalistic configuration possible to run on my Linksys NSLU2 (ARM cpu @ 266 MHz, RAM 32 MB).

- SIP calls between my IP phones and softphone
- Incoming/Outgoing calls through a SIP ITSP (ipness.com)
- Echo test to make sure audio is going through in some situation
- I only use the alaw codec, compatible with my IP phones and ITSP, you should avoid transcoding. My DSL connection offers dynamic IP only and gives around 3400 Kbps down/386 Kbps up. I could use the GSM codec, but the ITSP doesn't support it
- No voicemail or other apps

**My setup :**

- The NSLU2 is behind a NAT router
- Home phone on the same subnet as the NSLU2
- Work phone behind NAT
- Softphone used from several places

**Router configuration :**

- Forward port UDP/5060 to UDP/5070 to the NSLU2
- UDP/5060 is used for SIP traffic (signalling)
- UDP/5061 to UDP/5070 is used for RTP traffic (audio)

**Asterisk configuration files :**

Before diving into the configuration..
**IMPORTANT !!!**
If you want to comment something out in the configuration you'll start the line with a semi-colon (";")

The \# sign is used for file inclusions. **# IS NOT USED FOR COMMENTS !!!**

I moved unnecessary files under backup/

	root@nslu2:/etc/asterisk# ls -l
	total 40
	-rw-r--r-- 1 root root  247 2008-04-13 22:03 asterisk.conf
	drwxr-xr-x 2 root root 4096 2008-04-13 22:03 backup
	-rw-r--r-- 1 root root  141 2008-04-13 22:10 extensions.conf
	-rw-r--r-- 1 root root 1660 2008-04-13 23:34 features.conf
	-rw-r--r-- 1 root root 2158 2008-04-13 22:03 logger.conf
	-rw-r--r-- 1 root root  438 2008-04-13 23:37 modules.conf
	-rw-r--r-- 1 root root  395 2008-04-13 22:03 rtp.conf
	-rw-r--r-- 1 root root  299 2008-04-13 22:04 sip.conf
	-rw-r--r-- 1 root root  904 2008-04-13 23:40 custom_extensions.conf
	-rw-r--r-- 1 root root 1349 2008-04-14 09:31 custom_sip.conf

**/etc/asterisk/extensions.conf :**
  
    [general]
    
    static=yes
    writeprotect=no
    autofallthrough=yes
    clearglobalvars=no
    priorityjumping=no
    
    ; let's include custom_extensions.conf into extensions.conf
    #include "/etc/asterisk/custom_extensions.conf"



**/etc/asterisk/features.conf :** default configuration

**/etc/asterisk/logger.conf :** default configuration

**/etc/asterisk/modules.conf :**

Module loading order can be important. E.g. : res_features.so must be loaded before chan_sip.so


    
    [modules]
    autoload=no             ; only load explicitely declared modules
    
    load => app_echo.so     ; echo application
    load => codec_alaw.so   ; alaw codec for voice
    load => pbx_config.so   ; reading and loading configuration
    load => res_features.so ; chan_sip.so dependency
    load => chan_sip.so     ; SIP protocol
    load => app_dial.so     ; Dial application
    
    [global]



**/etc/asterisk/rtp.conf :**

The audio is going through these UDP ports, they must be forwarded to the server in the router.

In this example, the number of ports used by Asterisk is relatively low (I never have more than one call going through the PBX). Set as much as you need, a channel may need up to 2 ports. Also only even ports are actually used.

	[general]
	rtpstart=5061
	rtpend=5070

**/etc/asterisk/sip.conf :**

Having a dynamic IP, I must use externhost with a fresh rate of 60 seconds for resolving the domain.
If you have a static IP, define it in externip=

Localnet must be defined with your network subnet(s), localnet subnets are never passed in the "Via" parameter (can be seen in sip traces).

realm must be a unique ID

The "register =>" line is necessary in order to receive incoming calls from the ITSP.


    
    [general]
    
    context=incoming
    bindport=5060
    bindaddr=0.0.0.0
    
    srvlookup=yes
    
    externhost=mydynamichostname.dyndns.org
    externrefresh=60
    
    localnet=192.168.1.0/255.255.255.0
    
    realm=mydynamichostname.dyndns.org
    
    register => 3221234567:password:login@ipness.net:6060/3221234567
    
    #include "/etc/asterisk/custom_sip.conf"



**/etc/asterisk/custom_extensions.conf :**

custom_extensions.conf is my customized dialplan

- Home phone and softphone can call local phones (1XXX range) 
- Home phone and softphone can call national (e.g. : 02 123 45 67) and international numbers (00 1 910 123 4567) through the ITSP
- Work phone can only call local phones
- Every phone can call the echo application
- Incoming calls make 1001 ring first, then 1002 and finally 1000 (each with a timeout of 30 seconds)


		    
		[globals]
		    
		;;; apps context
		    
		[apps]
		exten => 444,1,Answer()
		exten => 444,n,Wait(1)
		exten => 444,n,Echo
		    
		    
		;;; incoming calls context
		    
		[incoming]
		exten => 3221234567,1,Dial(SIP/1001,30)
		exten => 3221234567,n,Dial(SIP/1002,30)
		exten => 3221234567,n,Dial(SIP/1000,30)
		    
		    
		;;; outgoing calls context
		    
		; local calls only
		[local]
		include => apps
		    
		exten => _1XXX,1,Dial(SIP/${EXTEN})
		exten => _1XXX,n,NoOp(===== DIAL STATUS --> ${DIALSTATUS} =====)
		exten => _1XXX,n,Hangup()
		    
		; national (Belgium, code 32) calls only
		[national]
		include => local
		include => apps
		    
		exten => _0N.,1,Dial(SIP/0032${EXTEN:1}@itsp_ipness)
		exten => _0N.,n,NoOp(===== DIAL STATUS --> ${DIALSTATUS} =====)
		exten => _0N.,n,Hangup()
		    
		; international calls
		[international]
		include => national
		include => local
		include => apps
		    
		exten => _00.,1,Dial(SIP/${EXTEN}@itsp_ipness)
		exten => _00.,n,NoOp(===== DIAL STATUS --> ${DIALSTATUS} =====)
		exten => _00.,n,Hangup()		



**/etc/asterisk/custom_sip.conf :**

custom_sip.conf is my customized accounts file.

canreinvite must be set to no for all sip accounts (unless you have several phones on the server subnet, than you can set to yes).
NAT must be set to yes for any device behind NAT routers.


    
    ; SIP accounts
    
    [1000]
    type=friend
    context=international
    callerid="Softphone" 
    qualify=yes
    secret=1111
    nat=yes
    canreinvite=no
    dtmfmode=rfc2833
    host=dynamic
    call-limit=2
    disallow=all                   
    allow=alaw
    
    [1001]
    type=friend
    context=international
    callerid="Home" 
    qualify=yes
    secret=1111
    nat=no ; IP phone is on the same subnet as the server                   
    canreinvite=no
    dtmfmode=rfc2833
    host=dynamic
    call-limit=2
    disallow=all                  
    allow=alaw
    
    [1002]
    type=friend
    context=local
    callerid="Work" 
    qualify=yes
    secret=1111
    nat=yes
    canreinvite=no
    dtmfmode=rfc2833
    host=dynamic
    call-limit=2
    disallow=all                  
    allow=alaw
    
    [1003]
    type=friend
    context=local
    callerid="Friend" 
    qualify=no
    secret=1111
    nat=yes
    canreinvite=no
    dtmfmode=rfc2833
    host=dynamic
    call-limit=1
    disallow=all                  
    allow=alaw
    
    
    ; ITSP
    
    [itsp_ipness]
    type=peer
    username=login
    secret=password
    fromuser=3221234567
    fromdomain=ipness.net
    host=ipness.net
    port=6060
    nat=yes 
    canreinvite=no
    context=incoming
    insecure=very
    qualify=yes
    disallow=all
    allow=alaw




**Result :**


    
    nslu2*CLI> show modules
    Module                         Description                              Use Count 
    app_echo.so                    Simple Echo Application                  0         
    codec_alaw.so                  A-law Coder/Decoder                      0         
    pbx_config.so                  Text Extension Configuration             0         
    res_features.so                Call Features Resource                   1         
    chan_sip.so                    Session Initiation Protocol (SIP)        0         
    app_dial.so                    Dialing Application                      0       
    
    nslu2*CLI> show applications
        -= Registered Asterisk Applications =-
           AbsoluteTimeout: Set absolute maximum time of call
                    Answer: Answer a channel if ringing
                BackGround: Play a file while awaiting extension
                      Busy: Indicate the Busy condition
                Congestion: Indicate the Congestion condition
                      Dial: Place a call and connect to the current channel
              DigitTimeout: Set maximum timeout between digits
                      Echo: Echo audio read back to the user
                ExecIfTime: Conditional application execution based on the current time
                      Goto: Jump to a particular priority, extension, or context
                    GotoIf: Conditional goto
                GotoIfTime: Conditional Goto based on the current time
                    Hangup: Hang up the calling channel
                 ImportVar: Import a variable from a channel into a new variable
                      NoOp: Do Nothing
                      Park: Park yourself
                ParkedCall: Answer a parked call
                  Progress: Indicate progress
                  ResetCDR: Resets the Call Data Record
           ResponseTimeout: Set maximum timeout awaiting response
                 RetryDial: Place a call, retrying on failure allowing optional exit extension.
                   Ringing: Indicate ringing tone
                  SayAlpha: Say Alpha
                 SayDigits: Say Digits
                 SayNumber: Say Number
               SayPhonetic: Say Phonetic
                       Set: Set channel variable(s) or function value(s)
               SetAccount: Set the CDR Account Code
               SetAMAFlags: Set the AMA Flags
              SetGlobalVar: Set a global variable to a given value
               SetLanguage: Set the channel's preferred language
                    SetVar: Set channel variable(s)
              SIPAddHeader: Add a SIP header to the outbound call
               SIPDtmfMode: Change the dtmfmode for a SIP call
              SIPGetHeader: Get a SIP header from an incoming call
                      Wait: Waits for some time
                 WaitExten: Waits for an extension to be entered
        -= 37 Applications Registered =-
    
    nslu2*CLI> show functions
    Installed Custom Functions:
    --------------------------------------------------------------------------------
    CHECKSIPDOMAIN        CHECKSIPDOMAIN()          Checks if domain is a local domain
    SIPCHANINFO           SIPCHANINFO(item)                    Gets the specified SIP parameter from the current channel
    SIPPEER               SIPPEER([:item])           Gets SIP peer information
    SIP_HEADER            SIP_HEADER()                   Gets the specified SIP header
    4 custom functions installed.



Asterisk is using around 12 MB of memory when idle.
