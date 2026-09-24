# Unity-MCP define 行列チェックリスト

任意の `FEATURE_*` シンボルを有効化 / 無効化した後に使う。目標: 制約が満たされるときだけ Editor がコンパイルし、MCP アセンブリがロードされること。

## Always（Unity-MCP 導入済み）

| Check | Expect |
|-------|--------|
| `com.ivanmurzak.unity.mcp` present | Package Manager |
| `UNITY_MCP_READY` / `UNITY_MCP_DEPS_3` | NuGet 復元後に DependencyResolver が設定 |
| `jp.kshoji.midi.mcp` loaded | `midi-features-status` → Assemblies |

## Feature matrix

| Define | Runtime asm | MCP asm | Smoke（特記なき限り Edit） |
|--------|-------------|---------|---------------------------|
| *（MCP 以外なし）* | — | `jp.kshoji.midi.mcp` | `midi-features-status`; Play: `midi-init` → `midi-devices-list` |
| `FEATURE_USE_TIMELINE` | `jp.kshoji.midi.timeline` | `jp.kshoji.midi.mcp.timeline` | `midi-timeline-setup-playback` |
| `FEATURE_INPUT_SYSTEM` | `jp.kshoji.midi.inputsystem` | `jp.kshoji.midi.mcp.inputsystem` | `midi-inputsystem-setup-bridge` |
| `FEATURE_USE_VISUALSCRIPTING` | `jp.kshoji.midi.visualscripting` | `jp.kshoji.midi.mcp.visualscripting` | `midi-vs-status` |
| `FEATURE_SCRIPTABLE_AUDIO`（+ Unity 6000.3+） | `jp.kshoji.midi.scriptableaudio` | Editor `jp.kshoji.midi.mcp.scriptableaudio` + Runtime `*.scriptableaudio.runtime` | Editor: `midi-sa-bootstrap-metronome`; Player: `midi-sa-validate` →（Play）`midi-sa-transport` |
| `FEATURE_CHUNITY` | `jp.kshoji.midi.chunity` | Editor `jp.kshoji.midi.mcp.chunity` + Runtime `*.chunity.runtime` | Editor: `midi-chunity-setup-bridge`; Player: `midi-chunity-validate-scene` |
| `FEATURE_USE_MPTK` | `jp.kshoji.midi.mptk` | Editor `jp.kshoji.midi.mcp.mptk` + Runtime `*.mptk.runtime` | Editor: `midi-mptk-bootstrap`; Player: `send-test-note`; Pro+`MPTK_PRO`: `effects-configure` / `voice-lifecycle` / `chord-progression` |
| `FEATURE_MIDI_NETWORK` | `jp.kshoji.midi.net` | Editor `jp.kshoji.midi.mcp.network` + Runtime `*.network.runtime` | Editor: `midi-net-hub-client-setup`; Player: `midi-net-discovery-status` / `midi-net-rtt` |
| `FEATURE_MIRROR`（+ `MIRROR`） | `jp.kshoji.midi.net.mirror` | *（`midi-net-bridge-setup` 経由のブリッジ）* | `midi-net-bridge-setup framework=mirror` |
| `FEATURE_NETCODE`（+ NGO） | `jp.kshoji.midi.net.netcode` | same | `framework=netcode` |
| `FEATURE_WSNET2`（+ `MIDI_HAS_WSNET2`） | `jp.kshoji.midi.net.wsnet2` | same | `midi-wsnet2-sync-defines` の後 `framework=wsnet2` |

## Define OFF

上記各行について: define を外す → 対応 MCP asm は `midi-features-status` で **not-loaded** であること。コア `jp.kshoji.midi.mcp` は引き続きコンパイルされること。

## Core tools（追加 FEATURE なし）

| Tool | Mode | Notes |
|------|------|-------|
| `midi2-devices-list` | Play | `initialize=true` のとき `InitializeMidi2` を呼ぶ（ReadOnly ではない） |
| `midi2-lifecycle` | Play | initialize / terminate / status |
| `midi2-send-ump` / `midi2-send-channel-voice` / `midi2-send-system` / `midi2-send-data` / `midi2-send-flex-data` / `midi2-send-stream` | Play | Structured + raw |
| `midi2-virtual-device` / `midi2-monitor-read` | Play | |
| `midi-send-message` / `midi-panic` / `midi-virtual-device-inject` / `midi-device-info` | Play（info も Play） | MIDI 1.0 カバレッジ |
| `ump-sequencer-control` / `ump-recorder-control` / `ump-sequence-convert` | Mixed | Core UMP Sequence |
| `midi-rtp-session` / `midi-ble-control` / `midi-nearby-control` / `midi2-udp-session` / `midi-transport-status` | Play | Transport |
| `midi-device-selection` / `midi-latency-calibration` / `midi-output-routing` / `midi-clock-*` / `midi-chord-state` / `midi-theory-utility` | Mixed | Foundation / Gameplay |
| `mpe-zone-status` | Edit/Play | `SetupMpeZone` / `setupIfMissing` の後（setup 時は ReadOnly ではない） |
| `midi-ci-discover` | Play | IN+OUT が必要; Discovery タイムアウトまで待機 |

## Smoke checklist（Cursor / Editor ホスト）

| Step | Expect |
|------|--------|
| `midi-features-status` | Assemblies に `jp.kshoji.midi.mcp`（＋ `jp.kshoji.midi.mcp.runtime`）が含まれる |
| Play: `midi-init` → `midi-devices-list` → `midi-send-note` → `midi-monitor-read` | Success chain |
| Play: `midi-send-message messageType=TimingClock` | Success |
| Play: `midi-panic` | Success |
| Play: `midi2-lifecycle action=initialize` → `midi2-send-channel-voice messageType=noteon` | MIDI2 デバイスがあるとき Success |
| Edit: `ump-sequence-convert`（サンプルアセット付き） | Success |
| Play: `midi-transport-status` | Success（プラットフォーム注記は可） |
| `FEATURE_MIDI_NETWORK`: `midi-net-hub-client-setup` | Network asm がロード（CS0104 なし） |

## Player スモーク（Standalone または Android）

ロードされるシーンに `MidiMcpRuntimeBootstrap` を置く（または `UNITY_MCP_HOST` を設定）して **Player** をビルドする。AI クライアントの `mcp.json` は **LAN 上 PC の MCP Server** を指す（デバイス IP ではない）。Android は **INTERNET** 権限と Server URL/port への到達を確認する。

| Step | Expect |
|------|--------|
| `midi-ping` | `player=true`、Host 設定後は `hostConfigured=true`、`aiToolAttrs>=1`；`optionalRuntime` に読み込み済み `*.runtime` |
| Host 空＋シーンに Bootstrap | Console に Host missing の `[Error]`/`[Warning]`；ping は `hostConfigured=false` ＋ hint |
| IL2CPP 後の `midi-ping` / tools/list | ツールが残る；`aiToolAttrs=0` なら `Mcp/**/Runtime/link.xml`（各 `jp.kshoji.midi.mcp*.runtime` の `preserve="all"`）を確認 |
| コア: `midi-init` → `midi-devices-list` → `midi-send-note` → `midi-monitor-read` | Success（実機 MIDI は環境依存） |
| 未配線 | 明確な `[Error]` / `EditorWiringRequiredError` — 先に Editor で配線（Player で create/assign しない） |
| 任意 `FEATURE_USE_MPTK` | Editor bootstrap → Player `validate-setup` → `send-test-note` → `panic` |
| 任意 `FEATURE_CHUNITY` | Editor `setup-bridge` → Player `validate-scene` / `diagnostics` |
| 任意 `FEATURE_SCRIPTABLE_AUDIO` | Editor `midi-sa-bootstrap-*` → Player `validate` →（Play）`transport` |
| 任意 `FEATURE_MIDI_NETWORK` | Editor `hub-client-setup` → Player `discovery-status` / `rtt` |

## Distribution

MCP は `Assets/MIDI/Scripts/Integrations/Mcp/` 配下の **同一リポジトリ任意** のまま。Asset Store コアに Unity-MCP や NuGet プラグインを同梱しない。別 UPM パッケージ化は任意で必須ではない。

VST ホストツール（`vst3-*`）は [Unity-VST3-Bridge](https://github.com/kshoji/Unity-VST3-Bridge) に残る（デスクトップ Player MCP；Android 実機 MCP 対象外）。

カバレッジ詳細: [unity-mcp-api-coverage.md](unity-mcp-api-coverage.md)。  
サンプルプロンプト: [unity-mcp-sample-prompts.md](unity-mcp-sample-prompts.md)。
