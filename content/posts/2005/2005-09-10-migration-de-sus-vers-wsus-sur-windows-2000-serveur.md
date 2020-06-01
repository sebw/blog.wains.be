---
date: 2005-09-10
title: "Migrate from SUS to WSUS under Windows 2000 Server"
---

SUS had been working pretty well for the last two years to deploy updates througout a network of around 30 clients under Win2000/XP.

Given the notification of the end of support of SUS by the 6th of June, I dived into the migration of the new release, the so called WSUS.

BE AWARE : migrate SUS during off peak hours, the server will be rebooted several times to complete the installation (critical apps, go to hell)

IIS 5.0 is needed, it was already installed though. BITS 2.0 and IE 6 being installed as well, it was okay with these ones, fortunately.

Then, the installation of the missing stuff
1. .NET framework (reboot)
2. .NET framework 1.1 patch (reboot)
3. WSUS needed a compatible SQL server. We use a MS SQL Server 2000 that I had to update to SP3a. After another reboot, everything seems to go well, fortunate me.
4. WSUS installation, follow the install wizard, after an ultimate reboot, the system should be ready. You just need to configure the whole thing, setup what you want it to download/update when synchronizing with Windows Update.
5. I uninstalled SUS from add/remove programs (or whatever it's called, I use a french version of WXP)
6. Configure the clients :
Start > Run > gpedit.msc
Computer configuration > Administration patterns > Windows components > Windows Update
Depending on your version of Windows, you may have different settings available, the most important one will be the "automatic update" one where you'll fill the server to use for updates, that should be the WSUS ip + port if needed

**Conclusion :** SUS was missing some very crucial options like an update monitor, not easily showing if updates went well. It was not very clear under SUS if clients had been updated or not, the way of verifying was quite awkward (check the connection logs somewhere on the fs). WSUS now has that feature and clearly shows what it is doing (or not)..

See :
[http://www.microsoft.com/windowsserversystem/updateservices/evaluation/faqs.mspx#EDAAA](http://www.microsoft.com/windowsserversystem/updateservices/evaluation/faqs.mspx#EDAAA)  

[http://forum.hardware.fr/hardwarefr/...sujet-217504-1.htm](http://forum.hardware.fr/hardwarefr/WindowsSoftwareReseaux/Tutoriel-Windows-Server-Update-Services-WSUS-sujet-217504-1.htm)




4 october edit :  

The default port of WSUS when updating from SUS is 8530. Either you update like I did and remove SUS after install, either you change the default port to 80 or you keep SUS and reconfigure all the clients to connect to port 8530




