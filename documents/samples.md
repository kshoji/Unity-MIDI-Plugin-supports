# Samples

This page describes the sample content shipped under:

- `Assets/MIDI/Samples/`

<div class="page" />

## Scenes

Location: `Assets/MIDI/Samples/Scenes/`

- `MidiSampleScene.unity`  
  Demonstrates MIDI 1.0 receive/send workflows via `MidiManager` and MIDI 1.0 event handler interfaces.  
  Includes an optional toggle to route output into an MPTK-backed virtual output device (when enabled).

- `Midi2SampleScene.unity`  
  Demonstrates MIDI 2.0 / UMP workflows via `Midi2Manager` and MIDI 2.0 handler interfaces.  
  Includes an optional toggle to mirror MIDI 2.0 sends into an MPTK MIDI 1.0 virtual sink (when enabled).

- `SampleMenuScene.unity`  
  Hub scene that lists and opens other sample scenes (`Window > MIDI > Samples/Open Sample Menu Scene`).

### Unity Ecosystem Integration

Location: `Assets/MIDI/Samples/Integrations/Scenes/`

- `MidiAnimatorIntegrationSampleScene.unity`  
  Demonstrates MIDI → Animator parameter driving via `MidiAnimatorDriver` / `MidiBlendTreeDriver`.  
  CC1 changes the cube's height, CC10/11 rotate it, and Note 60 changes the pulse effect.

- `MidiTimelineIntegrationSampleScene.unity`  
  Demonstrates Timeline-synchronized SMF playback via `PlayableDirector` + `SmfPlayer` + `MidiPlaybackTrack`.  
  During Play Mode, you can control Timeline Play / Pause / Seek from IMGUI.

- `MidiVisualScriptingIntegrationSampleScene.unity`  
  Demonstrates Visual Scripting integration via `MidiVisualScriptingBridge` and Event Bus listeners.  
  You can add **Events > MIDI** nodes to the Script Graph to extend it on the same GameObject.

### Gameplay

Location: `Assets/MIDI/Samples/Gameplay/`

- `Scenes/MidiGameplaySampleScene.unity`  
  Demonstrates the wiring and behavior of the gameplay components (`MidiDeviceFilter` → `MidiChannelFilter` → `MidiInputRouter` / `MidiNoteTracker`).  
  During Play Mode, you can simulate Note / CC / MIDI Start / SysEx through a virtual device from the IMGUI panel. Physical MIDI controllers work the same way.

- `Scenes/MidiClockSyncSampleScene.unity`  
  External MIDI Clock injection, `MidiClockSync` BPM estimation, and `SmfPlayerClockAdapter` demo.

- `Scenes/ChordScaleSampleScene.unity`  
  `ChordRecognition` / `MidiChordDetector` demo.

- `Scenes/ChordPuzzleSampleScene.unity`  
  Target-chord quiz (`MidiChordDetector.targetChord`) demo.

- `Scenes/ScalePracticeSampleScene.unity`  
  Practice demo with multiple scale types.

### Networking / Input System / Chunity

Location: `Assets/MIDI/Samples/Integrations/`

- `Networking/Scenes/MidiNetworkJamSampleScene.unity`  
  UDP-based `MidiNetworkHub` / `MidiNetworkClient` demo (`FEATURE_MIDI_NETWORK` required).

- `Networking/Scenes/MidiMirrorNetworkSampleScene.unity`  
  Mirror Host/Client MIDI (`FEATURE_MIDI_NETWORK` + `FEATURE_MIRROR`; Mirror installed).

- `Networking/Scenes/MidiNetcodeNetworkSampleScene.unity`  
  Netcode Host/Client MIDI (`FEATURE_MIDI_NETWORK` + `FEATURE_NETCODE`; NGO installed).

- `Networking/Scenes/MidiWsnet2NetworkSampleScene.unity`  
  WSNet2 Create/Join MIDI (`FEATURE_MIDI_NETWORK` + `FEATURE_WSNET2`; WSNet2 client + running servers).

- `InputSystem/Scenes/InputSystemBridgeSampleScene.unity`  
  `MidiInputSystemBridge` + Synthetic Device demo (`FEATURE_INPUT_SYSTEM` required).

- `Chunity/Scenes/ChunityBridgeSampleScene.unity`  
  `MidiChuckBridge` + `MidiChuckPatchHost` demo (external Chunity + `FEATURE_CHUNITY` required).

- `Chunity/Scenes/ChunityWorkflowsSampleScene.unity`  
  Poly / SMF / Clock / Event→MIDI workflow demos (`FEATURE_CHUNITY` required).

- `Chunity/Scenes/ChunityPresetsSampleScene.unity`  
  `MidiChuckPreset` / `MidiChuckParameterBinder` with Filter / Gain, etc. demo (`FEATURE_CHUNITY` required; Timeline optional via `FEATURE_USE_TIMELINE`).

- `Chunity/Scenes/ChunityMicFxSampleScene.unity`  
  Mic adc + PitShift FX, demo controlling the pitch-shift amount with CC1 (Mod Wheel) (`FEATURE_CHUNITY` required; Phase E).

- `Chunity/Scenes/ChunityGeneratorWorkflowSampleScene.unity`  
  Main + Sub Generator + Bridge demo (`FEATURE_CHUNITY_SCRIPTABLE_AUDIO` required).

### Scriptable Audio

Location: `Assets/MIDI/Samples/Integrations/ScriptableAudio/`

- `Scenes/ScriptableAudioMetronomeSampleScene.unity`  
  Metronome / DSP bootstrap demo (`FEATURE_SCRIPTABLE_AUDIO`, Unity 6000.3+).

- `Scenes/ScriptableAudioSequenceSampleScene.unity`  
  SMF sequence → DSP schedule demo.

- `Scenes/ScriptableAudioUmpSequenceSampleScene.unity`  
  UMP sequence → DSP schedule demo.

### Foundation

Location: `Assets/MIDI/Samples/Foundation/`

- `Scenes/FoundationSampleScene.unity`  
  Device selection, latency calibration, and Foundation UI shell demo.

### Maestro / MPTK

Location: `Assets/MIDI/Samples/MPTK/`

- `Scenes/MptkIntegrationSampleScene.unity`  
  Maestro / MPTK integration hub (`FEATURE_USE_MPTK` required; Pro features need `MPTK_PRO`).

### MIDI Tracker (separate sample)

Location: `Assets/MIDITracker/` (outside `Assets/MIDI/Samples/`, depends on the core plugin one-way)

- `Scenes/MidiTrackerSampleScene.unity`  
  Pattern-oriented MIDI tracker showcase (edit, arrange, record, SMF I/O, multi-platform UX).  
  See [MIDI Tracker README](../../MIDITracker/README.md) and [MIDI Tracker development plan](../../MIDITracker/DEVELOPMENT_PLAN.md).

<div class="page" />

## Scripts

Location: `Assets/MIDI/Samples/Scripts/`

- `MidiSampleScene.cs`  
  Typical responsibilities in a demo:
  - initialize MIDI
  - register handler objects
  - react to incoming messages (e.g., log note on/off)
  - send test messages
  - (optional) route output into MPTK via a virtual device

- `Midi2SampleScene.cs`  
  Typical responsibilities in a demo:
  - initialize MIDI 2.0
  - register UMP handlers
  - display decoded events
  - send example UMP messages
  - (optional) mirror sends into MPTK via a MIDI 1.0 virtual sink

### Unity Ecosystem Integration

Location: `Assets/MIDI/Samples/Integrations/Scripts/`

- `MidiAnimatorIntegrationSampleScene.cs`  
  Runtime construction of `MidiAnimatorDriver` / `MidiBlendTreeDriver`, virtual MIDI input, IMGUI control panel

- `MidiTimelineIntegrationSampleScene.cs`  
  Runtime construction of `TimelineAsset` / `MidiPlaybackTrack` / `SmfPlayer`, Timeline control UI

- `MidiVisualScriptingIntegrationSampleScene.cs`  
  Event Bus demo via `MidiVisualScriptingBridge` + `MidiVisualScriptingSampleFeedback`

- `MidiIntegrationSampleFactory.cs`  
  Shared helper for demo SMF arpeggio generation and virtual device registration

### Gameplay

Location: `Assets/MIDI/Samples/Gameplay/Scripts/`

- `MidiGameplaySampleScene.cs`  
  Typical responsibilities in a demo:
  - runtime construction of the filter chain (DeviceFilter → ChannelFilter)
  - NoteOn / CC / MIDI Start / SysEx bindings via `MidiInputMap` / `MidiInputRouter`
  - held-note tracking and chord detection via `MidiNoteTracker`
  - hardware-free testing by injecting into a virtual input device (`virtual:gameplay-sample`)
  - filter configuration and event-log display via IMGUI
- `MidiClockSyncSampleScene.cs` — Clock sync demo
- `ChordScaleSampleScene.cs` — chord/scale detection demo
- `GameplaySampleFactory.cs` — virtual MIDI / Clock injection helper

### Networking / Input System

Location: `Assets/MIDI/Samples/Integrations/`

- `Networking/Scripts/MidiNetworkJamSampleScene.cs` — LAN MIDI sync demo (`#if FEATURE_MIDI_NETWORK`)
- `InputSystem/Scripts/InputSystemBridgeSampleScene.cs` — Input System bridge (`#if FEATURE_INPUT_SYSTEM`)

### Foundation

Location: `Assets/MIDI/Samples/Foundation/Scripts/`

- `FoundationSampleScene.cs` — foundation demo

- `FileUtility.cs`, `AudioClipUtility.cs` (under `Assets/MIDI/Samples/Scripts/`)  
  Utility scripts used by the samples (file handling, audio clip helpers).

<div class="page" />

## WebGL templates

Location: `Assets/MIDI/Samples/WebGLTemplates/`

Contains WebGL build templates intended for MIDI-enabled WebGL deployments. Use these if you want consistent HTML/JS scaffolding for WebGL MIDI and BLE MIDI flows.

<div class="page" />

## Recommended workflow to test quickly

1. Open one of the sample scenes.
2. Enter Play Mode.
3. Connect a MIDI device (or use a network transport like RTP-MIDI / UDP MIDI 2.0).
4. Confirm:
- device attach events appear,
- note/CC events are received,
- sending produces output on the target device.

### MIDI 1.0 Basics (MidiSampleScene)

1. Open `MidiSampleScene.unity`.
2. Open `Window > MIDI > Monitor` ([Editor Tools (MIDI Monitor)](editor-tools.md)).
3. Enter Play Mode.
4. Connect a MIDI device (or use a network transport such as RTP-MIDI / UDP MIDI 2.0).
5. Confirm the following:
- device attach events appear.
- note and CC events are received (the monitor's IN column).
- sending produces output on the target device (the monitor's OUT column).

### Gameplay Components (MidiGameplaySampleScene)

1. Open `MidiGameplaySampleScene.unity`.
2. Enter Play Mode.
3. In the on-screen IMGUI panel, try the following:
   - **NoteOn C4 (ch0)** → fires the Router's Launch binding
   - **CC64 = 127 (ch0)** → fires the Toggle binding
   - **NoteOn C4 (ch1)** → blocked by the ChannelFilter (does not reach the Router / Tracker)
   - **Add D4 + E4 (ch0)** → 3-note chord detected by the NoteTracker
4. When a physical MIDI controller is connected, C4 / CC64 on ch0 behave the same way.
5. For details on the filter chain, see [Gameplay Components](gameplay.md).

### Animator Integration (MidiAnimatorIntegrationSampleScene)

1. Open `MidiAnimatorIntegrationSampleScene.unity`.
2. Enter Play Mode.
3. In the IMGUI panel, **CC1 = 127** → confirm the cube rises.
4. **Note 60** → confirm the pulse effect (scale change).
5. **Program Change 5** → confirm the `Program` Int parameter update and hue change.
6. **MIDI Start** → confirm the `TransportStart` Trigger and pulse effect.
7. **SysEx** → confirm `onMessage SysEx` appears in the Event Log (Animator parameters are not updated).
8. **CC10 / CC11** → confirm the cube rotates.
9. For details, see [Unity Ecosystem Integration](integrations.md).

### Timeline Integration (MidiTimelineIntegrationSampleScene)

1. Confirm the `com.unity.timeline` package is installed.
2. Confirm `FEATURE_USE_TIMELINE` is added in Project Settings.
3. Open `MidiTimelineIntegrationSampleScene.unity`.
3. Enter Play Mode and click **Play Timeline**.
4. Confirm the Director time and SmfPlayer time stay in sync.
5. Confirm the SmfPlayer follows with **Pause** / **Seek to 1.0 s**.

### Visual Scripting Integration (MidiVisualScriptingIntegrationSampleScene)

1. Confirm the `com.unity.visualscripting` package is installed.
2. Confirm `FEATURE_USE_VISUALSCRIPTING` is added in Project Settings.
3. Open `MidiVisualScriptingIntegrationSampleScene.unity`.
3. Enter Play Mode and confirm the cube's color changes with **NoteOn C4** / **CC1 = 127**.
4. You can add a Script Machine to the same GameObject and extend the graph with **Events > MIDI** nodes.

### Network MIDI (MidiNetworkJamSampleScene)

1. Add `FEATURE_MIDI_NETWORK` in Project Settings.
2. Open `MidiNetworkJamSampleScene.unity`.
3. In Play Mode, try the Hub / Client broadcast operations.

### Network MIDI — Mirror / Netcode / WSNet2

1. Install the framework and enable symbols (see [Integrations — Networking](integrations.md) / Networking README).
2. Open `MidiMirrorNetworkSampleScene.unity`, `MidiNetcodeNetworkSampleScene.unity`, or `MidiWsnet2NetworkSampleScene.unity`.
3. Mirror/Netcode: **Start Host** then a second instance as **Client**. WSNet2: **Create Room** then **Join by Room#** (servers must be running).
4. Use Note / CC / Seek buttons to verify sync.

### MidiClockSync / Chords & Scales (Gameplay samples)

1. Open `MidiClockSyncSampleScene.unity` and confirm Clock injection and BPM estimation in Play Mode.
2. Confirm chord/scale detection in `ChordScaleSampleScene.unity` / `ChordPuzzleSampleScene.unity` / `ScalePracticeSampleScene.unity`.
3. For details, see [Genre Kits](kits.md) and [Gameplay Components](gameplay.md).

### Input System Bridge (InputSystemBridgeSampleScene)

1. Add `FEATURE_INPUT_SYSTEM` in Project Settings (`com.unity.inputsystem` is required).
2. Open `InputSystemBridgeSampleScene.unity`.
3. In Play Mode, confirm the Synthetic Device and the bidirectional bridge.

### Chunity Bridge (ChunityBridgeSampleScene)

1. Install Chunity and place `Chunity.Runtime.asmdef.example` at the Scripts root as `Chunity.Runtime.asmdef`.
2. Add `FEATURE_CHUNITY` in Project Settings.
3. Open `Assets/MIDI/Samples/Integrations/Chunity/Scenes/ChunityBridgeSampleScene.unity`.
4. In Play Mode, confirm a monophonic SinOsc sounds from the Note On / Off buttons.

### Chunity Workflows (ChunityWorkflowsSampleScene)

1. Follow the same enablement steps as above.
2. Open `ChunityWorkflowsSampleScene.unity`.
3. Switch between the Poly / SMF / Clock / Event / Bank / Array / Adv (HostAdvancer) / Life (PatchLifecycle) tabs and confirm each demo.

### Chunity Presets (ChunityPresetsSampleScene)

1. Follow the same enablement steps as above.
2. Open `Assets/MIDI/Samples/Integrations/Chunity/Scenes/ChunityPresetsSampleScene.unity`.
3. Confirm the Soft / Bright / Pad preset switching, sliders such as Filter / Gain, and Note On.
4. (Optional) When `FEATURE_USE_TIMELINE` is enabled, demo buttons for the Timeline marker API also appear.

### Chunity Mic FX (ChunityMicFxSampleScene)

1. Follow the same enablement steps as above.
2. Open `Assets/MIDI/Samples/Integrations/Chunity/Scenes/ChunityMicFxSampleScene.unity`.
3. In Play Mode, confirm mic input is enabled, and confirm that the CC1 slider or button changes the PitShift shift amount.
4. CC1 (Mod Wheel) from a physical MIDI device also reaches the same parameter via `MidiChuckBridge`.

### Chunity Scriptable Generator (ChunityGeneratorWorkflowSampleScene)

1. In addition to the same prerequisites as the Chunity Bridge, apply the `useBuiltInAudioFilter` patch from the [Chunity Optional patch guide](../Scripts/Integrations/Chunity/Optional/README.md).
2. Install Unity package **`com.unity.collections`** if it is not already in the project.
3. Add `FEATURE_CHUNITY_SCRIPTABLE_AUDIO` in Project Settings (keep `FEATURE_CHUNITY` as well).
4. Open `ChunityGeneratorWorkflowSampleScene.unity` and confirm Main + Sub Generator + Bridge (spatialization with Note On and Near/Mid/Far).

### Scriptable Audio Pipeline

1. Confirm Unity 6000.3+ and add `FEATURE_SCRIPTABLE_AUDIO` (see [Integrations](integrations.md) / [Build PostProcessing](build-postprocessing.md)).
2. Open `ScriptableAudioMetronomeSampleScene.unity`, `ScriptableAudioSequenceSampleScene.unity`, or `ScriptableAudioUmpSequenceSampleScene.unity`.
3. In Play Mode, confirm the metronome or sequence scheduling behavior.

### Foundation (FoundationSampleScene)

1. Open `FoundationSampleScene.unity`.
2. In Play Mode, confirm device selection, latency calibration, and the Foundation UI shell.

### MIDI Tracker (MidiTrackerSampleScene)

1. Open `Assets/MIDITracker/Scenes/MidiTrackerSampleScene.unity`.
2. Enter Play Mode and exercise pattern edit / playback / arrangement (external MIDI sound source recommended).
3. For operations and phase status, see [MIDI Tracker README](../../MIDITracker/README.md).

If a sample doesn’t receive events:
- confirm the correct platform backend is in use,
- check transport requirements (permissions on WebGL, firewall for network transports, etc.).

If you use `MidiInputRouter` or filters, also see the wiring steps in [Gameplay Components](gameplay.md).

To try SMF playback/recording, you can use `SmfPlayer` / `MidiRecorder` in [SMF Tools](smf-tools.md).

To inspect SMF in Edit Mode, you can use the SMF preview in [Editor Tools](editor-tools.md).

<div class="page" />

## Source code example implementations referenced by docs

The documentation pages reference these “minimal, concrete” scripts:

- `Assets/MIDI/Samples/DocumentationExamples/Midi1QuickStartExample.cs`
- `Assets/MIDI/Samples/DocumentationExamples/Midi2QuickStartExample.cs`
- `Assets/MIDI/Samples/DocumentationExamples/MpeOutputExample.cs`
- `Assets/MIDI/Samples/DocumentationExamples/SmfPlaybackExample.cs`
- `Assets/MIDI/Samples/DocumentationExamples/RtpMidiTransportExample.cs`

MPTK-related examples:

- `Assets/MIDI/Samples/DocumentationExamples/MptkVirtualOutputSinkExample.cs`
- `Assets/MIDI/Samples/DocumentationExamples/MptkToMidiManagerInputExample.cs`
- `Assets/MIDI/Samples/DocumentationExamples/MptkBootstrapExample.cs` — Bootstrap / SmfPlayer integration / validation
- `Assets/MIDI/Samples/DocumentationExamples/MptkMidiEventPipelineExample.cs` — pre-synthesis rewrite (`MPTK_PRO`)
- `Assets/MIDI/Samples/DocumentationExamples/MptkWriterExternalExample.cs` — Writer / External round-trip (`MPTK_PRO`)
- `Assets/MIDI/Samples/DocumentationExamples/MptkInnerLoopExample.cs` — InnerLoop region loop (`MPTK_PRO`)
- `Assets/MIDI/Samples/DocumentationExamples/MptkListPlayerExample.cs` — Playlist bridge (`MPTK_PRO`)
- `Assets/MIDI/Samples/DocumentationExamples/MptkSoundFontLoaderExample.cs` — Runtime SoundFont load
- `Assets/MIDI/Samples/DocumentationExamples/MptkDelayedNoteDispatcherExample.cs` — CatchMusic-style delayed dispatch
- `Assets/MIDI/Samples/DocumentationExamples/MptkFilePlayerChannelsExample.cs` — FilePlayer channel theater

MPTK sample GUI scripts (attach in Play Mode; `FEATURE_USE_MPTK` + `MPTK_PRO` where noted):

- Scene: `Assets/MIDI/Samples/MPTK/Scenes/MptkIntegrationSampleScene.unity` (requires `FEATURE_USE_MPTK`)
- Base script: `Assets/MIDI/Samples/MPTK/Scripts/MptkIntegrationSampleScene.cs`
- `Assets/MIDI/Samples/MPTK/Scripts/MptkMidiEventPipelineSample.cs` — pre-synthesis rewrite GUI
- `Assets/MIDI/Samples/MPTK/Scripts/MptkWriterExternalSample.cs` — Writer / External / Join GUI
- `Assets/MIDI/Samples/MPTK/Scripts/MptkInnerLoopSample.cs` — InnerLoop GUI
- `Assets/MIDI/Samples/MPTK/Scripts/MptkListPlayerSample.cs` — List player GUI (`MPTK_PRO`)
- `Assets/MIDI/Samples/MPTK/Scripts/MptkPhaseDUtilitiesSample.cs` — Phase D utilities GUI
- `Assets/MIDI/Samples/MPTK/Scripts/MptkSpatializerSample.cs` — Spatializer GUI (`MPTK_PRO` + MidiSpatializer prefab)
- `Assets/MIDI/Samples/DocumentationExamples/MptkSpatializerExample.cs` — Spatializer host example
- `Assets/MIDI/Samples/DocumentationExamples/MptkTimelineMarkerExample.cs` — Timeline Seek / InnerLoop marker example
- Output preset: `Assets/MIDI/Scripts/Integrations/MPTK/Presets/MptkVirtualOutput.preset.asset`

If you’re using Maestro / MPTK, see:
- [Maestro / MPTK Integration](mptk.md)