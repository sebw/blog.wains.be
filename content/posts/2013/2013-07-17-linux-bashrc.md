---
date: 2013-07-17
title: "My bashrc"
---
Date: 2013-07-17
Tags: Linux

Not so much but it does the job.

    shopt -s histappend
    export HISTTIMEFORMAT='%F @ %T - '
    export HISTCONTROL=erasedups:ignorespace
    export HISTIGNORE="pwd:[bf]g:jobs:j:h:history:exit:reboot:restart:init *"
    export HISTSIZE=5000
    export HISTFILESIZE=5000
    export VISUAL=vim
