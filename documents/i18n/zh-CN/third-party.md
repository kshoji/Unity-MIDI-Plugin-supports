# 嵌入的第三方模块

本仓库包含一些嵌入式模块及传输协议所使用的第三方源代码。

本页面可帮助您找到这些模块的文档和许可证。

<div class="page" />

## RTP-MIDI-for-.NET (嵌入式模块)

位置：
- `Assets/MIDI/Scripts/RTP-MIDI-for-.NET/`

包含：
- 独立的 `README.md`、`LICENSE` 以及变更日志
- 位于其 `Documentation~/` 文件夹下的嵌入式文档页面

Unity 集成由以下文件提供：
- `Assets/MIDI/Scripts/MidiPlugin.RtpMidi.cs`

<div class="page" />

## mDNS / DNS-SD 第三方依赖（共享）

位置：
- `Assets/MIDI/Scripts/MdnsVendor/`（`jp.kshoji.mdns.vendor`）

由 Network MIDI 2.0 UDP 发现与 RTP-MIDI Zeroconf 共用。
如果您修改或重新分发该包，请确保遵守每个组件的许可证要求。

另请参阅：
- [联系与支持](contacts.md)（列出了发现依赖项及其版本）

<div class="page" />

## VST3 插件宿主（不同捆）

本 MIDI 软件包 **不重新分发** VST3 SDK、`VstHostNative.dll`、VST 宿主 C# 或第三方 `.vst3` 插件。

VST® 是 Steinberg Media Technologies GmbH 的注册商标。

若要在 Unity 中托管 VST3，请使用独立软件包 [Unity-VST3-Bridge](https://github.com/kshoji/Unity-VST3-Bridge)（`jp.kshoji.unity.vst3nativehost`）及其 `NOTICE.md`／文档。简短说明：[集成 — VST3 插件宿主](integrations.md#vst3-插件宿主独立软件包)。
