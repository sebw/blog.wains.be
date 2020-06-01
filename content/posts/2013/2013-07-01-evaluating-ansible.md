---
date: 2013-07-01
title: "Evaluating Ansible"
---







categories:
- Automation
- Linux


I'm currently actively working on Salt, I actually have a dozen production servers at work, running critical services through it. I commit new things into the production branch every couple of days.
Since Ansible seems to get all the rage (at least convinced a couple of fellow FOSS friends, Fabian, Serge, etc), I decided to give it a try and compare the two solutions.

I'm detailing here how you can start working with Ansible in about 3 minutes. I'm limiting this post to remote execution and won't cover playbooks. That's really for my own future reference.

By default, it uses Paramiko as transport, but in this example, I'm using regular SSH. I'm also working with two "minions" (Salt terms).


    
    master# apt-get -y install python-yaml python-jinja2
    master# ssh-keygen
    master# ssh-copy-id -i ansible root@host1
    master# ssh-copy-id -i ansible root@host2
    master# git clone git://github.com/ansible/ansible.git



In your .bashrc add :

    
    source /root/ansible/hacking/env-setup
    export ANSIBLE_HOSTS=/root/ansible/hosts
    export ANSIBLE_TRANSPORT=ssh



Edit /root/ansible/hosts and add :

    
    host1
    host2



Now let's test a simple command :


    
    master## ansible all -m ping 
    host02 | success >> {
        "changed": false, 
        "ping": "pong"
    host01 | success >> {
        "changed": false, 
        "ping": "pong"
    }
    
    master# ansible all -m command -a uptime
    host02 | success | rc=0 >>
     20:27:33 up  1:50,  2 users,  load average: 0.00, 0.00, 0.00
    
    host01 | success | rc=0 >>
     20:27:33 up  1:50,  2 users,  load average: 0.00, 0.00, 0.00
    




