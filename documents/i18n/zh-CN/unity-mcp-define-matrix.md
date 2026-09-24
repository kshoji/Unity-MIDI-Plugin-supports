# Unity-MCP define 矩阵检查清单

在启用/禁用可选 `FEATURE_*` 符号后使用。目标: 仅当约束满足时 Editor 才编译，且 MCP 程序集才会加载。

## Always（已安装 Unity-MCP）

| Check | Expect |
|-------|--------|
| `com.ivanmurzak.unity.mcp` present | Package Manager |
| `UNITY_MCP_READY` / `UNITY_MCP_DEPS_3` | NuGet 还原后由 DependencyResolver 设置 |
| `jp.kshoji.midi.mcp` loaded | `midi-features-status` → Assemblies |

## Feature matrix

| Define | Runtime asm | MCP asm | Smoke（除非注明，否则为 Edit） |
|--------|-------------|---------|---------------------------|
| *（除 MCP 外无）* | — | `jp.kshoji.midi.mcp` | `midi-features-status`; Play: `midi-init` → `midi-devices-list` |
| `FEATURE_USE_TIMELINE` | `jp.kshoji.midi.timeline` | `jp.kshoji.midi.mcp.timeline` | `midi-timeline-setup-playback` |
| `FEATURE_INPUT_SYSTEM` | `jp.kshoji.midi.inputsystem` | `jp.kshoji.midi.mcp.inputsystem` | `midi-inputsystem-setup-bridge` |
| `FEATURE_USE_VISUALSCRIPTING` | `jp.kshoji.midi.visualscripting` | `jp.kshoji.midi.mcp.visualscripting` | `midi-vs-status` |
| `FEATURE_SCRIPTABLE_AUDIO`（+ Unity 6000.3+） | `jp.kshoji.midi.scriptableaudio` | Editor `jp.kshoji.midi.mcp.scriptableaudio` + Runtime `*.scriptableaudio.runtime` | Editor：`midi-sa-bootstrap-metronome`；Player：`midi-sa-validate` →（Play）`midi-sa-transport` |
| `FEATURE_CHUNITY` | `jp.kshoji.midi.chunity` | Editor `jp.kshoji.midi.mcp.chunity` + Runtime `*.chunity.runtime` | Editor：`midi-chunity-setup-bridge`；Player：`midi-chunity-validate-scene` |
| `FEATURE_USE_MPTK` | `jp.kshoji.midi.mptk` | Editor `jp.kshoji.midi.mcp.mptk` + Runtime `*.mptk.runtime` | Editor：`midi-mptk-bootstrap`；Player：`send-test-note`；Pro+`MPTK_PRO`：`effects-configure` / `voice-lifecycle` / `chord-progression` |
| `FEATURE_MIDI_NETWORK` | `jp.kshoji.midi.net` | Editor `jp.kshoji.midi.mcp.network` + Runtime `*.network.runtime` | Editor：`midi-net-hub-client-setup`；Player：`midi-net-discovery-status` / `midi-net-rtt` |
| `FEATURE_MIRROR`（+ `MIRROR`） | `jp.kshoji.midi.net.mirror` | *（经 `midi-net-bridge-setup` 桥接）* | `midi-net-bridge-setup framework=mirror` |
| `FEATURE_NETCODE`（+ NGO） | `jp.kshoji.midi.net.netcode` | same | `framework=netcode` |
| `FEATURE_WSNET2`（+ `MIDI_HAS_WSNET2`） | `jp.kshoji.midi.net.wsnet2` | same | 先 `midi-wsnet2-sync-defines`，再 `framework=wsnet2` |

## Define OFF

对上表每一行: 移除 define → 对应 MCP asm 在 `midi-features-status` 中必须为 **not-loaded**，且核心 `jp.kshoji.midi.mcp` 仍须能编译。

## Core tools（无额外 FEATURE）

| Tool | Mode | Notes |
|------|------|-------|
| `midi2-devices-list` | Play | `initialize=true` 时调用 `InitializeMidi2`（非 ReadOnly） |
| `midi2-lifecycle` | Play | initialize / terminate / status |
| `midi2-send-ump` / `midi2-send-channel-voice` / `midi2-send-system` / `midi2-send-data` / `midi2-send-flex-data` / `midi2-send-stream` | Play | Structured + raw |
| `midi2-virtual-device` / `midi2-monitor-read` | Play | |
| `midi-send-message` / `midi-panic` / `midi-virtual-device-inject` / `midi-device-info` | Play（info 亦为 Play） | MIDI 1.0 覆盖 |
| `ump-sequencer-control` / `ump-recorder-control` / `ump-sequence-convert` | Mixed | Core UMP Sequence |
| `midi-rtp-session` / `midi-ble-control` / `midi-nearby-control` / `midi2-udp-session` / `midi-transport-status` | Play | Transport |
| `midi-device-selection` / `midi-latency-calibration` / `midi-output-routing` / `midi-clock-*` / `midi-chord-state` / `midi-theory-utility` | Mixed | Foundation / Gameplay |
| `mpe-zone-status` | Edit/Play | 在 `SetupMpeZone` / `setupIfMissing` 之后（setup 时非 ReadOnly） |
| `midi-ci-discover` | Play | 需要 IN+OUT；等待 Discovery 超时 |

## Smoke checklist（Cursor / Editor 主机）

| Step | Expect |
|------|--------|
| `midi-features-status` | Assemblies 包含 `jp.kshoji.midi.mcp`（及 `jp.kshoji.midi.mcp.runtime`） |
| Play: `midi-init` → `midi-devices-list` → `midi-send-note` → `midi-monitor-read` | Success chain |
| Play: `midi-send-message messageType=TimingClock` | Success |
| Play: `midi-panic` | Success |
| Play: `midi2-lifecycle action=initialize` → `midi2-send-channel-voice messageType=noteon` | 存在 MIDI2 设备时 Success |
| Edit: `ump-sequence-convert`（含示例资源） | Success |
| Play: `midi-transport-status` | Success（平台说明可接受） |
| `FEATURE_MIDI_NETWORK`: `midi-net-hub-client-setup` | Network asm 已加载（无 CS0104） |

## Player 冒烟（Standalone 或 Android）

在已加载场景中放置 `MidiMcpRuntimeBootstrap`（或设置 `UNITY_MCP_HOST`）后构建 **Player**。AI 客户端 `mcp.json` 指向**局域网 PC 上的 MCP Server**（不是设备 IP）。Android 需 **INTERNET** 权限，并确认可访问 Server URL/端口。

| Step | Expect |
|------|--------|
| `midi-ping` | `player=true`；设置 Host 后 `hostConfigured=true`；`aiToolAttrs>=1`；`optionalRuntime` 列出已加载的 `*.runtime` |
| Host 为空且场景有 Bootstrap | Console 出现 Host missing 的 `[Error]`/`[Warning]`；ping 显示 `hostConfigured=false` 与 hint |
| IL2CPP 后的 `midi-ping` / tools/list | 工具仍在；若 `aiToolAttrs=0`，检查 `Mcp/**/Runtime/link.xml`（各 `jp.kshoji.midi.mcp*.runtime` 的 `preserve="all"`） |
| 核心：`midi-init` → `midi-devices-list` → `midi-send-note` → `midi-monitor-read` | Success（硬件 MIDI 视环境而定） |
| 未接线 | 明确 `[Error]` / `EditorWiringRequiredError` — 先在 Editor 接线（Player 不做 create/assign） |
| 可选 `FEATURE_USE_MPTK` | Editor bootstrap → Player `validate-setup` → `send-test-note` → `panic` |
| 可选 `FEATURE_CHUNITY` | Editor `setup-bridge` → Player `validate-scene` / `diagnostics` |
| 可选 `FEATURE_SCRIPTABLE_AUDIO` | Editor `midi-sa-bootstrap-*` → Player `validate` →（Play）`transport` |
| 可选 `FEATURE_MIDI_NETWORK` | Editor `hub-client-setup` → Player `discovery-status` / `rtt` |

## Distribution

MCP 保持为 `Assets/MIDI/Scripts/Integrations/Mcp/` 下的 **同仓库可选** 内容。不要在 Asset Store 核心中附带 Unity-MCP 或 NuGet 插件。单独 UPM 打包仍为可选，非必需。

VST 主机工具（`vst3-*`）仍在 [Unity-VST3-Bridge](https://github.com/kshoji/Unity-VST3-Bridge)（桌面 Player MCP；非 Android MCP 目标）。

覆盖详情: [unity-mcp-api-coverage.md](unity-mcp-api-coverage.md)。  
示例提示: [unity-mcp-sample-prompts.md](unity-mcp-sample-prompts.md)。
