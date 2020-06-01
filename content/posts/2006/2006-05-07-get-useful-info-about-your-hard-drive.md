---
date: 2006-05-07
title: "Get useful info about your hard drive"
---

_(just because I'll forget the command again, [that's actually the whole point of this site :)] I needed to make a post about it :))_

tune2fs -l /dev/XdXX
    
    <code>[root@localhost](1199)# tune2fs -l /dev/hda1
    tune2fs 1.35 (28-Feb-2004)
    Filesystem volume name:   /boot
    Last mounted on:          
    Filesystem UUID:          1ba17048-a79d-444d-a712-a9c90aa97a04
    Filesystem magic number:  0xEF53
    Filesystem revision #:    1 (dynamic)
    Filesystem features:      has_journal ext_attr filetype needs_recovery sparse_super
    Default mount options:    (none)
    Filesystem state:         clean
    Errors behavior:          Continue
    Filesystem OS type:       Linux
    Inode count:              26104
    Block count:              104391
    Reserved block count:     5219
    Free blocks:              88910
    Free inodes:              26058
    First block:              1
    Block size:               1024
    Fragment size:            1024
    Blocks per group:         8192
    Fragments per group:      8192
    Inodes per group:         2008
    Inode blocks per group:   251
    Filesystem created:       Fri Nov 26 20:09:34 2004
    Last mount time:          Sun May  7 10:56:40 2006
    Last write time:          Sun May  7 10:56:40 2006
    Mount count:              428
    Maximum mount count:      -1
    Last checked:             Fri Nov 26 20:09:34 2004
    Check interval:           0 ()
    Reserved blocks uid:      0 (user root)
    Reserved blocks gid:      0 (group root)
    First inode:              11
    Inode size:               128
    Journal inode:            8
    Default directory hash:   tea
    Directory Hash Seed:      47a6d832-2794-4e6e-ac27-2e0d51ab1af0
    Journal backup:           inode blocks</code>




Another command will display even more information (superblocks, etc.) :

`dumpe2fs /dev/hda1`
