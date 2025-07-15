# Deckintosh Guide

![image](https://github.com/user-attachments/assets/7d3877d3-b9c4-4c05-a999-4d8caf38b596)

<hr>

An OpenCore EFI configuration for the Steam Deck

This is a guide targeted at getting users to run macOS natively on their Steam Decks.

Some things to know:

1. Barely anything works. Many devices do not have support. This is more of a "Look what I can do!" thing as of right now.

2. THIS CAN EASILY WIPE YOUR STEAMOS INSTALL IF YOU ARE NOT CAREFUL. Part of the macOS install is FORMATTING a storage device OF YOUR CHOOSING. IF YOU CHOOSE THE INTERNAL STORAGE OF THE STEAM DECK, IT WILL BE WIPED.

3. With #2 being said, you can, in fact, dual boot macOS and steamOS or windows on the same drive with some elbow grease.

4. I did not make any of the software used. I simply helped get macOS booting with some other devs and people interested. It was a team effort.

5. Stuff will be fixed or not depending on if someone takes interest and adds support. PLEASE dont ask when stuff with be supported, I dont know. As support is added, and added in a way the devs of the software are comfortiable with, I will update the guide.

6. PLEASE DO NOT FLOOD THE HACKINTOSH SERVERS FOR SUPPORT REQUESTS. Whether it be discord or others. These are niche devices that barely run macOS. For the love of all thats good and holy, dont pester the volunteers that help others with this meme of a computer. If you must ask, come here to and ask IN THE DISCUSSIONS.

7. Booting from the SD card slot will not work in most cases (booting an online recovery image *should* work as it is run from memory, not the SD card itself). If you are using an offline image or a full macOS install, you will need to boot from USB or the internal NVME.

<hr>

**Prerequisites**
- A Steam Deck (OLED), becase what else is this guide for?
- (Optionally) Another computer unless you have 15 hours of free time and can deal with installing Windows, continuing from there, formatting your install device again

**Getting Started**

Firstly, you need to understand how to run macOS on any other PC. Go to the [Dortania OpenCore install guide](https://dortania.github.io/OpenCore-Install-Guide/) and familiarize yourself with it. This is what we will be using.

For the most part, you will follow the steps in the OpenCore guide, so make sure that you read and understand it.

As you are following the guide and get to the [Gathering Files](https://dortania.github.io/OpenCore-Install-Guide/ktext.html) portion, you will want to have these files for the Steam Deck:

**ACPI**

For this, you are going to want to download a tool called [SSDTTime](https://github.com/corpnewt/SSDTTime). Running this tool on the Steam Deck, you are first going to choose option **P** to dump you DSDT. If you are running this tool from another machine, you'll need to have the SSDT/DSDT dumps from your Deck. Please don't try to use your other machine's dumps. That's just flatout stupid.
Then you are going to select option **3** to make a SSDT-EC.aml. It should be in the results folder. Copy this to your EFI -> ACPI folder, as per the OpenCore guide instructions.

You will also need the [CPUR](https://github.com/dortania/Getting-Started-With-ACPI/blob/master/extra-files/compiled/SSDT-CPUR.aml) SSDT.

In the end, you should have two files in the ACPI folder:

1. SSDT-CPUR.aml
2. SSDT-EC.aml

**Firmware Drivers**

You'll want to keep 2 (preferably 3 drivers) that're included in the stock EFI folder by default:

1. OpenHFSPlus.efi (HFS+/macOS Extended driver, REQUIRED TO BOOT THE INSTALLER)
2. OpenRuntime.efi (Runtime for OC, REQURIED)
3. OpenCanopy.efi (OPTIONAL, If you want a mac-like boot picker follow [this tutorial](https://dortania.github.io/OpenCore-Post-Install/cosmetic/gui.html))

Everything else can and SHOULD be trashed since some of these drivers have tool entries that add clutter to the boot picker.

**Tools and Resources**

Just drag the tools and the resources folders to the trash. Yes I'm serious. We dont need them right now and they can make things much more confusing in the boot menu. Just get rid of them.

**Kexts (USB mapping NEEDS Windows or macOS, read kext 4 for more details)**

*Note: Kexts with look like regular folders with .kext at the end on Windows and Linux. Just get the folders with the .kext at the end, these are what you want.*

Make sure you have these Kexts in your kexts folder:

1. [Lilu.kext](https://github.com/acidanthera/Lilu/releases) (macOS will NOT boot without this)
2. [VirtualSMC.kext](https://github.com/acidanthera/VirtualSMC/releases) *Yank VirtualSMC and SMCBatteryManager, throw away the folder, and then get [SMCAMDProcessor](https://github.com/trulyspinach/SMCAMDProcessor) and [SMCRadeonSensor](https://github.com/ChefKissInc/SMCRadeonSensors)*
3. [GUX-RyzenXHCIFix](https://github.com/RattletraPM/GUX-RyzenXHCIFix/releases/tag/v1.3.0b1-ryzenxhcifix) So I was actually able to boot without this. If you want it then here you go I guess
4. [USBToolBox.kext](https://github.com/USBToolBox/kext/releases), which is required when you're using USBMap.kext.
5. For USBMap.kext, make your own with the [tool](https://github.com/USBToolBox/tool) which REQUIRES Windows and DOES NOT support SteamOS or any other form of Linux, or if you dont have Windows and you're too lazy to boot into a Hiren's BootCD ISO, you can use [this](https://github.com/CodeRunner5235/Opencore-Steam-Deck/blob/main/UTBMap.zip) map which isn't guaranteed to actually work.
6. [AppleMCEReporterDisabler.kext](https://github.com/acidanthera/bugtracker/files/3703498/AppleMCEReporterDisabler.kext.zip) is only needed for macOS Big Sur and above.
7. [VoodooI2C](https://github.com/VoodooI2C/VoodooI2C) and [VoodooI2CHID](https://github.com/VoodooI2C/VoodooI2CHID) if you actually want your touchscreen to work. Unless you're some kind of insect that can use the trackpad sideways without messing up easily.

<hr>

**config.plist**

After nabbing the Sample.plist from the OpenCorePkg -> Docs folder, moving it to your OC folder and renaming it config.plist, follow the steps in that [Ryzen and Threadripper portion of the guide](https://dortania.github.io/OpenCore-Install-Guide/AMD/zen.html) with just a few changes:

2. For your AMD patches under the Kernel, the Steam Deck uses **4 cores** and 8 threads. Ignore the threads and just put in 4 cores.

3. Don't use XhciPortLimit under Kernel -> Quirks, especially if you're running macOS 11.3+ where it just breaks.

4. Under Misc -> Boot, don't enable HideAuxiliary. Otherwise, you can't see the macrecovery DMGs and other tools till you press 'Space'. And if you're like me and don't have a USB hub or docking station (good luck), you'll REALLY wanna turn this on.

5. Under Misc -> Security, set SecureBootModel to Disabled to make life easier.

6. Don't be an idiot and add all the boot arguments (under NVRAM > 7C436110-AB2A-4BBB-A880-FE41995C9F82 > boot-args) you see on the guide.
   I'd suggest using `-v debug=0x100 keepsyms=1 agdpmod=pikera -radcodec vsmcgen=1`.

7. If you want to run modern versions of macOS, I'd suggest MacBookPro16,X
   If you hate yourself and want to run Tahoe while it's in beta (as of this guide), use MacBookPro16,2.
   The reason why we're using MacBook Pros is to get the battery indicator to actually show up. Correct me if I'm wrong but MacPro7,1 doesn't have this.

9. Under UEFI -> Output, set DirectGopRendering to True and set the Resolution to 1280x720 (change the data type to a string)
   *IMPORTANT: Not enabling DirectGopRendering will result in OpenCore booting in the right orientation and resolution, but after that literally everything is garbled.
   And not setting the resolution to 1280x720 will immediately show a garbled output.
   While you're at it, make sure you also enable AppleEg2Info under UEFI > ProtocolOverrides and set ForceDisplayRotationInEFI under NVRAM > Add > 7C436110-AB2A-4BBB-A880-FE41995C9F82 to 90 (thanks Hack n' Patch). This will only rotate the bootloader though*

And that's it! After you follow the rest of the Install guide for Ryzen, you should be able to boot into an extremely slow macOS install. Have... fun.

<hr>

**Picture Examples**

*OC folder*
![image](https://github.com/user-attachments/assets/4f42c205-2a4a-4b3b-b24f-3e16e08a5e07)


*ACPI folder*
![image](https://github.com/user-attachments/assets/bf2fbe09-fbb3-43ce-b97d-cdc7109b469a)


*Drivers folder*
![image](https://github.com/user-attachments/assets/52ffe87a-b5cb-4a3c-8ca1-ba5b1d309aa1)


*Kexts folder*
![image](https://github.com/user-attachments/assets/b0fc4d40-4adf-4b85-aff5-96067341c09a)





