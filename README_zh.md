# 微星B660M迫击炮WiFi黑苹果EFI
同样也支持B660M迫击炮。

[English](/README.md)

## 我的配置
| 组件 | 型号 |
| --- | --- |
| CPU | i7-12700 |
| 主办 | 微星B660M迫击炮Wi-Fi DDR4 |
| 显卡 | 技嘉6800XT 超级雕 |
| 内存 | 英睿达铂胜DDR4 3200MHz 16GBx2 |
| Wi-Fi与蓝牙 | fenvi T919(BCM94360CD白果拆机卡) |
| 硬盘 | 三星PM9A1 2TB(`Windows`), 凯侠RC20(`macOS`)　|
| OpenCore版本 | 0.8.5 |
| macOS版本 | macOS Monterey 12.6 (21G115) |

## 哪些东西工作?
1. 几乎所有

## 哪些东西不工作?
1. 对于B660M迫击炮Wi-Fi版，由于新版AX201(蓝牙5.2)在macOS Monterey存在固件上传问题，故板载蓝牙无法正常工作，板载Wi-Fi也就没有放置对应驱动。推荐使用白果免驱卡。

注意⚠️：
* 截止2022年9月14日，OpenIntelWireless的[IntelBluetoothFirmware驱动](https://github.com/OpenIntelWireless/IntelBluetoothFirmware)已能够在Monterey支持新版AX201的蓝牙，如有需要，可自行添加kext。
* 由于12代cpu的核显无法正常驱动，随航不可用。

## 更新日志
### 2022-10-26
1. 更新opencore版本到0.8.5。
2. 更新AppleALC.kext版本到1.7.5。

### 2022-09-14
1. 更新opencore版本到0.8.4。

### 2022-08-15
1. 更新opencore版本到0.8.3。
2. 更新Lilu.kext版本到V1.6.2.
3. 更新AppleALC.kext版本到V1.7.4。
4. 更新WhateverGreen.kext版本到V1.6.1。
5. 增加CpuTopologyRebuild.kext，可以将i7-12700视为8C20T，应该可以增加单线程跑分性能，详见[关于CpuTopologyRebuild.kext](https://github.com/lyq1996/MSI-B660M-MORTAR-WIFI_Hackintosh_12700_6800XT/blob/main/README_zh.md#%E5%85%B3%E4%BA%8Ecputopologyrebuildkext%E9%87%8D%E8%A6%81)。

### 2022-07-21
1. 更新opencore版本到version 0.8.2.
2. 更新kexts。
3. 开启256MB的GPU resize bar以提升性能。如果遇到性能问题，请将ResizeAppleGpuBars设置为0，这将把resize bar设置到1MB。
4. 默认启用SMCRadeonGPU.kext和RadeonSensor.kext，可以在macOS上面监控radeon显卡的温度。等到macOS 13推送之后，则不再需要。
5. 添加之前没有添加的几个默认主题。

## BIOS的相关设置
### 关闭项
1. 安全启动`【必须】`
2. CFG lock`【必须】`
3. 快速启动`【可选】`

### 启用
1. Re-Size Bar`【必须】`
2. USB从s3/s4/s5唤醒 `【可选】如果需要从睡眠中通过USB HID设备唤醒的话`
3. ERP Ready  `【可选】如果需要从睡眠中通过USB HID设备唤醒的话`
4. SR-IOV `【可选】`

## 生成你的PlatformInfo`【！！！重要！！！】`
使用这个EFI前需要遵循这个指南：[using-gensmbios](https://dortania.github.io/OpenCore-Post-Install/universal/iservices.html#using-gensmbios)，生成PlatformInfo，然后在opencore配置文件中的`PlatformInfo - Generic`填入。

## 关于CpuTopologyRebuild.kext`【！！！重要！！！】`
这个kext将e-core视为p-core的一个逻辑核心。(推测)在12代异构cpu调度时，提高了p-core的调度机会，带来了单线程的更高性能（因为8C20T时大核心被调度的几率，大于20C20T时的大核心被调度几率）。同样，在虚拟机中，在p-core上调度的几率也会变大，因此虚拟机多核心跑分也会更高。在多线程cpu全吃满时，性能不变。  

所以，如果你的cpu不是12600(f/k/kf)/12700(f/k/kf)/12900(f/k/kf)，关闭这个kext，并且从boot args中移除`-ctrsmt`。

## 关于其他AMD显卡的支持
这个EFI无需修改，支持AMD 6000系列显卡。

对于5000系显卡以及以下，需要做一些小改动，参见[AMD GPUs #](https://dortania.github.io/GPU-Buyers-Guide/modern-gpus/amd-gpu.html#amd-gpus)。

## 关于USB映射
也许你需要做一些小修改才能够适配你的机箱，目前的usb映射来自于[yzchan](https://github.com/yzchan/MSI-MAG-B660M-MORTAR-DDR4-12600K-EFI/blob/master/USB%E5%AE%9A%E5%88%B6.md)(感谢yzchan给我们节省了大量时间)。

## 屏蔽其他NVMe硬盘
如果你需要屏蔽一些不支持黑苹果的硬盘，只要打开`ACPI - Add - SSDT-DNVMe.aml`，这会屏蔽插在第一个M.2槽(靠近cpu)的NVMe协议硬盘。

同样，你也可以修改`_SB_.PC00.PEG0.PEGP`，去屏蔽其他NVMe硬盘或者PCIE设备。参见[fixing-nvme](https://dortania.github.io/OpenCore-Post-Install/universal/sleep.html#fixing-nvme)。

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

## 关闭啰嗦模式
1. 关闭`Misc - Debug -  AppleDebug`、`Disable WatchDog`、 `ApplePanic`。
2. 移除`boot-args`中的`-v debug=0x100 keepsyms=1`。

## References
1. https://github.com/yzchan/MSI-MAG-B660M-MORTAR-DDR4-12600K-EFI
2. https://github.com/alyxferrari/OpenCore-Install-Guide/blob/alderlake/config.plist/alder-lake.md
3. https://github.com/duxphp/Hackintosh-12700KF-B660M-MORTAR-6600XT
4. https://www.reddit.com/r/hackintosh/comments/sp1zgv/opencore_alder_lake_12thgen_intel_hackintosh/
5. https://github.com/glekner/GIGABYTE-Z690I-Hackintosh
6. https://www.tonymacx86.com/threads/msi-pro-z690-a-ddr4-i7-12700k-amd-rx-580.319149/