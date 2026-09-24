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

- `SampleMenuScene.unity`  
  列出并打开其他示例场景的枢纽场景（`Window > MIDI > Samples/Open Sample Menu Scene`）。

### Unity 生态系统集成

位置：`Assets/MIDI/Samples/Integrations/Scenes/`

- `MidiAnimatorIntegrationSampleScene.unity`  
  演示了通过 `MidiAnimatorDriver` / `MidiBlendTreeDriver` 实现的 MIDI → Animator 参数驱动。  
  CC1 改变立方体的高度，CC10/11 使其旋转，Note 60 改变脉冲效果。

- `MidiTimelineIntegrationSampleScene.unity`  
  演示了通过 `PlayableDirector` + `SmfPlayer` + `MidiPlaybackTrack` 实现的 Timeline 同步 SMF 播放。  
  在播放模式下，可从 IMGUI 操作 Timeline 的 Play / Pause / Seek。

- `MidiVisualScriptingIntegrationSampleScene.unity`  
  演示了通过 `MidiVisualScriptingBridge` 与 Event Bus 监听器实现的 Visual Scripting 集成。  
  可在 Script Graph 中添加 **Events > MIDI** 节点，在同一 GameObject 上进行扩展。

### 游戏玩法

位置：`Assets/MIDI/Samples/Gameplay/`

- `Scenes/MidiGameplaySampleScene.unity`  
  演示了游戏玩法组件（`MidiDeviceFilter` → `MidiChannelFilter` → `MidiInputRouter` / `MidiNoteTracker`）的接线与行为。  
  在播放模式下，可通过 IMGUI 面板经由虚拟设备模拟 Note / CC / MIDI Start / SysEx。实机 MIDI 控制器的操作方式相同。

- `Scenes/MidiClockSyncSampleScene.unity`  
  外部 MIDI Clock 注入、`MidiClockSync` BPM 推定、`SmfPlayerClockAdapter` 演示。

- `Scenes/ChordScaleSampleScene.unity`  
  `ChordRecognition` / `MidiChordDetector` 演示。

- `Scenes/ChordPuzzleSampleScene.unity`  
  目标和弦测验（`MidiChordDetector.targetChord`）演示。

- `Scenes/ScalePracticeSampleScene.unity`  
  多种音阶类型的练习演示。

### 网络 / Input System / Chunity

位置：`Assets/MIDI/Samples/Integrations/`

- `Networking/Scenes/MidiNetworkJamSampleScene.unity`  
  基于 UDP 的 `MidiNetworkHub` / `MidiNetworkClient` 演示（需要 `FEATURE_MIDI_NETWORK`）。

- `Networking/Scenes/MidiMirrorNetworkSampleScene.unity`  
  Mirror Host/Client MIDI（`FEATURE_MIRROR` + 已安装 Mirror）。

- `Networking/Scenes/MidiNetcodeNetworkSampleScene.unity`  
  Netcode Host/Client MIDI（`FEATURE_NETCODE` + 已安装 NGO）。

- `Networking/Scenes/MidiWsnet2NetworkSampleScene.unity`  
  WSNet2 Create/Join MIDI（`FEATURE_WSNET2` + 客户端 + 服务器已启动）。

- `InputSystem/Scenes/InputSystemBridgeSampleScene.unity`  
  `MidiInputSystemBridge` + Synthetic Device 演示（需要 `FEATURE_INPUT_SYSTEM`）。

- `Chunity/Scenes/ChunityBridgeSampleScene.unity`  
  `MidiChuckBridge` + `MidiChuckPatchHost` 演示（需要外部 Chunity + `FEATURE_CHUNITY`）。

- `Chunity/Scenes/ChunityWorkflowsSampleScene.unity`  
  Poly / SMF / Clock / Event→MIDI 各工作流演示（需要 `FEATURE_CHUNITY`）。

- `Chunity/Scenes/ChunityPresetsSampleScene.unity`  
  `MidiChuckPreset` / `MidiChuckParameterBinder` 与 Filter / Gain 等演示（需要 `FEATURE_CHUNITY`；Timeline 可选，需 `FEATURE_USE_TIMELINE`）。

- `Chunity/Scenes/ChunityMicFxSampleScene.unity`  
  麦克风 adc + PitShift FX，通过 CC1（Mod Wheel）控制音高移位量的演示（需要 `FEATURE_CHUNITY`；Phase E）。

- `Chunity/Scenes/ChunityGeneratorWorkflowSampleScene.unity`  
  Main + Sub Generator + Bridge 演示（需要 `FEATURE_CHUNITY_SCRIPTABLE_AUDIO`）。

### Scriptable Audio

位置：`Assets/MIDI/Samples/Integrations/ScriptableAudio/`

- `Scenes/ScriptableAudioMetronomeSampleScene.unity`  
  节拍器 / DSP 引导演示（`FEATURE_SCRIPTABLE_AUDIO`，Unity 6000.3+）。

- `Scenes/ScriptableAudioSequenceSampleScene.unity`  
  SMF 序列 → DSP 调度演示。

- `Scenes/ScriptableAudioUmpSequenceSampleScene.unity`  
  UMP 序列 → DSP 调度演示。

### Foundation

位置：`Assets/MIDI/Samples/Foundation/`

- `Scenes/FoundationSampleScene.unity`  
  设备选择、延迟校准、Foundation UI 外壳演示。

### Maestro / MPTK

位置：`Assets/MIDI/Samples/MPTK/`

- `Scenes/MptkIntegrationSampleScene.unity`  
  Maestro / MPTK 集成枢纽（需要 `FEATURE_USE_MPTK`；Pro 功能需要 `MPTK_PRO`）。

### MIDI Tracker（独立示例）

位置：`Assets/MIDITracker/`（位于 `Assets/MIDI/Samples/` 之外，单向依赖核心插件）

- `Scenes/MidiTrackerSampleScene.unity`  
  面向 pattern 的 MIDI Tracker 展示（编辑、编曲、录音、SMF I/O、多平台 UX）。  
  参见 [MIDI Tracker README](../../../../MIDITracker/README.md) 与 [MIDI Tracker 开发计划](../../../../MIDITracker/DEVELOPMENT_PLAN.md)。

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

### Unity 生态系统集成

位置：`Assets/MIDI/Samples/Integrations/Scripts/`

- `MidiAnimatorIntegrationSampleScene.cs`  
  运行时构建 `MidiAnimatorDriver` / `MidiBlendTreeDriver`、虚拟 MIDI 输入、IMGUI 操作面板

- `MidiTimelineIntegrationSampleScene.cs`  
  运行时构建 `TimelineAsset` / `MidiPlaybackTrack` / `SmfPlayer`、Timeline 操作 UI

- `MidiVisualScriptingIntegrationSampleScene.cs`  
  通过 `MidiVisualScriptingBridge` + `MidiVisualScriptingSampleFeedback` 实现的 Event Bus 演示

- `MidiIntegrationSampleFactory.cs`  
  用于演示的 SMF 琶音生成、虚拟设备注册的通用辅助工具

### 游戏玩法

位置：`Assets/MIDI/Samples/Gameplay/Scripts/`

- `MidiGameplaySampleScene.cs`  
  演示脚本中的典型职责：
  - 运行时构建过滤器链（DeviceFilter → ChannelFilter）
  - 通过 `MidiInputMap` / `MidiInputRouter` 绑定 NoteOn / CC / MIDI Start / SysEx
  - 通过 `MidiNoteTracker` 追踪按下的音符并检测和弦
  - 通过向虚拟输入设备（`virtual:gameplay-sample`）注入实现无硬件测试
  - 通过 IMGUI 进行过滤器配置与事件日志显示
- `MidiClockSyncSampleScene.cs` — Clock 同步演示
- `ChordScaleSampleScene.cs` — 和弦/音阶判定演示
- `GameplaySampleFactory.cs` — 虚拟 MIDI / Clock 注入辅助工具

### 网络 / Input System

位置：`Assets/MIDI/Samples/Integrations/`

- `Networking/Scripts/MidiNetworkJamSampleScene.cs` — LAN MIDI 同步演示（`#if FEATURE_MIDI_NETWORK`）
- `InputSystem/Scripts/InputSystemBridgeSampleScene.cs` — Input System 桥接（`#if FEATURE_INPUT_SYSTEM`）

### Foundation

位置：`Assets/MIDI/Samples/Foundation/Scripts/`

- `FoundationSampleScene.cs` — Foundation 演示

- `FileUtility.cs`, `AudioClipUtility.cs`（位于 `Assets/MIDI/Samples/Scripts/`）  
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

### MIDI 1.0 基础（MidiSampleScene）

1. 打开 `MidiSampleScene.unity`。
2. 打开 `Window > MIDI > Monitor`（[编辑器工具 (MIDI 监视器)](editor-tools.md)）。
3. 进入播放模式。
4. 连接 MIDI 设备（或使用 RTP-MIDI / UDP MIDI 2.0 等网络传输方式）。
5. 确认以下内容：
- 显示设备连接事件。
- 接收到 Note 和 CC 事件（监视器的 IN 列）。
- 发送操作在目标设备上产生了输出（监视器的 OUT 列）。

### 游戏玩法组件（MidiGameplaySampleScene）

1. 打开 `MidiGameplaySampleScene.unity`。
2. 进入播放模式。
3. 在屏幕上的 IMGUI 面板中尝试以下操作：
   - **NoteOn C4 (ch0)** → 触发 Router 的 Launch 绑定
   - **CC64 = 127 (ch0)** → 触发 Toggle 绑定
   - **NoteOn C4 (ch1)** → 被 ChannelFilter 阻止（不会到达 Router / Tracker）
   - **Add D4 + E4 (ch0)** → 由 NoteTracker 检测出 3 音符和弦
4. 连接实机 MIDI 控制器时，ch0 的 C4 / CC64 也会以相同方式工作。
5. 有关过滤器链的详细信息，请参阅 [游戏玩法组件](gameplay.md)。

### Animator 集成（MidiAnimatorIntegrationSampleScene）

1. 打开 `MidiAnimatorIntegrationSampleScene.unity`。
2. 进入播放模式。
3. 在 IMGUI 面板中，**CC1 = 127** → 确认立方体上升。
4. **Note 60** → 确认脉冲效果（缩放变化）。
5. **Program Change 5** → 确认 `Program` Int 参数更新与色相变化。
6. **MIDI Start** → 确认 `TransportStart` Trigger 与脉冲效果。
7. **SysEx** → 确认事件日志中显示 `onMessage SysEx`（Animator 参数不会更新）。
8. **CC10 / CC11** → 确认立方体旋转。
9. 有关详细信息，请参阅 [Unity 生态系统集成](integrations.md)。

### Timeline 集成（MidiTimelineIntegrationSampleScene）

1. 确认已安装 `com.unity.timeline` 包。
2. 确认已在 Project Settings 中添加 `FEATURE_USE_TIMELINE`。
3. 打开 `MidiTimelineIntegrationSampleScene.unity`。
3. 进入播放模式并点击 **Play Timeline**。
4. 确认 Director time 与 SmfPlayer time 保持同步。
5. 确认使用 **Pause** / **Seek to 1.0 s** 时 SmfPlayer 会跟随。

### Visual Scripting 集成（MidiVisualScriptingIntegrationSampleScene）

1. 确认已安装 `com.unity.visualscripting` 包。
2. 确认已在 Project Settings 中添加 `FEATURE_USE_VISUALSCRIPTING`。
3. 打开 `MidiVisualScriptingIntegrationSampleScene.unity`。
3. 进入播放模式，确认使用 **NoteOn C4** / **CC1 = 127** 时立方体颜色发生变化。
4. 可在同一 GameObject 上添加 Script Machine，并通过 **Events > MIDI** 节点扩展图表。

### 网络 MIDI（MidiNetworkJamSampleScene）

1. 在 Project Settings 中添加 `FEATURE_MIDI_NETWORK`。
2. 打开 `MidiNetworkJamSampleScene.unity`。
3. 在播放模式下尝试 Hub / Client 的广播操作。

### 网络 MIDI — Mirror / Netcode / WSNet2

1. 安装框架并启用符号（见 [集成 — Networking](integrations.md) / Networking README）。
2. 打开 `MidiMirrorNetworkSampleScene` / `MidiNetcodeNetworkSampleScene` / `MidiWsnet2NetworkSampleScene`。
3. Mirror/Netcode：**Start Host** 后在第二实例 **Client**。WSNet2：**Create Room** 后 **Join by Room#**（服务器须已启动）。
4. 用 Note / CC / Seek 验证同步。

### MidiClockSync / 和弦/音阶（Gameplay 示例）

1. 打开 `MidiClockSyncSampleScene.unity`，在播放模式下确认 Clock 注入与 BPM 推定。
2. 在 `ChordScaleSampleScene.unity` / `ChordPuzzleSampleScene.unity` / `ScalePracticeSampleScene.unity` 中确认和弦/音阶判定。
3. 有关详细信息，请参阅 [类型套件](kits.md) 与 [游戏玩法组件](gameplay.md)。

### Input System 桥接（InputSystemBridgeSampleScene）

1. 在 Project Settings 中添加 `FEATURE_INPUT_SYSTEM`（需要 `com.unity.inputsystem`）。
2. 打开 `InputSystemBridgeSampleScene.unity`。
3. 在播放模式下确认 Synthetic Device 与双向桥接。

### Chunity 桥接（ChunityBridgeSampleScene）

1. 安装 Chunity，并将 `Chunity.Runtime.asmdef.example` 作为 `Chunity.Runtime.asmdef` 放置到 Scripts 根目录。
2. 在 Project Settings 中添加 `FEATURE_CHUNITY`。
3. 打开 `Assets/MIDI/Samples/Integrations/Chunity/Scenes/ChunityBridgeSampleScene.unity`。
4. 在播放模式下确认通过 Note On / Off 按钮发出单音 SinOsc。

### Chunity 工作流（ChunityWorkflowsSampleScene）

1. 执行与上述相同的启用步骤。
2. 打开 `ChunityWorkflowsSampleScene.unity`。
3. 通过标签页切换 Poly / SMF / Clock / Event / Bank / Array / Adv（HostAdvancer）/ Life（PatchLifecycle），确认各演示。

### Chunity 预设（ChunityPresetsSampleScene）

1. 执行与上述相同的启用步骤。
2. 打开 `Assets/MIDI/Samples/Integrations/Chunity/Scenes/ChunityPresetsSampleScene.unity`。
3. 确认 Soft / Bright / Pad 预设切换、Filter / Gain 等滑块，以及 Note On。
4. （可选）启用 `FEATURE_USE_TIMELINE` 时，还会显示 Timeline 标记 API 的演示按钮。

### Chunity 麦克风 FX（ChunityMicFxSampleScene）

1. 执行与上述相同的启用步骤。
2. 打开 `Assets/MIDI/Samples/Integrations/Chunity/Scenes/ChunityMicFxSampleScene.unity`。
3. 在播放模式下确认麦克风输入已启用，并确认 CC1 滑块或按钮会改变 PitShift 的移位量。
4. 实机 MIDI 的 CC1（Mod Wheel）也会经由 `MidiChuckBridge` 到达同一参数。

### Chunity Scriptable Generator（ChunityGeneratorWorkflowSampleScene）

1. 在与 Chunity 桥接相同的前提下，额外应用 [Chunity Optional 补丁说明](../../../Scripts/Integrations/Chunity/Optional/README.md) 中的 `useBuiltInAudioFilter` 补丁。
2. 若项目中尚无，请安装 Unity 包 **`com.unity.collections`**。
3. 在 Project Settings 中添加 `FEATURE_CHUNITY_SCRIPTABLE_AUDIO`（同时保留 `FEATURE_CHUNITY`）。
4. 打开 `ChunityGeneratorWorkflowSampleScene.unity`，确认 Main + Sub Generator + Bridge（通过 Note On 与 Near/Mid/Far 实现空间化）。

### Scriptable Audio 管线

1. 确认 Unity 6000.3+，并添加 `FEATURE_SCRIPTABLE_AUDIO`（参见 [集成](integrations.md) / [构建后处理](build-postprocessing.md)）。
2. 打开 `ScriptableAudioMetronomeSampleScene.unity`、`ScriptableAudioSequenceSampleScene.unity` 或 `ScriptableAudioUmpSequenceSampleScene.unity`。
3. 在播放模式下确认节拍器或序列调度行为。

### Foundation（FoundationSampleScene）

1. 打开 `FoundationSampleScene.unity`。
2. 在播放模式下确认设备选择、延迟校准与 Foundation UI 外壳。

### MIDI Tracker（MidiTrackerSampleScene）

1. 打开 `Assets/MIDITracker/Scenes/MidiTrackerSampleScene.unity`。
2. 进入播放模式，练习 pattern 编辑 / 播放 / 编曲（推荐使用外部 MIDI 音源）。
3. 操作说明与阶段状态见 [MIDI Tracker README](../../../../MIDITracker/README.md)。

如果示例未接收到事件：
- 确认是否使用了正确的平台后端；
- 检查传输层要求（WebGL 上的权限、网络传输的防火墙设置等）。

如果使用 `MidiInputRouter` 或过滤器，请同时参阅 [游戏玩法组件](gameplay.md) 中的接线步骤。

若要尝试 SMF 的播放/录制，可使用 [SMF 工具](smf-tools.md) 中的 `SmfPlayer` / `MidiRecorder`。

若要在编辑模式下检查 SMF，可使用 [编辑器工具](editor-tools.md) 中的 SMF 预览。

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
- `Assets/MIDI/Samples/DocumentationExamples/MptkBootstrapExample.cs` — Bootstrap / SmfPlayer 联动 / 校验
- `Assets/MIDI/Samples/DocumentationExamples/MptkMidiEventPipelineExample.cs` — 合成前 rewrite（`MPTK_PRO`）
- `Assets/MIDI/Samples/DocumentationExamples/MptkWriterExternalExample.cs` — Writer / External 往返（`MPTK_PRO`）
- `Assets/MIDI/Samples/DocumentationExamples/MptkInnerLoopExample.cs` — InnerLoop 区间循环（`MPTK_PRO`）
- `Assets/MIDI/Samples/DocumentationExamples/MptkListPlayerExample.cs` — 播放列表（`MPTK_PRO`）
- `Assets/MIDI/Samples/DocumentationExamples/MptkSoundFontLoaderExample.cs` — 运行时 SoundFont
- `Assets/MIDI/Samples/DocumentationExamples/MptkDelayedNoteDispatcherExample.cs` — 延迟派发
- `Assets/MIDI/Samples/DocumentationExamples/MptkFilePlayerChannelsExample.cs` — 通道控制

MPTK 示例 GUI（Play Mode 中挂载；按需启用 `FEATURE_USE_MPTK` + `MPTK_PRO`）：

- 场景：`Assets/MIDI/Samples/MPTK/Scenes/MptkIntegrationSampleScene.unity`（需要 `FEATURE_USE_MPTK`）
- 基础脚本：`Assets/MIDI/Samples/MPTK/Scripts/MptkIntegrationSampleScene.cs`
- `Assets/MIDI/Samples/MPTK/Scripts/MptkMidiEventPipelineSample.cs` — 合成前 rewrite GUI
- `Assets/MIDI/Samples/MPTK/Scripts/MptkWriterExternalSample.cs` — Writer / External / Join GUI
- `Assets/MIDI/Samples/MPTK/Scripts/MptkInnerLoopSample.cs` — InnerLoop GUI
- `Assets/MIDI/Samples/MPTK/Scripts/MptkListPlayerSample.cs` — 播放列表 GUI（`MPTK_PRO`）
- `Assets/MIDI/Samples/MPTK/Scripts/MptkPhaseDUtilitiesSample.cs` — Phase D 工具 GUI
- `Assets/MIDI/Samples/MPTK/Scripts/MptkSpatializerSample.cs` — Spatializer GUI（`MPTK_PRO` + MidiSpatializer prefab）
- `Assets/MIDI/Samples/DocumentationExamples/MptkSpatializerExample.cs` — Spatializer 示例
- `Assets/MIDI/Samples/DocumentationExamples/MptkTimelineMarkerExample.cs` — Timeline Seek / InnerLoop 示例
- 输出预设：`Assets/MIDI/Scripts/Integrations/MPTK/Presets/MptkVirtualOutput.preset.asset`

如果您正在使用 Maestro / MPTK，请参阅：
- [Maestro / MPTK 集成](mptk.md)