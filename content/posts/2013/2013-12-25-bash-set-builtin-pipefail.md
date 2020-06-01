---
date: 2013-12-25
title: "Bash set builtin- pipefail    "
---

author: admin   
comments: false   

layout: post   


tags: bash, linux


By default, if you pipe commands, the exit status reported will be the one of the last command.

Example:

    ls -l test.txt | mail -s "test" example@example.org
    
If test.txt doesn't exist, the exit status would still be 0 because the mail command was successful.

If you want to change that behavior, you should enable the pipefail builtin:

    > false
    > echo $?
    1
    > true
    > echo $?
    0
    > false | true
    > echo $?
    0
    > set -o pipefail
    > false | true
    > echo $?
    1

