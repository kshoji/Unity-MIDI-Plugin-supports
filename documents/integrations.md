# Unity Ecosystem Integrations

This page describes the integration features with Timeline, Animator, Visual Scripting, Input System, Networking, Scriptable Audio Pipeline, Chunity (ChucK), and Maestro / MPTK.

Namespaces:

| Integration | Namespace |
|------|----------|
| Animator | `jp.kshoji.unity.midi` |
| Timeline | `jp.kshoji.unity.midi.timeline` |
| Visual Scripting | `jp.kshoji.unity.midi.visualscripting` |
| Input System | `jp.kshoji.unity.midi.integrations.inputsystem` |
| Networking | `jp.kshoji.unity.midi.net` |
| Scriptable Audio | `jp.kshoji.unity.midi.scriptableaudio` |
| Chunity | `jp.kshoji.unity.midi.integrations.chunity` |
| MPTK | `jp.kshoji.unity.midi.mptk` |

Locations:

| Integration | Location |
|------|------|
| Animator | `Assets/MIDI/Scripts/Integrations/Animator/` |
| Timeline | `Assets/MIDI/Scripts/Integrations/Timeline/` |
| Visual Scripting | `Assets/MIDI/Scripts/Integrations/VisualScripting/` |
| Input System | `Assets/MIDI/Scripts/Integrations/InputSystem/` |
| Networking | `Assets/MIDI/Scripts/Integrations/Networking/` |
| Scriptable Audio | `Assets/MIDI/Scripts/Integrations/ScriptableAudio/` |
| Chunity | `Assets/MIDI/Scripts/Integrations/Chunity/` |
| MPTK | `Assets/MIDI/Scripts/Integrations/MPTK/` |

### Unity-MCP (optional)

Cursor / MCP clients can drive diagnostics and setup via [Unity-MCP](https://github.com/IvanMurzak/Unity-MCP). Tools live under `Assets/MIDI/Scripts/Integrations/Mcp/` and compile only when `com.ivanmurzak.unity.mcp` is installed (plus NuGet gates). **Not part of Asset Store core distribution.**

| Item | Value |
|------|-------|
| Sample prompts | [unity-mcp-sample-prompts.md](unity-mcp-sample-prompts.md) |
| Checklist | [unity-mcp-define-matrix.md](unity-mcp-define-matrix.md) |
| Integration README | [../Scripts/Integrations/Mcp/README.md](../Scripts/Integrations/Mcp/README.md) |
| VST host | Separate package — [Unity-VST3-Bridge](https://github.com/kshoji/Unity-VST3-Bridge) |

Quick path: install Unity-MCP → Play Mode `midi-init` → `midi-devices-list`. For a **built Player**, add `MidiMcpRuntimeBootstrap`, point **Host** at the LAN MCP Server, and use control tools only after Editor wiring (see [MCP README](../Scripts/Integrations/Mcp/README.md)). Networking tools require `FEATURE_MIDI_NETWORK` (`midi-net-*` setup = Editor; `discovery-status` / `rtt` = Player). MIDI 2.0 / MPE / CI tools are always in the core MCP asm (`midi2-*`, `mpe-zone-status`, `midi-ci-discover`). VST host MCP is desktop-oriented in [Unity-VST3-Bridge](https://github.com/kshoji/Unity-VST3-Bridge) (not an Android MCP target).

<div class="page" />

## Prerequisites

### Timeline Integration

1. Install **Timeline** (`com.unity.timeline`) via the Unity Package Manager
2. Add the scripting define symbol `FEATURE_USE_TIMELINE` in Project Settings

| Item | Value |
|------|-----|
| Assembly Definition | `jp.kshoji.midi.timeline` |
| Scripting Define Symbol | `FEATURE_USE_TIMELINE` |
| Settings Path | `Project Settings > Player > Other Settings > Script Compilation > Scripting Define Symbols` |

When the symbol is not defined, the Timeline integration asmdef and sample code (inside `#if FEATURE_USE_TIMELINE` blocks) are not compiled.

### Visual Scripting Integration

1. Install **Visual Scripting** (`com.unity.visualscripting`) via the Unity Package Manager
2. Add the scripting define symbol `FEATURE_USE_VISUALSCRIPTING` in Project Settings

| Item | Value |
|------|-----|
| Assembly Definition | `jp.kshoji.midi.visualscripting` |
| Scripting Define Symbol | `FEATURE_USE_VISUALSCRIPTING` |
| Settings Path | `Project Settings > Player > Other Settings > Script Compilation > Scripting Define Symbols` |

When the symbol is not defined, the Visual Scripting integration asmdef and sample code (inside `#if FEATURE_USE_VISUALSCRIPTING` blocks) are not compiled.

### Input System Integration

1. Install **Input System** (`com.unity.inputsystem`) via the Unity Package Manager
2. Add the scripting define symbol `FEATURE_INPUT_SYSTEM` in Project Settings

| Item | Value |
|------|-----|
| Assembly Definition | `jp.kshoji.midi.inputsystem` |
| Scripting Define Symbol | `FEATURE_INPUT_SYSTEM` |
| Settings Path | `Project Settings > Player > Other Settings > Script Compilation > Scripting Define Symbols` |

When the symbol is not defined, the Input System integration asmdef and sample code (inside `#if FEATURE_INPUT_SYSTEM` blocks) are not compiled.

### Networking Integration

1. Add `FEATURE_MIDI_NETWORK` (UDP hub/client — no extra package)
2. Optional bridges (packages **not** bundled):
   - Mirror: install Mirror → `FEATURE_MIRROR` (+ `MIRROR`)
   - Netcode: UPM `com.unity.netcode.gameobjects` → `FEATURE_NETCODE` (`MIDI_HAS_NETCODE` via `versionDefines`)
   - WSNet2: copy client + `WSNet2.Runtime.asmdef` → `FEATURE_WSNET2` (`MIDI_HAS_WSNET2` via Editor sync); run Lobby/Game servers separately

| Item | Value |
|------|-----|
| Core assembly | `jp.kshoji.midi.net` |
| Bridge assemblies | `jp.kshoji.midi.net.mirror` / `.netcode` / `.wsnet2` |
| Scripting Define Symbol | `FEATURE_MIDI_NETWORK` (+ optional bridge symbols) |
| Settings Path | `Project Settings > Player > Other Settings > Script Compilation > Scripting Define Symbols` |

Details: [Networking Integration](../Scripts/Integrations/Networking/README.md) · [Build Post-processing](build-postprocessing.md#optional-mirror--netcode--wsnet2-bridges)

### Scriptable Audio Pipeline Integration

1. Use **Unity 6000.3 LTS** or later (except WebGL)
2. Add the scripting define symbol `FEATURE_SCRIPTABLE_AUDIO` in Project Settings
3. Optional packages (already bundled in `Packages/manifest.json` when using this repository):
   - `com.unity.burst`
   - `com.unity.collections`

| Item | Value |
|------|-----|
| Assembly Definition | `jp.kshoji.midi.scriptableaudio` |
| Scripting Define Symbol | `FEATURE_SCRIPTABLE_AUDIO` |
| Settings Path | `Project Settings > Player > Other Settings > Script Compilation > Scripting Define Symbols` |

When the symbol is not defined, or on Unity 6.2 and earlier / WebGL, the integration asmdef is not compiled (`ScriptableAudioUtility.IsAvailable == false`). The core `jp.kshoji.midi` can still be built as usual.

For details on the enablement procedure, see [Build Post-processing — Scriptable Audio Pipeline Integration](build-postprocessing.md#scriptable-audio-pipeline-integration-optional).

### Chunity (ChucK) Integration

1. Install [Chunity](https://chuck.stanford.edu/chunity/) **separately** (this plugin does not bundle the runtime)
2. Copy `Integrations/Chunity/Optional/Chunity.Runtime.asmdef.example` to the Chunity Scripts root as `Chunity.Runtime.asmdef`
3. Add the scripting define symbol `FEATURE_CHUNITY` in Project Settings

| Item | Value |
|------|-----|
| Assembly Definition | `jp.kshoji.midi.chunity` |
| Scripting Define Symbol | `FEATURE_CHUNITY` |
| Settings Path | `Project Settings > Player > Other Settings > Script Compilation > Scripting Define Symbols` |

When the symbol is not defined, the integration asmdef and the sample's `#if FEATURE_CHUNITY` blocks are not compiled. For details on the enablement procedure, see [Build Post-processing — Chunity Integration](build-postprocessing.md#chunity-chuck-integration-optional).

#### Scriptable Audio Generator (additional)

1. Apply the `useBuiltInAudioFilter` patch described in the [Chunity Optional patch guide](../Scripts/Integrations/Chunity/Optional/README.md) to Chunity
2. Install Unity package **`com.unity.collections`** (required by `jp.kshoji.midi.chunity.scriptableaudio`)
3. Add `FEATURE_CHUNITY_SCRIPTABLE_AUDIO` (requires `FEATURE_CHUNITY`)

| Item | Value |
|------|-----|
| Assembly Definition | `jp.kshoji.midi.chunity.scriptableaudio` |
| Package | `com.unity.collections` |
| Scripting Define Symbol | `FEATURE_CHUNITY` + `FEATURE_CHUNITY_SCRIPTABLE_AUDIO` |
| Sample | `ChunityGeneratorWorkflowSampleScene` |

Details: [Chunity Integration](../Scripts/Integrations/Chunity/README.md)

### Maestro / MPTK Integration

1. Install Maestro / MidiPlayerTK **separately** (this plugin does not bundle the runtime)
2. Add the scripting define symbol `FEATURE_USE_MPTK` in Project Settings
3. When using **Maestro Pro** APIs, also add `MPTK_PRO`

| Item | Value |
|------|-----|
| Assembly Definition | `jp.kshoji.midi.mptk` |
| Scripting Define Symbol | `FEATURE_USE_MPTK` (+ optionally `MPTK_PRO`) |
| Location | `Assets/MIDI/Scripts/Integrations/MPTK/` |

`FEATURE_USE_MPTK` enables the Free-tier bridge (virtual MIDI device sink / MPTK-as-input adapters).  
`MPTK_PRO` further unlocks code gated with `#if MPTK_PRO` that depends on Maestro Pro APIs — for example the `OnMidiEvent` rewrite pipeline (`MptkMidiEventPipeline`), Writer / External player bridges, InnerLoop, ListPlayer, Spatializer, and related Pro samples. Without `MPTK_PRO`, those Pro-only paths stay out of compilation; Free-tier integration still builds when only `FEATURE_USE_MPTK` is set.

For details, see [Maestro / MPTK Integration](mptk.md) and [MPTK Integration](../Scripts/Integrations/MPTK/README.md).

### Animator Integration

- No additional packages or scripting define symbols are required (included in the core asmdef `jp.kshoji.midi`)

For a detailed enablement procedure, see [Build Post-processing — Unity Integration Packages](build-postprocessing.md#unity-integration-packages-optional).

<div class="page" />

## Animator Integration

Automatically converts MIDI input into Unity Animator parameters. Performance and system messages are mapped through bindings, while SysEx / Raw UMP are received via the `onMessage` callback.

### Components

| Component | Role |
|----------------|------|
| `MidiAnimatorDriver` | MIDI reception → Animator parameters + `onMessage` |
| `MidiAnimatorMapping` | ScriptableObject. Binding definitions can be shared |
| `MidiBlendTreeDriver` | Feeds 2–4 channel CC into Blend Tree axes |

### Two-layer model

| Layer | Target | Use |
|----------|------|------|
| Bindings | MIDI mappable to Float / Int / Bool / Trigger | CC, notes, Program Change, Clock, etc. |
| `onMessage` | All messages (including SysEx / Raw UMP) | Custom scripts, payload processing |

`onMessage` and bindings can be used together for the same message. To avoid double processing, use only one of them.

### Binding (`MidiParameterBinding`)

| Field | Description |
|------------|------|
| `messageType` | `MidiOutgoingMessageType` (NoteOn / ControlChange / TimingClock, etc.) |
| `group` | 0–15, `-1` = all groups |
| `channel` | 0–15, `-1` = all channels (ignored for group-only messages such as Clock / Start) |
| `controllerOrNote` | Note / CC / Program number, etc. |
| `valueFilter` | Value filter, `-1` = all |
| `mapMessageValue` | Maps NoteOn velocity to Float/Int (equivalent to the old Velocity) |
| `animatorParameterName` | Target Animator parameter name |
| `parameterType` | Float / Int / Bool / Trigger |
| `responseCurve` | Normalized input 0–1 → output |
| `smoothingTime` | Smoothing seconds for CC / Pitch Bend, etc. |
| `deviceIdFilter` | Device ID filter (empty = all) |

### Mapping rules

| messageType | Recommended parameterType | Behavior |
|-------------|-------------------|------|
| NoteOn / NoteOff | Bool / Trigger | On/Off or Trigger (only on On) |
| NoteOn + `mapMessageValue` | Float / Int | Velocity normalization |
| ControlChange / PitchWheel / Aftertouch | Float | 0–1 normalization + curve |
| ProgramChange / SongSelect, etc. | Int | Sets `number` directly |
| TimingClock / Start / Stop / Reset, etc. | **Trigger** | `SetTrigger` on reception |
| SystemExclusive / Raw UMP | — | **`onMessage` only** |

**Note:** Binding TimingClock to a Trigger fires on every tick. Be mindful of the timing at which the Animator Trigger is consumed, or thin out the ticks in `onMessage`.

### Setup

1. Attach `MidiAnimatorDriver` to a GameObject that has an Animator
2. Create a mapping via `Assets > Create > MIDI > Animator Mapping` (optional)
3. Configure the bindings (Message Type / Group / Channel in the Inspector)
4. If SysEx / UMP is needed, connect a listener to `onMessage`
5. Call `MidiManager.InitializeMidi()` in the scene bootstrap

### Binding examples

| Use | messageType | controllerOrNote | parameterType | Parameter name |
|------|-------------|------------------|---------------|--------------|
| Motion speed | ControlChange | 1 | Float | Speed |
| Jump | NoteOn | 60 | Trigger | Jump |
| Strike strength | NoteOn + mapMessageValue | 60 | Float | StrikePower |
| Tone number | ProgramChange | 5 | Int | Program |
| Playback start | Start | — | Trigger | TransportStart |

### `onMessage` example

```csharp
driver.onMessage.AddListener(args =>
{
    if (args.message.messageType == MidiOutgoingMessageType.SystemExclusive)
    {
        var payload = args.message.payload;
        // SysEx processing
    }
});
```

### MidiBlendTreeDriver

For XY pads and DJ controllers. Specify 2–4 CCs and feed 0–1 normalized values into the corresponding Animator float parameters (default: `BlendX` / `BlendY`).

<div class="page" />

## Timeline Integration

Play SMF, record MIDI, and generate markers on the Unity Timeline.

### Tracks and clips

| Type | Class | Binding | Description |
|------|--------|----------------|------|
| Playback track | `MidiPlaybackTrack` | `SmfPlayer` | Plays SMF clips synchronized to Timeline time |
| Playback clip | `MidiPlaybackClip` | — | `MidiSequenceAsset` reference, BPM, channel remap, `mute` / `solo` |
| Record track | `MidiRecordTrack` | `MidiRecorder` | Records MIDI input during the clip interval |
| Record clip | `MidiRecordClip` | — | Start/Stop recording while the clip is active |

### Setup (playback)

1. Place a `PlayableDirector` and `SmfPlayer` on a GameObject
2. Select **Add > MIDI Playback Track** in the Timeline window
3. Assign `SmfPlayer` to the track's Binding
4. Place a **Midi Playback Clip** on the track
5. Set a `MidiSequenceAsset` in the clip's `Sequence Asset`
6. Confirm in Play mode that the Timeline and SMF play in sync

### Time synchronization

The clip-local time of the Timeline is the master for `SmfPlayer.Seek()`.

- During Play: when the difference exceeds 30 ms, it follows by seeking
- Pause / scrub: pauses the `SmfPlayer` and seeks to the position

Per-clip output control is possible via `MidiPlaybackClip`'s `outputChannel` (`-1` = keep original channel), `mute`, and `solo`.

**Limitations:** Piano-roll editing and direct editing of UMP clips on the Timeline are not supported.

### Markers

| Marker | Description |
|----------|------|
| `MidiMarker` | Sends a MIDI message at the specified position (`MidiTimelineNotificationReceiver` required) |
| `MidiSignalEmitter` | Sends a MIDI message together with a Timeline Signal |
| `MidiBarMarker` | Bar head. Displays time signature information as a label |
| `MidiTempoMarker` | Tempo change position |

`MidiMarker` / `MidiSignalEmitter` set what to send via `MidiTimelineOutgoingMessage`. In addition to MIDI 1.0 performance and system messages, they also support SysEx / System Common / Raw UMP (MIDI 2.0).

**Setup (send marker)**

1. Add `MidiTimelineNotificationReceiver` to the same GameObject as the `PlayableDirector` (also possible via `Window > MIDI > Timeline > Add Notification Receiver To Director`)
2. Place a `MidiMarker` or `MidiSignalEmitter` on the Timeline's Marker Track
3. Set the Message Type / Group / Channel / Payload (for SysEx / UMP) in the Inspector

**Auto-generation**: Select a `MidiSequenceAsset` in the Project window and run `Window > MIDI > Timeline > Generate Markers From Sequence Asset`. Use it with the PlayableDirector open in the Timeline window.

### Signal integration

Set `MidiTimelineSignalHandler` as the target of a Signal Receiver and call the following from a Timeline Signal:

- `SendNoteOn(int note, int velocity)`
- `SendNoteOff(int note, int velocity)`
- `SendControlChange(int controller, int value)`
- `SendProgramChange(int program)`
- `SendSystemExclusive(byte[] payload)`
- `SendRawUmp(uint[] umps)`
- `SendOutgoingMessage(MidiTimelineOutgoingMessage message)` — supports all message types

<div class="page" />

## Visual Scripting Integration

Build MIDI input/output on a Script Graph without writing code.

### Bridge

Add `MidiVisualScriptingBridge` to a GameObject that has a Script Graph attached. It forwards events from `MidiManager` to the Visual Scripting Event Bus. Typed event nodes and the generic `On MIDI Message` can be used together, but connecting both to the same message causes double execution, so use only one of them.

### Event nodes (Events > MIDI)

| Node | Output |
|--------|------|
| **On MIDI Message** | deviceId, messageType, group, channel, number, value, data3, payload, payloadLength — all MIDI 1.0 / SysEx / Raw UMP |
| On MIDI Note On | group, channel, note, velocity, deviceId |
| On MIDI Note Off | group, channel, note, velocity, deviceId |
| On MIDI Control Change | group, channel, controller, value, deviceId |
| On MIDI Program Change | group, channel, program, deviceId |
| On MIDI Pitch Bend | group, channel, amount (0–16383), deviceId |

### Action nodes

| Category | Node |
|----------|--------|
| MIDI > Send | **Send MIDI Message** (generic DTO + payload), Send MIDI Note On / Off / Control Change / Program Change / **Pitch Bend** (all support `group`) |
| MIDI > Playback | SMF Player Play / Stop |
| MIDI > Utility | Get Active MIDI Notes, **Serialize MIDI UMP Words**, **Deserialize MIDI UMP Words** |
| MIDI > Note | Note Name To Number / Number To Note Name / Is Note In Scale |

**Send MIDI Message payload examples**

| messageType | payload |
|-------------|---------|
| SystemExclusive | SysEx byte sequence (`F0 ... F7`) |
| SystemCommonMessage | System Common byte sequence |
| Midi2RawUmp | A byte sequence concatenating UMP words in big-endian 4-byte units. Can also be generated with the `Serialize MIDI UMP Words` unit |

### Setup

1. Install Visual Scripting via the Package Manager
2. Attach a `Script Machine` and `MidiVisualScriptingBridge` to a GameObject
3. Create a Script Graph and place event nodes from **Events > MIDI**
4. Call `MidiManager.InitializeMidi()` in the scene bootstrap
5. Confirm in Play mode that MIDI input → the graph executes

### Is Note In Scale

Uses `MidiScaleUtility`. Supports `Major` / `NaturalMinor` / `MajorPentatonic` / `MinorPentatonic`.

<div class="page" />

## Input System Integration

Using the Unity Input System's Synthetic Device and `.inputactions`, connect MIDI input/output to your existing Input Action-based game logic.

### Components

| Component | Direction | Role |
|----------------|------|------|
| `MidiInputSystemBridge` | MIDI → Input System | Injects received MIDI into the Synthetic Device. SysEx / Raw UMP via the `onMessage` callback |
| `InputSystemToMidiBridge` | Input System → MIDI | Sends Input Actions as `MidiOutgoingMessage` |
| `MidiSyntheticDevice` | — | 128 notes + 128 CC + channel-dedicated axes |
| `MidiInputSystemMapping` | — | A ScriptableObject of Action names and MIDI conditions |

### Synthetic Device layout

| Control | Path example | Content |
|--------------|--------|------|
| `note[128]` | `<MidiSynthetic>/note60` | Note 0–1 (velocity / Poly AT) |
| `cc[128]` | `<MidiSynthetic>/cc1` | CC 0–1 |
| `pitch[16]` | `<MidiSynthetic>/pitch0` | Pitch Bend 0–1 (0.5 = center) |
| `program[16]` | `<MidiSynthetic>/program0` | Program Change 0–1 |
| `channelPressure[16]` | `<MidiSynthetic>/channelPressure0` | Channel Aftertouch 0–1 |
| `systemPulse[16]` | `<MidiSynthetic>/systemPulse0` | Pulses for Clock / Start / Stop, etc. (group index) |

SysEx / System Common / Raw UMP cannot be mapped to the button/axis model, so they are not supported by the Synthetic Device. Receive them as a `MidiOutgoingMessage` DTO via `MidiInputSystemBridge.onMessage`.

### Mapping (MIDI → Input System)

Create a `MidiInputSystemMapping` via `Assets > Create > MIDI > Input System > Mapping`.

| Field | Description |
|------------|------|
| `actionName` | Action name within `.inputactions` (for documentation) |
| `messageType` | `MidiOutgoingMessageType` (NoteOn / ControlChange / PitchWheel, etc.) |
| `group` | 0–15, `-1` = all groups |
| `channel` | 0–15, `-1` = all channels |
| `controllerOrNote` | Note / CC number / match condition |
| `valueFilter` | Value filter, `-1` = all |
| `targetControlIndex` | CC fallback target (`-1` = `controllerOrNote`) |
| `preferDedicatedControl` | If `true`, use pitch / program / channelPressure / systemPulse |
| `invertAxis` | Inverts the normalized axis |

### Reverse binding (Input System → MIDI)

The `InputToMidiBinding` of `InputSystemToMidiBridge` uses a common DTO format.

| Field | Description |
|------------|------|
| `messageType` / `canceledMessageType` | Send type on performed / canceled |
| `group` / `channel` / `number` / `value` / `data3` | `MidiOutgoingMessage` fields |
| `useActionValue` | Converts the Action value to 0–127 (0–16383 for Pitch) |
| `sendOnPerformed` / `sendOnCanceled` | Send timing |

The old fields `note` / `controller` / `sendControlChange` are still read.

### Setup

1. Install Input System via the Package Manager
2. Define `FEATURE_INPUT_SYSTEM`
3. Add `MidiInputSystemBridge` to a GameObject and assign a `MidiInputSystemMapping` if needed
4. Specify `<MidiSynthetic>/note60`, etc. in the Binding Path of `.inputactions`
5. If output is needed, add `InputSystemToMidiBridge`
6. If SysEx / UMP is needed, connect a listener to `onMessage`

### Sample

| Scene | Path |
|--------|------|
| Input System bridge | `Assets/MIDI/Samples/Integrations/InputSystem/Scenes/InputSystemBridgeSampleScene.unity` |

<div class="page" />

## Scriptable Audio Pipeline Integration

Provides the Unity 6.3+ [Scriptable Audio Pipeline](https://docs.unity3d.com/6000.3/Documentation/Manual/audio-scriptable-processors.html) as an **optional foundation** for realtime audio generation coordinated with MIDI timing. It includes a metronome Generator, a DSP clock bridge, and Pipe communication DTOs as reference implementations.

### Quick start

1. Following the [enablement procedure](build-postprocessing.md#scriptable-audio-pipeline-integration-optional), define `FEATURE_SCRIPTABLE_AUDIO`
2. Add **Scriptable Audio Bootstrap** (`ScriptableAudioBootstrap`) to an empty GameObject
3. Play mode — confirm that you hear a 120 BPM click sound

Or open the sample scene:

`Assets/MIDI/Samples/Integrations/ScriptableAudio/Scenes/ScriptableAudioMetronomeSampleScene.unity`

### Components / API

| Type | Role |
|----|------|
| `ScriptableAudioBootstrap` | Wires up AudioSource + Generator + Bridge all at once |
| `ScriptableAudioUtility` | `IsAvailable`, `EnsureAudioSource`, `AttachGenerator`, `ValidateSetup` |
| `MidiMetronomeGenerator` | Reference `IAudioGenerator` (metronome click) |
| `MidiDspClockBridge` | Sends Transport to the Generator via a DSP snapshot + Pipe |
| `MidiDspClockSnapshot` | A read-only timing DTO for game logic |
| `MidiDspSequenceScheduler` | SMF tick → DSP sample scheduling + Pipe note events |
| `MidiDspSequenceClockMode` | `SmfTempoMap` / `ExternalClock` (exclusive switching of the tempo axis) |
| `MidiDspSequenceMidiOutBridge` | Mirrors scheduled notes to `MidiManager` (main-thread dispatch) |
| `MidiDspUmpSequenceScheduler` | UMP clip tick → DSP sample scheduling + Pipe |
| `MidiDspUmpSequenceClockMode` | `UmpTempoMap` / `ExternalClock` |
| `MidiDspUmpMidi2OutBridge` | Mirrors scheduled UMP packets to `MidiManager.SendMidi2RawUmp` (frame granularity) |
| `MidiDspUmpSequenceBootstrap` | Wires up AudioSource + UMP Scheduler + reference synth + Clock all at once |
| `UmpSequenceSynthGenerator` | Reference `IAudioGenerator` (UMP polyphonic, for verification) |
| `UmpSequenceAsset` | A ScriptableObject holding `.midi2` binary (`ToUmpSequence()`) |
| `MidiDspUmpSequenceSmfFallback` | A fallback that delegates to the SMF Scheduler via `SequenceConverter` |
| `MptkDspUmpSequenceOutput` | UMP Out → MPTK virtual device (`FEATURE_USE_MPTK`; under `MPTK/ScriptableAudio/`) |
| `ScriptableAudioSetupReport` | Setup diagnostics (Error / Warning) |

### Sample demo

| Mode | Content |
|--------|------|
| Standalone | Clicks at `MidiDspClockBridge.fallbackBpm` (default 120) |
| External Clock | Switch via the sample UI. Inject Start / Timing Clock / Stop from the virtual device `virtual:scriptable-audio-sample` |

### SMF sequence playback (DSP sync)

`MidiDspSequenceScheduler` maps the SMF Note On/Off to absolute sample positions on the Unity DSP clock and plays them with the reference synth `MidiSequenceSynthGenerator`. It is a separate layer from `SmfPlayer` (frame-driven, MIDI device output).

| Mode | Content |
|--------|------|
| SMF tempo map | Local Transport (Play/Stop/Seek). Tempo comes from meta events in the SMF + `tempoFactor` |
| External Clock | `clockMode = ExternalClock` + `MidiClockSync`. Follows Start/Stop; BPM is scaled by `EstimatedBpm` / `externalClockReferenceBpm` |
| Hardware MIDI output | Optionally add `MidiDspSequenceMidiOutBridge`. Mirrors the same schedule as the Pipe to `MidiManager` (frame granularity) |

Sample: `Assets/MIDI/Samples/Integrations/ScriptableAudio/Scenes/ScriptableAudioSequenceSampleScene.unity`

Both coexist with `MidiManager.InitializeMidi()`.

### UMP sequence playback (DSP sync)

`MidiDspUmpSequenceScheduler` maps the Note On/Off and UMP channel voices of a `UmpSequence` (`.midi2` clip) to absolute sample positions on the Unity DSP clock and plays them with the reference synth `UmpSequenceSynthGenerator`. It is a **separate layer** from `UmpSequencer` (wall-clock + dedicated-thread driven).

| Mode | Content |
|--------|------|
| UMP tempo map | Local Transport (Play / Pause / Stop / Seek). Tempo comes from Flex `Set Tempo` + PPQ + `tempoFactor` |
| External Clock | `clockMode = ExternalClock` + `MidiClockSync`. Follows Start/Stop; BPM is scaled by `EstimatedBpm` / `externalClockReferenceBpm` |
| Hardware MIDI 2.0 output | Optionally add `MidiDspUmpMidi2OutBridge`. Mirrors the same schedule as the Pipe as raw UMP packets to `MidiManager.SendMidi2RawUmp` (frame granularity) |
| System / SysEx | With `scheduleSystemMessages = true`, schedules UMP System (type `0x1`). Data SysEx (type `0x3`) is supported both as completed and split/reassembled forms via Pipe (inline ≤52B) and UmpOut mirroring |

**When to use which:**

| Use | Recommended |
|------|------|
| Clip editing / recording / `.midi2` trial playback | `UmpSequencer` |
| In-game BGM / loops / Seek / DSP-synced audio | `MidiDspUmpSequenceScheduler` |
| MIDI 1.0 SMF only | `MidiDspSequenceScheduler` |
| Hardware MIDI 2.0 output (frame granularity acceptable) | `MidiDspUmpMidi2OutBridge` + `MidiManager.SendMidi2RawUmp` |

Sample: `Assets/MIDI/Samples/Integrations/ScriptableAudio/Scenes/ScriptableAudioUmpSequenceSampleScene.unity`

Coexists with `MidiManager.InitializeMidi()` / `InitializeMidi2()`. When using the UMP output bridge, call `InitializeMidi2()` and a MIDI 2.0 output device must be connected.

#### Quick start (UMP)

1. Following the [enablement procedure](build-postprocessing.md#scriptable-audio-pipeline-integration-optional), define `FEATURE_SCRIPTABLE_AUDIO`
2. Add **Midi Dsp Ump Sequence Bootstrap** (`MidiDspUmpSequenceBootstrap`) to an empty GameObject
3. Assign a `UmpSequence` in code, or use the demo factory of the sample scene
4. Play mode — confirm that notes are audible through the reference synth

```csharp
var bootstrap = gameObject.AddComponent<MidiDspUmpSequenceBootstrap>();
bootstrap.sequence = UmpSequenceReader.ReadSequence(stream)[0];
bootstrap.EnsureReady();
bootstrap.Scheduler.Play();
```

To turn `.midi2` into an asset, create a `UmpSequenceAsset` (menu **Assets > Create > MIDI > Import UMP Sequence Asset From File**, or `CreateAssetMenu`) and pass the result of `asset.ToUmpSequence()` to the Bootstrap / Scheduler. For details, see [SMF Tools — UmpSequenceAsset](smf-tools.md#umpsequenceasset).

#### Items you can check in the sample UI

| UI | Content |
|----|------|
| C major scale (MIDI 1.0 / MIDI 2.0) | Note On/Off playback (MIDI 2.0 uses 32-bit velocity) |
| Tempo change (120 → 90 BPM) | No beat drift across the Flex `Set Tempo` section |
| Loop first 4 notes | `loopStartTick` / `loopEndTick` (0–1920) |
| Tempo factor | Playback speed multiplier |
| External MIDI Clock | Follows Start/Stop |
| Mirror UMP packets | Enables `MidiDspUmpMidi2OutBridge` (the bridge is created automatically after toggling) |
| Validate Setup | Diagnostic log equivalent to `ScriptableAudioUtility.ValidateUmpSequenceSetup` |

#### Extensions

| Feature | Usage |
|------|--------|
| Group Mute / Solo | `MidiDspUmpSequenceScheduler.SetGroupMute(group, mute)` / `SetGroupSolo(group, solo)` (Group 0–15) |
| MIDI 2.0 controls / attributes | `scheduleMidi2Controls` / `scheduleNativeUmpPackets` (sends CC / PC / PNC, etc. via Pipe or raw UMP) |
| SMF fallback | Bootstrap's `playbackMode = SmfDelegated`. `SequenceConverter.ConvertUmpSequence` → `MidiDspSequenceScheduler` |
| MPTK integration | `MptkDspUmpSequenceOutput` in `MPTK/ScriptableAudio/` (`FEATURE_USE_MPTK` + `FEATURE_SCRIPTABLE_AUDIO`). Routes UMP Out to an MPTK virtual device |

#### Known limitations (reference synth / Pipe)

| Item | Behavior |
|------|------|
| System (type `0x1`) | Can be dispatched to Scheduler → Pipe / UmpOut. **Not processed by the reference synth** (no sound) |
| Data 128-bit (type `0x5`) and 3-word or more | `umpPacketTable` + **full UmpOut mirror**. Pipe inline and the reference synth support **1–2 words only** |
| UMP Stream (type `0xF`) | Out of scope |
| Hardware output precision | Frame granularity (different from the DSP sample precision of the built-in synth) |
| WebGL / Unity 6.2 and earlier | Integration not supported (same as "Limitations" below) |

#### Manual verification checklist (sample scene)

Open `ScriptableAudioUmpSequenceSampleScene` in Play Mode and confirm the following.

1. The MIDI 1.0 / MIDI 2.0 scales sound through the reference synth
2. In the tempo-change demo, beats do not drift and accumulate
3. With loop ON, the 4-note section repeats / it can resume after Seek
4. With External Clock ON, it follows the virtual device's Start/Stop
5. Validate Setup has no Errors, and no bridge-missing Warning appears when UMP Out is OFF
6. (Optional) With UMP Out ON + `InitializeMidi2()` + a real device, raw UMP arrives
7. (Optional) Play a real `.midi2` via `UmpSequenceAsset` and confirm it sounds roughly the same as `UmpSequencer`

The Edit Mode self-checks (Pipe blittable, tick ↔ DSP, Extractor, risk mitigation) are included in **Window > MIDI > Validate Scriptable Audio Setup** or `ScriptableAudioUtility.ValidateFoundation()`. Automated regression via the Unity Test Runner has not been added.

For details on the implementation layout and diagnostic API, also see [Scriptable Audio Integration](../Scripts/Integrations/ScriptableAudio/README.md).

### Diagnostics

- Code: `ScriptableAudioUtility.ValidateSetup(gameObject)` → `ScriptableAudioSetupReport.ToSummary()`
- UMP only: `ScriptableAudioUtility.ValidateUmpSequenceSetup(gameObject)`
- Editor: **Window > MIDI > Validate Scriptable Audio Setup** (validates the selected GameObject, or the first Bootstrap in the scene when nothing is selected. Automatically distinguishes metronome / SMF / UMP)

### Limitations

- **WebGL**: The integration asmdef is excluded. It does not cause a build error; the API is `IsAvailable == false`
- **Unity 6.2 and earlier**: Not compiled due to source guards
- **Double driving the same output**: Do not use `UmpSequencer` and `MidiDspUmpSequenceScheduler` simultaneously for the same music output (unless intentional)
- Details: [Platforms — Scriptable Audio](platforms.md#scriptable-audio-pipeline-optional-integration) / [Scriptable Audio Integration](../Scripts/Integrations/ScriptableAudio/README.md)

<div class="page" />

## Chunity (ChucK) Integration

An **optional integration** that connects ChucK patches on an externally installed [Chunity](https://chuck.stanford.edu/chunity/) to this plugin's MIDI I/O, SMF, Clock, and Timeline / Visual Scripting. ChucK's built-in `MidiIn` depends on OS device numbers and does not pass through this plugin's virtual devices, filters, or network routes, so the core of the integration is **Unity MIDI → ChucK global variables / Events**.

Official API reference: [Chunity Documentation](https://chuck.stanford.edu/chunity/documentation/)

### Quick start

1. Following the [enablement procedure](build-postprocessing.md#chunity-chuck-integration-optional), prepare Chunity and `FEATURE_CHUNITY`
2. Place a `ChuckMainInstance` (and optionally `ChuckSubInstance`)
3. Assign a `.ck` / inline patch to `MidiChuckPatchHost` on the same GameObject (or referenced target)
4. Add `MidiChuckBridge` — by default **Use Default Convention** is on
5. Send MIDI (a real device or virtual Inject) in Play mode and confirm the patch responds

### Components

| Component | Role | Phase |
|----------------|------|-------|
| `MidiChuckInstanceTarget` | Main / Sub dispatch (String / array / associative array / RunFile args / ListenOnce, etc.) | A |
| `MidiChuckBridge` | MIDI → SetInt / SetFloat / Array / Associative + SignalEvent | 1, A |
| `MidiChuckPatchHost` | Runs inline / TextAsset / StreamingAssets `.ck` (Context Menu "Run Patch Now") | 1 |
| `MidiChuckMapping` | MIDI condition → ChucK global / Event (including `AssociativeInt` / `AssociativeFloat`) | 1, A |
| `MidiChuckUtility` | Default global names and `IsAvailable` / `SetupHint` / `SetChuckLogLevel` | 1, E |
| `MidiChuckEventToMidi` | ChucK Event → UnityEvent / MIDI OUT (scalar Get + array read `arraySource`) | 2, D |
| `MidiChuckSmfLink` | `SmfPlayer` → virtual device (default `virtual:chuck-smf`) → Bridge | 2 |
| `MidiChuckClockSync` | `MidiClockSync` → `bpm` / `beat` / `bar` / transport Event | 2 |
| `MidiChuckPolyVoiceHelper` | Polyphonic voiceId (+ optionally `activeNotes[]`, etc.) | 2 |
| `MidiChuckFloatSyncer` / `MidiChuckIntSyncer` / `MidiChuckStringSyncer` | `Chuck*Syncer` wrappers (specify global names from the Inspector) | B |
| `MidiChuckFloatArraySyncer` / `MidiChuckIntArraySyncer` | Array Syncer wrappers | B |
| `MidiChuckParameterPoller` | Read pair for ParameterBinder (multi-parameter polling) | B |
| `MidiChuckHostAdvancer` | Unity master timeStep / pos / tick Event bridge | C |
| `MidiChuckPatchLifecycle` | Cooperative stop Event + shred cleanup + optional Restart | C |
| `MidiChuckSampleBank` | Program / Note / Bank → sample path → SetString + play Event | D |
| `MidiChuckPreset` | float/int snapshot (+ optional patch) | 3 |
| `MidiChuckParameterBinder` | Preset application, live SetFloat / SetInt | 3 |
| `MidiChuckMarker`, etc. | Timeline markers / parameter clips (including Phase E actions) | 3, E (`FEATURE_USE_TIMELINE`) |
| ChucK VS Units | Set/Get String / array, RunFile, Event listen, HostAdvancer, PatchLifecycle, SampleBank, etc. | 3, E (`FEATURE_USE_VISUALSCRIPTING`) |

### Default convention (when no mapping matches)

| MIDI | ChucK |
|------|--------|
| Note On | `midiNote`, `midiVelocity`, Event `noteOn` |
| Note Off | `midiNote`, Event `noteOff` |
| CC | `ccNumber`, `ccValue`, Event `controlChange` (optionally `cc[]`) |
| Pitch Bend | `pitchBend` (-1..1) |
| Program Change | `program` |

A minimal contract example on the sample `.ck` side:

```chuck
global Event noteOn;
global Event noteOff;
global int midiNote;
global float midiVelocity;

SinOsc osc => dac;
while (true) {
    noteOn => now;
    Std.mtof(midiNote) => osc.freq;
    midiVelocity => osc.gain;
}
```

### Mapping (`MidiChuckMapping`)

Use `MidiChuckBinding` to declare "which MIDI condition corresponds to which ChucK variable / Event." Combine `messageType` / `group` / `channel` / `controllerOrNote` (all `-1` = all) with the write target (int / float / Event / float array), `floatScale`, and `broadcastEvent`. You can swap in a different `.ck` without code changes.

### Choosing between Phase 2–3

| Use | Recommended |
|------|------|
| SMF → ChucK | Align the virtual device with `MidiChuckSmfLink` and match `SmfPlayer.outputDeviceId` to the Bridge's `deviceIdFilter` |
| External Clock | `MidiChuckClockSync` (BPM → `bpm`, beat / bar / Start-Stop Event) |
| Polyphony | `MidiChuckPolyVoiceHelper` + Bridge's `useDefaultConvention = false` (prevents double firing with the monophonic convention) |
| Preset UI | `MidiChuckPreset` + `MidiChuckParameterBinder` (sample: `ChunityPresetsSampleScene`) |
| ChucK → MIDI OUT | `MidiChuckEventToMidi` |

### Timeline / Visual Scripting (Phase 3 / E)

| Additional symbol | Assembly | Content |
|--------------|----------|------|
| `FEATURE_USE_TIMELINE` | `jp.kshoji.midi.chunity.timeline` | `MidiChuckMarker` (SetString / Set*Array / RunFile / HostAdvancer* / Lifecycle* / SampleBank, etc.) / `MidiChuckTimelineNotificationReceiver` / `MidiChuckParamTrack` |
| `FEATURE_USE_VISUALSCRIPTING` | `jp.kshoji.midi.chunity.visualscripting` | Units in the **MIDI / Chunity** category (Phase E: Get* / RunFile / Event listen / HostAdvancer / PatchLifecycle / SampleBank, etc.). Register: `Window → MIDI → Visual Scripting → Register Chunity Nodes` |

Editor diagnostics (no additional define required): **Window → MIDI → Chunity → Diagnostics** — log level and integration state.

### Scriptable Audio Generator (additional option)

Leaving the control path (Bridge / Mapping / Preset, etc.) as is, this replaces only the audio output from `OnAudioFilterRead` with the Unity 6.3+ **Scriptable Generator** (`IAudioGenerator`). It is a separate symbol from the main Scriptable Audio integration such as the metronome (`FEATURE_SCRIPTABLE_AUDIO`).

| Type | Role |
|----|------|
| `ChuckMainGeneratorDriver` | Drives the Main VM with `AudioSource.generator` |
| `ChuckSubGeneratorDriver` | Drives the Sub (with spatialization) with the Generator |
| `ChuckAudioOutputMode` | `Auto` / `FilterRead` / `ScriptableGenerator` |

| Mode | Behavior |
|--------|------|
| `Auto` (recommended) | Generator in supported environments, otherwise (WebGL, etc.) the traditional FilterRead |
| `FilterRead` | The same `OnAudioFilterRead` as standard Chunity |
| `ScriptableGenerator` | Strictly uses the Generator (falls back with a one-time warning when unsupported) |

**Setup:**

1. Apply the patch from the [enablement procedure (Scriptable Audio Generator)](build-postprocessing.md#chunity-scriptable-audio-generator-optional), install **`com.unity.collections`**, and add `FEATURE_CHUNITY_SCRIPTABLE_AUDIO`
2. Add `ChuckMainGeneratorDriver` to `ChuckMainInstance` (add `ChuckSubGeneratorDriver` to each `ChuckSubInstance` when using Sub)
3. Set the **Audio Output Mode** (usually `Auto`)
4. Play — the Driver sets `useBuiltInAudioFilter = false` while the Generator is connected to prevent double playback

**Operational notes:**

| Item | Content |
|------|------|
| No double playback | Disable FilterRead when the Generator is enabled. Do not drive them simultaneously |
| Main → Sub order | When **both are Generator**, `ChuckGeneratorTracker` allows the Sub after the Main VM has advanced. When the Main is FilterRead, it depends on the Chunity Mixer order |
| Microphone (`adc`) | The Main Driver supplies the recording buffer to the DSP from the main thread via a lock-free ring |
| Mute | Respects `AudioSource.mute`. Advances the VM clock while muting output only |
| Sample rate | Warns when the ChucK initialization rate differs from the Generator `AudioFormat.sampleRate` |

### Limitations / roadmap

- The Chunity runtime is **not bundled**. All three are required: installation, `Chunity.Runtime.asmdef`, and `FEATURE_CHUNITY`
- If only the symbol is enabled but Chunity is not installed, type resolution errors occur (same as MPTK)
- Users can always call the raw Chunity API directly. This plugin is a thin bridge layer in the MIDI domain
- **Not implemented (roadmap):** MIDI 2.0 / UMP mapping, InstanceTarget's String / Once / GetArray / RunFile arguments, Syncer / associative array / `*_AT` writes, VM / shred control helpers, UGen probe, Host Time Advancer, etc. — see [Future features in the table of contents](index.md#future-features-unimplemented)
- Detailed procedure: [Chunity Integration](../Scripts/Integrations/Chunity/README.md) / [Build Post-processing](build-postprocessing.md#chunity-chuck-integration-optional)

<div class="page" />

## Networking Integration

LAN MIDI event and SMF playback sync. Core path is UDP (`FEATURE_MIDI_NETWORK`). Optional bridges reuse `MidiNetworkMessage` / `MidiNetworkMessageCodec` on Mirror, Netcode for GameObjects, or [WSNet2](https://github.com/KLab/wsnet2).

### Core (UDP)

| Component | Role |
|-----------|------|
| `MidiNetworkHub` | Host; `OnOutboundMessage` feed for bridges |
| `MidiNetworkClient` | Inject received events into a virtual device |
| `MidiPlaybackSync` | `SmfPlayer` position sync |
| `MidiNetworkMessageCodec` | Shared binary codec for optional bridges |

### Optional bridges

| Component | Transport | Authority |
|-----------|-----------|-----------|
| `MidiMirrorBridge` | Mirror `NetworkBehaviour` | Host / server |
| `MidiNetcodeBridge` | NGO `NetworkBehaviour` | Host / server |
| `MidiWsnet2Bridge` | WSNet2 `Room` RPC | Master |

Install the framework locally; do not commit Mirror / NGO / WSNet2 into distribution branches. Shared wiring: assign Master/Host `hub`, peer `playbackSync`, then exercise sample scenes. Bridges subscribe to hub **only while Host/Master**. `ForwardTo*` returns `MidiNetworkTransmitResult`.

Full steps: [Networking Integration](../Scripts/Integrations/Networking/README.md)

<div class="page" />

## Sample scenes

| Scene | Path |
|--------|------|
| Animator integration | `Assets/MIDI/Samples/Integrations/Scenes/MidiAnimatorIntegrationSampleScene.unity` |
| Timeline integration | `Assets/MIDI/Samples/Integrations/Scenes/MidiTimelineIntegrationSampleScene.unity` |
| Visual Scripting integration | `Assets/MIDI/Samples/Integrations/Scenes/MidiVisualScriptingIntegrationSampleScene.unity` |
| Input System bridge | `Assets/MIDI/Samples/Integrations/InputSystem/Scenes/InputSystemBridgeSampleScene.unity` |
| Network MIDI (UDP) | `Assets/MIDI/Samples/Integrations/Networking/Scenes/MidiNetworkJamSampleScene.unity` |
| Network MIDI (Mirror) | `Assets/MIDI/Samples/Integrations/Networking/Scenes/MidiMirrorNetworkSampleScene.unity` |
| Network MIDI (Netcode) | `Assets/MIDI/Samples/Integrations/Networking/Scenes/MidiNetcodeNetworkSampleScene.unity` |
| Network MIDI (WSNet2) | `Assets/MIDI/Samples/Integrations/Networking/Scenes/MidiWsnet2NetworkSampleScene.unity` |
| Scriptable Audio integration | `Assets/MIDI/Samples/Integrations/ScriptableAudio/Scenes/ScriptableAudioMetronomeSampleScene.unity` |
| Scriptable Audio SMF sequence | `Assets/MIDI/Samples/Integrations/ScriptableAudio/Scenes/ScriptableAudioSequenceSampleScene.unity` |
| Scriptable Audio UMP sequence | `Assets/MIDI/Samples/Integrations/ScriptableAudio/Scenes/ScriptableAudioUmpSequenceSampleScene.unity` |
| Chunity bridge | `Assets/MIDI/Samples/Integrations/Chunity/Scenes/ChunityBridgeSampleScene.unity` |
| Chunity workflow | `Assets/MIDI/Samples/Integrations/Chunity/Scenes/ChunityWorkflowsSampleScene.unity` |
| Chunity presets | `Assets/MIDI/Samples/Integrations/Chunity/Scenes/ChunityPresetsSampleScene.unity` |
| Chunity mic FX | `Assets/MIDI/Samples/Integrations/Chunity/Scenes/ChunityMicFxSampleScene.unity` |
| Chunity Generator Workflow | `Assets/MIDI/Samples/Integrations/Chunity/Scenes/ChunityGeneratorWorkflowSampleScene.unity` |

Each scene supports IMGUI testing with virtual MIDI input. For a detailed procedure, see [Samples](samples.md).

The Animator `MidiAnimatorSample.controller` is bundled in `Samples/Integrations/Resources/`. To regenerate it, run `Window > MIDI > Samples > Generate Integration Sample Assets`.

<div class="page" />

## VST3 Plugin Host (separate package)

VST3 instrument/effect hosting is **not** part of this MIDI plugin and is **not** shipped in MIDI releases (`VstHostNative.dll`, VST3 SDK, and VST host C# are excluded).

Use the separate package:

| Item | Value |
|------|-------|
| Display name | Unity Plugin Host for VST3 |
| UPM / Git URL | `https://github.com/kshoji/Unity-VST3-Bridge.git` |
| Package id | `jp.kshoji.unity.vst3nativehost` |

Setup, scan paths, and optional MIDI wiring live in that repository (`Documentation~/usage.md`, `midi-integration.md`). This MIDI package only exposes public MIDI events for subscribers; do not add VST implementation under `Assets/MIDI`. Desktop Player MCP for VST lives in the Bridge package; **Android device MCP is not a VST3 target**.

Optional MIDI helpers in the VST package (requires `FEATURE_MIDI_PLUGIN`):

| Feature | Components |
|---------|------------|
| CC / pitch bend → VST parameters | `VstMidiParameterMapping`, `VstHostMidiParameterMapper` |
| SMF → VST instrument | `VstHostSmfLink` + `SmfPlayer.outputDeviceId` → virtual device → `VstHostMidiAdapter` (optional channel routes) |
| Presets / A/B | `VstPresetAsset`, `VstPresetBrowser`, Window → VST3 Host → Preset Browser |

Additional VST package integrations (Timeline / Input System packages enable optional assemblies automatically):

| Feature | Components |
|---------|------------|
| Timeline parameter automation | `VstParameterTrack` / `VstParameterClip`, `VstProgramChangeMarker` (`FEATURE_USE_TIMELINE`) |
| Animator ↔ VST | `VstAnimatorMapping`, `VstAnimatorDriver` |
| Input System → VST | `InputSystemToVstBridge` (`FEATURE_INPUT_SYSTEM`) or MIDI `InputSystemToMidiBridge` → Adapter |
| Editor tools | Plugin Browser (category + vendor/tag), Activity Monitor, Virtual Controller, Project Settings → VST3 Host |
| Plugin chain | `VstPluginChain`, `VstHostChannelRouteSync`, `VstHostMidiFilterLink`, `VstHostEventSink` |
| Scriptable Audio (Unity 6.3+) | `VstHostGenerator`, `VstHostDspMidiOutBridge` → scheduler `extraTimedMidiOutput` |
| Visual Scripting | VST3 Host units / events (`FEATURE_USE_VISUALSCRIPTING`) |
| Network MIDI → VST | `VstHostNetworkMidiLink` (`FEATURE_MIDI_NETWORK`) |
| Chunity ↔ VST | `VstHostChuckEventMidiLink`, `VstHostChuckEffectBridge` (`FEATURE_CHUNITY`) |

Details: VST package `Documentation~/timeline.md`, `animator-input.md`, `editor-tools.md`, `plugin-chain.md`, `scriptable-audio.md`, `visual-scripting.md`, `network-midi.md`, `chunity.md`.

<div class="page" />

## Related documents

- [Gameplay Components](gameplay.md) — `SmfPlayer` / `MidiRecorder` / `MidiInputRouter` / `MidiClockSync`
- [SMF Tools](smf-tools.md) — `MidiSequenceAsset` / `UmpSequenceAsset` / `TempoMapExtractor`
- [Utilities](utilities.md) — `MidiNoteUtility` / `MidiMessageBuilder`
- [Build Post-processing](build-postprocessing.md) — Enabling optional integration symbols
- [Samples](samples.md) — Procedures for integration sample scenes
- [Embedded Third-Party Modules](third-party.md) — Vendored modules (VST host is a separate package, not bundled here)
