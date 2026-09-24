# Unity-MCP 示例提示词集

Related: [Define 矩阵](unity-mcp-define-matrix.md) · [API coverage](unity-mcp-api-coverage.md) · [MCP README](../../../Scripts/Integrations/Mcp/README.md)

面向 Cursor 等 MCP 客户端、调用 Unity MIDI Plugin MCP 工具时的**按场景示例提示词**。按类别举例，并非逐个 Tool ID 的完整清单。

## 用法

1. 在项目中引入 [Unity-MCP](https://github.com/IvanMurzak/Unity-MCP)（`com.ivanmurzak.unity.mcp`），并在 **Window → AI Game Developer** 中配置 Cursor 联动。
2. 将下方提示词粘贴到聊天中（路径、BPM、deviceId 等请按环境改写）。
3. 可选依赖类别在缺少对应 `FEATURE_*` 时不会出现工具本身。参见 [define-matrix](unity-mcp-define-matrix.md)。
4. 发送、监视以及多数播放操作**必须进入 Play Mode**。Edit Mode 会返回 `[Error] … Play Mode …`。

---

## 1. Meta / 诊断

**何时使用：** 首次接触 MCP、不清楚哪些集成已启用、查找文档位置、或想安全地添加 define 时。

**主要工具：** `midi-features-status` · `midi-features-enable` · `midi-integration-docs`

```text
确认 Unity MIDI Plugin 的 MCP 已配置。
1. 用 midi-features-status 汇总 FEATURE_*、包、asm、VST 检测结果
2. 用 midi-integration-docs 指引 Chunity 与 MPTK 的导入文档
若要添加 define，仅使用 allowlist，并以 confirm=true 调用 midi-features-enable
```

```text
想操作 VST3 主机，但本仓库的 MIDI MCP 预期没有 vst3-*。
用 midi-features-status 确认是否存在 jp.kshoji.unity.vst3nativehost，
再用 midi-integration-docs topic=vst3 引导到 VST 侧计划。
```

---

## 2. Core（初始化 / 设备 / 发送 / 监视）

**何时使用：** 想快速确认实体或虚拟 OUT 能否收到。想用 Monitor 区分收发。想在音符名与编号间转换。

**主要工具：** `midi-init` · `midi-devices-list` · `midi-send-note` / `cc` / `pc` · `midi-monitor-read` / `clear` · `midi-note-utility` · `midi-panic`  

```text
进入 Play Mode 后：
midi-init → midi-devices-list → 用 midi-send-note 向第一个 OUT 发送 C4（note=60, velocity=100），
用 midi-monitor-read 确认能否看到 NoteOn。结束后 midi-panic。
```

```text
以 channel=0, value=64 发送 CC1（Modulation），看监视器是否出现。
deviceId 从 midi-devices-list 的 OUT 中选择。
```

```text
用 midi-note-utility 将音符名 D#4 转为编号，将编号 72 转为音符名。
```

---

## 3. SMF / UMP 序列与录音

**何时使用：** 将 `.mid` / `.midi2` 资产化并用 SmfPlayer 播放。查看速度图。将输入录成 Sequential Asset。

**主要工具：** `smf-preview-info` · `smf-import-asset` · `ump-import-asset` · `smf-player-control` · `midi-recorder-control` · `tempo-map-extract` · `ump-sequencer-control` 等

```text
用 smf-preview-info 汇总 Assets 下 sample.mid（轨数、速度、长度）。
没问题则用 smf-import-asset 创建 MidiSequenceAsset。
再用 smf-player-control 在场景中 create / assign SmfPlayer，
Play Mode 下 Play → 数秒后 Stop。
```

```text
在当前场景准备 MidiRecorder，Play Mode 下 start → 键盘输入 → stop →
用 save-asset 保存录音结果。
```

```text
对已导入的 Sequence 用 tempo-map-extract 列出速度变化点。
```

---

## 4. Gameplay（Router / Filter / Virtual / Settings）

**何时使用：** 想快速放置 NoteOn → UnityEvent 脚手架。想按通道/设备过滤。想向虚拟设备注入以单独测处理器。想改 Project Settings。

**主要工具：** `midi-setup-router` · `midi-note-tracker-state` · `midi-filter-configure` · `midi-virtual-device` · `midi-virtual-device-inject` · `midi-project-settings-get` / `set`

```text
用 midi-setup-router 创建 MidiInputMap + MidiInputRouter，
添加一条 NoteOn → UnityEvent 绑定。并简短说明虚拟设备注入的冒烟测试步骤。
```

```text
在 Play Mode 用 midi-virtual-device 注册，注入 NoteOn C4，
用 midi-note-tracker-state 查看按住中的音符是否增加。
```

```text
用 midi-project-settings-get 汇总当前 MIDI Project Settings；
若需变更，先提出差异再 midi-project-settings-set（需要 confirm 时请遵守）。
```

---

## 5. Timeline / Animator / Input System / Visual Scripting

**何时使用：** 想在 Director 上搭建 SMF 播放/录音轨。想用 MIDI 驱动 Animator 参数。想与 Input System Synthetic Device 往返。只想完成 VS 节点注册（自动编辑图不在范围内）。

**主要工具：** `midi-timeline-*` · `midi-animator-*` · `midi-inputsystem-*` · `midi-vs-register-nodes` / `midi-vs-status`  
**前提 define：** Timeline / Input System / VS 各需对应 `FEATURE_*`

```text
在 FEATURE_USE_TIMELINE 前提下，用 midi-timeline-setup-playback
在当前场景组装 PlayableDirector + SmfPlayer + Playback Track/Clip。
用 bind-clip 绑定已有 SequenceAsset。
```

```text
用 midi-animator-add-driver 为 Animator 添加 Driver + Mapping，
用 midi-animator-add-binding 添加 CC1 → Float 参数绑定。
在 Play Mode 说明 midi-animator-read-params 的读法。
```

```text
在 FEATURE_INPUT_SYSTEM 下执行 midi-inputsystem-setup-bridge，
用 midi-inputsystem-list-controls 与 suggest-bindings 为 .inputactions 提议路径。
```

```text
用 midi-vs-status 查看注册情况；不足则 midi-vs-register-nodes。
图编辑可手工完成，只需报告注册结果与缺失依赖。
```

---

## 6. Scriptable Audio

**何时使用：** 想在 Unity 6 Scriptable Audio 上引导节拍器或 DSP Sequence，并验证播放/时钟。

**主要工具：** `midi-sa-bootstrap-metronome` / `smf` / `ump` · `midi-sa-transport` · `midi-sa-clock-mode` · `midi-sa-validate`  
**前提：** `FEATURE_SCRIPTABLE_AUDIO`（Unity 6000.3+）  
```text
在 FEATURE_SCRIPTABLE_AUDIO 下 bootstrap 节拍器（bpm=120）→ 用 midi-sa-validate 修正，
再在 Play Mode 用 midi-sa-transport 直到能听到咔嗒声。
```

```text
用 midi-sa-bootstrap-smf 将已有 MidiSequenceAsset 接到 DSP 路径，
并简短演示 clock-mode 与 transport（Play/Stop/Seek）。
```

---

## 7. Chunity（ChucK）

**何时使用：** 因依赖或 Upstream PR 不足而无声。想脚手架放置 Bridge + PatchHost。想避免复音双重触发。想在 Monitor 确认 ChucK Event → MIDI OUT。想触碰 Generator / Timeline 集成。

**主要工具：** `midi-chunity-diagnostics` / `validate-scene` / `setup-bridge` / `run-patch` / `set-global` · `poly-setup` · `event-to-midi` · `generator-*` 等  
**前提：** `FEATURE_CHUNITY`（Generator 需 `FEATURE_CHUNITY_SCRIPTABLE_AUDIO`）  
**Resource 示例：** `midi://chunity/scene-status`

```text
无声原因排查：
用 midi-chunity-diagnostics 与 midi-chunity-validate-scene（也可用 Resource midi://chunity/scene-status）
报告 Main/Sub/Bridge/PatchHost/define/Upstream PR/WebGL ArraySyncer 注意点。
若缺少 PR，判断是否需要 apply-upstream-prs（confirm=true）。
```

```text
用 setup-bridge 放置 Bridge + PatchHost，用 run-patch 运行接收 noteOn 的短 .ck。
Play Mode 发送 C4，用 poll-global 确认全局 midiNote / noteOn 的响应。
```

```text
用 midi-chunity-poly-setup（useDefaultConvention=false）避免复音双重触发，
在重叠 NoteOn 下检查 voiceId / activeNotes 是否合理。
```

```text
用 midi-chunity-event-to-midi 添加 Event→MIDI 绑定，
从补丁触发 Event → 用 midi-monitor-read 确认 OUT。
```

---

## 8. MPTK（Maestro）

**何时使用：** 想用 `mptk:internal` 确认发声。想试 SMF Preview Audio (MPTK) 等价流程。想触碰 Pro 的 Writer / Pipeline / External。想与 SA DSP 镜像。

**主要工具：** `midi-mptk-bootstrap` / `validate-setup` / `send-test-note` / `panic` · `preview-smf` · `global-settings` · `distance-audio` · Pro `effects-configure` / `voice-lifecycle` / `chord-progression` · `writer-*` / `pipeline-*` · `dsp-*-mirror` · experimental `velocity-attenuation`  
**前提：** `FEATURE_USE_MPTK`（Pro 为 `MPTK_PRO`）  
**Resource 示例：** `midi://mptk/setup-report`

```text
在 FEATURE_USE_MPTK 下：
Play Mode → midi-init → midi-mptk-bootstrap → validate-setup →
midi-mptk-send-test-note（C4）确认发声 → midi-mptk-panic。
失败时汇总 Resource midi://mptk/setup-report。
```

```text
用 midi-mptk-preview-smf 按 Preview Audio (MPTK) 等价方式播放 MidiSequenceAsset（或 .mid），
结束后 panic。
```

```text
MPTK_PRO 前提：writer-load → writer-play（或 write-temp）。
必要时用 external-play 播放 file:// 或 https://，并经 InputAdapter 观察 Monitor。
```

```text
在 Pro Pipeline 中用 pipeline-configure / edit-rule 配置 ch9 Drop + 简单 inject 规则，
播放后读取 midi://mptk/pipeline/stats。与 InputAdapter 并用时请遵循推荐的 post-rewrite 路径。
```

```text
FEATURE_USE_MPTK：用 midi-mptk-global-settings get/set runInBackground；
用 midi-mptk-distance-audio 应用 DistanceAttenuation（与 Spatializer Track/Channel 不同）。
MPTK_PRO：midi-mptk-effects-configure 启用 EnableFilter；
midi-mptk-voice-lifecycle pause 再 resume；
midi-mptk-chord-progression list genre=Pop 再 play uplifting_pop（生成，不是 midi-chord-state 识别）。
```

---

## 9. Networking

**何时使用：** 想看 UDP Hub/Client 脚手架与 RTT。只想要 Mirror / NGO / WSNet2 桥接的落脚点（本体包另装）。

**主要工具：** `midi-net-hub-client-setup` · `midi-net-rtt` · `midi-net-discovery-status` · `midi-net-bridge-setup` · `midi-wsnet2-sync-defines`  
**前提：** `FEATURE_MIDI_NETWORK`  
**Resource 示例：** `midi://network/status`

```text
在 FEATURE_MIDI_NETWORK 下，用 midi-net-hub-client-setup（127.0.0.1）搭建 loopback Hub/Client。
Play Mode + midi-init 后，用 midi-net-rtt ping=true 取 RTT。
可选再看 discovery-status 与 Resource midi://network/status。
```

```text
Hub 搭建后，用 framework=mirror（或 netcode / wsnet2）执行 midi-net-bridge-setup。
WSNet2 请先 midi-wsnet2-sync-defines。不提交厂商包——只报告缺失依赖。
```

---

## 10. MIDI 2.0 / MPE / MIDI-CI

**何时使用：** 想枚举/发送 UMP 设备、确认 MPE 区、试 Capability Inquiry（Discovery）。

**主要工具：** `midi2-devices-list` · `midi2-send-ump` / 结构化 `midi2-send-*` · `mpe-zone-status` · `midi-ci-discover`  
```text
Play Mode 下：midi2-devices-list（initialize=true）→ 向有效 OUT 发送
MIDI 2.0 Note On 等价 UMP（或 midi2-send-channel-voice），并报告结果。
```

```text
在 CI 设备已接 IN+OUT 的前提下执行 midi-ci-discover，
汇总 discoveredCount / MUID / capability 标志。
```

```text
在 MPE 区已设置（或 setupIfMissing）的前提下讲解 mpe-zone-status 内容。
```

---

## 11. Transport / Foundation（辅助）

**何时使用：** 想确认 RTP / BLE / Nearby / UDP2 会话状态，以及输出路由、Clock、延迟校准的入口。完整复刻深层硬件 UI 不在范围内。

**主要工具：** `midi-rtp-session` · `midi-ble-control` · `midi-nearby-control` · `midi2-udp-session` · `midi-transport-status` · `midi-output-routing` · `midi-clock-sync` / `output` · `midi-latency-calibration` · `midi-device-selection` 等

```text
用 midi-transport-status 列出可用传输与注意事项。
若试 RTP，只给出 midi-rtp-session 的最小步骤（深层 Companion 设置不在范围内）。
```

```text
查看 midi-output-routing 现状，并简短说明如何导向特定 deviceId。
必要时用一句话区分 midi-clock-sync / midi-clock-output。
```

---

## 速查表（场景 → 章节）

| 场景 | 查看 |
|------|------|
| MCP 是否可用 / 哪些 FEATURE 生效 | §1 Meta |
| C4 是否到达实体设备 | §2 Core |
| 放置 SMF 并播放/录音 | §3 SMF |
| 游戏侧输入接线 | §4 Gameplay |
| Timeline / Animator / Input System / VS | §5 |
| Scriptable Audio 能否听到咔嗒 | §6 |
| ChucK 集成诊断～发声 | §7 |
| Maestro 发声 / Preview / Pro | §8 |
| 网络同步落脚点 | §9 |
| UMP / MPE / CI | §10 |
| RTP / BLE / Clock 等入口 | §11 |

VST 主机操作（`vst3-*`）不在本文档范围内。请参阅 [Unity-VST3-Bridge](https://github.com/kshoji/Unity-VST3-Bridge) 侧文档。
