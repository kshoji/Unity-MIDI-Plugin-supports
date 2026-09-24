# Unity-MCP define matrix checklist

Use this after enabling/disabling optional `FEATURE_*` symbols. Goal: Editor compiles and MCP assemblies load only when their constraints are satisfied.

## Always (Unity-MCP installed)

| Check | Expect |
|-------|--------|
| `com.ivanmurzak.unity.mcp` present | Package Manager |
| `UNITY_MCP_READY` / `UNITY_MCP_DEPS_3` | Set by DependencyResolver after NuGet restore |
| `jp.kshoji.midi.mcp` loaded | `midi-features-status` → Assemblies |

## Feature matrix

| Define | Runtime asm | MCP asm | Smoke (Edit unless noted) |
|--------|-------------|---------|---------------------------|
| *(none beyond MCP)* | — | `jp.kshoji.midi.mcp` | `midi-features-status`; Play: `midi-init` → `midi-devices-list` |
| `FEATURE_USE_TIMELINE` | `jp.kshoji.midi.timeline` | `jp.kshoji.midi.mcp.timeline` | `midi-timeline-setup-playback` |
| `FEATURE_INPUT_SYSTEM` | `jp.kshoji.midi.inputsystem` | `jp.kshoji.midi.mcp.inputsystem` | `midi-inputsystem-setup-bridge` |
| `FEATURE_USE_VISUALSCRIPTING` | `jp.kshoji.midi.visualscripting` | `jp.kshoji.midi.mcp.visualscripting` | `midi-vs-status` |
| `FEATURE_SCRIPTABLE_AUDIO` (+ Unity 6000.3+) | `jp.kshoji.midi.scriptableaudio` | Editor `jp.kshoji.midi.mcp.scriptableaudio` + Runtime `*.scriptableaudio.runtime` | Editor: `midi-sa-bootstrap-metronome`; Player: `midi-sa-validate` → (Play) `midi-sa-transport` |
| `FEATURE_CHUNITY` | `jp.kshoji.midi.chunity` | Editor `jp.kshoji.midi.mcp.chunity` + Runtime `*.chunity.runtime` | Editor: `midi-chunity-setup-bridge`; Player: `midi-chunity-validate-scene` |
| `FEATURE_USE_MPTK` | `jp.kshoji.midi.mptk` | Editor `jp.kshoji.midi.mcp.mptk` + Runtime `*.mptk.runtime` | Editor: `midi-mptk-bootstrap`; Player: `send-test-note`; Pro+`MPTK_PRO`: `effects-configure` / `voice-lifecycle` / `chord-progression` |
| `FEATURE_MIDI_NETWORK` | `jp.kshoji.midi.net` | Editor `jp.kshoji.midi.mcp.network` + Runtime `*.network.runtime` | Editor: `midi-net-hub-client-setup`; Player: `midi-net-discovery-status` / `midi-net-rtt` |
| `FEATURE_MIRROR` (+ `MIRROR`) | `jp.kshoji.midi.net.mirror` | *(bridge via `midi-net-bridge-setup`)* | `midi-net-bridge-setup framework=mirror` |
| `FEATURE_NETCODE` (+ NGO) | `jp.kshoji.midi.net.netcode` | same | `framework=netcode` |
| `FEATURE_WSNET2` (+ `MIDI_HAS_WSNET2`) | `jp.kshoji.midi.net.wsnet2` | same | `midi-wsnet2-sync-defines` then `framework=wsnet2` |

## Define OFF

For each row above: remove the define → corresponding MCP asm must be **not-loaded** in `midi-features-status`, and core `jp.kshoji.midi.mcp` must still compile.

## Core tools (no extra FEATURE)

| Tool | Mode | Notes |
|------|------|-------|
| `midi2-devices-list` | Play | Calls `InitializeMidi2` when `initialize=true` (not ReadOnly) |
| `midi2-lifecycle` | Play | initialize / terminate / status |
| `midi2-send-ump` / `midi2-send-channel-voice` / `midi2-send-system` / `midi2-send-data` / `midi2-send-flex-data` / `midi2-send-stream` | Play | Structured + raw |
| `midi2-virtual-device` / `midi2-monitor-read` | Play | |
| `midi-send-message` / `midi-panic` / `midi-virtual-device-inject` / `midi-device-info` | Play (info Play) | MIDI 1.0 coverage |
| `ump-sequencer-control` / `ump-recorder-control` / `ump-sequence-convert` | Mixed | Core UMP Sequence |
| `midi-rtp-session` / `midi-ble-control` / `midi-nearby-control` / `midi2-udp-session` / `midi-transport-status` | Play | Transport |
| `midi-device-selection` / `midi-latency-calibration` / `midi-output-routing` / `midi-clock-*` / `midi-chord-state` / `midi-theory-utility` | Mixed | Foundation / Gameplay |
| `mpe-zone-status` | Edit/Play | After `SetupMpeZone` / `setupIfMissing` (not ReadOnly when setup) |
| `midi-ci-discover` | Play | Needs IN+OUT; waits for Discovery timeout |

## Smoke checklist (Cursor / Editor host)

| Step | Expect |
|------|--------|
| `midi-features-status` | Assemblies include `jp.kshoji.midi.mcp` (+ `jp.kshoji.midi.mcp.runtime`) |
| Play: `midi-init` → `midi-devices-list` → `midi-send-note` → `midi-monitor-read` | Success chain |
| Play: `midi-send-message messageType=TimingClock` | Success |
| Play: `midi-panic` | Success |
| Play: `midi2-lifecycle action=initialize` → `midi2-send-channel-voice messageType=noteon` | Success when MIDI2 devices exist |
| Edit: `ump-sequence-convert` (with sample assets) | Success |
| Play: `midi-transport-status` | Success (platform notes OK) |
| `FEATURE_MIDI_NETWORK`: `midi-net-hub-client-setup` | Network asm loads (no CS0104) |

## Player smoke (Standalone or Android)

Build a **Player** with `MidiMcpRuntimeBootstrap` in a loaded scene (or set `UNITY_MCP_HOST`). Point the AI client `mcp.json` at the **MCP Server on the LAN PC**, not the device IP. Android: ensure **INTERNET** permission and that the device can reach the Server URL/port.

| Step | Expect |
|------|--------|
| `midi-ping` | `player=true`, `aiToolAttrs>=1`, `hostConfigured=true` after Host is set; `optionalRuntime` lists loaded `*.runtime` asms |
| `midi-ping` with empty Host + Bootstrap in scene | Console shows Host missing `[Error]`/`[Warning]`; ping shows `hostConfigured=false` + hint |
| `midi-ping` / tools/list after IL2CPP | Tools still listed; if `aiToolAttrs=0`, check `Mcp/**/Runtime/link.xml` (`preserve="all"` on each `jp.kshoji.midi.mcp*.runtime`) |
| Core: `midi-init` → `midi-devices-list` → `midi-send-note` → `midi-monitor-read` | Success (device-dependent for hardware MIDI) |
| Wiring missing | Clear `[Error]` / `EditorWiringRequiredError` — wire in Editor first (no Player create/assign) |
| Optional `FEATURE_USE_MPTK` | Editor bootstrap first → Player `midi-mptk-validate-setup` → `send-test-note` → `panic` |
| Optional `FEATURE_CHUNITY` | Editor `setup-bridge` → Player `validate-scene` / `diagnostics` |
| Optional `FEATURE_SCRIPTABLE_AUDIO` | Editor `midi-sa-bootstrap-*` → Player `midi-sa-validate` → (Play) `midi-sa-transport` |
| Optional `FEATURE_MIDI_NETWORK` | Editor `hub-client-setup` → Player `discovery-status` / `rtt` |

## Distribution

MCP stays **same-repository optional** under `Assets/MIDI/Scripts/Integrations/Mcp/`. Do not ship Unity-MCP or NuGet plugins in Asset Store core. Separate UPM packaging remains optional and is not required.

VST host tools (`vst3-*`) remain in [Unity-VST3-Bridge](https://github.com/kshoji/Unity-VST3-Bridge) (desktop Player MCP; not an Android MCP target).

Coverage detail: [unity-mcp-api-coverage.md](unity-mcp-api-coverage.md).  
Sample prompts: [unity-mcp-sample-prompts.md](unity-mcp-sample-prompts.md).
