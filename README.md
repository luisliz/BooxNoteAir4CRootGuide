**INITIAL DISCLAIMER: screwing this up could brick your device, do not attempt unless you thoroughly understand what is happening and why, please read all instructions top to bottom first so you know what to expect**

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
* The Magisk APK, which you will want to use [this link] **on your device** to download and install on your device -- Magisk is the modern way of handling root/superuser access on your Android device

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
