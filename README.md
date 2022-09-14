# Hackintosh for MSI MAG B660M MORTAR WIFI DDR4
MSI MAG B660M MORTAR DDR4 is also supported.

[简体中文](/README_zh.md)

## My Configurations
| Components | Model |
| --- | --- |
| CPU | Intel i7 12700 |
| Motherboard | MSI MAG B660M MORTAR WIFI DDR4 |
| Graphics cards | GIGABYTE AORUS Radeon™ RX 6800 XT MASTER 16G |
| Memory | Crucial Ballistix DDR4 3200 16Gx2 |
| Wifi and Bluetooth card | fenvi T919(BCM94360CD) |
| Drive | Samsung PM9A1 2TB for `Windows`, Kioxia RC20 for `macOS`　|
| OpenCore version | 0.8.4 |
| macOS version | macOS Monterey 12.6 (21G115) |

## What's working?
1. Almost All.

## What's not working?
1. For WIFI montherboard, due the firmware upload problem of the new batches AX201(bluetooth 5.2) in macOS Monterey, onboard bluetooth was not working. I also don't want to add the corresponding wifi driver, it is recommended to use official apple wifi card.

⚠️:
* OpenIntelWireless's [IntelBluetoothFirmware driver](https://github.com/OpenIntelWireless/IntelBluetoothFirmware) has been able to support the Bluetooth of the new batches of AX201 in Monterey. You can add kext by yourself.
* Sidecar doesn't working due to the internal graphic card of the 12th gen intel CPU was unable to drive. 

## Changelog
### 2022-09-14
1. Update opencore to version 0.8.4.

### 2022-08-15
1. Update opencore to version 0.8.3.
2. Update Lilu.kext to V1.6.2.
3. Update AppleALC.kext to V1.7.4.
4. Update WhateverGreen.kext to V1.6.1.
5. Add CpuTopologyRebuild.kext to recognize i7-12700 as 8C20T processor, which should have better single-thread performance, more details: [About CpuTopogyRebuild.kext](https://github.com/lyq1996/MSI-B660M-MORTAR-WIFI_Hackintosh_12700_6800XT#about-cputopologyrebuildkext-important).

### 2022-07-21
1. Update opencore to version 0.8.2.
2. Update kexts.
3. Enable 256MB GPU resize bar for better perference. Note: If you has sleep issues with large BARs, change ResizeAppleGpuBars to 0, it will set resize bar to 1MB.
4. Default enable SMCRadeonGPU.kext and RadeonSensor.kext for monitoring radeon GPU temperature on macOS.
5. Add missing default themes.

## BIOS Settings
### Disable
1. Secure Boot `[Required]`
2. Intel CFG lock `[Required]`
3. Fast Boot `[Optional]`

### Enable
1. Re-Size Bar Support `[Required]`
2. USB wake up from s3/s4/s5 `[Optional] used for wake up from sleep using USB HID device.`
3. ERP Ready `[Optional] used for wake up from sleep using USB HID device.`
4. SR-IOV `[Optional]`


## Generate your PlatformInfo `[!!!Important!!!]`
To use this EFI, fllowing this guide: [using-gensmbios](https://dortania.github.io/OpenCore-Post-Install/universal/iservices.html#using-gensmbios) to generate platform info and filling info into opencore config `PlatformInfo - Generic`.

## About CpuTopologyRebuild.kext `[!!!Important!!!]`
This kext spoof e-core as logical core of p-core, which increases the chance of p-core being scheduled in 12-gen heterogeneous CPUs, resulting in better single-thread performance(the chance of p-core being scheduled at 8C20T is greater than that at 20C20T). In virutal machine, multi-thread performance was also increased. 

So, If your CPU is not 12600(f/k/kf)/12700(f/k/kf)/12900(f/k/kf), remove this kext and remove the `-ctrsmt` boot args!!!

## Support for other AMD GPUs
This EFI supports 6000 Series AMD GPUs.

Changes are required to support 5000 series and below. See [AMD GPUs #](https://dortania.github.io/GPU-Buyers-Guide/modern-gpus/amd-gpu.html#amd-gpus) for more details.

## About USB map
Maybe you need to make some changes to suit your USB and case, current usb mapping is from [yzchan](https://github.com/yzchan/MSI-MAG-B660M-MORTAR-DDR4-12600K-EFI/blob/master/USB%E5%AE%9A%E5%88%B6.md)(thanks yzchan for saved us a lot of time).

## Disbale NVMe drive
If you want to disable a NVMe drive that doesn't support hackintosh, just enable `ACPI - Add - SSDT-DNVMe.aml`, it will disable the NVMe drive which inserted into M.2 slot 1. 

You can also change `_SB_.PC00.PEG0.PEGP` to disable other NVMe drive or PCIE devices. See: [fixing-nvme](https://dortania.github.io/OpenCore-Post-Install/universal/sleep.html#fixing-nvme) for more details

```
DefinitionBlock ("", "SSDT", 2, "DRTNIA", "spoof", 0x00000000)
{
    External (_SB_.PC00.PEG0.PEGP, DeviceObj)

    Method (_SB.PC00.PEG0.PEGP._DSM, 4, NotSerialized)  // _DSM: Device-Specific Method
    {
        If ((!Arg2 || !_OSI ("Darwin")))
        {
            Return (Buffer (One)
            {
                 0x03                                             // .
            })
        }

        Return (Package (0x0A)
        {
            "name", 
            Buffer (0x09)
            {
                "#display"
            }, 

            "IOName", 
            "#display", 
            "class-code", 
            Buffer (0x04)
            {
                 0xFF, 0xFF, 0xFF, 0xFF                           // ....
            }, 

            "vendor-id", 
            Buffer (0x04)
            {
                 0xFF, 0xFF, 0x00, 0x00                           // ....
            }, 

            "device-id", 
            Buffer (0x04)
            {
                 0xFF, 0xFF, 0x00, 0x00                           // ....
            }
        })
    }
}
```

## Disable verbose 
1. Set `Misc - Debug -  AppleDebug`、`Disable WatchDog`、 `ApplePanic` to false.
2. Change `-v debug=0x100 keepsyms=1` in `boot-args`.

## References
1. https://github.com/yzchan/MSI-MAG-B660M-MORTAR-DDR4-12600K-EFI
2. https://github.com/alyxferrari/OpenCore-Install-Guide/blob/alderlake/config.plist/alder-lake.md
3. https://github.com/duxphp/Hackintosh-12700KF-B660M-MORTAR-6600XT
4. https://www.reddit.com/r/hackintosh/comments/sp1zgv/opencore_alder_lake_12thgen_intel_hackintosh/
5. https://github.com/glekner/GIGABYTE-Z690I-Hackintosh
6. https://www.tonymacx86.com/threads/msi-pro-z690-a-ddr4-i7-12700k-amd-rx-580.319149/
