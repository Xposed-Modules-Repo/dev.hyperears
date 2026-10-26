# HyperEars

[源码仓库][source] · [English][english] · [兼容性][compatibility] ·
[安装指南][installation] · [问题排查][troubleshooting]

[source]: https://github.com/silverpoetry/HyperEars
[english]: https://github.com/silverpoetry/HyperEars/blob/main/README_EN.md
[compatibility]: https://github.com/silverpoetry/HyperEars/blob/main/docs/compatibility.md
[installation]: https://github.com/silverpoetry/HyperEars/blob/main/docs/installation.md
[troubleshooting]: https://github.com/silverpoetry/HyperEars/blob/main/docs/troubleshooting.md

HyperEars 是面向 Xiaomi HyperOS 的 LSPosed 模块。它为受支持的第三方蓝牙耳机补充
MiLink 融合设备中心所需的设备身份、能力和运行状态，使这些设备能够复用系统原生的
耳机卡片、设备流转和音量控制流程。

模块不替换 Android 的 A2DP/HFP 音频链路，不接管配对、通话或音频路由。私有控制通道
仅由确实需要协议遥测的设备会话建立。

## 功能

- 将受支持的第三方耳机发布为 MiLink 耳机设备，复用系统设备流转和音量控制。
- 按实际适配能力提供整机、左右耳和充电盒电量；标准耳机回退到 Android 系统电量。
- 将已确认的厂商私有协议映射为系统卡片支持的降噪、关闭、通透和型号专属模式。
- 兼容旧版 HyperOS 与 HyperOS 4 的原生三态耳机卡片；抗风噪按型号使用降噪分支开关，
  或复用该型号不适用的原生模式槽位。
- 按用户选择从耳机卡片打开真实蓝牙设备详情、对应厂商控制 App 或 HyperEars；厂商 App
  不可用时回退到系统设备详情。
- 在应用内展示蓝牙会话、协议确认和 MiLink 发布生命周期，便于定位兼容问题；兼容性页面可按
  品牌、型号、电量与噪声能力搜索。
- 应用提供 Miuix 与 Material 3 两套可切换界面；没有已保存界面偏好时默认使用 Miuix。
  Miuix 可配置明暗模式、界面缩放、菜单栏模糊和悬浮菜单栏，Material 3 保持系统动态配色
  与标准底栏。
- FitClip Ultra 在协议确认后使用 MiLink 原生模式卡片提供“标准模式 / 游戏模式”，
  不会因此声明耳机具有降噪能力。
- FitBuds Turbo 在协议确认后提供四态降噪与游戏模式，抗风噪和游戏开关分别按有效状态显示。
- 漫步者 TWS 耳电量零值显示为未知；双耳不可用时继续更新盒电量并清除旧耳电量。

## 连接提示与电量通知

从 3.0.0 起，可复用系统原生的耳机连接展示：

- “连接时显示”提供智能、仅弹窗、仅超级岛、关闭。默认智能，按系统判断在桌面／外屏桌面
  显示电量弹窗，在应用内显示连接超级岛。
- 弹窗显示左右耳、充电盒或头戴式整机电量；已知盒电量无需充电即可显示，未知电量及时清除。
  充电标志保持耳机实际回报，图片使用模块提供的通用资源。
- 超级岛显示已知的左右耳或整机电量，使用系统原生动画和显示时长；当前没有充电盒区域。
  只有系统整机电量的 TWS 暂不使用头戴式模板。
- “耳机电量通知”独立控制，默认关闭；通知随状态更新，可打开设备详情或执行原生断开操作。
  清理通知后，后续状态回报可以重新发布。

这些功能需在 LSPosed 中勾选 `com.xiaomi.bluetooth`，升级或调整作用域后重启设备。
实机基线为 HyperOS 4，已验证 Bose QuietComfort Headphones 和 vivo TWS Air3 Pro 的对应展示；
其他系统版本按宿主入口兼容情况启用。首次配对和仅开盖发现继续由系统及设备原有支持决定。

## 兼容性概览

| 品牌或范围 | 当前适配范围 |
|---|---|
| vivo / iQOO | TWS Air3 Pro 实机验证；TWS 3e 公开实现；其他已登记型号使用家族协议确认 |
| OPPO Enco | Air2 Pro、Free4、X3、Air5 参考协议；其他 Enco 型号使用家族协议确认 |
| Technics EAH-AZ | AZ80 实机验证：组件电量、三态控制；其他 EAH-AZ TWS 公开协议探测 |
| StarRing / 籁特易耳 | Ultra 实机验证；其他型号使用标准耳机回退 |
| Bose | QuietComfort Headphones 实机验证；已登记 BMAP 产品按产品身份和控制方言细化 |
| Edifier / 漫步者 | W860NB PRO、花再 Evo Pro、FitClip Ultra 实机验证；FitBuds Turbo 原版实测、维护布局待复核；其他已登记型号协议探测 |
| ROSESELSA / 弱水时砂 | 琉璃 X / Ultra、Furina 实机验证；其他已登记产品公开实现或协议探测 |
| NiceHCK / YuanDao | OriG in 公开实现；其他型号使用标准耳机回退 |
| MOONDROP / 水月雨 | Robin 公开协议；Pudding 实机验证；MIRAGE 原版部分实测、维护版待复核；提供合法响应确认的电量与三态控制，其他型号标准回退 |
| 荣耀 | X5s Pro 实机验证；协议确认后提供组件电量和降噪、关闭、通透 |
| 华为 | 5i、Pro 3、FreeClip 2 实机验证；FreeClip 2 仅组件电量；其他型号公开实现或家族探测 |
| QCY | Crossky C50S 公开协议；同协议家族进行协议确认，其他型号使用标准耳机回退 |
| Sony | WF-1000XM6、WH-1000XM4 实机验证；其他已登记型号公开实现或家族探测 |
| Samsung Galaxy Buds | Buds2 Pro 原版实测、维护版待复核；其他已登记型号参考实现或家族外推，按有效回报开放组件电量、噪声与触控设置 |
| 其他标准 A2DP/HFP 耳机 | 设备流转、系统音量和 Android 整机电量回退 |

“公开实现”“参考协议”和“家族确认”不等于 HyperEars 实机验证。家族适配器默认不
开放未经确认的私有写能力；只有收到合法协议响应后，才向 MiLink 发布对应电量或噪声
控制能力。具体型号、证据等级、判型条件和开放能力以
[完整兼容性矩阵](https://github.com/silverpoetry/HyperEars/blob/main/docs/compatibility.md)为准。

FitBuds Turbo、MIRAGE 与 Buds2 Pro 的原版实测记录保留，维护版实机复核继续跟踪。
新版 Bose 卡片修复已通过布局与模式映射测试，实际显示和点击仍待设备复核。
Space Travel 2 Ultra 目前只补充家族名称识别，提供系统整机电量、音量和流转。

## 系统要求

- Xiaomi HyperOS，Android 15 或更高版本；
- root 与可正常工作的 LSPosed，API 101 或更高版本；
- 目标耳机已通过系统蓝牙完成配对；
- 使用原生连接展示或电量通知时，勾选 `com.xiaomi.bluetooth` 作用域；
- LSPosed 必选作用域为 `com.android.bluetooth` 和 `com.milink.service`；如需厂商 App
  运行时退避，再按[控制 App 目录](https://github.com/silverpoetry/HyperEars/blob/main/docs/control-apps.md)
  勾选实际使用的厂商应用。

公开测试范围包含 HyperOS 4 的 MiLink 原生三态卡片。AOSP、MIUI、非小米 ROM 和
Android 15 以下系统不在当前公开测试范围内。

## 安装

1. 从本仓库的 [Releases](https://github.com/Xposed-Modules-Repo/dev.hyperears/releases)
   下载 APK。
2. 安装 APK，在 LSPosed 中启用 HyperEars。
3. 确认核心作用域为 `com.android.bluetooth` 和 `com.milink.service`；使用原生连接展示或
   电量通知时，另外勾选 `com.xiaomi.bluetooth`。
4. 重启设备，使各作用域加载新版模块；仅切换蓝牙开关不保证系统蓝牙扩展重新加载。
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
发布。本项目与 Xiaomi、vivo、iQOO、OPPO、Panasonic、Technics、Bose、Edifier、
ROSESELSA、NiceHCK、MOONDROP、荣耀、华为、QCY、Sony、Samsung 及相关品牌无关；商标和产品名称
仅用于兼容性描述。

协议研究所参考项目及其许可证见主仓库的
[第三方声明](https://github.com/silverpoetry/HyperEars/blob/main/THIRD_PARTY_NOTICES.md)。
