---
date: 2006-05-03
title: "Issues with Iptables and frox (ftp transparent proxy)"
---

If you set up a transparent ftp proxy using frox (as described here : [http://blog.wains.be/?p=46](http://blog.wains.be/?p=46)) with iptables along on your linux gateway, you'll probably stumble upon some issues...



Let's say you run frox on port 2121 and redirect any request made on port 21 to frox, you should use this kind of rule : 

`iptables -A PREROUTING -s 10.0.0.0/24 -p tcp -m tcp --dport 21 -j REDIRECT --to-ports 2121`

You should first make sure you've set these rules for frox :


    
    <code>iptables -A INPUT -m state --state RELATED,ESTABLISHED -j ACCEPT
    iptables -A INPUT -p tcp -m tcp --dport 2121 --syn -j ACCEPT</code>



The first line will allow any input trafic in relation with a connection made by the server, the second line would simply allow the connection to the frox daemon..

Using this scheme, you should be able to connect to a FTP server through frox, somehow you should not see files nor directories (ls command) because the passive connection is not allowed. Uh oh.. why not ?

You need to enable connection tracking for FTP on port 21 and 2121 :
`/sbin/modprobe ip_conntrack_ftp ports=21,2121`

Now, connecting to a FTP server through frox should work :-)

If you run a Red Hat based system, running "service iptables restart" will get rid of the ip_conntrack_ftp module, you should tweak the init file (/etc/init.d/iptables) to force the module like this :


    
    <code>start() {
        # Do not start if there is no config file.
        [ -f "$IPTABLES_DATA" ] || return 1
    
        echo -n $"Applying $IPTABLES firewall rules: "
    
        OPT=
        [ "x$IPTABLES_SAVE_COUNTER" = "xyes" ] && OPT="-c"
    
        $IPTABLES-restore $OPT $IPTABLES_DATA
        if [ $? -eq 0 ]; then
            success; echo
        else
            failure; echo; return 1
        fi
    
        if [ -n "$IPTABLES_MODULES" ]; then
            echo -n $"Loading additional $IPTABLES modules: "
            ret=0
            for mod in $IPTABLES_MODULES; do
                echo -n "$mod "
                modprobe $mod > /dev/null 2>&1
                let ret+=$?;
            done
            [ $ret -eq 0 ] && success || failure
            echo
        fi
    
        /sbin/modprobe ip_conntrack_ftp ports=21,2121
    
        touch $VAR_SUBSYS_IPTABLES
    }</code>



