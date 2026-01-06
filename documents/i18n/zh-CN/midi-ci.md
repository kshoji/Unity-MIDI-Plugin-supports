# MIDI-CI / 能力协商

本项目包含对 MIDI 能力查询 (MIDI-CI) 风格工作流的实验性支持。

实现位置：
- `Assets/MIDI/Scripts/MidiCapabilityNegotiator.cs`

<div class="page" />

## 什么是 MIDI-CI

能力协商器监听传入的 SysEx 消息，并参与设备对设备之间的协商流程（身份/能力交换），以便应用程序发现支持的功能并协商行为。

由于 MIDI-CI 高度依赖于对端设备的实现和对 SysEx 的支持，请将其视为一项高级功能。

<div class="page" />

## 实践说明

- MIDI-CI 使用 SysEx 消息；部分平台/传输协议可能会限制 SysEx 或需要特殊权限。
- 如果您依赖 MIDI-CI，请在每个目标平台和传输协议（USB/BLE/网络）上进行测试。

<div class="page" />

## 后续阅读内容

- [MIDI 1.0 (MidiManager)](midi1.md) (SysEx 发送/接收)
- [传输协议与平台说明](transports.md)