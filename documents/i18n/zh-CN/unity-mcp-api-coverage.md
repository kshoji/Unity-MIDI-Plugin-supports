# Unity-MCP API 覆盖矩阵

Pinned: `com.ivanmurzak.unity.mcp` **0.89.0**  
Related: [define 矩阵](unity-mcp-define-matrix.md) · [示例提示词](unity-mcp-sample-prompts.md) · [MCP README](../../../Scripts/Integrations/Mcp/README.md)

Legend: **Direct tool** · **Generic tool** · **Raw only** · **Diagnostic only** · **Out of scope**

## MIDI 1.0 (`MidiOutgoingMessageType`)

| Enum / API | Coverage | Tool |
|------------|----------|------|
| NoteOn / NoteOff | Direct | `midi-send-note` |
| ControlChange | Direct | `midi-send-cc` |
| ProgramChange | Direct | `midi-send-pc` |
| ChannelAftertouch, PolyphonicAftertouch, PitchWheel | Generic | `midi-send-message` |
| SysEx / SystemCommon / SingleByte / MTC / Song* / Tune / Clock / Start/Continue/Stop / ActiveSensing / Reset / Misc / Cable | Generic | `midi-send-message` |
| All Sound/Notes Off | Direct | `midi-panic` |
| Virtual inject（以上全部） | Generic | `midi-virtual-device-inject`（+ 便捷工具 `midi-virtual-device`） |
| Device name/vendor/product | Direct | `midi-device-info` |
| Midi2RawUmp（位于 MIDI1 enum） | Raw only | `midi2-send-ump` |

## MIDI 2.0 / UMP（`MidiManager` send / inject）

| Category | Coverage | Tool |
|----------|----------|------|
| Initialize / Terminate / status | Direct | `midi2-lifecycle` |
| Device list | Direct | `midi2-devices-list` |
| Channel voice（Note/AT/CC/PC/PB/Per-Note/RPN/NRPN relative） | Direct | `midi2-send-channel-voice` |
| Utility + MIDI1 System on UMP path | Direct | `midi2-send-system` |
| SysEx data | Direct | `midi2-send-data` |
| Flex: tempo / time sig / key / text | Direct | `midi2-send-flex-data` |
| Flex: metronome / chord name structs | Raw only | `midi2-send-ump`（结构化 MCP 延后 — 复杂结构体） |
| Stream: EndpointDiscovery / StartOfClip / EndOfClip | Direct | `midi2-send-stream` |
| Stream: Endpoint Info/Identity/Name/Product/FunctionBlock/Config | Raw only | `midi2-send-ump` |
| Raw UMP escape hatch | Raw only | `midi2-send-ump` |
| Virtual register + inject | Direct | `midi2-virtual-device` |
| UMP monitor filter | Direct | `midi2-monitor-read` |

## UMP Sequence

| API | Coverage | Tool |
|-----|----------|------|
| UmpSequencer play/seek/loop | Direct | `ump-sequencer-control` |
| UmpSequencer record/save | Direct | `ump-recorder-control` |
| Sequence ↔ UmpSequence | Direct | `ump-sequence-convert` |
| Import `.midi2` asset | Direct | `ump-import-asset` |

## Transport

| API | Coverage | Tool |
|-----|----------|------|
| RTP-MIDI start/stop/connect | Direct | `midi-rtp-session` |
| BLE scan/advertise | Direct | `midi-ble-control`（平台门控） |
| Nearby discover/advertise | Direct | `midi-nearby-control` |
| UDP MIDI 2 / UMP Endpoint | Direct | `midi2-udp-session`（密钥永不回显） |
| Aggregate status | Diagnostic | `midi-transport-status` |
| `FEATURE_ANDROID_COMPANION_DEVICE` | Diagnostic only | `midi-features-status` / docs（构建后处理，非运行时传输工具） |

## Foundation / Gameplay

| API | Coverage | Tool |
|-----|----------|------|
| MidiDeviceSelection | Direct | `midi-device-selection` |
| MidiLatencyCalibrator | Direct | `midi-latency-calibration` |
| MidiOutputRoutingPreset | Direct | `midi-output-routing` |
| MidiClockSync / MidiClockOutput | Direct | `midi-clock-sync` / `midi-clock-output` |
| ChordRecognition / MidiChordDetector | Direct | `midi-chord-state` |
| Note/scale theory | Direct | `midi-theory-utility`（+ 现有 `midi-note-utility`） |
| SmfPlayer loop/gain/transpose full edit | Out of scope | 使用 `smf-player-control` + Inspector；如有需要可后续添加 `smf-player-configure` |
| InputMap binding CRUD（全部消息类型） | Out of scope | 现有 `midi-setup-router` 覆盖 create/append；完整 list/update/remove 延后 |
| Animator mapping list/update/remove | Out of scope | 现有 `midi-animator-add-*` 仍以 append 为导向 |

## Monitor

| Item | Coverage | Notes |
|------|----------|-------|
| includeRaw / sinceTimestamp | Direct | `midi-monitor-read` |
| MidiMonitorMessageType 扩展（Channel AT / Poly AT / Sys Common/Realtime 拆分） | Out of scope | 需要 `MidiMonitorMessageType` 生产者侧的核心分类变更；Detail 字符串仍为事实来源 |
| Device attach/detach Resource | Out of scope | 优先使用 `midi-device-info` + Play Mode 设备列表 |

## MPTK Free extras

| Tool | Coverage |
|------|----------|
| `midi-mptk-stream-command` | Direct（`MptkUtility`） |
| `midi-mptk-midi2-device` | Direct |
| `midi-mptk-mpe-zone` | Direct（`ConfigureZone`） |
| `midi-mptk-delayed-dispatch` | Direct |
| `midi-mptk-global-settings` | Direct（`MPTK_RunInBackground` / `MPTK_AudioListener`） |
| `midi://mptk/setup-report` | ValidateSetup + 诊断（voice stats / SoundFont / global knobs） |
| `midi-mptk-distance-audio` | Direct（`MptkDistanceAudioSettings` / DistanceAttenuation · Orientation） |
| `midi-mptk-bank-program` | Direct（+ `action=list-drums` 鼓组预设一览） |
| `midi://mptk/soundfont` | SoundFont 名 + 鼓组预设一览 |
| `midi-mptk-velocity-attenuation` | Direct（**experimental** `MPTK_VelocityAttenuation`；默认 960） |

## MPTK Pro extras

| Tool | Coverage |
|------|----------|
| `midi-mptk-effects-configure` | Direct（`MptkSynthEffectsController` / SoundFont filter·reverb·chorus） |
| `midi-mptk-voice-lifecycle` | Direct（`MptkVoiceLifecycle` / `PauseVoices`·`ResumeVoices`） |
| `midi-mptk-chord-progression` | Direct（`MptkChordProgressionPlayer` / Maestro progression presets） |

## MIDI-CI

| Item | Coverage |
|------|----------|
| Discovery | Direct（`midi-ci-discover`） |
| Profile / Property / Protocol Negotiation | 在库 API 就绪前为 Out of scope |

## Scriptable Audio / Network（Editor 与 Player）

| Tool | Host |
|------|------|
| `midi-sa-bootstrap-*` | **Editor**（接线） |
| `midi-sa-transport` / `midi-sa-clock-mode` / `midi-sa-validate` | **Player** 控制（需已有 Generator） |
| `midi-net-hub-client-setup` / `midi-net-bridge-setup` / `midi-wsnet2-sync-defines` | **Editor**（接线 / Define） |
| `midi-net-discovery-status` / `midi-net-rtt` | **Player** 控制（`ensure*` 创建仅 Editor） |

## VST3

| Item | Coverage |
|------|----------|
| Host operations | 本仓库 Out of scope | 见 [Unity-VST3-Bridge](https://github.com/kshoji/Unity-VST3-Bridge)；MIDI Meta 仅检测包。桌面 Player 的 MCP 工具在该包中；**Android 设备 MCP 不是 VST3 目标**。 |

## Safety metadata

| Tool | Note |
|------|------|
| `midi2-devices-list` | 已移除 `ReadOnlyHint`（initialize 有副作用） |
| `mpe-zone-status` | 已移除 `ReadOnlyHint`（setupIfMissing） |
| `midi-net-discovery-status` | 已移除 `ReadOnlyHint`（ensure*） |
| `midi-net-rtt` | 已移除 `ReadOnlyHint`（ping） |
| Destructive paths | define 启用、资源覆盖（UMP convert/save、routing preset）需 `confirm=true` |
