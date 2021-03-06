# Security and FileVault

* Supported version: 0.5.7

**Attention to all users, please note this guide and other khronokernel sites will be shutting down on April 16th, 2020. Reason for this is we've decided to move the guides to a dedicated organization to help simplify the hackintosh process and provide a single, trusted source for hackintosh information. This new organization will be known as [Dortania](https://github.com/dortania).**

Links to the new sites:

* [OpenCore Desktop Guide](https://dortania.github.io/OpenCore-Desktop-Guide/)
* [Getting Started with ACPI](https://dortania.github.io/Getting-Started-With-ACPI/)
* [GPU Buyers Guide](https://dortania.github.io/GPU-Buyers-Guide/)
* [Wireless Buyers Guide](https://dortania.github.io/Wireless-Buyers-Guide/)
* [Anti-Hackintosh Buyers Guide](https://dortania.github.io/Anti-Hackintosh-Buyers-Guide/)

So something that makes OpenCore truly special is how it's been built with security in mind which is quite rare especially in the Hackintosh community. Well here we'll be going through and setting up some of OpenCore's great Security features:

* [FileVault](/post-install/security.md#FileVault)
* [Vault](/post-install/security.md#Vault)
* [ScanPolicy](/post-install/security.md#ScanPolicy)

## FileVault

FileVault is macOS's builtin drive encyption, and with OpenCore support for it has been drastcally improved compared to the legacy Clover drivers.

To start, you'll need the following .efi drivers:

* OpenRuntime.efi
  * [OpenUsbKbDxe.efi](https://github.com/acidanthera/AppleSupportPkg/releases) for DuetPkg users(systems without UEFI support)

**Do not use VirtualSMC.efi with OpenCore, its already baked inside**. You do however require VirtualSMC.kext still

Setting in your config.plist:

* Misc -> Boot 
  * `PollAppleHotKeys` set to YES(While not needed can be helpfu)
* Misc -> Security
  * `AuthRestart` set to YES(Enables Authenticated restart for FileVault2 so password is not required on reboot. Can be concidered a security risk so optional)
* NVRAM -> Add -> 4D1EDE05-38C7-4A6A-9CC6-4BCCA8B38C14
  * `UIScale` set to `02` for high resolution small displays
* UEFI -> Input
  * `KeySupport` set to YES(Only when using OpenCore's builtin input, users of OpenUsbKbDxe should avoid)
* UEFI -> Output
  * `ProvideConsoleGOP` to YES
* UEFI -> Protocols
  * `FirmwareVolume` set to YES
  * `HashServices` set to YES for Broadwell and older(this includes X99), this is needed for systems with broken SHA-1 hashing
  * `AppleSmcIo` set to YES(this replaces VirtualSMC.efi)
* UEFI -> Quirks
  * `RequestBootVarRouting` set to YES
  * `ExitBootServicesDelay` set to `3000`-`5000` if you recieve `Still waiting for root device` on Aptio IV firmwares(Broadwell and older)

With all this, you can proceed to enable FileVault like on a normal mac under `System Preferences -> Security & Privacy -> FileVault`

For UI issues, see [Fixing Resolution and Verbose](/post-install/verbose.md)

## Vault

**What is vaulting?**

Well vaulting is based around 2 things, vault.plist and vault.sig:

* vault.plist: a "snapshot" of your EFI
* vault.sig: validation of vault.plist

This can be seen as secure boot for OpenCore, so no one can modify it and get in without your permission. 

The specifics of vaulting is that a 256 byte RSA-2048 signature of vault.plist will be shoved into our OpenCore.efi. This key can either be shoved into [OpenCoreVault.c](https://github.com/acidanthera/OpenCorePkg/blob/master/Platform/OpenCore/OpenCoreVault.c) before compiling or with `sign.command` if you already have OpenCore.efi compiled.

Do note that nvram.plist won't be vaulted so users with emulated NVRAM still have risk of someone adding/removing certain NVRAM variables

**Settings in your config.plist**:

* `Misc -> Security -> Vault`:
   * `Basic`: Requires just vault.plist to be present, mainly used for filesystem integrity verification
   * `Secure`: Requires both vault.plist and vault.sig, used for best security as vault.plist changes require a new signature
* `Booter -> ProtectSecureBoot:` `YES`
   * Needed with Insyde firmwares for fixing secureboot keys and reporting violations

**Setting up vault**:

Grab OpenCorePkg and open the `CreateVault` folder, inside we'll find the following:

* `create_vault.sh`
* `RsaTool`
* `sign.command`

The last one is what we care about: `sign.command`

So when we run this command, it'll look for the EFI folder located beside our Utilities folder, so we want to bring either our personal EFI into the OpenCorePkg folder or bring Utilities into our EFI folder:

![](https://cdn.discordapp.com/attachments/456913818467958789/673348313814401044/Screen_Shot_2020-02-01_at_7.05.29_PM.png)

Now we're ready to run `sign.command`: 

![](https://cdn.discordapp.com/attachments/456913818467958789/673348777897099274/Screen_Shot_2020-02-01_at_7.07.28_PM.png)


**Disabling Vault after setup**:

If you're doing heavy troublehooting or have the need to disable Vault, the main things to change:

* Grab a new copy of OpenCore.efi
* `Misc -> Security -> Vault` set to Optional


## ScanPolicy

What this quirk allows to prevent scanning and booting from untrusted sources. Setting to `0` will allow all sources present to be bootable but calculating a specific ScanPolicy value will allow you a greater range of flexibilty and security.

To calculate the ScanPolicy value, you simply add up all the hexidecimal values(with a hexideciaml calculator, you can access this from the built-in macOS caluclator app with `⌘+3`). Once it's all added up, you would add this hexidecimal value to ScanPolicy(you will need to convert it to a decimal value first, Xcode will automatically convert it when you paste it)

`0x00000001 (bit 0)` — OC\_SCAN\_FILE\_SYSTEM\_LOCK

* restricts scanning to only known file systems defined as a part of this policy. File system drivers may not be aware of this policy, and to avoid mounting of undesired file systems it is best not to load its driver. This bit does not affect dmg mounting, which may have any file system. Known file systems are prefixed with OC_SCAN\_ALLOW\_FS_.

`0x00000002 (bit 1)` — OC\_SCAN\_DEVICE\_LOCK

* restricts scanning to only known device types defined as a part of this policy. This is not always possible to detect protocol tunneling, so be aware that on some systems it may be possible for e.g. USB HDDs to be recognised as SATA. Cases like this must be reported. Known device types are prefixed with OC_SCAN\_ALLOW\_DEVICE_.

`0x00000100 (bit 8)` — OC\_SCAN\_ALLOW\_FS\_APFS

* allows scanning of APFS file system.

`0x00000200 (bit 9)` — OC\_SCAN\_ALLOW\_FS\_HFS

* allows scanning of HFS file system.

`0x00000400 (bit 10)` — OC\_SCAN\_ALLOW\_FS\_ESP

* allows scanning of EFI System Partition file system.

`0x00010000 (bit 16)` — OC\_SCAN\_ALLOW\_DEVICE\_SATA

* allow scanning SATA devices.

`0x00020000 (bit 17)` — OC\_SCAN\_ALLOW\_DEVICE\_SASEX

* allow scanning SAS and Mac NVMe devices.

`0x00040000 (bit 18)` — OC\_SCAN\_ALLOW\_DEVICE\_SCSI

* allow scanning SCSI devices.

`0x00080000 (bit 19)` — OC\_SCAN\_ALLOW\_DEVICE\_NVME

* allow scanning NVMe devices.

`0x00100000 (bit 20)` — OC\_SCAN\_ALLOW\_DEVICE\_ATAPI

* allow scanning CD/DVD devices.

`0x00200000 (bit 21)` — OC\_SCAN\_ALLOW\_DEVICE\_USB

* allow scanning USB devices.

`0x00400000 (bit 22)` - OC\_SCAN\_ALLOW\_DEVICE\_FIREWIRE

* allow scanning FireWire devices. 

`0x00800000 (bit 23)` — OC\_SCAN\_ALLOW\_DEVICE\_SDCARD

* allow scanning card reader devices.

By default, ScanPolicy is given a value of `0xF0103`(983,299) which is the combination of the following:

* OC\_SCAN\_FILE\_SYSTEM\_LOCK 
* OC\_SCAN\_DEVICE\_LOCK
* OC\_SCAN\_ALLOW\_FS\_APFS
* OC\_SCAN\_ALLOW\_DEVICE\_SATA 
* OC\_SCAN\_ALLOW\_DEVICE\_SASEX 
* OC\_SCAN\_ALLOW\_DEVICE\_SCSI 
* OC\_SCAN\_ALLOW\_DEVICE\_NVME

And lets just say for this example that you want to add OC\_SCAN\_ALLOW\_DEVICE\_USB:

`0x00200000` + `0xF0103` = `0x2F0103`

And converting this to decimal gives us `3,080,451`
