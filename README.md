# Boox Palma 2 Root Guide

**INITIAL DISCLAIMER: screwing this up could brick your device, do not attempt unless you thoroughly understand what is happening and why, please read all instructions top to bottom first so you know what to expect**

**CREDITS AND THANKS: huge thanks to Renate from MobileRead.com for the resources here I used to create this guide: https://www.temblast.com/edl.htm**

OK. On with the show ;)

# What Is Rooting?
Normally when using an Android or iOS device, you as the owner/user of the device cannot perform the device's equivalent of "su," "sudo," "run as Administrator," etc. The manufacturer reserves that right to themselves. This is basically because they don't want to be bothered with the support cost of you doing something that irreversibly breaks your device that they would then have to re-install or perform some other labor-intensive repair to fix. However, some manufacturers are more draconian about this [*stares intently at Apple*] than others. Rooting a Boox device is generally very simple. They always use Qualcomm processors with predictable ways of accessing the low-level hardware so that you can perform the operations necessary.

# Why Root Your Device?
You may not need to at all! It really depends on what you're trying to do. On pen-enabled devices like the Note Air 4C, rooting lets you edit the /onyxconfig/eac_config file which controls ink optimization like Boox enabled for OneNote -- faster/no-lag writing in the app. On the Palma 2, a device which (sadly) has no EMR layer for use with a stylus, rooting still lets you do things like use Titanium Backup to back entire apps and their data up to any location, or run software like nmap with privileges required to do things like putting WiFi interfaces into promiscuous mode so you can gather more data. It's the sort of thing where if you know you need it, you know why you need it.

# K, How Does This Work?
So, first, these instructions are for Windows. If you know how to do this on other operating systems, please let me know and if you like I'll add the contents of your guide to mine or simply link to yours.
You will need the following:
* The ["EDL" utility](https://www.temblast.com/edl.htm) which is used to interface with Qualcomm devices like your Palma 2 on a lower level
* A ["loader" file](https://github.com/bkerler/Loaders/blob/main/lenovo_motorola/0000000000000000_bdaf51b59ba21d8a_fhprg.bin) for EDL which permits it to communicate with the device correctly
* The [Zadig utility](https://zadig.akeo.ie/) which installs a device driver used for your 
* The Android [platform-tools](https://developer.android.com/tools/releases/platform-tools#downloads) package for Windows which sends the Palma's Android OS commands
* The Magisk APK, which you will want to use [this link](https://github.com/topjohnwu/magisk/releases) **on your device** to download and install on your device -- Magisk is the modern way of handling root/superuser access on your Android device

Broadly, you are going to do this:
* Reboot your device to EDL mode
* Use EDL to grab both boot partition images from the Palma 2
* Reboot your device to regular Android mode
* Send both of the boot images to your Palma 2 with ADB
* Use the Magisk app to patch each image to permit root access
* Pull the patched boot images back to your computer with ADB
* Reboot your device to EDL mode again
* Use EDL to send the Magisk-patched boot images to the appropriate boot partitions
* Reboot your device to regular Android mode
* Open Magisk and make any changes to settings you want to make

Sounds good? Let's get started.

# Step by Step
* Put your "platform-tools" folder from Google somewhere easy for you to get to from the command line. I just put it in my home folder. If you do that, you will be able to run it from the command line (Win-R, `cmd`, Enter) with something like `platform-tools-latest-windows\platform-tools\adb`
* Go to the Settings menu on your Palma 2 (pull down the menu from the upper right corner, select the gear icon that shows up in the upper right), scroll down to "More Settings", turn "USB Debug Mode" on
* Connect your Palma 2 to your computer. On the command line, try `platform-tools-latest-windows\platform-tools\adb devices` What you're looking for is something like this:
```
PS C:\Users\jtd\Downloads> ..\platform-tools-latest-windows\platform-tools\adb devices
List of devices attached
f69d9611        device
```
This step is a little fiddly. At first your device will probably not connect. You do have to go through "approving" the computer on the Palma 2 which will pop up after a few tries of "adb devices" after turning USB Debug Mode on. It's not super consistent but it will work eventually.

* Once you can see the Palma 2 with `adb devices,` do `adb reboot edl`. It will look on the Palma 2 like nothing has happened. That's fine, don't worry, it has. It might also go almost entirely black. That's also fine.
* Download and run Zadig (found above). Go to "Options" and pick "List All Devices." From the dropdown, look at your list of USB devices. You should see "Quectel QDLoader 9008." Select that. Click "install driver."
* Download the loader file and EDL, from the list above. Notice that the loader file has a really long, complicated name. To make your life easier, rename it to something  like palma2.bin. On the command line, try `.\edl /lpalma2.bin` (yes, just like that -- assuming the edl.exe file is in your current directory and so is the palma2.bin file you renamed from the loader download -- the EDL program uses this weird command line switch format where you do not want a space between the l from /l and the name of the loader file.) You should see something like this:

```
PS C:\Users\jtd\Downloads> .\edl /lpalma2.bin
Found EDL 9008, handshaking... nope, resetting... version 2.1
HWID: 0013f0e100000000, JTAG: 0013f0e1, OEM: 0000, Model: 0000
Hash: d40eee56f3194665-574109a39267724a-e7944134cd53cb76-7e293d3c40497955-bc8a4519ff992b03-1fadc6355015ac87 (x3)
Sending palma2.bin 100% ok, starting... ok, waiting for Firehose... ok
```

* Now try `.\edl /u /g` . You should see it print the partition table.
* Pull the A slot boot partition from the Palma 2: `.\edl /u /r /pboot_a boota.img`
* Same for the B slot: `.\edl /u /r /pboot_b bootb.img`
* Modern Android devices have two "boot slots" they can use, which sort of gives you the ability to dual-boot various versions of the operating system. You don't need to worry about that specifically, but I have no idea if the boot partition slots are materially different from each other on Boox, so to be safe, we'll pull both partitions like we just did, modify both of them, and write them both to the Palma 2.
* Reboot the device back to Android with `.\edl /z`
* Use ADB to push both of the boot images to your device: `adb push boota.img /sdcard` and `adb push bootb.img /sdcard`.
* You installed Magisk from Github as shown above already, right? Great! Open Magisk and click the "Install" button in the upper right. Pick "select and patch a file." You will do this twice, once for boota.img and once for bootb.img. Each time it will produce a new .img boot partition file ending with a random set of characters. Make sure you keep track of which one is which, because again, I have no idea if it matters for Boox! You should probably rename them to something like `magisk-boota.img` and `magisk-bootb.img."
* Assuming you did that, do an `adb pull` of the new Magisk files back to your computer.
* Back to EDL mode. `adb reboot edl`.
* Now we push the modified boot images back on to your Palma 2's storage in the right places. `.\edl /u /w /pboota magisk-boota.img` and `.\edl /u /w /pbootb magisk-bootb.img`, followed by `.\edl /z` to reboot back to Android again.
* You're running rooted! 🎉
* You can double check by opening Magisk again. One thing I would recommend is to go into the Magisk settings and block anything work or bank related from being able to see that you're rooted.

Enjoy! 
