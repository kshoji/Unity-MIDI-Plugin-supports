# Maestro / MPTK 集成（虚拟设备与适配器）

本页面介绍了可选的 **Maestro / MidiPlayerTK (MPTK)** 集成层。

它提供了两种高级工作流：

1. **将 MPTK 作为虚拟 MIDI 输出设备 (Sink)**  
   将 `MidiManager` 的事件路由到 MPTK 合成器中（实时播放）。

2. **将 MPTK 作为虚拟 MIDI 输入设备 (Source)**  
   获取 MPTK 的回调（来自 `MidiFilePlayer` 或 `MidiStreamPlayer`），并将其作为等效的 MIDI 事件**注入**到 `MidiManager` 中，就像它们来自真实设备一样。

> 术语说明：在本插件中，“虚拟设备”是指在 `MidiManager` 的设备列表和事件管线中参与运行的软件终端，它不涉及平台的原生 MIDI 后端。

<div class="page" />

## 启用 / 禁用（编译时）

该集成由一个脚本定义符号控制：

- `FEATURE_USE_MPTK`

当该定义**不存在**（或未安装 MPTK）时，集成脚本会编译为空桩 (Stubs)，从而确保项目仍能正常构建。

### 如何启用

在 Unity 中：

- **Project Settings → Player → Other Settings → Script Compilation → Scripting Define Symbols**
- 添加：`FEATURE_USE_MPTK`

<div class="page" />

## 包含的内容

### 虚拟设备注册与输入注入

`MidiManager` 为软件终端提供了辅助 API：

- 注册/注销虚拟设备 ID（作为输入、输出或两者兼有）
- 直接向内部管线注入 MIDI 事件（例如“模拟此设备发送了一个 Note On”等）

当您想要模拟设备、桥接其他系统或对处理器进行单元测试时，可以使用这些 API。

### MPTK 虚拟 MIDI 设备 (Sink)

`MptkMidiDevice` 实现了 MIDI 事件处理器接口，并将事件转发到内部的 MPTK `MidiStreamPlayer`。

在以下场景中使用它：

- `MidiManager` → MPTK 合成器播放
- 创建一个与真实设备并列显示的“软件输出 deviceId”

### MPTK → MidiManager 适配器 (Source)

`MptkMidiManagerInputAdapter` 可以订阅 MPTK 回调事件（通常由 MPTK 播放器暴露），并将匹配的 MIDI 1.0 风格事件注入到 `MidiManager` 中。

在以下场景中使用它：

- 使用 MPTK 播放/实时生成的内容来驱动您现有的 `MidiManager` 处理器
- 让 MPTK 表现得像一个虚拟的*输入*设备

### 便捷工具

`MptkUtility` 封装了常用的设置逻辑：

- 确保 MPTK 全局变量存在
- 创建 MPTK 播放器
- 创建并注册虚拟设备
- 创建适配器
- 提供了将 MIDI 事件作为虚拟输入注入的辅助方法

<div class="page" />

## 示例代码（开箱即用）

这些示例位于 `Assets/MIDI/Samples/DocumentationExamples/` 目录下：

- **MIDI 1.0 → MPTK (虚拟输出 Sink)**  
  `Assets/MIDI/Samples/DocumentationExamples/MptkVirtualOutputSinkExample.cs`

- **MPTK → MIDI 1.0 (作为虚拟输入注入到 MidiManager)**  
  `Assets/MIDI/Samples/DocumentationExamples/MptkToMidiManagerInputExample.cs`

<div class="page" />

## 示例场景（已集成）

内置的示例场景也包含了可选的 MPTK 集成：

- MIDI 1.0 示例场景脚本：  
  `Assets/MIDI/Samples/Scripts/MidiSampleScene.cs`  
  （添加了一个开关，用于使用基于 MPTK 的虚拟输出设备）

- MIDI 2.0 示例场景脚本：  
  `Assets/MIDI/Samples/Scripts/Midi2SampleScene.cs`  
  （添加了一个开关，用于将 MIDI 2.0 发送的内容*镜像*到 MPTK MIDI 1.0 虚拟 Sink 中）

<div class="page" />

## ID 选择、组与路由

**推荐规范**：

- 使用带有明显虚拟前缀的名称（例如 `mptk:internal`, `virtual:sequencer`, `test:device`）。
- 除非您有意模拟多个组，否则请使用 `group = 0`。

由于虚拟设备会出现在设备集合中，您可以构建 UI 让用户像选择硬件设备一样选择它。

<div class="page" />

## 故障排除

### “在编辑器中可以编译，但在 CI 或其他机器上失败”
- 确保仅在项目中确实存在 MPTK 资源时才启用 `FEATURE_USE_MPTK`。

### “未接收到事件”
- 确认虚拟设备已注册为**输入 (Input)**（这样注入的事件才会被视为来自输入设备）。
- 确认您的处理器已在 `MidiManager` 中注册。
- 确认 `deviceId` 和 `group` 与您预期的匹配。

### “MPTK Sink 没有声音”
- 确认虚拟设备已注册为**输出 (Output)**。
- 确认 Sink 的 `deviceId` 与您发送消息的目标设备一致。
- 确认项目中的 MPTK 全局设置/资源（SoundFont / 配置）是有效的。

<div class="page" />

## 相关文档

- [MIDI 1.0 (MidiManager)](midi1.md)
- [示例项目](samples.md)
