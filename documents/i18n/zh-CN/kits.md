# 流派套件

提供面向细分领域的扩展与横断基础功能。

> **MPTK 输出路由：** 当套件的 `outputDeviceId` 为空时，可通过 `outputPreset`（`MidiOutputRoutingPreset`）统一路由到 MPTK 虚拟输出 (`mptk:internal`) 等目标。随附预设：`Assets/MIDI/Scripts/Integrations/MPTK/Presets/MptkVirtualOutput.preset.asset`。详情请参阅 [Maestro / MPTK 集成 — 示例场景 / 输出预设 / 编辑器试听](mptk.md#示例场景--输出预设--编辑器试听)。

| 套件 / 功能 | 命名空间 | Assembly Definition | 脚本定义符号 |
|--------|----------|---------------------|-------------------------|
| 网络 MIDI 同步 | `jp.kshoji.unity.midi.net` | `jp.kshoji.midi.net` | `FEATURE_MIDI_NETWORK` |
| **MidiClockSync** | `jp.kshoji.unity.midi` | `jp.kshoji.midi`（核心） | 不需要 |
| **和弦与音阶判定** | `jp.kshoji.unity.midi` / `.util` | `jp.kshoji.midi`（核心） | 不需要 |
| **Input System 桥接** | `jp.kshoji.unity.midi.integrations.inputsystem` | `jp.kshoji.midi.inputsystem` | `FEATURE_INPUT_SYSTEM` |
| **Foundation** | `jp.kshoji.unity.midi.foundation` | `jp.kshoji.midi.foundation` | 不需要 |

位置：

| 套件 | 位置 |
|--------|------|
| 网络 | `Assets/MIDI/Scripts/Integrations/Networking/` |
| **Clock / 和弦判定（核心）** | `Assets/MIDI/Scripts/Gameplay/`（含 `Theory/`） |
| **Input System** | `Assets/MIDI/Scripts/Integrations/InputSystem/` |
| **Foundation** | `Assets/MIDI/Scripts/Foundation/` |
| Foundation UI | `Assets/MIDI/UI/Foundation/` |
| 示例 | `Assets/MIDI/Samples/Gameplay/` / `Assets/MIDI/Samples/Integrations/Networking/` / `Assets/MIDI/Samples/Integrations/InputSystem/` / `Assets/MIDI/Samples/Foundation/` |

### 套件之间的依赖关系

| 套件 / 功能 | 主要依赖组件 |
|---------------|------------------------|
| 网络 MIDI 同步 | `SmfPlayer`, `MidiManager` |
| Input System 桥接 | `MidiManager` |
| Timeline 集成 | `SmfPlayer`, `TempoMapExtractor`, `MidiRecorder` |
| Animator 集成 | `MidiInputRouter`, `MidiCcSmoother`, `MidiNoteTracker` |

<div class="page" />

## 网络 MIDI 同步

在 LAN 上同步 MIDI 事件与 SMF 播放位置。内置基于 UDP 的轻量传输，无需 Netcode / Mirror 等额外包。

### 前提条件

在 Project Settings 中添加脚本定义符号：

- `FEATURE_MIDI_NETWORK`

| 项目 | 值 |
|------|-----|
| Assembly Definition | `jp.kshoji.midi.net` |
| 设置路径 | `Project Settings > Player > Other Settings > Script Compilation > Scripting Define Symbols` |

### 主要组件

| 组件 | 作用 |
|----------------|------|
| `MidiNetworkHub` | 主机。分发 MIDI 事件 |
| `MidiNetworkClient` | 将接收到的事件注入虚拟设备 |
| `MidiNetworkMessage` | 可序列化 DTO |
| `MidiPlaybackSync` | `SmfPlayer` 播放位置同步（`broadcastInterval` 默认 0.1s，`seekThreshold` 默认 0.05s） |
| `MidiSessionDiscovery` | LAN 会话 UDP 广播（payload 含 `hubPort`） |
| `MidiSessionDiscoveryListener` | LAN 会话 UDP 监听（`OnSessionAdvertisement`） |
| `MidiLatencyCompensation` | RTT 辅助（UDP Ping/Pong → `estimatedRttMs`） |

### 同步模式

| 模式 | 用途 |
|--------|------|
| Broadcast | 将主机输入分发给所有客户端 |
| Merge | 在主机上合并所有客户端的输入 |
| Playback | 仅同步播放位置 |

### 可选：Mirror / Netcode / WSNet2 桥接

UDP 的 `MidiNetworkHub` / `MidiNetworkClient` 无需额外包。可选桥接将相同的 `MidiNetworkMessage` / `MidiNetworkMessageCodec` 放到已有的游戏传输上。包 **不会** 随本仓库分发。

| 符号 | 程序集 | 组件 | 包 / 门控 |
|--------|----------|-----------|----------------|
| `FEATURE_MIRROR`（+ `FEATURE_MIDI_NETWORK` + `MIRROR`） | `jp.kshoji.midi.net.mirror` | `MidiMirrorBridge` | 安装 Mirror；`MIRROR` 由 Mirror 定义 |
| `FEATURE_NETCODE`（+ `FEATURE_MIDI_NETWORK` + `MIDI_HAS_NETCODE`） | `jp.kshoji.midi.net.netcode` | `MidiNetcodeBridge` | UPM `com.unity.netcode.gameobjects`（`versionDefines` → `MIDI_HAS_NETCODE`） |
| `FEATURE_WSNET2`（+ `FEATURE_MIDI_NETWORK` + `MIDI_HAS_WSNET2`） | `jp.kshoji.midi.net.wsnet2` | `MidiWsnet2Bridge` | [WSNet2](https://github.com/KLab/wsnet2) 客户端 + `WSNet2.Runtime.asmdef`；Editor 同步 `MIDI_HAS_WSNET2`。Lobby/Game 服务器需单独启动 |

启用流程：添加符号 → 安装框架 → 按 [Networking 集成](../../../Scripts/Integrations/Networking/README.md) 接线 → 打开 `Samples/Integrations/Networking/Scenes/`。

### 示例

| 场景 | 前提 |
|-------|----------|
| `.../Networking/Scenes/MidiNetworkJamSampleScene.unity` | `FEATURE_MIDI_NETWORK` |
| `.../Networking/Scenes/MidiMirrorNetworkSampleScene.unity` | Mirror + `FEATURE_MIRROR` |
| `.../Networking/Scenes/MidiNetcodeNetworkSampleScene.unity` | NGO + `FEATURE_NETCODE` |
| `.../Networking/Scenes/MidiWsnet2NetworkSampleScene.unity` | WSNet2 + 服务器 + `FEATURE_WSNET2` |

<div class="page" />

## 横断基础

提供外部 MIDI Clock 同步、和弦与音阶判定，以及 Input System 桥接。详情另请参阅 [面向游戏玩法的组件](gameplay.md)。

### MidiClockSync

同步到外部 MIDI Clock（Timing Clock / Start / Stop / Continue），并提供 BPM、拍位置与小节位置。

| 属性 / API | 说明 |
|------------------|------|
| `pulsesPerQuarterNote` | 每拍的 Clock 脉冲数（MIDI 标准 24） |
| `beatsPerBar` | 每小节的拍数（用于 `onBar`，默认 4） |
| `deviceIdFilter` | 空 = 全部设备 |
| `EstimatedBpm` | 根据最近脉冲估算的 BPM |
| `IsBpmStable` | BPM 估算是否稳定 |
| `onBeat` / `onBar` | 拍 / 小节边界事件 |
| `onStarted` / `onStopped` | Start / Stop 接收事件 |

`SmfPlayerClockAdapter` 通过 `SmfPlayerClockMode` 切换行为：

| 模式 | 行为 |
|--------|------|
| `Follow` | 将外部 Clock 的估算 BPM 反映到 `SmfPlayer.tempoBpm` |
| `Step` | 在外部 Clock 的每个拍上将 SMF 播放位置前进一拍 |
| `Free` | 忽略外部 Clock |

**限制：** 不支持 Song Position Pointer (SPP)、MTC 和 Ableton Link。

### 和弦与音阶判定

| 组件 / 工具类 | 作用 |
|-------------------------------|------|
| `ChordRecognition` | 根据按下的音符集合推断和弦名（`C`、`Cm7` 等） |
| `ScaleUtility` | 扩展 `MidiScaleUtility` 的音阶归属判定 |
| `MidiChordDetector` | 与 `MidiNoteTracker` 联动，进行和弦变化 / 音阶外检测 / 目标和弦测验 |

### Input System 桥接（可选）

| 组件 | 作用 |
|----------------|------|
| `MidiSyntheticDevice` | Synthetic Input Device（128 音符 + 128 CC + 通道专用轴） |
| `MidiInputSystemBridge` | MIDI → Input System 状态注入。SysEx / Raw UMP 通过 `onMessage` |
| `InputSystemToMidiBridge` | Input Action → MIDI 发送 |
| `MidiInputSystemMapping` | Action ↔ MIDI 条件的 ScriptableObject |

前提：包 `com.unity.inputsystem`、符号 `FEATURE_INPUT_SYSTEM`。  
关于设置与 Synthetic Device 布局的详情，请参阅 [Unity 生态系统集成 — Input System](integrations.md#input-system-集成)。

通过 `Assets > Create > MIDI > Input System > Mapping` 创建 `MidiInputSystemMapping`。`MidiActionBinding` 的主要字段：

| 字段 | 说明 |
|------------|------|
| `actionName` | `.inputactions` 中的 Action 名 |
| `messageType` | NoteOn / ControlChange / PitchWheel / ProgramChange 等 |
| `group` | 0–15，`-1` = 全部 group |
| `channel` | 0–15，`-1` = 全部通道 |
| `controllerOrNote` | CC 编号或音符编号 |
| `valueFilter` | 值过滤器，`-1` = 全部 |
| `targetControlIndex` | CC 回退目标（`-1` = `controllerOrNote`） |
| `preferDedicatedControl` | 优先使用专用轴（pitch / program / channelPressure / systemPulse） |
| `invertAxis` | 反转归一化轴 |

### 示例

| 场景 | 说明 |
|--------|------|
| `Assets/MIDI/Samples/Gameplay/Scenes/MidiClockSyncSampleScene.unity` | Clock 注入 / BPM 估算 / `SmfPlayerClockAdapter` |
| `Assets/MIDI/Samples/Gameplay/Scenes/ChordScaleSampleScene.unity` | 和弦识别与音阶测验 |
| `Assets/MIDI/Samples/Gameplay/Scenes/ChordPuzzleSampleScene.unity` | 目标和弦测验（`MidiChordDetector`） |
| `Assets/MIDI/Samples/Gameplay/Scenes/ScalePracticeSampleScene.unity` | 音阶练习 |
| `Assets/MIDI/Samples/Integrations/InputSystem/Scenes/InputSystemBridgeSampleScene.unity` | Synthetic Device 演示（需要 `FEATURE_INPUT_SYSTEM`） |
| `Assets/MIDI/Samples/Foundation/Scenes/FoundationSampleScene.unity` | 设备选择、延迟校准、Foundation UI 外壳 |

<div class="page" />

## 相关文档

- [示例项目](samples.md) — 套件示例场景列表
- [构建后处理 — 网络可选套件](build-postprocessing.md#网络可选套件)
- [构建后处理 — 横断基础可选套件](build-postprocessing.md#横向基础可选套件)
- [SMF 工具](smf-tools.md) — `SmfPlayer` / `TempoMapExtractor`
- [面向游戏玩法的组件](gameplay.md) — `MidiClockSync` / `MidiCcSmoother` / 和弦判定
