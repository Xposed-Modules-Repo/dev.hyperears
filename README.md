# HyperEars

[源码仓库](https://github.com/silverpoetry/HyperEars) ·
[English](https://github.com/silverpoetry/HyperEars/blob/main/README_EN.md) ·
[兼容性](https://github.com/silverpoetry/HyperEars/blob/main/docs/compatibility.md) ·
[安装指南](https://github.com/silverpoetry/HyperEars/blob/main/docs/installation.md) ·
[问题排查](https://github.com/silverpoetry/HyperEars/blob/main/docs/troubleshooting.md)

HyperEars 是面向 Xiaomi HyperOS 的 LSPosed 模块。它为受支持的第三方蓝牙耳机补充
MiLink 融合设备中心所需的设备身份、能力和运行状态，使这些设备能够复用系统原生的
耳机卡片、设备流转和音量控制流程。

模块不替换 Android 的 A2DP/HFP 音频链路，不接管配对、通话或音频路由。私有控制通道
仅由确实需要协议遥测的设备会话建立。

## 功能

- 将受支持的第三方耳机发布为 MiLink 耳机设备，复用系统设备流转和音量控制。
- 按实际适配能力提供整机、左右耳和充电盒电量；标准耳机回退到 Android 系统电量。
- 将已确认的厂商私有协议映射为系统卡片支持的降噪、关闭、通透和型号专属模式。
- 从耳机卡片打开真实蓝牙设备详情，而不是进入载体型号的厂商设置页。
- 在应用内展示蓝牙会话、协议确认和 MiLink 发布生命周期，便于定位兼容问题。

## 兼容性概览

| 品牌或范围 | 当前适配范围 |
|---|---|
| vivo / iQOO | TWS Air3 Pro 实机验证；TWS 3e 公开实现；其他已登记型号使用家族协议确认 |
| OPPO Enco | Air2 Pro、Free4、X3、Air5 参考协议；其他 Enco 型号使用家族协议确认 |
| StarRing / 籁特易耳 | Ultra 实机验证；其他型号使用标准耳机回退 |
| Bose | QuietComfort Headphones 实机验证；已登记 BMAP 产品按产品身份和控制方言细化 |
| Edifier / 漫步者 | W860NB PRO、花再 Evo Pro 实机验证；W820/W830/W860 产品线及家族协议确认 |
| ROSESELSA / 弱水时砂 | EARFREE i5、BudsFeel MK2 公开实现；相关产品线使用协议确认 |
| NiceHCK / YuanDao | OriG in 公开实现；其他型号使用标准耳机回退 |
| MOONDROP / 水月雨 | Robin 公开协议；协议确认后提供左右耳电量和降噪、关闭、通透 |
| 荣耀 | X5s Pro 实机验证；协议确认后提供组件电量和降噪、关闭、通透 |
| QCY | Crossky C50S 公开协议；同协议家族进行协议确认，其他型号使用标准耳机回退 |
| Sony | 已登记 WH、WF、LinkBuds、CH 和 ULT 型号使用公开协议与家族确认 |
| 其他标准 A2DP/HFP 耳机 | 设备流转、系统音量和 Android 整机电量回退 |

“公开实现”“参考协议”和“家族确认”不等于 HyperEars 实机验证。家族适配器默认不
开放未经确认的私有写能力；只有收到合法协议响应后，才向 MiLink 发布对应电量或噪声
控制能力。具体型号、证据等级、判型条件和开放能力以
[完整兼容性矩阵](https://github.com/silverpoetry/HyperEars/blob/main/docs/compatibility.md)为准。

## 系统要求

- Xiaomi HyperOS，Android 15 或更高版本；
- root 与可正常工作的 LSPosed，API 101 或更高版本；
- 目标耳机已通过系统蓝牙完成配对；
- LSPosed 必选作用域为 `com.android.bluetooth` 和 `com.milink.service`；如需厂商 App
  运行时退避，再按[控制 App 目录](https://github.com/silverpoetry/HyperEars/blob/main/docs/control-apps.md)
  勾选实际使用的厂商应用。

AOSP、MIUI、非小米 ROM 和 Android 15 以下系统不在当前公开测试范围内。

## 安装

1. 从本仓库的 [Releases](https://github.com/Xposed-Modules-Repo/dev.hyperears/releases)
   下载 APK。
2. 安装 APK，在 LSPosed 中启用 HyperEars。
3. 确认作用域为 `com.android.bluetooth` 和 `com.milink.service`。
4. 重启设备，使蓝牙进程和 MiLink 进程同时加载模块。
5. 连接耳机后打开 HyperEars，检查会话中的 Adapter、传输、协议和 MiLink 状态。

首次从早期开发测试包迁移时，如遇签名不一致，应先在 LSPosed 中禁用旧模块、重启、
卸载旧 APK，再安装正式版本并重新启用。完整步骤见
[安装指南](https://github.com/silverpoetry/HyperEars/blob/main/docs/installation.md)。

## 架构与扩展

设备匹配按以下顺序回退：

```text
具体型号 Adapter → 厂商品牌/协议家族 Adapter → Standard Bluetooth Adapter
```

每台已连接耳机拥有独立的有状态 Adapter 和 `ProtocolSession`。Adapter 负责设备身份、
能力、传输策略与运行状态；`ProtocolSession` 保存流式解码、握手、序列号和请求队列；
WireCodec 只负责厂商帧的字节编解码。UI 和 MiLink 桥只消费统一状态快照，不识别具体
品牌实现。

新增型号通常只需扩展具体型号 Adapter 或家族协议配置。需要协议确认的候选设备先执行
只读探测，确认成功后再更新能力或替换为更具体的 Adapter。完整设计见
[系统模块架构](https://github.com/silverpoetry/HyperEars/blob/main/docs/system-module-architecture.md)。

## 仓库与反馈

本仓库由 Xposed Modules Repository 托管，用于 LSPosed 模块索引元数据和 APK Release。
HyperEars 的源码、开发历史、Issue、Pull Request、完整文档和原始 Release 均由
[主仓库](https://github.com/silverpoetry/HyperEars)维护。

新增设备适配应提供可复现的协议证据，并通过主仓库提交 Pull Request。请勿在 Issue、
日志或抓包中公开完整设备地址、账号信息、密钥或其他个人数据。

## 许可与声明

HyperEars 以 [GNU GPL-3.0-only](https://github.com/silverpoetry/HyperEars/blob/main/LICENSE)
发布。本项目与 Xiaomi、vivo、iQOO、OPPO、Bose、Edifier、ROSESELSA、NiceHCK、Sony
及相关品牌无关；商标和产品名称仅用于兼容性描述。

协议研究所参考项目及其许可证见主仓库的
[第三方声明](https://github.com/silverpoetry/HyperEars/blob/main/THIRD_PARTY_NOTICES.md)。
