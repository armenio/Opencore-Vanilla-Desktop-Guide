# Clover Boot Arg Conversion

* Supported version: 0.5.7

**Attention to all users, please note this guide and other khronokernel sites will be shutting down on April 16th, 2020. Reason for this is we've decided to move the guides to a dedicated organization to help simplify the hackintosh process and provide a single, trusted source for hackintosh information. This new organization will be known as [Dortania](https://github.com/dortania).**

Links to the new sites:

* [OpenCore Desktop Guide](https://dortania.github.io/OpenCore-Desktop-Guide/)
* [Getting Started with ACPI](https://dortania.github.io/Getting-Started-With-ACPI/)
* [GPU Buyers Guide](https://dortania.github.io/GPU-Buyers-Guide/)
* [Wireless Buyers Guide](https://dortania.github.io/Wireless-Buyers-Guide/)
* [Anti-Hackintosh Buyers Guide](https://dortania.github.io/Anti-Hackintosh-Buyers-Guide/)

This section is mainly used for explaining what boot-args are no longer relevant, it's quite common for users to be still carrying legacy args which have little to no real affect in newer versions of macOS or have little use in OpenCore

This list is based of memory and an annoyed self with seeing these flags keep popping up, got other flags to add then I recommend [opening an issue](https://github.com/khronokernel/Opencore-Vanilla-Desktop-Guide/issues). All help is welcomed!

## macOS flags

**dart=0**: 
* Used for disabling VT-D support
* With Clover, when this flag was present it would also drop your DMAR table from ACPI 
* This flag also requires SIP to be disabled in macOS 10.15 catalina, so with OpenCore this flag is no longer recommended and instead replaced with `Kernel -> Quirks -> DisableIoMapper`

**kext-dev-mode=1**:
* Used for allowing unsigned kexts to be loaded, flag only present in Yosemite
* `CSR_ALLOW_UNSIGNED_KEXTS` bit to be flipped in `csr-active-config` NVRAM variable for newer releases
* This is not needed on OpenCore due to the kernel injection method used: Attatching to the prelinked kernel


## Kexts flags

**nvda_drv=1**: Used for enabling Nvidia's WebDrivers, no longer works in macOS 10.12
* This flag was actually turned into `nvda_drv_vrl=1` for Sierra and High Sierra


## Chameleon flags


For some reason people kept using these flags into Clover which had no effect, and so we really need to stop the train on this one with OpenCore

**PCIRootUID=Value**

* This sets the `_UID` of `Device (PCI0)` to whatever the value is, supposedly needed on legacy AMD GPUs but this is debatable. Ironically Clover still uses this flag but most users know it from Chameleon. [Source](https://github.com/CloverHackyColor/CloverBootloader/blob/81f2b91b1552a4387abaa2c48a210c63d5b6233c/rEFIt_UEFI/Platform/FixBiosDsdt.cpp#L1630-L1674)

**GraphicsEnabler=Yes/No**

* InjectAMD/Nvidia was the Clover equivlant but no feature parity in OpenCore besides running [WhateverGreen](https://github.com/acidanthera/WhateverGreen)

**IGPEnabler=Yes/No**

* Same idea as GraphicsEnabler, Clover equivlant is InjectIntel so feature parity would be [WhateverGreen's Framebuffer patching](https://github.com/acidanthera/WhateverGreen/blob/master/Manual/FAQ.IntelHD.en.md)

