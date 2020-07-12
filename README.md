# Thinkpad L530 Hackintosh

The purpose of this guide is to help people installing MacOS on a Thinkpad L530. This current EFI works like a charm with MacOS Mojave 10.14.6

I haven't tried catalina yet. It was working fine with both Sierra and High Sierra back in the day but i cant confirm if it is still working on previous versions of MacOS. If you want to try other versions, update me if it works ! 


I figured out most things by toying with clover bootloader and reading guides. With the latest revisions of the clover bootloader, the process got easier : it no longer needs a custom dsdt for custom brightness range + an injector kext (thanks to whatevergreen), battery etc. 
I've been alone with this laptop running MacOS for years, desperatly looking for help. But people only seemed to care for the t430 and the x230 at the time.
Now that it works fine, I guess it is time to share my work !


Most of what i did to fine tune the laptop was about 2 years ago and I just updated my kexts and bootloader since then. 
I'm not sure I could do everything again from scratch but I do a lots of backups of my files so I don't have to figure this out again. :)
Some basic knowledge about hackintosh is needed to do this. Find out for yourselves, I'm not clever enough to do a beginner guide.


Note also that there are several revisions of this model. Mine is 2475A38 (first model, june 2012), bios rev 2.70.
Some guys with newer L530 flavours got it to work, but I can't garantee my files will work with your L530.


# MY SPECS :

HM76 Chipset

I7 3632qm

2x8gb DDR3 Corsair Vengeance 1600mhz

Samsung EVO 840 ssd

1366x768 shitty screen

9-cells battery 

Docking station 4337 (only the audio jack won't work on it, but I'm using a Focusrite Scarlett 2i2 when docked)


Note that Intel core cpus will work, but if you have the celeron model, you'll have to toss this (bad) cpu since it isn't supported by MacOS.
but I suggest getting an i7, the 3520m for example if you are low on money (dual core, 2,9ghz)

The 3632qm is the best CPU you can get with the right tdp for this laptop, but it could be tricky to find one.


# BIOS SETTINGS 
(These are mine, maybe some of them are pointless - always on usb, power on AC etc were bothering sleep at the time)

Always On USB : disable

Express Card Speed : Automatic 

Power on with AC attach : Disabled

Intel Rapid Start technology : Disabled

Sata controller mode : AHCI

Security Chip : Disabled

Execution prevention : Enabled

Intel Virtualization technology : Enabled (I you are running VM on top of MacOS)

Secure Boot : disabled

Boot Mode : UEFI only, CSM disabled


# Creating a MacOS installer 

Use the method you want to get macOS. To me, the easiest way is to get an Olarila Image, burn it to an usb drive using balena etcher and then replacing the content of its EFI partition with mine. 
This should be easy using MacOS to make the installer (using a Mac, a VM or even a dirty temporary installation using a distro I won't name here)
You could probably also do it from Linux or Windows, but I've always been too lazy to try it.

Olarila images : https://www.olarila.com/topic/6278-new-olarila-images/

Balena Etcher : https://etcher.io

ESP Mounter pro (to mount the EFI partition) : https://www.olarila.com/topic/4975-esp-mounter-pro-v19/

(Optionnal - you can do this after MacOS is installed) Generate your SMBIOS serial number : Look for a guide if needed.
You can use Clover Configurator to open your Config.plist located in /EFI/Clover/
The Smbios is ready, (using Macbook Pro 9,1), you just need to generate the Smuuid and your serial number.

If your screen is using an higher resolution than 1366x768, you'll need to change the platform-id in the graphics section of the config.plist to 0x01660004



# Installing MacOS
Note that these informations are for installing MacOS on its own disk. If you want to use the same disk for several OSes, then google is your friend !

You should be able to reach the installer without difficulty. 
Format the disk to APFS with a guid partition table. 
Then install MacOS.

It is possible that the PS2 driver won't work only during the install, so better to have USB keyboard and mouse !

# MacOS first boot

Once you've reached the desktop you're almost there. 
Use ESP mounter pro again to replace to content of your hard drive's EFI. I highly suggest not mounting both your EFI partitions at the same time (mistakes can happen)

Reboot without the usb stick. Enjoy !

# Enable Touch to Click using the MacOS terminal

(One command at a time)

defaults write com.apple.AppleMultitouchTrackpad Clicking -bool true

sudo defaults write com.apple.driver.AppleBluetoothMultitouch.trackpad Clicking -bool true

sudo defaults -currentHost write NSGlobalDomain com.apple.mouse.tapBehavior -int 1

sudo defaults write NSGlobalDomain com.apple.mouse.tapBehavior -int 1

And while you have the terminal open : 

# Disable Gatekeeper (optional) :
sudo spctl --master-disable

# Power Chime On AC (because why not)
defaults write com.apple.PowerChime ChimeOnAllHardware -bool true; open /System/Library/CoreServices/PowerChime.app &


# CPU Power management
You can't use PluginType with an Ivy Bridge cpu. (only haswell and above)
On this one, I've always been using this script to generate an ssdt for power management.

This is the way : 
https://www.olarila.com/topic/6451-guide-generate-ssdt-for-power-management-with-ssdtprgen/

At this point, everything except for card reader and Wifi/bluetooth should work.
The card reader worked for me on 10.12.6, but I had to install it to S/L/E and manually input the device id. 
There is probably a cleaner way of doing this now, but since I don't use the card reader at all, I don't know.

# Using a compatible wifi card

About Wifi/bluetooth, this can be more problematic and harder to achieve and I don't think the way I did this is the easiest.
The pcie whitelist is still active on my thinkpad but I flashed my Atheros ar9280 EEPROM so the bios would see an intel centrino card instead. Then I used fakePciid so MacOS sees my wifi card for what it really is. The problem is that if you do this, your wifi card will only work on MacOS.
But removing the whitelist using a custom bios would be wayyyyy better and maybe easier. 
I flashed the card using linux on another laptop.  

TO BE CONTINUED



