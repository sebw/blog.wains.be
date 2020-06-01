---
date: 2007-01-25
title: "New (neat) postfix 2.3 feature - custom bounce messages"
---







categories:
- Postfix


Bounce messages sent by the postfix daemon used to be hardcoded until version 2.3.
This is actually great news for non-english speaking or not email-litterate users.



This is an excerpt of my postfix config :
`maximal_queue_lifetime = 1d
delay_warning_time = 1h
bounce_template_file = /etc/postfix/bounce.cf`

It is important to know the $delay_warning_time and $maximal_queue_lifetime time values.

Let's start by copying the default bounce template file : 
`cp /etc/postfix/bounce.cf.default /etc/postfix/bounce.cf`

Edit /etc/postfix/bounce.cf to your needs, here is an example (excerpt) :


    
    <code>Charset: us-ascii
    From: MAILER-DAEMON (Mail Delivery System)
    Subject: Delayed Mail (still being retried)
    Postmaster-Subject: Postmaster Warning: Delayed Mail
    
    Bonjour, je suis votre serveur email.
    Mon nom est $myhostname.
    
    ##############################################
    #  CECI EST JUSTE UN MESSAGE D'INFORMATION.  #
    # VOUS NE DEVEZ PAS RENVOYER VOTRE MESSAGE ! #
    ##############################################
    
    Un de vos messages n'a pas pu etre delivre pendant $delay_warning_time_hours heure(s).
    Le systeme va reesayer jusqu'au moment ou le message sera vieux de $maximal_queue_lifetime_days jour(s).
    
    Si le probleme persiste, verifiez la validite de l'adresse email.
    
    Pour plus d'assistance, contactez un administrateur du systeme.
    
    Si vous contactez l'administrateur, veuillez lui faire parvenir ce qui suit.
    Vous pouvez effacer la partie personnelle de votre message.
    
                       Merci,
                       Votre serveur email
    EOF</code>



You must pay close attention to the $delay_warning_time_* and $maximal_queue_lifetime_* values in the template file.
Look what Postfix authors have to say about these values :

`# The delay template is used when mail is delayed. Note a neat trick:
#

It means, if I had like a warning delay time of 30 minutes, I'd need to replace :
"Un de vos messages n'a pas pu etre delivre pendant $delay_warning_time_hours heure(s)."
By
"Un de vos messages n'a pas pu etre delivre pendant $delay_warning_time_minutes minutes(s)."

The same applies to $maximal_queue_lifetime_days.. you get the idea

If postfix finds a mismatch between the values stored in the postfix config and the template config, you'd get a warning message in the logs.

You can test your bounce template by using :
postconf -b bounce.cf


    
    <code>Bonjour, je suis votre serveur email.
    Mon nom est mx.domain.be.
    
    ##############################################
    #  CECI EST JUSTE UN MESSAGE D'INFORMATION.  #
    # VOUS NE DEVEZ PAS RENVOYER VOTRE MESSAGE ! #
    ##############################################
    
    Un de vos messages n'a pas pu etre delivre pendant 1 heure(s).
    Le systeme va reesayer jusqu'au moment ou le message sera vieux de 1 jour(s).
    
    Si le probleme persiste, verifiez la validite de l'adresse email.
    
    Pour plus d'assistance, contactez un administrateur du systeme.
    
    Si vous contactez l'administrateur, veuillez lui faire parvenir ce qui suit.
    Vous pouvez effacer la partie personnelle de votre message.
    
                       Merci,
                       Votre serveur email
    EOF</code>



More info : [http://www.postfix.org/bounce.5.html](http://www.postfix.org/bounce.5.html)
