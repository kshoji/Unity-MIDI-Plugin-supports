# 编辑器扩展验证检查清单

从提交 `33b2c6053118d8e33dd785ee797693348d76407c` 到 `HEAD` 的差异中新增或更新的 Unity Editor 扩展的验证检查清单。

> **注：** 建议与 [示例场景验证检查清单](sample-scene-test-checklist.md) 一并使用。验证仅 Play 模式可用的工具时，需要 `MidiSampleScene` 或 `MidiGameplaySampleScene` 等支持 Play 的场景。

<div class="page" />

## 通用前置准备

- [ ] 在 Unity 编辑器中打开项目，**Console 中无编译错误**
- [ ] `Window > MIDI` 菜单下各项可见
- [ ] 验证仅 Play 模式工具前，准备会调用 `MidiManager.InitializeMidi()` 的示例场景
- [ ] 可按需打开 `Window > MIDI > Monitor` 确认 IN/OUT

### Scripting Define Symbols 一览

| 符号 | 目标编辑器扩展 |
|----------|------------------|
| `FEATURE_USE_MPTK` + Maestro / MPTK 资源 | SMF 预览的 **Preview Audio (MPTK)** |
| `FEATURE_USE_TIMELINE` + `com.unity.timeline` | Timeline 集成编辑器（标记生成、Clip / Marker Inspector 等） |
| `FEATURE_USE_VISUALSCRIPTING` + `com.unity.visualscripting` | Visual Scripting 菜单、节点注册 |

<div class="page" />

## 新增 / 更新的 Editor 扩展一览

### 核心编辑器工具（`Window > MIDI`）

| # | 工具 | 菜单 | 模式 | 主要源文件 |
|---|--------|----------|--------|------------|
| 1 | MIDI 监视器（更新） | `Window > MIDI > Monitor` | 仅 Play | `MidiMonitorWindow.cs` 等 |
| 2 | 虚拟 MIDI 控制器（新增） | `Window > MIDI > Virtual Controller` | 仅 Play | `VirtualMidiControllerWindow.cs` |
| 3 | SMF 预览（新增） | `Window > MIDI > SMF Preview` | 仅 Edit | `SmfPreviewWindow.cs` |
| 4 | SMF 导入（新增） | `Assets > Import MIDI File...` | 仅 Edit | `SmfImportUtility.cs` |
| 5 | Project Settings（新增） | `Edit > Project Settings > MIDI` | Edit | `MidiProjectSettingsProvider.cs` |

### Unity 集成编辑器

| # | 工具 | 菜单 | 前提 |
|---|--------|----------|------|
| 6 | 生成集成示例资源 | `Window > MIDI > Samples > Generate Integration Sample Assets` | 无 |
| 7 | Animator Driver Inspector | Inspector（`MidiAnimatorDriver`） | 无 |
| 8 | Timeline 标记生成 | `Window > MIDI > Timeline > Generate Markers From Sequence Asset` | `FEATURE_USE_TIMELINE` |
| 9 | 添加 Timeline Notification Receiver | `Window > MIDI > Timeline > Add Notification Receiver To Director` | `FEATURE_USE_TIMELINE` |
| 10 | Visual Scripting 辅助 | `Window > MIDI > Visual Scripting/*` | `FEATURE_USE_VISUALSCRIPTING` |

<div class="page" />

## 推荐测试顺序

1. **Project Settings** — 首次导入时的自动生成、设置 UI
2. **SMF Preview / Import** — 仅在 Edit 模式完成的功能
3. **MIDI Monitor** — Play 中的 IN/OUT 捕获、过滤、导出
4. **Virtual Controller** — 与 Monitor 并用确认发送
5. **SMF Preview Audio (MPTK)** — 启用 MPTK 时的 Play 模式试听
6. **Unity 集成编辑器** — Animator / Timeline / Visual Scripting

<div class="page" />

## 分工具检查清单

### 1. Project Settings（`Edit > Project Settings > MIDI`）

**目的：** 管理全局 MIDI 设置，首次导入时自动生成资源

- [ ] 打开项目后自动生成 `Assets/MIDI/Resources/MidiProjectSettings.asset`
- [ ] **Devices** 部分：可编辑并保存 Default Input / Output Device Id
- [ ] **Bluetooth MIDI**：可更改 Auto Scan / Timeout
- [ ] **RTP-MIDI**：可更改 Port / Session Name
- [ ] **Debug**：可更改 Log Level / Enable MIDI Monitor On Play
- [ ] **Development**：可更改 Development Build Only Verbose Log
- [ ] Play 模式中，可从已连接设备下拉选择 Default Device
- [ ] `enableMidiMonitorOnPlay = true` 时，进入 Play 会自动打开 Monitor

---

### 2. SMF Preview（`Window > MIDI > SMF Preview`）

**目的：** 在 Edit 模式下预览与导出 `.mid`

#### 文件加载

- [ ] 打开窗口后显示拖放区域「Drop .mid file here」
- [ ] 拖放 `.mid` / `.midi` → 显示摘要（Format / Ticks/Quarter / Tracks / Length）
- [ ] 通过 `Assets > Import MIDI File...` 选择文件 → 打开 SMF Preview 并加载
- [ ] 加载无效文件时，HelpBox 显示错误消息

#### 内容显示

- [ ] 在 **Tracks** 列表中选择轨道 → **Events** 列表切换
- [ ] Events 列（Tick / Time / Type / Ch / Detail）正确显示
- [ ] **Note Roll (text)** 折叠展开 → 显示音符列表

#### 导出

- [ ] **Export as MidiSequenceAsset** → 在 Project 中保存 `.asset` 并 Ping
- [ ] 导出的 `MidiSequenceAsset` 可被 `SmfPlayer` 或 Timeline Clip 引用
- [ ] **Export as JSON** → 保存外部 `.json` 并显示状态消息

#### 音频预览（MPTK）

**前提：** `FEATURE_USE_MPTK`、Maestro / MPTK 资源

- [ ] **Preview Audio (MPTK)** → 进入 Play 模式并开始播放 SMF
- [ ] Console 出现 `[SMF Preview Playback] Playing ...` 日志
- [ ] **Stop Preview** → 停止播放，销毁 `SmfPreviewPlaybackHost`
- [ ] 播放结束后，主机 GameObject 自动销毁

**未启用 MPTK 时：**

- [ ] Audio Preview 部分显示 Info HelpBox（需要 MPTK），不显示按钮

---

### 3. MIDI Monitor（`Window > MIDI > Monitor`）

**目的：** Play 中实时显示 MIDI IN/OUT 并导出日志

**前提：** Play 模式（Edit 模式下不记录消息）

#### 基本显示

- [ ] Play `MidiSampleScene` 等并打开 Monitor
- [ ] IN：实体输入或 Inject 显示 Note On/Off / CC / PC
- [ ] OUT：显示来自 `MidiSend` 或 Virtual Controller 的发送
- [ ] 显示 Time / Dir / Device / Ch / Type / Detail 列
- [ ] Note 类 Detail 包含音名（例如 `C4 (60)`）
- [ ] 设备连接 / 断开事件以 Device 类型显示

#### 工具栏

- [ ] **Clear** → 清除日志
- [ ] **Auto Scroll** → 新增行时滚动到末尾
- [ ] 更改 **Max Lines** → 限制保留行数（1–10000）

#### 过滤

- [ ] **Direction**：All / IN / OUT
- [ ] **Device**：按已连接设备筛选
- [ ] **Ch**：通道筛选
- [ ] **Type**：NoteOn / NoteOff / CC / PC / Device
- [ ] **Search**：Detail / Device 部分匹配搜索
- [ ] 关闭再打开窗口 → 过滤器设置从 EditorPrefs 保存并恢复

#### 列宽调整

- [ ] 拖动列标题右端 → 更改列宽
- [ ] 关闭再打开窗口 → 列宽保持

#### 导出

- [ ] **Export CSV** → 将过滤后的日志保存为 `.csv`
- [ ] **Export TXT** → 将过滤后的日志保存为 `.txt`
- [ ] **Export SMF** → 将过滤后的 OUT 事件保存为 `.mid`
- [ ] 无法导出（无事件）时，status 显示错误消息
- [ ] 导出的 SMF 可在 SMF Preview 中重新加载

#### 生命周期

- [ ] Play 结束后，日志内容仍保留在窗口中
- [ ] 再次 Play → 追加新消息（可用 Clear 清除）

---

### 4. Virtual MIDI Controller（`Window > MIDI > Virtual Controller`）

**目的：** 无需实体设备即可发送 Note / CC / PC / Pitch Bend

**前提：** Play 模式，且已调用 `MidiManager.InitializeMidi()`

#### Edit 模式

- [ ] Edit 模式下显示 HelpBox「Play mode only」，不可操作

#### Play 模式 — 设备

- [ ] 显示输出设备列表（Project Settings 默认 / 已连接设备 / `editor:virtual-controller`）
- [ ] 未连接时自动注册 `editor:virtual-controller` 虚拟输出
- [ ] Channel / Group / Velocity 设置生效

#### Play 模式 — 键盘

- [ ] 点击 2 八度键盘（C3–B4）→ Note On
- [ ] 松开鼠标 → Note Off
- [ ] Monitor OUT 显示 Note On/Off

#### Play 模式 — CC / 其他

- [ ] 操作 16 个 CC 滑块 → 发送 CC（在 Monitor OUT 确认）
- [ ] CC 编号设置经 EditorPrefs 保存 / 恢复
- [ ] Program Change / Pitch Bend 发送（UI 部分）
- [ ] **All Notes Off** → 向全通道发送 CC 123
- [ ] **Panic** → 向全通道发送 CC 120 + 123

#### Play 结束

- [ ] Play 结束时，按下的音符被 All Off

---

### 5. 生成集成示例资源

**菜单：** `Window > MIDI > Samples > Generate Integration Sample Assets`

- [ ] 执行菜单 → 显示完成对话框
- [ ] 生成 `Assets/MIDI/Samples/Integrations/Resources/MidiAnimatorSample.controller`
- [ ] Controller 包含 Height / Pulse / BlendX / BlendY 参数
- [ ] 再次执行时覆盖并重新生成现有 Controller

---

### 6. MidiAnimatorDriver Inspector

**目的：** 编辑 Animator 绑定，Play 中显示实时值

**步骤：**

1. 打开 `MidiAnimatorIntegrationSampleScene`，或选择带有 `MidiAnimatorDriver` 的 GameObject
2. 查看 Inspector

- [ ] 显示 Animator / Mapping / CC Smoother / Register With MidiManager 字段
- [ ] 未设置 Mapping 时，Bindings 数组可编辑
- [ ] **Add Binding** / **Remove Last** 可添加 / 删除绑定
- [ ] 设置 Mapping 时，HelpBox 提示 Bindings 使用 Asset 侧
- [ ] 未设置 Animator 时，HelpBox 警告
- [ ] Play 模式中显示绑定的 Animator 参数实时值

---

### 7. Timeline 集成编辑器

**前提：** `FEATURE_USE_TIMELINE`、`com.unity.timeline`

#### MidiPlaybackClip Inspector

- [ ] 在 Timeline 上选择 Midi Playback Clip → 可编辑 Sequence Asset / Tempo BPM / Output Channel / Mute / Solo
- [ ] 设置 Sequence Asset 时显示 Tracks 数量 HelpBox

#### MidiPlaybackClip Timeline 显示

- [ ] Clip 显示名称随 Sequence Asset 名称变化
- [ ] Mute / Solo 时显示 `(Muted)` / `(Solo)` 后缀

#### 标记 Inspector / Timeline 显示

- [ ] `MidiBarMarker` — Bar Number / 拍号、Timeline 工具提示
- [ ] `MidiTempoMarker` — BPM、Timeline 工具提示
- [ ] `MidiMarker` / `MidiSignalEmitter` — 编辑发送消息、工具提示

#### Generate Markers From Sequence Asset

**步骤：**

1. 在 Project 中选择 `MidiSequenceAsset`
2. 在 Timeline 窗口中打开 PlayableDirector
3. 执行 `Window > MIDI > Timeline > Generate Markers From Sequence Asset`

- [ ] 生成小节标记（MidiBarMarker）
- [ ] 生成速度变更标记（MidiTempoMarker）
- [ ] 可用 Undo 还原
- [ ] 未选择 Asset / 未分配 Director 时，对话框给出指引

#### Add Notification Receiver To Director

- [ ] 在 Timeline 中选择 Director 后执行菜单
- [ ] 添加 `MidiTimelineNotificationReceiver` 组件
- [ ] 已存在时不会重复添加

---

### 8. Visual Scripting 辅助

**前提：** `FEATURE_USE_VISUALSCRIPTING`

**菜单：** `Window > MIDI > Visual Scripting/*`

#### 自动注册

- [ ] 项目加载后，`jp.kshoji.midi.visualscripting` 自动添加到 Visual Scripting Node Library

#### Open Visual Scripting Settings

- [ ] 打开 `Project Settings > Visual Scripting`

#### Regenerate MIDI Nodes

- [ ] 执行 → Console 显示 `[MIDI Visual Scripting] Node library regeneration requested.`（成功时）
- [ ] Script Graph 中显示 **Events > MIDI** 节点类别

#### Show Node Categories

- [ ] 对话框列出 MIDI/Events、MIDI/Send、MIDI/Playback、MIDI/Utility、MIDI/Note

#### 节点行为（联合确认）

- [ ] Play `MidiVisualScriptingIntegrationSampleScene`
- [ ] 经 Script Graph 执行 Note On / CC 事件

---

## 跨工具联动确认

以下是确认多个编辑器扩展协同工作的集成测试。

### A. Virtual Controller → Monitor

- [ ] Play 中用 Virtual Controller 发送 Note → 显示在 Monitor OUT

### B. SMF Preview → MidiSequenceAsset → Timeline

- [ ] 在 SMF Preview 中加载 `.mid` → 导出 MidiSequenceAsset
- [ ] 分配给 Timeline 的 Midi Playback Clip → Play 时同步播放（`MidiTimelineIntegrationSampleScene`）

### C. SMF Preview → MPTK 试听

- [ ] 在 SMF Preview 中 **Preview Audio (MPTK)** → 自动进入 Play 模式 → 音频输出
- [ ] 在 Monitor 中确认 OUT 消息（MPTK 虚拟输出）

### D. Monitor → SMF 再导入

- [ ] Play 中进行 MIDI 操作 → 记录到 Monitor → **Export SMF**
- [ ] 在 SMF Preview 中加载导出的 `.mid` → 事件内容一致

### E. Project Settings → Virtual Controller

- [ ] 更改 Default Output Device Id → 反映到 Virtual Controller 设备列表

<div class="page" />

## 新增源文件（参考）

| 文件 | 作用 |
|----------|------|
| `MidiMonitorWindow.cs` | MIDI 监视器 UI（CSV/TXT/SMF 导出、列宽调整） |
| `MidiMonitorLifecycle.cs` | Play 开始钩子、自动打开 Monitor |
| `MidiMonitorLogBuffer.cs` | 日志缓冲 |
| `MidiMonitorSettings.cs` | 过滤 / 列宽 EditorPrefs |
| `MidiMonitorSmfExportUtility.cs` | Monitor 日志 → SMF 转换 |
| `VirtualMidiControllerWindow.cs` | 虚拟控制器 UI |
| `VirtualMidiControllerState.cs` | CC 编号 / 设备 ID EditorPrefs |
| `UI/PianoKeyboardElement.cs` | 编辑器键盘 UI |
| `UI/CcSliderBankElement.cs` | CC 滑块组 UI |
| `SmfPreviewWindow.cs` | SMF 预览窗口 |
| `SmfPreviewModel.cs` | 预览数据模型 |
| `SmfImportUtility.cs` | SMF 加载 / 导出 |
| `SmfPreviewAudioPlayback.cs` | MPTK Play 模式试听 |
| `MidiProjectSettingsProvider.cs` | Project Settings UI |
| `MidiProjectSettingsBootstrap.cs` | 首次 Settings 资源生成 |
| `Integrations/MidiIntegrationSampleSetup.cs` | 集成示例 Controller 生成 |
| `Integrations/Animator/MidiAnimatorDriverEditor.cs` | Animator Driver Inspector |
| `Integrations/Timeline/*` | Timeline Clip / Marker 编辑器、标记生成 |
| `Integrations/VisualScripting/MidiVisualScriptingMenu.cs` | VS 节点注册菜单 |

<div class="page" />

## 相关文档

- [编辑器工具](editor-tools.md)
- [示例场景验证检查清单](sample-scene-test-checklist.md)
- [Unity 生态系统集成](integrations.md)
- [SMF 工具](smf-tools.md)
- [Maestro / MPTK 集成](mptk.md)
