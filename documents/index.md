# Unity MIDI Plugin — Documentation Index

This documentation describes the contents of `Assets/MIDI`, how the runtime API is structured, and how to use MIDI 1.0, MIDI 2.0 (UMP), MPE, and related transports in Unity.

## NotebookLM
I've created an AI chat that registers this manual.  
If you have any questions, please use this tool first. (A Google account is required.)  
https://notebooklm.google.com/notebook/c98f2512-35e0-4396-9a9d-686f662b6f4a

## Languages
- [日本語](i18n/ja/index.md)
- [中文(简体)](i18n/zh-CN/index.md)

## Contents

### Getting started
- [Getting Started (Install, Initialize, Send/Receive)](getting-started.md)
- [Build PostProcessing & Scripting Define Symbols](build-postprocessing.md)
- [Platforms & Limitations](platforms.md)

### Core APIs
- [MIDI 1.0 (MidiManager)](midi1.md)
- [MIDI 2.0 / UMP (Midi2Manager)](midi2.md)
- [MPE (MIDI Polyphonic Expression)](mpe.md)
- [SMF / Sequencing (jp.kshoji.midisystem)](smf.md)

### Utilities & Editor Tools
- [Utilities (MidiNoteUtility / MidiMessageBuilder / PitchBend / Timing)](utilities.md)
- [Editor Tools (Monitor / Virtual Controller / SMF Preview / Project Settings)](editor-tools.md)

### Gameplay Components
- [Gameplay Components (InputMap / NoteTracker / Filter)](gameplay.md)
- [SMF Tools (SmfPlayer / MidiRecorder / TempoMapExtractor)](smf-tools.md)

### Unity Ecosystem Integration
- [Unity Integration (Timeline / Animator / Visual Scripting / Input System / Scriptable Audio / Chunity)](integrations.md)

### Genre Kits
- [Genre Kits (Networking / Clock / Chords & Scales / Input System / Foundation)](kits.md)

### Transports & integrations
- [Transports & Platforms](transports.md)
- [Inter-App MIDI — Cross‑platform Notes (Android, iOS/macOS, Linux)](inter-app-midi.md)
- [Maestro / MPTK Integration (Virtual Devices + Adapters)](mptk.md)

### Project architecture notes
- [Virtual Devices & Event Injection](virtual-devices.md)
- [MIDI-CI / Capability Negotiation](midi-ci.md)
- [Editor & Lifecycle Notes](editor-and-lifecycle.md)
- [Embedded Third-Party Modules](third-party.md)

### Samples & reference
- [Samples](samples.md)
- [Tested Devices](tested-devices.md)
- [Contacts / Support](contacts.md)
- [Version History](version-history.md)

### Future features (unimplemented)

The following are not included in the current release. Refer to them as a roadmap.

Already available elsewhere (do not treat as missing): pitch-bend normalize / 14-bit split (`PitchBendUtility` in [Utilities](utilities.md)); composable Channel/Device filters ([Gameplay](gameplay.md)); runtime latency calibration (`MidiLatencyCalibrator` in Foundation / [Genre Kits](kits.md)); one-way SMF → JSON export from [SMF Preview](editor-tools.md).

| Category | Feature | Overview |
|----------|---------|----------|
| Editor | Device Browser | `Window > MIDI > Device Browser` — connected device list, Vendor/Product ID, test send |
| Editor | Scene View Debug Overlay | Displays held notes and recent messages in the scene during Play |
| Editor | Latency Measurement (RTT tool) | Editor send→receive round-trip UI (for BLE / RTP-MIDI evaluation; distinct from Foundation tap calibration) |
| Gameplay | MPE High-level API | `MpeInputHandler` — `UnityEvent` integrating per-note expression (low-level in [MPE](mpe.md)) |
| Gameplay | MIDI Event Routing | Priority and exclusive control across multiple `MidiInputRouter` instances |
| Utilities | 14-bit CC | `MidiCc14BitUtility` — assemble/decompose CC MSB/LSB pairs |
| Utilities | Pitch Bend → semitones | Convert 14-bit pitch bend to a configurable semitone range (normalize/split already shipped) |
| Utilities | UMP Parser Helper | `UmpParser` — typed decomposition of UMP word sequences |
| Utilities | SysEx Builder / Parser | High-level assemble and parse helpers beyond `MidiMessageBuilder.SystemExclusive` / send APIs |
| Utilities | SMF ↔ JSON (bidirectional runtime) | Runtime import/export both ways (Editor Preview already exports JSON one-way) |
| Chunity | MIDI 2.0 / UMP Mapping | 32-bit velocity / per-note controller → ChucK globals (currently via MIDI 1.0 path) |
| Chunity | InstanceTarget API Completion | Thin wrappers over the official API surface such as `SetString` / `ListenForChuckEventOnce` / `Get*Array` / `RunFile` arguments |
| Chunity | Syncer / Poller | ChucK → Unity read-back via `Chuck*Syncer` / EventListener wrapping |
| Chunity | Associative Arrays, `*_AT`, VM Control | Named parameters, audio-thread write boundaries, `SetRunning` / stop Event conventions |
| Chunity | UGen Probe / Host Time Advancer | Waveform visualization, Unity-driven ChucK time (complementing Clock sync) |
| Chunity Generator | Optimization | Native pointer API, Burst, Scriptable Effect / Root Output (when needed) |

## Directory structure (Assets/MIDI)

- `Plugins/`  
  Native (and WebGL JS) plugins per platform (Android/iOS/macOS/Linux/WSA/WebGL).
- `Scripts/`  
  Main C# runtime:
    - `MidiManager.cs` (MIDI 1.0)
    - `Midi2Manager.cs` (MIDI 2.0 / UMP)
    - platform plugins: `MidiPlugin.*.cs`, `Midi2Plugin.*.cs`
    - event handler interfaces: `IMidi*EventHandler`, `IMidi2*EventHandler`
    - MPE: `MpeManager.cs`, `IMpeEventHandler.cs`
    - MIDI-CI: `MidiCapabilityNegotiator.cs`
    - virtual devices: `MidiManager.VirtualDevices.cs`
- `Scripts/Utilities/`  
  Domain-grouped runtime helpers (namespace `jp.kshoji.unity.midi.util`):
    - `Messaging/` — `MidiOutgoingMessage`, `MidiMessageBuilder`, `PitchBendUtility`, `MidiControlSmoothing`, `AutomationInterpolation`, …
    - `MusicTheory/` — `MidiNoteUtility`
    - `Smf/` — `TempoMapExtractor`, `TempoMap`, `MeasureTimeUtility`, `TupletUtility`, `SwingUtility`, `MidiMetaMessageFactory`, …
    - `Monitor/` — `MidiMonitorLogData`, `MidiMonitorFormatHelper`
- `Scripts/Editor/`  
  Editor extensions:
    - `PostProcessBuild.cs` (build post-processing)
    - `MidiMonitorWindow.cs` etc. (MIDI monitor)
    - `VirtualMidiControllerWindow.cs` (virtual MIDI controller)
    - `SmfPreviewWindow.cs` (SMF preview / import)
    - `MidiProjectSettingsProvider.cs` (Project Settings)
    - `UI/PianoKeyboardElement.cs` (includes `MidiKeyboardLogic`)
- `Resources/MidiProjectSettings.asset`  
  Global MIDI settings (auto-generated on first use)
- `Scripts/Gameplay/`  
  Gameplay components:
    - `MidiInputMap.cs` / `MidiInputRouter.cs` (MIDI → UnityEvent)
    - `MidiNoteTracker.cs` (held-note state management)
    - `MidiFilterBase.cs` / `MidiChannelFilter.cs` / `MidiDeviceFilter.cs` (event filtering)
    - `SmfPlayer.cs` / `MidiRecorder.cs` / `MidiRecordingSession.cs` (SMF playback/recording)
    - `MidiClockSync.cs` / `MidiClockOutput.cs` / `MidiChordDetector.cs` (Clock sync / chord detection)
    - `MidiCcProcessorBase.cs` / `MidiCcSmoother.cs` / `MidiCcButton.cs` (CC processing)
    - `Theory/` — `ChordRecognition`, `MidiScaleUtility` / `ScaleUtility`
- `Scripts/Integrations/Animator/`  
  Animator integration (`MidiAnimatorDriver`, `MidiBlendTreeDriver`)
- `Scripts/Integrations/Timeline/`  
  Timeline integration (`MidiPlaybackTrack`, `MidiRecordTrack`, markers)
- `Scripts/Integrations/VisualScripting/`  
  Visual Scripting nodes (`MidiVisualScriptingBridge`, event / action units)
- `Scripts/Integrations/Networking/`  
  Network MIDI sync (`MidiNetworkHub`, `MidiNetworkClient`, optional Mirror / Netcode / WSNet2 bridges, `FEATURE_MIDI_NETWORK` + bridge symbols)
- `Scripts/Integrations/InputSystem/`  
  Input System bridge (`MidiInputSystemBridge`, `MidiSyntheticDevice`, etc., `FEATURE_INPUT_SYSTEM`)
- `Scripts/Integrations/ScriptableAudio/`  
  Scriptable Audio Pipeline integration (`FEATURE_SCRIPTABLE_AUDIO`, Unity 6000.3+)
- `Scripts/Integrations/Chunity/`  
  Chunity integration (`FEATURE_CHUNITY`, runtime not bundled) and Scriptable Generator (`FEATURE_CHUNITY_SCRIPTABLE_AUDIO`)
- `Scripts/Foundation/`  
  Foundation (`MidiLatencyCalibrator`, `MidiDeviceSelection`, `MidiOutputRoutingPreset`, `MidiFoundationUiController`, etc.)
- `UI/Foundation/`  
  UXML / USS for the foundation UI
- `Settings/`  
  Shared Input System assets (e.g. `MidiController.inputactions`)
- `Scripts/MidiSequenceAsset.cs`  
  SMF ScriptableObject (`Create > MIDI > Sequence Asset`)
- `Scripts/UmpSequenceAsset.cs`  
  UMP `.midi2` ScriptableObject
- `Scripts/MidiProjectSettings.cs`  
  Global MIDI settings ScriptableObject
- `Scripts/midisystem/`  
  Standard MIDI File (SMF) reader/writer + sequencing model (`Sequence`, `Track`, messages).
- `Scripts/UmpSequencer/`  
  UMP sequence utilities (clip/container read/write, sequencer, SMF↔UMP converter).
- `Scripts/RTP-MIDI-for-.NET/`  
  RTP-MIDI implementation (embedded module + its own docs).
- `Scripts/MdnsVendor/`  
  Shared mDNS / DNS-SD vendor libraries (Network MIDI 2.0 + RTP-MIDI Zeroconf).
- `Samples/`  
  Example scenes and scripts.
- `Samples/Integrations/`  
  Unity ecosystem integration samples (Animator / Timeline / Visual Scripting / Networking / Input System / Chunity / Scriptable Audio).
- `Samples/Gameplay/`  
  Gameplay samples (Clock sync / chord & scale detection).
- `Samples/Foundation/`  
  Foundation samples (device selection / latency / UI shell).

## Concepts & terminology

- **DeviceId**: A string identifier used throughout the API to refer to a MIDI endpoint.
- **Group**: MIDI 2.0 group index (0–15). Many MIDI 1.0 APIs accept `group` for consistency.
- **Channel**: MIDI channel (0–15). Note that some APIs use 0-based channel numbers.
- **UMP (Universal MIDI Packet)**: MIDI 2.0 packet format represented as `uint[]` words in this project.

## Quick start checklist

1. Decide which feature set you need:
    - MIDI 1.0 events and sending: `MidiManager`
    - MIDI 2.0 / UMP parsing and sending: `Midi2Manager`
    - Android Inter-App MIDI: see [Inter-App MIDI](inter-app-midi.md)
    - MPE management: `MpeManager` on top of `MidiManager`
    - Bind MIDI → UnityEvent from the Inspector: `MidiInputRouter` in [Gameplay Components](gameplay.md)
2. Implement one or more event handler interfaces.
3. Register handler objects with the manager.
4. Initialize the manager (or ensure it’s present in the scene).
5. Test using the sample scenes in `Assets/MIDI/Samples/Scenes`.
6. During development, check input/output messages via `Window > MIDI > Monitor` ([Editor Tools](editor-tools.md)).
