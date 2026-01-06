# 示例项目

本页面介绍了位于以下目录中的示例内容：

- `Assets/MIDI/Samples/`

<div class="page" />

## 场景 (Scenes)

位置：`Assets/MIDI/Samples/Scenes/`

- `MidiSampleScene.unity`  
  演示了通过 `MidiManager` 和 MIDI 1.0 事件处理器接口实现的 MIDI 1.0 接收/发送工作流。  
  包含一个可选开关，用于将输出路由到基于 MPTK 的虚拟输出设备（如果已启用该功能）。

- `Midi2SampleScene.unity`  
  演示了通过 `Midi2Manager` 和 MIDI 2.0 处理器接口实现的 MIDI 2.0 / UMP 工作流。  
  包含一个可选开关，用于将 MIDI 2.0 发送的内容镜像到 MPTK MIDI 1.0 虚拟 Sink 中（如果已启用该功能）。

<div class="page" />

## 脚本 (Scripts)

位置：`Assets/MIDI/Samples/Scripts/`

- `MidiSampleScene.cs`  
  演示脚本中的典型职责：
  - 初始化 MIDI
  - 注册处理器对象
  - 响应传入的消息（例如：记录 note on/off 日志）
  - 发送测试消息
  - （可选）通过虚拟设备将输出路由到 MPTK

- `Midi2SampleScene.cs`  
  演示脚本中的典型职责：
  - 初始化 MIDI 2.0
  - 注册 UMP 处理器
  - 显示解码后的事件
  - 发送 UMP 示例消息
  - （可选）通过 MIDI 1.0 虚拟 Sink 将发送内容镜像到 MPTK

- `FileUtils.cs`, `AudioClipUtils.cs`  
  示例中使用的工具脚本（文件处理、音频剪辑辅助工具）。

<div class="page" />

## WebGL 模板 (WebGL templates)

位置：`Assets/MIDI/Samples/WebGLTemplates/`

包含专为支持 MIDI 的 WebGL 部署而设计的构建模板。如果您需要为 WebGL MIDI 和 BLE MIDI 流程提供一致的 HTML/JS 脚手架，请使用这些模板。

<div class="page" />

## 快速测试的推荐工作流

1. 打开其中一个示例场景。
2. 进入播放模式。
3. 连接 MIDI 设备（或使用 RTP-MIDI / UDP MIDI 2.0 等网络传输方式）。
4. 确认以下内容：
- 显示设备连接 (Attach) 事件；
- 接收到 Note/CC 事件；
- 发送操作在目标设备上产生了输出。

如果示例未接收到事件：
- 确认是否使用了正确的平台后端；
- 检查传输层要求（WebGL 上的权限、网络传输的防火墙设置等）。

<div class="page" />

## 文档中引用的源代码示例实现

文档页面引用了以下“精简且具体”的脚本：

- `Assets/MIDI/Samples/DocumentationExamples/Midi1QuickStartExample.cs`
- `Assets/MIDI/Samples/DocumentationExamples/Midi2QuickStartExample.cs`
- `Assets/MIDI/Samples/DocumentationExamples/MpeOutputExample.cs`
- `Assets/MIDI/Samples/DocumentationExamples/SmfPlaybackExample.cs`
- `Assets/MIDI/Samples/DocumentationExamples/RtpMidiTransportExample.cs`

与 MPTK 相关的示例：

- `Assets/MIDI/Samples/DocumentationExamples/MptkVirtualOutputSinkExample.cs`
- `Assets/MIDI/Samples/DocumentationExamples/MptkToMidiManagerInputExample.cs`

如果您正在使用 Maestro / MPTK，请参阅：
- [Maestro / MPTK 集成](mptk.md)