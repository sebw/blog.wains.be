---
date: 2015-04-17
title: "Adding a disk to a Synology Hybrid RAID (SHR) (with no data protection) to make a RAID1 array"
---
Date: 2015-04-17

I bought a Synology DS214j a couple of months ago.

I bought only one 2TB disk in the first place. My plan was to buy a second disk a couple of months later so I would have a RAID1 array made of two disks with different mileage and probably different life expectancy. I set it up using the recommended option: "Synology Hybrid RAID (SHR)".

Personal opinion: I always believed that starting a RAID1 with disks from the same batch and more or less same production date is a recipe for disaster. They will start doing their job at the same time, will wear at the same rate and probably die around the same time. It is known that reconstructing a faulty RAID1 array can cause the second drive to die because of the long process of reconstruction and high read operations.

So back to the point of the article, adding a second disk to a SHR array...

For some reason, Synology made things confusing because of the vocabulary used.

You should stop the NAS, insert the drive. Start the NAS again, then go in "Storage Manager", where you would find a "Volume 1". Click on "Manage", and then you are presented with two options saying "Expand".

To me, "expanding" something is increasing its size. But by Synology dictionary, if you have a Synology Hybrid RAID (without data protection), and add a second disk to it, it will not expand the volume but will mirror the first drive.

(Merriam Webster tends to agree with me)[[http://www.merriam-webster.com/dictionary/expand](http://www.merriam-webster.com/dictionary/expand)].

Apparently, if you have a 4 bay NAS, adding a second drive would actually start expanding the size of the volume. I cannot confirm this as I only have a 2 bay unit.

Conclusion: adding a second drive and "expanding" an existing "Synology Hybrid RAID (with no data protection)" actually results in "Synology Hybrid RAID (SHR) (with data protection of 1 disk fault-tolerance)". Don't worry, it won't turn into a RAID-0.

Thanks for making simple things confusing, Synology!
