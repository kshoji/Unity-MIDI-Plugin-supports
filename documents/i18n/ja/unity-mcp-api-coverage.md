# Unity-MCP API カバレッジ行列

Pinned: `com.ivanmurzak.unity.mcp` **0.89.0**  
Related: [define 行列](unity-mcp-define-matrix.md) · [サンプルプロンプト](unity-mcp-sample-prompts.md) · [MCP README](../../../Scripts/Integrations/Mcp/README.md)

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
| Virtual inject（上記すべて） | Generic | `midi-virtual-device-inject`（+ 便利ツール `midi-virtual-device`） |
| Device name/vendor/product | Direct | `midi-device-info` |
| Midi2RawUmp（MIDI1 enum 上） | Raw only | `midi2-send-ump` |

## MIDI 2.0 / UMP（`MidiManager` send / inject）

| Category | Coverage | Tool |
|----------|----------|------|
| Initialize / Terminate / status | Direct | `midi2-lifecycle` |
| Device list | Direct | `midi2-devices-list` |
| Channel voice（Note/AT/CC/PC/PB/Per-Note/RPN/NRPN relative） | Direct | `midi2-send-channel-voice` |
| Utility + MIDI1 System on UMP path | Direct | `midi2-send-system` |
| SysEx data | Direct | `midi2-send-data` |
| Flex: tempo / time sig / key / text | Direct | `midi2-send-flex-data` |
| Flex: metronome / chord name structs | Raw only | `midi2-send-ump`（構造化 MCP は延期 — 複雑な構造体） |
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
| BLE scan/advertise | Direct | `midi-ble-control`（プラットフォームゲート） |
| Nearby discover/advertise | Direct | `midi-nearby-control` |
| UDP MIDI 2 / UMP Endpoint | Direct | `midi2-udp-session`（シークレットはエコーしない） |
| Aggregate status | Diagnostic | `midi-transport-status` |
| `FEATURE_ANDROID_COMPANION_DEVICE` | Diagnostic only | `midi-features-status` / docs（ビルド後処理であり、ランタイム輸送ツールではない） |

## Foundation / Gameplay

| API | Coverage | Tool |
|-----|----------|------|
| MidiDeviceSelection | Direct | `midi-device-selection` |
| MidiLatencyCalibrator | Direct | `midi-latency-calibration` |
| MidiOutputRoutingPreset | Direct | `midi-output-routing` |
| MidiClockSync / MidiClockOutput | Direct | `midi-clock-sync` / `midi-clock-output` |
| ChordRecognition / MidiChordDetector | Direct | `midi-chord-state` |
| Note/scale theory | Direct | `midi-theory-utility`（+ 既存 `midi-note-utility`） |
| SmfPlayer loop/gain/transpose full edit | Out of scope | `smf-player-control` + Inspector を使用; 必要なら後で `smf-player-configure` を追加 |
| InputMap binding CRUD（全メッセージ種別） | Out of scope | 既存 `midi-setup-router` が create/append をカバー; 完全な list/update/remove は延期 |
| Animator mapping list/update/remove | Out of scope | 既存 `midi-animator-add-*` は append 志向のまま |

## Monitor

| Item | Coverage | Notes |
|------|----------|-------|
| includeRaw / sinceTimestamp | Direct | `midi-monitor-read` |
| MidiMonitorMessageType 拡張（Channel AT / Poly AT / Sys Common/Realtime 分割） | Out of scope | `MidiMonitorMessageType` 生成側のコア分類変更が必要; Detail 文字列が引き続きソース・オブ・トゥルース |
| Device attach/detach Resource | Out of scope | `midi-device-info` + Play Mode のデバイス一覧を優先 |

## MPTK Free extras

| Tool | Coverage |
|------|----------|
| `midi-mptk-stream-command` | Direct（`MptkUtility`） |
| `midi-mptk-midi2-device` | Direct |
| `midi-mptk-mpe-zone` | Direct（`ConfigureZone`） |
| `midi-mptk-delayed-dispatch` | Direct |
| `midi-mptk-global-settings` | Direct（`MPTK_RunInBackground` / `MPTK_AudioListener`） |
| `midi://mptk/setup-report` | ValidateSetup + 診断（voice stats / SoundFont / global knobs） |
| `midi-mptk-distance-audio` | Direct（`MptkDistanceAudioSettings` / DistanceAttenuation · Orientation） |
| `midi-mptk-bank-program` | Direct（+ `action=list-drums` でドラムプリセット一覧） |
| `midi://mptk/soundfont` | SoundFont 名 + ドラムプリセット一覧 |
| `midi-mptk-velocity-attenuation` | Direct（**experimental** `MPTK_VelocityAttenuation`；既定 960） |

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
| Profile / Property / Protocol Negotiation | ライブラリ API が揃うまで Out of scope |

## Scriptable Audio / Network（Editor と Player）

| Tool | Host |
|------|------|
| `midi-sa-bootstrap-*` | **Editor**（配線） |
| `midi-sa-transport` / `midi-sa-clock-mode` / `midi-sa-validate` | **Player** 制御（既存 Generator 前提） |
| `midi-net-hub-client-setup` / `midi-net-bridge-setup` / `midi-wsnet2-sync-defines` | **Editor**（配線 / Define） |
| `midi-net-discovery-status` / `midi-net-rtt` | **Player** 制御（`ensure*` 作成は Editor のみ） |

## VST3

| Item | Coverage |
|------|----------|
| Host operations | 本リポジトリでは Out of scope | [Unity-VST3-Bridge](https://github.com/kshoji/Unity-VST3-Bridge) を参照; MIDI Meta はパッケージ検出のみ。デスクトップ Player 向け MCP はそのパッケージ側。**Android 実機 MCP は VST3 対象外**。 |

## Safety metadata

| Tool | Note |
|------|------|
| `midi2-devices-list` | `ReadOnlyHint` 削除（initialize の副作用） |
| `mpe-zone-status` | `ReadOnlyHint` 削除（setupIfMissing） |
| `midi-net-discovery-status` | `ReadOnlyHint` 削除（ensure*） |
| `midi-net-rtt` | `ReadOnlyHint` 削除（ping） |
| Destructive paths | define 有効化・アセット上書き（UMP convert/save、routing preset）で `confirm=true` |
