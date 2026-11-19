# ASUS FX608JMR Hackintosh EFI（OpenCore）

适用于 ASUS TUF Gaming F16（FX608JMR 系列，Intel Core i7-14650HX / Raptor Lake-HX）的 OpenCore EFI。
作者机器已验证 macOS 15.8.3 (24H241)，SMBIOS 使用 `MacBookPro16,2`。

> **⚠️ 使用前必读：本仓库的 SMBIOS 身份信息已被清空**
>
> `EFI/OC/config.plist` 中 `PlatformInfo > Generic` 的 `SystemSerialNumber`、`MLB`、`SystemUUID` 为空字符串，`ROM` 为 6 个零字节。
>
> 请先用 [GenSMBIOS](https://github.com/corpnewt/GenSMBIOS) 选择 `MacBookPro16,2` 生成你自己的三码并填入，否则 iCloud / iMessage / App Store 等功能无法正常使用。
>
> **不要使用别人公开的序列号，也不要把你填入后的 config 提交到公开仓库。**

## 验证环境

| 项目 | 配置 |
|---|---|
| 机型 | ASUS TUF Gaming F16（FX608JMR） |
| CPU | Intel Core i7-14650HX（Raptor Lake-HX） |
| 核显 | Intel Iris Xe（PCI `0xA78B`），由 WhateverXe 驱动 |
| 独显 | NVIDIA（不受支持，未禁用） |
| 屏幕 | 2560x1600 165Hz eDP（CSOT） |
| 无线 | Realtek Wi-Fi + Bluetooth（AirportRTW / RealtekBluetoothFirmware） |
| 有线 | Realtek RTL8111 |
| 音频 | AppleALC，`layout-id = 14` |
| 触控板 | VoodooI2C / VoodooI2CHID |
| 键盘 | VoodooPS2 |
| 电源 | VirtualSMC + SMCBatteryManager（电池可读） |
| SMBIOS | `MacBookPro16,2` |
| macOS | 15.8.3 (24H241) |
| OpenCore | 1.0.9（REL-109-2026-11-08） |

> 表中列出的是作者机器配置与 EFI 内包含的驱动组件；具体功能（Wi-Fi、蓝牙、音频等）请以你的实机测试为准。

## 使用步骤

1. 备份你现有的 ESP / EFI 目录。
2. 用 GenSMBIOS 生成 `MacBookPro16,2` 的 `SystemSerialNumber`、`MLB`、`SystemUUID`、`ROM`，填入 `EFI/OC/config.plist` 的 `PlatformInfo > Generic`。
3. 将本仓库的 `EFI` 目录放到 ESP 的 `EFI` 下。`EFI` 目录只包含 `EFI/Boot` 和 `EFI/OC`，**不会**覆盖 Windows 的 `EFI/Microsoft`。
4. BIOS 建议设置：
   - 关闭 **Secure Boot**（必要时清除 Secure Boot 密钥）
   - 本 EFI 已使用 `DisableIoMapper = true` 和 `AppleXcpmCfgLock = true`，VT-d / CFG Lock 可保持 BIOS 默认
   - `SecureBootModel` 为 `Disabled`、`Vault` 为 `Optional`
   - Above 4G Decoding、Resizable BAR 等请与你的硬件实际状态匹配
5. 首次启动建议临时打开调试日志（见下文），确认无报错后再关闭。

开机时按 **空格** 可以显示被 `HideAuxiliary` 隐藏的 Recovery 等辅助项。

## 目录结构

```
EFI/
├── Boot/
│   └── BOOTx64.efi          # OpenCore 引导入口
└── OC/
    ├── ACPI/                # SSDT（EC、GPI0、PNLF、USB 等）
    ├── Drivers/             # HfsPlus / OpenCanopy / OpenRuntime / ResetNvramEntry
    ├── Kexts/               # 驱动
    ├── Resources/           # OpenCanopy 主题
    ├── OpenCore.efi
    └── config.plist         # 已清空 SMBIOS，关闭调试日志
LICENSES/
├── WhateverXe-LICENSE.txt   # WhateverXe 的许可文本（源自 WhateverGreen）
└── NOTICE.md                # 各组件来源与许可说明
```

本仓库**不包含**：Windows 的 `EFI/Microsoft`、BIOS 固件 dump、OpenCore 调试日志、`config.plist` 的历史备份。

## 已知问题

- **USB-A 口按 USB2 速率工作**：EFI 内自带 `USBToolBox.kext` + `UTBMap.kext`（映射 HS02–HS14、SS03–SS04），但默认**禁用**，因此 macOS 只枚举出 15 个 USB2 端口。要启用 USB3：
  1. 在 `Kernel > Add` 中启用 `USBToolBox.kext` 和 `UTBMap.kext`；
  2. 把 `UTBMap.kext/Contents/Info.plist` 中的 `IONameMatch` 从 `XHC` 改为 `XHCI`（本机 ACPI 控制器名为 `XHCI`）；
  3. 重启后确认 `SS03/SS04` 出现。
  雷雳/USB4 控制器上的 `SS01` 端口不受此影响。
- **睡眠可能失败**：本平台出现过 `Sleep Wake failure in EFI`（0x1F）。作者机器上通过 `pmset` 关闭了空闲睡眠，这不在 EFI 内；合盖睡眠请自行测试，若失败需要进一步 ACPI/电源管理调试。
- **独显未禁用**：本 WhateverXe 分支不支持 `-wegnoegpu`，NVIDIA 独显仍供电。需要省电可自行添加 ACPI `_OFF` SSDT 或在 BIOS 中限制为仅核显。
- **HiDPI 覆盖文件不在 EFI 内**：高分辨率内屏若需要 HiDPI，需另外安装 `/Library/Displays/Contents/Resources/Overrides/` 下的覆盖文件。
- **关机 panic（本机特有）**：作者机器上另有一块 Intel 128G NVMe，关机时会触发 `nvme: PCI link down. Delete IO submission queue` panic；移除该盘后解决。若你有类似加装盘，可参考这一点排查。
- **boot-args 含 `-wex-*`**：这些参数仅适用于本仓库使用的 WhateverXe 分支，不要与其他 WhateverGreen 构建混用。

## 调试

发布版默认关闭所有日志：

- `Misc > Debug > Target = 0`
- `Misc > Debug > DisplayLevel = 0`
- `Misc > Debug > AppleDebug / ApplePanic / SysReport = false`
- `Misc > Security > ExposeSensitiveData = 0`

排错时可临时改为：

- `Target = 0x4F`（79）
- `DisplayLevel = 0x80000002`（2147483650）
- `AppleDebug = true`

日志会写到 ESP 根目录的 `opencore-*.txt`。**发布前记得改回去并删除日志**（日志可能包含启动路径、设备 UUID 等信息）。

## 许可与致谢

各 kext / 工具版权归原作者所有，详细来源与许可见 [`LICENSES/NOTICE.md`](LICENSES/NOTICE.md)。

- WhateverXe：基于 [WhateverGreen](https://github.com/acidanthera/WhateverGreen) 的定制分支，版权声明与许可全文见 `LICENSES/WhateverXe-LICENSE.txt`
- OpenCore、Lilu、VirtualSMC、AppleALC、NVMeFix、VoodooPS2 等：acidanthera 项目
- VoodooI2C / VoodooI2CHID：VoodooI2C 项目
- USBToolBox / UTBMap / UTBDefault：USBToolBox 项目
- 其余驱动请见 `NOTICE.md`

若上游项目使用 GPL 等 copyleft 许可，分发二进制时请同时提供对应源码的获取方式。

## 免责声明

本项目仅供学习与技术研究使用。在非 Apple 硬件上运行 macOS 违反 Apple 的最终用户许可协议（EULA）；使用本 EFI 造成的任何数据丢失、硬件损坏或账号问题由使用者自行承担，作者不提供任何担保。请勿将本项目用于商业用途，也不要分发任何 Apple 的受版权保护文件。
