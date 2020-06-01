---
date: 2007-11-23
title: "Cut MP3 under Linux Ubuntu"
---







categories:
- Linux


If you want to cut MP3 files under Ubuntu, install the following package from the repositories

`sudo apt-get install poc-streamer`

It contains a utility **mp3cut**

**Syntax :**

mp3cut [ -o outputfile ] [ -T title ] [ -A artist ] [ -N album-name ] [ -t [hh:]mm:ss[+ms]-[hh:]mm:ss[+ms] ] mp3file [[ -t ...  ] mp3file1 ...]


The following examples cuts a 120 minute MP3 in 2 parts where the first file will be 70 min long :

`$ mp3cut -o output1.mp3 -t 00:00+000-01:10:00+000 input.mp3 
Writing to output1.mp3
Extracting 00:00:00+000-01:10:00+000 from input.mp3
output1.mp3 written
.
$ mp3cut -o output2.mp3 -t 01:10:00+000 input.mp3 
Writing to output2.mp3
Extracting 01:10:00+000-00:00:00+000 from input.mp3
output2.mp3 written`



**Edit Oct 3 2008**

If you have a cue file and one big mp3 you need to split, there's **mp3splt**


    
    <code>$ mp3splt -c underworld.cue underworld.mp3 
    Mp3Splt 2.1 (2004/Sep/28) by Matteo Trotta 
    THIS SOFTWARE COMES WITH ABSOLUTELY NO WARRANTY! USE AT YOUR OWN RISK!
    Warning: found Xing or Info header, mp3 may be VBR. Switching to Frame mode...
    MPEG 1 Layer 3 - 44100 Hz - Joint Stereo - FRAME MODE - Total time: 75m.19s
    Reading informations from Underworld - Everything, Everything.cue...
    Tracks: 8 
    Artist: Underworld
    Album: Everything, Everything
    
      12 % -> Underworld - 01 - Juanita + Kiteless.mp3... OK
      25 % -> Underworld - 02 - Cups.mp3... OK
      37 % -> Underworld - 03 - Push Upstairs.mp3... OK
      50 % -> Underworld - 04 - Pearls Girl.mp3... OK
      62 % -> Underworld - 05 - Jumbo.mp3... OK
      75 % -> Underworld - 06 - Shudder + King Of Snake.mp3... OK
      87 % -> Underworld - 07 - Born Slippy Nuxx.mp3... OK
     100 % -> Underworld - 08 - Rez + Cowgirl.mp3... OK (EOF)
    
    All files successfully splitted! Processed 173025 frames - Sync errors: 0</code>



