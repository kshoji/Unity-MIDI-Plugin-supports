# 示例场景验证检查清单

用于验证剩余示例场景的检查清单。

> **注：** `MptkIntegrationSampleScene.unity` 另外需要 `FEATURE_USE_MPTK`。

<div class="page" />

## 通用前置准备

各场景共同确认的项目：

- [ ] 打开场景并进入 **Play 模式**（控制台无错误）
- [ ] 打开 `Window > MIDI > Monitor`，按需确认 IN/OUT
- [ ] 启用相应的 **Scripting Define Symbols**（见下表）
- [ ] IMGUI 面板显示，并对按钮操作有响应

### Scripting Define Symbols 一览

| 符号 | 目标场景 |
|----------|------------|
| `FEATURE_USE_TIMELINE` + `com.unity.timeline` | MidiTimelineIntegrationSampleScene |
| `FEATURE_USE_VISUALSCRIPTING` + `com.unity.visualscripting` | MidiVisualScriptingIntegrationSampleScene |
| `FEATURE_MIDI_NETWORK` | MidiNetworkJamSampleScene |
| `FEATURE_INPUT_SYSTEM` + `com.unity.inputsystem` | InputSystemBridgeSampleScene |

<div class="page" />

## 场景一览

| # | 类别 | 场景 | 路径 |
|---|----------|--------|------|
| 1 | 游戏玩法基础 | MidiGameplaySampleScene | `Assets/MIDI/Samples/Gameplay/Scenes/MidiGameplaySampleScene.unity` |
| 2 | Unity 集成 | MidiAnimatorIntegrationSampleScene | `Assets/MIDI/Samples/Integrations/Scenes/MidiAnimatorIntegrationSampleScene.unity` |
| 3 | Unity 集成 | MidiTimelineIntegrationSampleScene | `Assets/MIDI/Samples/Integrations/Scenes/MidiTimelineIntegrationSampleScene.unity` |
| 4 | Unity 集成 | MidiVisualScriptingIntegrationSampleScene | `Assets/MIDI/Samples/Integrations/Scenes/MidiVisualScriptingIntegrationSampleScene.unity` |
| 5 | 游戏玩法 | ChordPuzzleSampleScene | `Assets/MIDI/Samples/Gameplay/Scenes/ChordPuzzleSampleScene.unity` |
| 6 | 游戏玩法 | ChordScaleSampleScene | `Assets/MIDI/Samples/Gameplay/Scenes/ChordScaleSampleScene.unity` |
| 7 | 游戏玩法 | MidiClockSyncSampleScene | `Assets/MIDI/Samples/Gameplay/Scenes/MidiClockSyncSampleScene.unity` |
| 8 | 游戏玩法 | ScalePracticeSampleScene | `Assets/MIDI/Samples/Gameplay/Scenes/ScalePracticeSampleScene.unity` |
| 9 | Input System | InputSystemBridgeSampleScene | `Assets/MIDI/Samples/Integrations/InputSystem/Scenes/InputSystemBridgeSampleScene.unity` |
| 10 | 网络 | MidiNetworkJamSampleScene | `Assets/MIDI/Samples/Integrations/Networking/Scenes/MidiNetworkJamSampleScene.unity` |
| 11 | Foundation | FoundationSampleScene | `Assets/MIDI/Samples/Foundation/Scenes/FoundationSampleScene.unity` |

<div class="page" />

## 推荐测试顺序

1. **基础：** MidiGameplaySampleScene
2. **Unity 集成：** Animator → Timeline → Visual Scripting
3. **横切基础：** ClockSync → Chord/Scale → InputSystem
4. **网络 / Foundation：** MidiNetworkJamSampleScene → FoundationSampleScene

<div class="page" />

## 分场景检查清单

### 1. MidiGameplaySampleScene

**路径：** `Assets/MIDI/Samples/Gameplay/Scenes/MidiGameplaySampleScene.unity`  
**目的：** DeviceFilter → ChannelFilter → Router / NoteTracker 管线

- [ ] Play 开始后，IMGUI 显示管线状态
- [ ] **NoteOn C4 (ch0)** → Router 的 Launch 绑定触发（日志 / 计数器更新）
- [ ] **CC64 = 127 (ch0)** → Toggle 绑定触发
- [ ] **NoteOn C4 (ch1)** → 被 ChannelFilter 拦截（不到达 Router / Tracker）
- [ ] **Add D4 + E4 (ch0)** → 3 音符和弦检测消息
- [ ] SmfPlayer 播放 / MidiRecorder 录制部分可工作
- [ ] 连接实体 MIDI 控制器时，ch0 的 C4 / CC64 行为相同

---

### 2. MidiAnimatorIntegrationSampleScene

**路径：** `Assets/MIDI/Samples/Integrations/Scenes/MidiAnimatorIntegrationSampleScene.unity`  
**目的：** MIDI → Animator 参数驱动

- [ ] **CC1 = 127** → 立方体上升
- [ ] **Note 60** → 脉冲效果（缩放变化）
- [ ] **CC10 / CC11** → 立方体旋转
- [ ] 虚拟 MIDI Inject 与实体输入均可产生相同变化

---

### 3. MidiTimelineIntegrationSampleScene

**路径：** `Assets/MIDI/Samples/Integrations/Scenes/MidiTimelineIntegrationSampleScene.unity`  
**前提：** `FEATURE_USE_TIMELINE`、`com.unity.timeline`

- [ ] **Play Timeline** → SMF 开始播放
- [ ] Director 时间与 SmfPlayer 时间同步
- [ ] **Pause** → SmfPlayer 暂停
- [ ] **Seek to 1.0 s** → SmfPlayer 跟随

---

### 4. MidiVisualScriptingIntegrationSampleScene

**路径：** `Assets/MIDI/Samples/Integrations/Scenes/MidiVisualScriptingIntegrationSampleScene.unity`  
**前提：** `FEATURE_USE_VISUALSCRIPTING`、`com.unity.visualscripting`

- [ ] **NoteOn C4** → 立方体颜色变化
- [ ] **CC1 = 127** → 视觉反馈变化
- [ ] 事件经 Event Bus / Script Graph 到达

---

### 5. ChordPuzzleSampleScene

**路径：** `Assets/MIDI/Samples/Gameplay/Scenes/ChordPuzzleSampleScene.unity`  
**目的：** 和弦名称测验（MidiScaleQuiz）

- [ ] 画面显示 Target chord
- [ ] **Hold C Major / C Minor / Cmaj7** → 识别和弦出现在日志中
- [ ] **Check Answer** → Correct / Incorrect 判定
- [ ] 答对时 **Next Puzzle** 进入下一题
- [ ] **Release All** 释放音符

---

### 6. ChordScaleSampleScene

**路径：** `Assets/MIDI/Samples/Gameplay/Scenes/ChordScaleSampleScene.unity`  
**目的：** ChordRecognition + MidiChordDetector + MidiScaleQuiz

- [ ] **Hold C Major** → Chord = C、C Major scale = OK
- [ ] **Hold C Minor** → Chord = Cm
- [ ] **Add Out-of-scale Note (61)** → Out of scale 显示 / 日志
- [ ] **Evaluate Quiz Target = C** → 测验判定
- [ ] **Release All** 重置状态

---

### 7. MidiClockSyncSampleScene

**路径：** `Assets/MIDI/Samples/Gameplay/Scenes/MidiClockSyncSampleScene.unity`  
**目的：** 外部 MIDI Clock 同步与 BPM 估算

- [ ] **Inject Start** → IsPlaying = true
- [ ] **Inject 24 Timing Clocks** → BPM 估算值更新，Bar/Beat 前进
- [ ] **Inject Stop** → 停止
- [ ] **Set Adapter Mode: Step / Follow** → SmfPlayerClockAdapter 模式切换
- [ ] onBeat 事件出现在日志中

---

### 8. ScalePracticeSampleScene

**路径：** `Assets/MIDI/Samples/Gameplay/Scenes/ScalePracticeSampleScene.unity`  
**目的：** 全部音阶类型的从属判定

- [ ] 切换音阶类型（Major / Minor 等）→ In scale 显示变化
- [ ] **Add C4 (60)** → In scale: Yes（选择 Major 时）
- [ ] **Add C#4 (61)** → In scale: No，Scale violation 日志
- [ ] **Add E4 (64)** → 和弦识别显示更新
- [ ] **Release All** 清除状态

---

### 9. InputSystemBridgeSampleScene

**路径：** `Assets/MIDI/Samples/Integrations/InputSystem/Scenes/InputSystemBridgeSampleScene.unity`  
**前提：** `FEATURE_INPUT_SYSTEM`

- [ ] Play 开始后生成 Synthetic Device
- [ ] MIDI Inject 反映到 Input System 状态
- [ ] Input Action → MIDI 发送（双向桥接）
- [ ] 在 Monitor 中确认 IN/OUT

---

### 10. MidiNetworkJamSampleScene

**路径：** `Assets/MIDI/Samples/Integrations/Networking/Scenes/MidiNetworkJamSampleScene.unity`  
**前提：** `FEATURE_MIDI_NETWORK`

- [ ] 通过 Hub / Client 环回或两个实例连接
- [ ] Broadcast 模式下 MIDI 事件注入到 Client 侧虚拟设备
- [ ] 切换 Merge / Playback 模式（若有 UI）
- [ ] 在 Monitor 中确认 Client 侧 IN

---

### 11. FoundationSampleScene

**路径：** `Assets/MIDI/Samples/Foundation/Scenes/FoundationSampleScene.unity`  
**目的：** 设备选择、延迟校准、Foundation UI

- [ ] Play 开始后显示 Foundation UI shell
- [ ] 设备选择 UI 列出已连接设备
- [ ] 延迟校准流程可工作
- [ ] 设置持久化（若有）可工作

<div class="page" />

## 相关文档

- [示例项目](samples.md)
- [类型套件](kits.md)
- [Unity 生态系统集成](integrations.md)
- [面向游戏玩法的组件](gameplay.md)
