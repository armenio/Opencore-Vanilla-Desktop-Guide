# Legacy Install

* Supported version: 0.5.7

**Attention to all users, please note this guide and other khronokernel sites will be shutting down on April 16th, 2020. Reason for this is we've decided to move the guides to a dedicated organization to help simplify the hackintosh process and provide a single, trusted source for hackintosh information. This new organization will be known as [Dortania](https://github.com/dortania).**

Links to the new sites:

* [OpenCore Desktop Guide](https://dortania.github.io/OpenCore-Desktop-Guide/)
* [Getting Started with ACPI](https://dortania.github.io/Getting-Started-With-ACPI/)
* [GPU Buyers Guide](https://dortania.github.io/GPU-Buyers-Guide/)
* [Wireless Buyers Guide](https://dortania.github.io/Wireless-Buyers-Guide/)
* [Anti-Hackintosh Buyers Guide](https://dortania.github.io/Anti-Hackintosh-Buyers-Guide/)

Wanna try OpenCore but can't boot UEFI based sources? Well don't fret, there's hope for you! OpenCore supports DuetPkg which emulates a UEFI environment for legacy systems.

To start, you need the following:

* BootInstall.command
* Install source(MacOS Journaled + GUID, size of USb depends on the version of macOS. 16GB recommended for catalina)

![BootInstall Location](https://i.imgur.com/D9YT3M4.png)

Within your OpenCore build folder, navigate to `Utilities/BootInstall`. Here you'll find a file called `BootInstall.command`. What this does is install DuetPkg to your desired drive.

![](https://i.imgur.com/FoJs4zU.png)

Now you'll want to run `BootInstall.command`, do note that you may need `sudo` for this to work correctly on newer versions of macOS

```text
sudo Utilities/BootInstall/BootInstall.command
```

![Disk Selection/writing new MBR](https://i.imgur.com/20BQvtv.png)

This will give you a list of available disks, choose yours and you will be prompted to write a new MBR. Choose yes`[y]` and you'll be finished.

![Finished Installer](https://i.imgur.com/w3AyfVd.png)

![Base EFI](https://i.imgur.com/Lhw52gb.png)

This will provide you with an EFI partition with a `boot` file, this is where we'll add our OpenCore EFI.

**Note**: The firmware drivers used for legacy users differ slightly:

* [OpenUsbKbDxe.efi](https://github.com/acidanthera/OpenCorePkg/releases) 
   * For picker support in the Opencore menu
* [HfsPlusLegacy.efi](https://github.com/acidanthera/OcBinaryData/blob/master/Drivers/HfsPlusLegacy.efi)
   * Needed for seeing Hfs drives like installers, the legacy variant is required for SnadyBridge and older due to missing RDRAND instruction support
