# Unity MIDI 插件 — 文档索引

本中心文档介绍了 `Assets/MIDI` 目录的内容、运行时 API 的结构，以及如何在 Unity 中使用 MIDI 1.0、MIDI 2.0 (UMP)、MPE 和相关传输协议。

## NotebookLM
我们开发了一款人工智能程序来记录本手册。  
如有任何疑问，请先在此处联系我们。（需要使用谷歌账号。）  
https://notebooklm.google.com/notebook/12f7cab5-0554-4f42-9f26-71f2f3507b1b

## 语言
- [日本語](../ja/index.md)
- [English](../../index.md)

## 目录内容

### 入门指南
- [入门指南（安装、初始化、发送/接收）](getting-started.md)
- [构建后处理与脚本定义符号](build-postprocessing.md)
- [平台与限制](platforms.md)

### 核心 API
- [MIDI 1.0 (MidiManager)](midi1.md)
- [MIDI 2.0 / UMP (Midi2Manager)](midi2.md)
- [MPE (MIDI 多维多音列表达)](mpe.md)
- [SMF / 标准 MIDI 文件 (jp.kshoji.midisystem)](smf.md)

### 传输与集成
- [传输协议与平台](transports.md)
- [应用间 MIDI — 跨平台说明 (Android, iOS/macOS, Linux)](inter-app-midi.md)
- [Maestro / MPTK 集成 (虚拟设备与适配器)](mptk.md)

### 架构与进阶
- [虚拟设备与事件注入](virtual-devices.md)
- [MIDI-CI / 能力协商](midi-ci.md)
- [编辑器与生命周期说明](editor-and-lifecycle.md)
- [嵌入的第三方模块](third-party.md)

### 示例与参考
- [示例项目](samples.md)
- [已测试设备](tested-devices.md)
- [联系与支持](contacts.md)
- [版本历史](version-history.md)

## 目录结构 (Assets/MIDI)

- `Plugins/`  
  各平台的原生（及 WebGL JS）插件（Android/iOS/macOS/Linux/WSA/WebGL）。
- `Scripts/`  
  主要 C# 运行时代码：
    - `MidiManager.cs` (MIDI 1.0)
    - `Midi2Manager.cs` (MIDI 2.0 / UMP)
    - 平台插件：`MidiPlugin.*.cs`, `Midi2Plugin.*.cs`
    - 事件处理器接口：`IMidi*EventHandler`, `IMidi2*EventHandler`
    - MPE：`MpeManager.cs`, `IMpeEventHandler.cs`
    - MIDI-CI：`MidiCapabilityNegotiator.cs`
    - 虚拟设备：`MidiManager.VirtualDevices.cs`
- `Scripts/midisystem/`  
  标准 MIDI 文件 (SMF) 读写器与序列模型（`Sequence`, `Track`, 消息）。
- `Scripts/UmpSequencer/`  
  UMP 序列工具（Clip/容器读写、序列化、SMF↔UMP 转换器）。
- `Scripts/RTP-MIDI-for-.NET/`  
  RTP-MIDI 实现（嵌入式模块及其自带文档）。
- `Scripts/UdpMidi2Discovery/`  
  UDP MIDI 2.0 工作流的发现依赖项。
- `Samples/`  
  示例场景与脚本。

## 概念与术语

- **DeviceId**: API 中用于引用 MIDI 端口的字符串标识符。
- **Group**: MIDI 2.0 组索引 (0–15)。为保持 API 一致性，MIDI 1.0 API 也包含 `group` 参数。
- **Channel**: MIDI 通道 (0–15)。
- **UMP (通用 MIDI 数据包)**: MIDI 2.0 的基本数据格式，在本插件中表示为 `uint[]` 数组。

## 快速入门检查清单

1. 确定所需功能集：
    - MIDI 1.0 事件与发送：`MidiManager`
    - MIDI 2.0 / UMP 解析与发送：`Midi2Manager`
    - Android 应用间 MIDI：参见 [应用间 MIDI](inter-app-midi.md)
    - MPE 管理：在 `MidiManager` 之上使用 `MpeManager`
2. 实现一个或多个事件处理器接口。
3. 向管理器注册处理器对象。
4. 初始化管理器（或确保其存在于场景中）。
5. 使用 `Assets/MIDI/Samples/Scenes` 中的示例场景进行测试。
