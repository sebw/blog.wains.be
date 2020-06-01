---
date: 2013-04-05
title: "Salt Stack, a (serious) alternative to Puppet"
---



I couldn't write it better : see [http://www.lecloud.net/post/29325359938/salt-to-the-rescue](http://www.lecloud.net/post/29325359938/salt-to-the-rescue)

So basically, Salt is a configuration management system (à la Puppet) and allows remote execution (à la Rundeck).

First thing first, it is very easy to install. I know Puppet now offers repositories and it's probably as easy, but Salt is just a package with a couple of dependencies. Actually to achieve the same tasks you have to have Puppet and Mcollective, which are still two distinct products. Salt does the job from one package.

Then, it's based on Python, YAML and Jinja.

The documentation is very good, and the community very active (got answers within 30 seconds in #salt on Freenode).

The last thing I like : minions keep a constant connection to the master. You can push  changes to minions immediately. I attended the Puppet Fundamentals training late last year and asked about a "push" of changes instead of a "pull". It seems like there's a solution but the trainer couldn't get it working.

One thing they could improve is the frontpage of their site. When you go to http://www.saltstack.org you are redirected to http://saltstack.com/community.html instead of http://saltstack.com/about.html which explains what the product does.

Installation (RHEL):

Server:

    yum --enablerepo=epel install salt-master

Edit /etc/salt/master:

    
    file_roots:
      base:
        - /srv/salt
      dev:
        - /srv/salt/dev
      prd:
      - /srv/salt/prd
    
    pillar_roots:
      base:
      - /srv/pillar


Restart the service:

    service salt-master restart

Client:
    
    yum --enablerepo install salt-minion


Edit /etc/salt/minion:
    
    master: your.master.server.example.org


Restart the service:

    service salt-minion restart


Now you should see a pending key with "salt-key". See "salt-key -h" for more info.

Basically, modules are called "states".

Pillars are kind of variables you can use in your files.

This is the content of /srv on my master :

    .
    ./pillar
    ./salt
    ./salt/prd
    ./salt/dev
    ./salt/sandbox
    ./salt/sandbox/motd
    ./salt/sandbox/ntpd
    ./salt/sandbox/apache
    ./salt/sandbox/sshd
    ./salt/sandbox/snmpd
    ./salt/acc
    ./salt/common
    ./salt/common/groups
    ./salt/common/users
    ./salt/common/packages
    ./salt/common/files
    ./salt/common/sudo

I have 5 environments :

    - sandbox : where I develop states
    - dev : development servers
    - acc : staging servers
    - prd : production servers
    - common : states common to all environments (sshd, snmpd, etc.)

If you look in /etc/salt/master, you'll see there's a "base" environment. This is where your top.sls (the key component of your salt architecture) will reside :

    
    # cat /srv/salt/top.sls
    common:
      '*':
        - packages
        - users
        - groups
        - files
        - sudo
    
    dev:
      '*.dev.example.org':
        - dev
    
    acc:
      '*.acc.example.org':
        - acc
    
    prd:
      '*.prd.example.org':
        - prd
    
    sandbox:
      'salt-client*':
        - motd
        - apache
        - ntpd
        - snmpd
        - sshd


You can see I started working with Salt only a couple of days ago. My states are still in the "sandbox" environment.

How you can push states to minions :

    salt '*' state.highstate

/srv/pillar/top.sls

    
    base:
      '*':
        - convention-os


/srv/pillar/convention-os.sls

    
    convention-os:
      pkg:
        {% if grains['os_family'] == 'RedHat' %}
          apache: httpd
          snmpd: net-snmp
          vim: vim-enhanced
        {% elif grains['os_family'] == 'Debian' %}
          apache: apache2
          snmpd: snmpd
          vim: vim
        {% endif %}
      service:
        {% if grains['os_family'] == 'RedHat' %}
          apache: httpd
          ntpd: ntpd
          sshd: sshd
        {% elif grains['os_family'] == 'Debian' %}
          apache: apache2
          ntpd: ntp
          sshd: ssh
        {% endif %}


States can be named this way /srv/salt/env/motd.sls or /srv/salt/env/motd/init.sls
I tend to prefer the later.

Here's an example of state calling pillars :

    
    apache:
      pkg:
        - installed
        - name: {{ pillar['convention-os']['pkg']['apache'] }}
      service:
        - running
        - name: {{ pillar['convention-os']['service']['apache'] }}


This is a pretty rough post, sorry about that. I just wanted to spread the word about Salt and hope you'll consider joining in.

Documentation:

- Online : [http://docs.saltstack.com/](http://docs.saltstack.com/)
- PDF : [http://media.readthedocs.org/pdf/salt/latest/salt.pdf](http://media.readthedocs.org/pdf/salt/latest/salt.pdf)


