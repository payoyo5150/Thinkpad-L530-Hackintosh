# Thinkpad L530 Hackintosh

The EFI in the release section of this repo works like a charm with MacOS Mojave 10.14.6 (Sleep/wake, Graphics, Power management, Trackpad, Sound, Docking station, ExpressCard, USB... except for the SD Card Reader). Bluetooth and Wifi (with legacy Airdrop support) work for me as well with an AR9280 wifi card, but it is difficult to achieve because of the PCIe Whitelist this laptop has.

I haven't tried Catalina yet. It was working fine with both Sierra and High Sierra back in the day but i cant confirm if it is still working on previous versions of MacOS, since my EFI changed a bit and went through a lot of updates.
I figured out most things by toying with clover bootloader and reading guides. I've been alone with this laptop running MacOS for years, desperatly looking for help. But people only seemed to care for the t430 and the x230 at the time.
Now that it works fine, I guess it is time to share my work !


Some basic knowledge about hackintosh is needed to do this. Find out for yourselves, I'm not clever enough to do a full beginner guide.

Note also that there are several revisions of this model. Mine is 2475A38 (first model, june 2012), bios rev 2.70.
Some guys with newer L530 flavours got it to work, but I can't garantee my files will work with your L530.


# My specs :

HM76 Chipset / I7 3632qm / 2x8gb DDR3 Corsair Vengeance 1600mhz / Samsung EVO 840 ssd / 1366x768 shitty screen / 9-cells battery / Docking station 4337 (only the audio jack won't work on it, but I'm using a Focusrite Scarlett 2i2 when docked)

Note that Intel core cpus will work, but if you have the celeron model, you'll have to toss this (bad) cpu since it isn't supported by MacOS.
but I suggest getting an i7, the 3520m for example if you are low on money (dual core, 2,9ghz)

The 3632qm is the best CPU you can get with the right tdp for this laptop, but it could be tricky to find one.


# BIOS SETTINGS 

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

Use the method you prefer to get macOS. Once you have a MacOS install USB stick, mount its EFI, wipe it and replace the files with mine (in the "Release" section on the right). If you prepare your EFI on MacOS, use ESP mounter or Clover Configurator to do so. 

ESP Mounter pro (to mount the EFI partition) : https://www.olarila.com/topic/4975-esp-mounter-pro-v19/

(Optionnal - you can do this after MacOS is installed) Generate your SMBIOS serial number : https://hackintosher.com/guides/quick-fixes-facetime-icloud-imessage-hackintosh-not-working/ (number 4 on this guide)
You can use Clover Configurator to open your Config.plist located in /EFI/Clover/

If your screen is using an higher resolution than 1366x768, you'll need to change the platform-id in the graphics section of the config.plist to 0x01660004


# Installing MacOS

Note that these informations are for installing MacOS on its own disk. If you want to use the same disk for several OSes, then google is your friend !

You should be able to reach the installer without difficulty. 
Format the disk to APFS with a guid partition table. 
Then install MacOS.

It is possible that the PS2 driver won't work only during the install, so better to have USB keyboard and mouse !

# MacOS first boot

Once you've reached the desktop you're almost there. 
Use ESP mounter pro again to replace to content of your hard drive's EFI with the one from the USB stick. I highly suggest not mounting both your EFI partitions at the same time (mistakes can happen)

Reboot without the usb stick. Enjoy !
now time for some fine tuning 

# Terminal : Disable Gatekeeper (optional) :
sudo spctl --master-disable

# Terminal : Power Chime On AC (because why not)
defaults write com.apple.PowerChime ChimeOnAllHardware -bool true; open /System/Library/CoreServices/PowerChime.app &

The AC input connector tends to break on the L530 (Glued mine 2 times already - Update, 3 times), so an audio feedback is cool.

# CPU Power management

This is the way : 
https://www.olarila.com/topic/6451-guide-generate-ssdt-for-power-management-with-ssdtprgen/

At this point, everything except for card reader and Wifi/bluetooth should work.
The card reader worked for me on 10.12.6, but I had to install it to S/L/E and manually input the device id. 
There is probably a cleaner way of doing this now, but since I don't use the card reader at all, I don't know.

# Audio
I ended up using Voodoo HDA, first because it allows me to boost a bit the audio output, but also because it is the only solution to have a working microphone.
The kext is in my EFI, but if you want the prefpane use this : https://github.com/chris1111/VoodooHDA-2.9.2-Clover-V15/files/4242657/VoodooHDA.2.9.2.Clover-V15.zip
(install in UEFI mode only)

I've tried both AppleHDA back in the day and AppleALC. If you want to use appleALC, you should use the layout id 28 prior to mojave. 3 is fine under Mojave. But the microphone won't work.

# Webcam

I have no webcam on mine. Not all L530 have the same webcam model. If it is working OOB, then it is supported.

# Trackpad

go te system preferences > accessibility > mouse & trackpad > trackpad options 
activate inertia, and set the scrolling speed all the way up

# Using a compatible wifi card

About Wifi/bluetooth, this can be more problematic and harder to achieve and I don't think the way I did this is the easiest. I flashed the card using linux on another laptop with no PCIe whitelist. The pcie whitelist is still active on my thinkpad but I flashed my Atheros ar9280 EEPROM so the bios would see an intel centrino card instead. Then I used fakePciid so MacOS sees my wifi card for what it really is. The problem is that if you do this, your wifi card will only work on MacOS.

Here's a guide : https://github.com/nouatzi/AR9285-rebranding

But removing the whitelist using a custom bios flashed with an SPI programmer would be wayyyyy better and maybe easier. 


TO BE CONTINUED





