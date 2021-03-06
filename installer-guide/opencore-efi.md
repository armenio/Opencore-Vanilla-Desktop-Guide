# Creating the USB

* Supported version: 0.5.7

**Attention to all users, please note this guide and other khronokernel sites will be shutting down on April 16th, 2020. Reason for this is we've decided to move the guides to a dedicated organization to help simplify the hackintosh process and provide a single, trusted source for hackintosh information. This new organization will be known as [Dortania](https://github.com/dortania).**

Links to the new sites:

* [OpenCore Desktop Guide](https://dortania.github.io/OpenCore-Desktop-Guide/)
* [Getting Started with ACPI](https://dortania.github.io/Getting-Started-With-ACPI/)
* [GPU Buyers Guide](https://dortania.github.io/GPU-Buyers-Guide/)
* [Wireless Buyers Guide](https://dortania.github.io/Wireless-Buyers-Guide/)
* [Anti-Hackintosh Buyers Guide](https://dortania.github.io/Anti-Hackintosh-Buyers-Guide/)

Requirements:

* [OpenCorePkg](https://github.com/acidanthera/OpenCorePkg/releases), highly recommend running the debug version to show more info
* [AppleSupportPkg](https://github.com/acidanthera/AppleSupportPkg/releases)
* [ProperTree](https://github.com/corpnewt/ProperTree) to edit .plist files (OpenCore Configurator is another tool but is heavily outdated and the Mackie version is known for corruption. **Please avoid these kinds of tools at all costs!**).
* Cleaned NVRAM(This is seriously important if you used Clover before, as many variables will remain causing conflicts. Luckily with OC you can press `CleanNvram` in the boot picker when `AllowNvramReset` is enabled in your config)
* Basic knowledge of how a Hackintosh works and what files yours requires(ie: Type of network controller).
* You must remove Clover from your system entirely if you wish to use it as your main boot-loader. Keep a backup of your Clover based EFI. See here on what needs to be cleaned: [Clover Conversion](https://github.com/khronokernel/Opencore-Vanilla-Desktop-Guide/tree/master/clover-conversion)

# Making the installer

Depending on which OS you're on, see your specific section on making the USB, then once done return here on setting up OpenCore:

* [macOS users](/installer-guide/mac-install.md)
* [Windows users](/installer-guide/winblows-install.md)
* [Linux users](/installer-guide/linux-install.md)


# Setting up the EFI

To setup OpenCore’s folder structure, you’ll want to grab the EFI folder found in OpenCorePkg's releases page(this will already be done on the `BOOT` USB drive for windows users):

![base EFI folder](https://cdn.discordapp.com/attachments/683011276938543134/696593582701805568/Screen_Shot_2020-04-05_at_11.33.56_PM.png)

Now something you'll notice is that it comes with a bunch of files in `Drivers` and `Tools` folder, we don't want most of these:

* **Remove from Drivers:**
   * OpenUsbKbDxe.efi
      * Used for OpenCore picker on **legacy systems running DuetPkg**, [not recommended and even harmful on Ivy Bridge and newer](https://applelife.ru/threads/opencore-obsuzhdenie-i-ustanovka.2944066/page-176#post-856653)
   * NvmExpressDxe.efi
      * Used for Haswell and older when no NVMe driver is built into the firmware
   * XhciDxe.efi
      * Used for Sandy Bridge and older when no XHCI driver is built into the firmware
   * HiiDatabase.efi
      * Used for fixing GUI support like OpenShell.efi on Sandy Bridge and older
   * OpenCanopy.efi
      * This is OpenCore's optional GUI, we'll be going over how to set this up in post-install so remove this for now

* **Remove everything from Tools:**
   * Way to many to list them all, but I recommend keeping OpenShell.efi for troubleshooting pruposes
      
A cleaned up EFI:

![Clean EFI](https://cdn.discordapp.com/attachments/683011276938543134/696593842849316904/Screen_Shot_2020-04-05_at_11.34.58_PM.png)

Now you can place **your** necessary firmware drivers(.efi) from AppleSupportPkg into the _Drivers_ folder and Kexts/ACPI into their respective folders. Please note that UEFI drivers from Clover are not supported with OpenCore!(EmuVariableUEFI, AptioMemoryFix, OsxAptioFixDrv, etc). Please see the [Clover firmware driver conversion](https://github.com/khronokernel/Opencore-Vanilla-Desktop-Guide/blob/master/clover-conversion/clover-efi.md) for more info on supported drivers and those merged into OpenCore.

Here's what a populated EFI can look like:

![Populated EFI folder](https://cdn.discordapp.com/attachments/683011276938543134/696594137591578644/Screen_Shot_2020-04-05_at_11.36.13_PM.png)

**Reminder**:

* SSDTs and custom DSDTs(`.aml`) go in ACPI folder
* Kexts(`.kext`) go in Kexts folder
* Firmware drivers(`.efi`) go in the Drivers folder

## Now head to [Gathering Files](/ktext.md) to get the needed kexts and firmware drivers

