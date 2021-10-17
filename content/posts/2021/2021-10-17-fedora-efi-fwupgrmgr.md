---
date: 2021-10-10
title: "Fixing fwupdmgr's UEFI ESP partition not detected or configured on Fedora 34"
---

## Lenovo Thunderbolt docking stations are terrible

Ever since getting my new Thinkpad t14s, my Lenovo ThinkPad Thunderbolt 3 gen 1 docking (Type 40AC) started acting up on the USB side.

The USB was flapping constantly (keyboard, mouse, camera and DAC attached to a USB switch plugged into the docking). The keyboard was missing keys, the mouse was lagging every other seconds, etc.

As a workaround, I decided to plug my USB stuff directly into the laptop and move on with life.

But then a couple of weeks later, the docking started acting up with the external display as well. The external display was going blank for no reason. The temporary fix was to unplug and replug the Thunderbolt cable.

I assumed the docking was slowly dying so I asked our IT department for a replacement and they told me they were overwhelmed with Lenovo docking station issues and they were out of stock of Gen 2s anyway.

## Dell docking stations are actually quit good

A US based colleague then suggested to get a Dell Thunderbolt Dock WD19TBS instead, claiming that everything worked perfectly on his P51 and that I could update it with `fwupdmgr` (which is impossible with Lenovo Gen1s, you need a Windows machine).

I ordered one and got:

- no external display issues anymore :D
- USB flappings, still :(
- a very good impression on the built, it's solid, probably twice the weight of the Lenovo and it has a fan (that you barely hear when it runs)

I decided to try to update the docking firmwares with `fwupdmgr` and indeed, it just works.

## Upgrading T14s firmwares

Then I decided to upgrade the laptop firmwares but it was not possible because of this error:

```
WARNING: UEFI ESP partition not detected or configured
```

My laptop is installed in EFI and everything sits in `/boot/efi`.

If you look at the suggested [documentation](https://github.com/fwupd/fwupd/wiki/PluginFlag:esp-not-found), they suggest to edit the `uefi_capsule.conf` if nothing else works.

I didn't see the need to do so because `/boot/efi` is a location that is supposedly checked.

I decided to proceed with the following:

```bash
sudo vim /etc/fwupd/uefi_capsule.conf
```

```ini
OverrideESPMountPoint=/boot/efi
```

And rebooted.

## Bingo

From this point on, I was able to update every and each firmwares that could be updated:

```bash
sudo fwupdmgr refresh --force
sudo fwupdmgr get-devices
sudo fwupdmgr update
```

There were so many things to upgrade that I had to reboot a couple of times.

I also managed to update my T480s.

And guess what? The USB flapping is gone.

## Conclusion

Lenovo Thunderbolt docking stations are quite terrible and unreliable (sources: own experience and IT support claims).

Updating the Gen1 with `fwupdmgr` is not possible. Only Gen 2s.

The Dell can be updated with `fwupdmgr`.

`fwupdmgr` can ignore your `/boot/efi` and you need to hardcode the value in a config file.

Thanks Dell for making a docking that just works!