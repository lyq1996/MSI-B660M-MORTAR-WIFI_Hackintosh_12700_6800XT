# Hackintosh for MSI MAG B660M MORTAR WIFI DDR4
MSI MAG B660M MORTAR DDR4 is also supported.

## My Configurations
| Components | Model |
| --- | --- |
| CPU | Intel i7 12700|
| Motherboard | MSI MAG B660M MORTAR WIFI DDR4 |
| Graphics cards | GIGABYTE AORUS Radeon™ RX 6800 XT MASTER 16G |
| Memory | Crucial Ballistix DDR4 3200 16Gx2 |
| Wifi and Bluetooth card | fenvi T919(BCM94360CD) |
| Drive | Samsung PM9A1 2TB for Windows, Kioxia RC20 1TB for macOS |
| OpenCore version | 0.8.0 |
| macOS version | macOS Monterey 12.4 |

## BIOS Settings
### Disable
1. Fast Boot
2. Secure Boot
4. Intel CFG lock

### Enable
1. Re-Size Bar Support
2. ERP Ready

## Generate your PlatformInfo
Fllowing this guide: [using-gensmbios](https://dortania.github.io/OpenCore-Post-Install/universal/iservices.html#using-gensmbios) to generate platform info and filling info into opencore config `PlatformInfo - Generic`.

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
1. Set `Misc - Debug -  AppleDebug`、`Disable WatchDog`、 `ApplePanic` to false
2. Change `NVRAM - Add - 7C436110-AB2A-4BBB-A880-FE41995C9F82 - boot-args` to `-wegnoigpu agdpmod=pikera`

## Support for other AMD GPUs
See [AMD GPUs
#](https://dortania.github.io/GPU-Buyers-Guide/modern-gpus/amd-gpu.html#amd-gpus) for more details.

## About USB map
Maybe you need to make some changes to suit your USB and case, current usb mapping is from [yzchan](https://github.com/yzchan/MSI-MAG-B660M-MORTAR-DDR4-12600K-EFI/blob/master/USB%E5%AE%9A%E5%88%B6.md)(thanks yzchan for saved us a lot of time).

## References
1. https://github.com/yzchan/MSI-MAG-B660M-MORTAR-DDR4-12600K-EFI
2. https://github.com/alyxferrari/OpenCore-Install-Guide/blob/alderlake/config.plist/alder-lake.md
3. https://github.com/duxphp/Hackintosh-12700KF-B660M-MORTAR-6600XT
4. https://www.reddit.com/r/hackintosh/comments/sp1zgv/opencore_alder_lake_12thgen_intel_hackintosh/
5. https://github.com/glekner/GIGABYTE-Z690I-Hackintosh
6. https://www.tonymacx86.com/threads/msi-pro-z690-a-ddr4-i7-12700k-amd-rx-580.319149/
