---
date: 2016-01-20
title: "Parsing XML with xgrep"
---

Imagine this file.xml containing:

    <vehicleinformation version="1.1" timestamp="1453323673">
      <vehicle locationX="4.336531" locationY="50.835707">Be.NMBS.P8401</vehicle>
      <stops number="9">
        <stop id="0" delay="16" canceled="0">
          <station id="BE.NMBS.008814001">Brussel-Zuid / Bruxelles-Midi</station>
          <time formatted="2016-01-20T17:06:00">1453305960</time>
          <platform normal="1">12</platform>
        </stop>
        <stop id="1" delay="18" canceled="0">
          <station id="BE.NMBS.008813003">Bru.-Centraal / Brux.-Central</station>
      	  <time formatted="2016-01-20T17:10:00">1453306200</time>
      	  <platform normal="1">3</platform></stop>
    [...]
      
You can get the value of the delay in Brux.-Central (here 18 minutes) by specifying the [XPath](https://en.wikipedia.org/wiki/XPath) with the -x option, like this:
 
    xgrep -x "/vehicleinformation/stops/stop[2]//@delay" file.xml
    
 I use [XPath Checker for Firefox](https://addons.mozilla.org/en-US/firefox/addon/xpath-checker/) to easily get the XPath.
