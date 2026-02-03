# 虚拟设备与事件注入

本项目支持“虚拟”（软件）MIDI 设备，它们与硬件/网络设备一样参与相同的设备列表和事件管线。

该功能不仅用于可选的集成（例如 Maestro / MPTK），还适用于：
- 自动化测试
- 模拟工具
- 将其他输入源桥接到 `MidiManager`
- 创建软件“回环 (Loopback)”设备

实现位置：
- `Assets/MIDI/Scripts/MidiManager.VirtualDevices.cs`

<div class="page" />

## 什么是虚拟设备？

**虚拟设备**由一个 `deviceId` 字符串标识（您可以自定义命名规范）。注册后，它会显示在：

- `MidiManager.Instance.DeviceIdSet`
- `MidiManager.Instance.InputDeviceIdSet`
- `MidiManager.Instance.OutputDeviceIdSet`

……并且它会触发与真实设备相同的连接/断开 (Attach/Detach) 回调。

<div class="page" />

## 注册 / 注销

将虚拟设备注册为输入和/或输出：

- `RegisterVirtualMidiDevice(deviceId, input: true/false, output: true/false)`
- `UnregisterVirtualMidiDevice(deviceId)`

推荐规范：
- 使用明显的前缀，如 `virtual:...` 或 `mptk:...`。
- 除非您有意模拟多个组，否则请使用 `group = 0`。

<div class="page" />

## 注入事件（“模拟设备发送的消息”）

虚拟设备可以将 MIDI 消息注入到管理器的内部管线中：

- Note On/Off
- CC, 程序变更 (Program Change)
- 触后 (Aftertouch), 弯音 (Pitch Wheel)
- SysEx
- 系统常用 / 实时消息（在适用时）

重要提示：
- **注入操作不会发送到硬件或后端。**
- 注入用于模拟软件生成的输入，就像它是从 `deviceId` 到达的一样。

这就是“将 MPTK 作为虚拟输入源”的工作原理。

另请参阅：
- [Maestro / MPTK 集成](mptk.md)