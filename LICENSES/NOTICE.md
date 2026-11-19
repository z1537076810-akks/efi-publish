# 第三方组件与许可说明

本 EFI 中的驱动、引导器与工具版权均归各自作者所有。本仓库只做配置整合与分发，未修改上游代码（`WhateverXe` 为作者的定制分支，见下）。

分发或再发布前，请核对各项目仓库中最新的 LICENSE 文本，并保留 kext 内的版权信息。

## 引导器与工具

| 组件 | 上游项目 | 说明 |
|---|---|---|
| OpenCore、BOOTx64、OpenRuntime、OpenCanopy、HfsPlus、ResetNvramEntry | https://github.com/acidanthera/OpenCorePkg | BSD 类许可，保留版权声明 |

## 驱动（Kexts）

| 组件 | 上游项目 / 说明 |
|---|---|
| Lilu | https://github.com/acidanthera/Lilu |
| VirtualSMC、SMCBatteryManager、SMCProcessor、SMCSuperIO、SMCLightSensor | https://github.com/acidanthera/VirtualSMC |
| WhateverGreen | https://github.com/acidanthera/WhateverGreen |
| WhateverXe | 基于 WhateverGreen 的定制分支（`org.b00t0x.WhateverXe`）；许可全文见 `WhateverXe-LICENSE.txt`，源码请向作者获取 |
| AppleALC | https://github.com/acidanthera/AppleALC |
| NVMeFix | https://github.com/acidanthera/NVMeFix |
| BrightnessKeys | https://github.com/acidanthera/BrightnessKeys |
| RestrictEvents | https://github.com/acidanthera/RestrictEvents |
| BlueToolFixup | https://github.com/acidanthera/BrcmPatchRAM |
| VoodooPS2Controller（含 Keyboard / Input 插件） | https://github.com/acidanthera/VoodooPS2 |
| VoodooI2C、VoodooI2CHID | https://github.com/VoodooI2C/VoodooI2C |
| USBToolBox、UTBMap、UTBDefault | https://github.com/USBToolBox/kext |
| RealtekRTL8111 | https://github.com/Mieze/RTL8111_driver_for_OS_X |
| CpuTopologyRebuild | 上游项目；请核对作者仓库的许可 |
| ForgedInvariant | 上游项目；请核对作者仓库的许可 |
| AirportRTW | Realtek Wi-Fi 驱动；请核对上游项目许可 |
| RealtekBluetoothFirmware | Realtek 蓝牙固件注入；请核对上游项目许可 |
| XHCI-unsupported | 上游项目；请核对作者仓库的许可 |
| AMFIAnchor、AMFIAnchorInjector | 本地辅助 kext；来源与许可请向本仓库作者确认后再分发 |

## 备注

- 部分上游项目（例如 VoodooI2C、VoodooPS2 等）可能采用 GPL 等 copyleft 许可。分发二进制时，请同时提供对应版本源码的获取方式（上游仓库链接或随附源码）。
- 本仓库不包含任何 Apple 版权文件（安装镜像、Firmware、系统 kext 等）。
- BIOS 固件 dump、Windows 启动管理器、OpenCore 调试日志均未包含在本仓库中。
