# Genre Kits

Provides niche-oriented extensions and cross-cutting foundation features.

> **MPTK output routing:** When a kit's `outputDeviceId` is empty, you can use `outputPreset` (`MidiOutputRoutingPreset`) to route commonly to an MPTK virtual output (`mptk:internal`) or similar. Bundled preset: `Assets/MIDI/Scripts/Integrations/MPTK/Presets/MptkVirtualOutput.preset.asset`. For details, see [Maestro / MPTK Integration — Sample scenes / output presets / editor audition](mptk.md#sample-scene--output-preset--editor-preview).

| Kit / feature | Namespace | Assembly Definition | Scripting Define Symbol |
|--------|----------|---------------------|-------------------------|
| Network MIDI sync | `jp.kshoji.unity.midi.net` | `jp.kshoji.midi.net` | `FEATURE_MIDI_NETWORK` |
| **MidiClockSync** | `jp.kshoji.unity.midi` | `jp.kshoji.midi` (core) | not required |
| **Chord & scale detection** | `jp.kshoji.unity.midi` / `.util` | `jp.kshoji.midi` (core) | not required |
| **Input System bridge** | `jp.kshoji.unity.midi.integrations.inputsystem` | `jp.kshoji.midi.inputsystem` | `FEATURE_INPUT_SYSTEM` |
| **Foundation** | `jp.kshoji.unity.midi.foundation` | `jp.kshoji.midi.foundation` | not required |

Location:

| Kit | Location |
|--------|------|
| Networking | `Assets/MIDI/Scripts/Integrations/Networking/` |
| **Clock / chord detection (core)** | `Assets/MIDI/Scripts/Gameplay/` (incl. `Theory/`) |
| **Input System** | `Assets/MIDI/Scripts/Integrations/InputSystem/` |
| **Foundation** | `Assets/MIDI/Scripts/Foundation/` |
| Foundation UI | `Assets/MIDI/UI/Foundation/` |
| Samples | `Assets/MIDI/Samples/Gameplay/` / `Assets/MIDI/Samples/Integrations/Networking/` / `Assets/MIDI/Samples/Integrations/InputSystem/` / `Assets/MIDI/Samples/Foundation/` |

### Dependencies between kits

| Kit / feature | Main dependency components |
|---------------|------------------------|
| Network MIDI sync | `SmfPlayer`, `MidiManager` |
| Input System bridge | `MidiManager` |
| Timeline integration | `SmfPlayer`, `TempoMapExtractor`, `MidiRecorder` |
| Animator integration | `MidiInputRouter`, `MidiCcSmoother`, `MidiNoteTracker` |

<div class="page" />

## Network MIDI sync

Synchronizes MIDI events and SMF playback position over a LAN. It has a built-in lightweight UDP-based transport, so no additional packages such as Netcode / Mirror are required.

### Prerequisites

Add the scripting define symbol in Project Settings:

- `FEATURE_MIDI_NETWORK`

| Item | Value |
|------|-----|
| Assembly Definition | `jp.kshoji.midi.net` |
| Settings path | `Project Settings > Player > Other Settings > Script Compilation > Scripting Define Symbols` |

### Key components

| Component | Role |
|----------------|------|
| `MidiNetworkHub` | Host. Distributes MIDI events |
| `MidiNetworkClient` | Injects received events into a virtual device |
| `MidiNetworkMessage` | Serializable DTO |
| `MidiPlaybackSync` | `SmfPlayer` playback-position sync (`broadcastInterval` default 0.1s, `seekThreshold` default 0.05s) |
| `MidiSessionDiscovery` | LAN session UDP advertisement (`hubPort` in payload) |
| `MidiSessionDiscoveryListener` | LAN session UDP listen (`OnSessionAdvertisement`) |
| `MidiLatencyCompensation` | RTT helpers (UDP Ping/Pong → `estimatedRttMs`) |

### Sync modes

| Mode | Use |
|--------|------|
| Broadcast | Distribute host input to all clients |
| Merge | Merge all client input on the host |
| Playback | Sync playback position only |

### Optional: Mirror / Netcode / WSNet2 bridges

UDP `MidiNetworkHub` / `MidiNetworkClient` need no extra packages. Optional bridges carry the same `MidiNetworkMessage` / `MidiNetworkMessageCodec` over a game transport you already use. Packages are **not** bundled in this repository.

| Symbol | Assembly | Component | Package / gate |
|--------|----------|-----------|----------------|
| `FEATURE_MIRROR` (+ `FEATURE_MIDI_NETWORK` + `MIRROR`) | `jp.kshoji.midi.net.mirror` | `MidiMirrorBridge` | Install Mirror; `MIRROR` from Mirror’s defines |
| `FEATURE_NETCODE` (+ `FEATURE_MIDI_NETWORK` + `MIDI_HAS_NETCODE`) | `jp.kshoji.midi.net.netcode` | `MidiNetcodeBridge` | UPM `com.unity.netcode.gameobjects` (`versionDefines` → `MIDI_HAS_NETCODE`) |
| `FEATURE_WSNET2` (+ `FEATURE_MIDI_NETWORK` + `MIDI_HAS_WSNET2`) | `jp.kshoji.midi.net.wsnet2` | `MidiWsnet2Bridge` | Copy [WSNet2](https://github.com/KLab/wsnet2) client + `WSNet2.Runtime.asmdef` (see Optional helper); Editor syncs `MIDI_HAS_WSNET2`. Lobby/Game servers must run separately |

Enablement (summary):

1. Add `FEATURE_MIDI_NETWORK`, then the bridge symbol(s) above.
2. Install the matching framework (do not leave Mirror / NGO / WSNet2 on distribution branches unless your game already depends on them).
3. Wire Host/Master `hub` + client/peer `playbackSync` as in [Networking Integration](../Scripts/Integrations/Networking/README.md).
4. Open the sample scenes under `Assets/MIDI/Samples/Integrations/Networking/Scenes/`.

### Samples

| Scene | Requires |
|-------|----------|
| `.../Networking/Scenes/MidiNetworkJamSampleScene.unity` | `FEATURE_MIDI_NETWORK` |
| `.../Networking/Scenes/MidiMirrorNetworkSampleScene.unity` | Mirror + `FEATURE_MIRROR` |
| `.../Networking/Scenes/MidiNetcodeNetworkSampleScene.unity` | NGO + `FEATURE_NETCODE` |
| `.../Networking/Scenes/MidiWsnet2NetworkSampleScene.unity` | WSNet2 + servers + `FEATURE_WSNET2` |

<div class="page" />

## Cross-cutting foundation

Provides external MIDI Clock sync, chord & scale detection, and an Input System bridge. See also [Gameplay Components](gameplay.md).

### MidiClockSync

Synchronizes to an external MIDI Clock (Timing Clock / Start / Stop / Continue) and provides BPM, beat position, and bar position.

| Property / API | Description |
|------------------|------|
| `pulsesPerQuarterNote` | Clock pulses per quarter note (MIDI standard 24) |
| `beatsPerBar` | Beats per bar (for `onBar`, default 4) |
| `deviceIdFilter` | Empty = all devices |
| `EstimatedBpm` | BPM estimated from recent pulses |
| `IsBpmStable` | Whether the BPM estimate is stable |
| `onBeat` / `onBar` | Beat / bar boundary events |
| `onStarted` / `onStopped` | Start / Stop received events |

`SmfPlayerClockAdapter` switches behavior with `SmfPlayerClockMode`:

| Mode | Behavior |
|--------|------|
| `Follow` | Reflect the external Clock's estimated BPM into `SmfPlayer.tempoBpm` |
| `Step` | Advance the SMF playback position by one beat on each external Clock beat |
| `Free` | Ignore the external Clock |

**Limitations:** Song Position Pointer (SPP), MTC, and Ableton Link are not supported.

### Chord & scale detection

| Component / utility | Role |
|-------------------------------|------|
| `ChordRecognition` | Infers a chord name (`C`, `Cm7`, etc.) from the set of held notes |
| `ScaleUtility` | Scale-membership detection extending `MidiScaleUtility` |
| `MidiChordDetector` | Works with `MidiNoteTracker`; chord-change / out-of-scale detection / target-chord quiz |

### Input System bridge (optional)

| Component | Role |
|----------------|------|
| `MidiSyntheticDevice` | Synthetic Input Device (128 notes + 128 CCs + channel-specific axes) |
| `MidiInputSystemBridge` | MIDI → Input System state injection. SysEx / Raw UMP via `onMessage` |
| `InputSystemToMidiBridge` | Input Action → MIDI send |
| `MidiInputSystemMapping` | ScriptableObject of Action ↔ MIDI conditions |

Prerequisites: package `com.unity.inputsystem`, symbol `FEATURE_INPUT_SYSTEM`.  
For details on setup and the Synthetic Device layout, see [Unity Ecosystem Integrations — Input System](integrations.md#input-system-integration).

Create a `MidiInputSystemMapping` via `Assets > Create > MIDI > Input System > Mapping`. Main fields of `MidiActionBinding`:

| Field | Description |
|------------|------|
| `actionName` | Action name inside the `.inputactions` |
| `messageType` | NoteOn / ControlChange / PitchWheel / ProgramChange, etc. |
| `group` | 0–15, `-1` = all groups |
| `channel` | 0–15, `-1` = all channels |
| `controllerOrNote` | CC number or note number |
| `valueFilter` | Value filter, `-1` = all |
| `targetControlIndex` | CC fallback target (`-1` = `controllerOrNote`) |
| `preferDedicatedControl` | Prefer a dedicated axis (pitch / program / channelPressure / systemPulse) |
| `invertAxis` | Invert the normalized axis |

### Samples

| Scene | Description |
|--------|------|
| `Assets/MIDI/Samples/Gameplay/Scenes/MidiClockSyncSampleScene.unity` | Clock injection / BPM estimation / `SmfPlayerClockAdapter` |
| `Assets/MIDI/Samples/Gameplay/Scenes/ChordScaleSampleScene.unity` | Chord recognition and scale quiz |
| `Assets/MIDI/Samples/Gameplay/Scenes/ChordPuzzleSampleScene.unity` | Target-chord quiz (`MidiChordDetector`) |
| `Assets/MIDI/Samples/Gameplay/Scenes/ScalePracticeSampleScene.unity` | Scale practice |
| `Assets/MIDI/Samples/Integrations/InputSystem/Scenes/InputSystemBridgeSampleScene.unity` | Synthetic Device demo (`FEATURE_INPUT_SYSTEM` required) |
| `Assets/MIDI/Samples/Foundation/Scenes/FoundationSampleScene.unity` | Device selection / latency calibration / Foundation UI shell |

<div class="page" />

## Related docs

- [Samples](samples.md) — list of kit sample scenes
- [Build PostProcessing — Network Optional Kit](build-postprocessing.md#network-optional-kit)
- [Build PostProcessing — Cross-Cutting Foundation Optional Kit](build-postprocessing.md#cross-cutting-optional-kit)
- [SMF Tools](smf-tools.md) — `SmfPlayer` / `TempoMapExtractor`
- [Gameplay Components](gameplay.md) — `MidiClockSync` / `MidiCcSmoother` / chord detection
