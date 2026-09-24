# SMF Tools (SmfPlayer / MidiRecorder / TempoMapExtractor)

This page describes the components and utilities for playing, recording, and analyzing the tempo of Standard MIDI Files (SMF).

Namespaces:
- Components: `jp.kshoji.unity.midi`
- Utilities: `jp.kshoji.unity.midi.util`

Location:
- `Assets/MIDI/Scripts/Gameplay/SmfPlayer.cs`
- `Assets/MIDI/Scripts/Gameplay/MidiRecorder.cs`
- `Assets/MIDI/Scripts/MidiSequenceAsset.cs`
- `Assets/MIDI/Scripts/UmpSequenceAsset.cs`
- `Assets/MIDI/Scripts/Editor/UmpImportUtility.cs` / `UmpAssetImportMenu.cs`
- `Assets/MIDI/Scripts/Utilities/Smf/TempoMapExtractor.cs`, etc.

<div class="page" />

## Overview

| Component / Utility | Role |
|--------------------------------|------|
| `MidiSequenceAsset` | Hold SMF binary data as a ScriptableObject |
| `UmpSequenceAsset` | Hold `.midi2` (UMP MIDI Clip) binary data as a ScriptableObject |
| `SmfPlayer` | Play SMF / Sequence with `SequencerImpl` and output to `MidiManager` |
| `MidiRecorder` | Record live MIDI input as SMF |
| `TempoMapExtractor` | Extract tempo and time signatures from a Sequence as a time series |

All of these wrap the existing `jp.kshoji.midisystem` (`Sequence` / `SequencerImpl`). For details on the low-level SMF I/O API, see [SMF / Sequencing](smf.md).

<div class="page" />

## MidiSequenceAsset

A ScriptableObject that holds SMF data as a Unity asset. Use it as a playback source for `SmfPlayer` or as a save destination for `MidiRecorder`.

### How to create

- Inspector: `Assets > Create > MIDI > Sequence Asset`
- Code: `MidiSequenceAsset.CreateFromSequence(sequence, sourcePath)`

### API

| Method | Description |
|----------|------|
| `SetSmfData(byte[] data, string sourcePath)` | Set the SMF binary data |
| `ToSequence()` | Convert to a `Sequence` |
| `CreateFromSequence(Sequence, string)` | Create an asset from a Sequence |

<div class="page" />

## UmpSequenceAsset

A ScriptableObject that holds `.midi2` (UMP MIDI Clip / SMF2CLIP) binary data as a Unity asset. Use it as a playback source for `UmpSequencer` or the Scriptable Audio `MidiDspUmpSequenceScheduler`.

### How to create

- Inspector: `Assets > Create > MIDI > UMP Sequence Asset` (empty asset)
- From a file: `Assets > Create > MIDI > Import UMP Sequence Asset From File` (select a `.midi2`)
- Code: `UmpSequenceAsset.CreateFromSequence(umpSequence, sourcePath)` / `UmpImportUtility`

### API

| Method / Property | Description |
|------------------------|------|
| `SetMidi2Data(byte[] data, string sourcePath, int index)` | Set the `.midi2` binary data |
| `ToUmpSequence()` | Convert the configured clip to a `UmpSequence` |
| `ToUmpSequenceList()` | Convert all clips in the file |
| `CreateFromSequence(UmpSequence, string)` | Create an asset from a `UmpSequence` |
| `ClipIndex` | Index within a multi-clip file (usually 0) |

### Usage example with Scriptable Audio

```csharp
var asset = /* UmpSequenceAsset assigned in the Inspector */;
var bootstrap = GetComponent<MidiDspUmpSequenceBootstrap>();
bootstrap.sequence = asset.ToUmpSequence();
bootstrap.EnsureReady();
bootstrap.Scheduler.Play();
```

For details on DSP-synchronized playback, see [Unity Ecosystem Integration — UMP sequence](integrations.md#ump-sequence-playback-dsp-sync). For wall-clock playback, see [MIDI 2.0 — UmpSequencer](midi2.md#umpsequencer-midi-20-clip-sequencing).

<div class="page" />

## SmfPlayer

A component that integrates `SequencerImpl` into the Unity lifecycle and plays SMF synchronized to game time. Output is sent to `MidiManager` via the internal `MidiManagerReceiverBridge`.

### Setup

1. Attach `SmfPlayer` to a GameObject
2. Assign a `MidiSequenceAsset` to `Sequence Asset` (or call `SetSequence()` at runtime)
3. Specify `Output Device Id` (empty = first output device)
4. Call `MidiManager.InitializeMidi()` in your scene bootstrap

### MPTK audio output (`FEATURE_USE_MPTK`)

To output to an MPTK synth instead of a hardware MIDI device:

1. Enable `FEATURE_USE_MPTK` in Project Settings
2. Add `MptkSmfPlayerOutput` to the same GameObject
3. In Play mode, `Configure()` sets `outputDeviceId` to `mptk:internal`

For details, see [Maestro / MPTK Integration — SmfPlayer linkage](mptk.md#mptksmfplayeroutput--smfplayer-integration).

### Editor SMF preview

After loading a `.mid` from `Window > MIDI > SMF Preview`, you can preview it in Play Mode from **Preview Audio (MPTK)** (`FEATURE_USE_MPTK` required). For details, see [Maestro / MPTK Integration — Editor SMF preview](mptk.md#editor-smf-preview-window--midi--smf-preview).

### Main settings

| Field | Description |
|------------|------|
| `sequenceAsset` | The SMF asset to play |
| `group` | MIDI group (0–15) |
| `outputDeviceId` | Output device ID (empty = auto-select) |
| `outputChannel` | Map all messages to this channel (`-1` = keep original channel) |
| `playOnAwake` | Auto-play after Awake |
| `loop` / `loopCount` | Loop playback (`-1` = infinite) |
| `tempoBpm` | Playback tempo (changes during runtime are also reflected) |

### Control API

| Method | Description |
|----------|------|
| `Play()` | Start playback (resumes from position when paused) |
| `Pause()` | Pause |
| `Stop()` | Stop and return to the beginning |
| `Seek(float timeSeconds)` | Seek the playback position |
| `SetTrackMute(index, mute)` | Mute a track |
| `SetTrackSolo(index, solo)` | Solo a track |
| `SetSequenceAsset(asset)` | Swap the asset and reload |
| `SetSequence(sequence)` | Directly specify a runtime Sequence |

### State properties

| Property | Description |
|------------|------|
| `State` | `Stopped` / `Playing` / `Paused` |
| `CurrentTimeSeconds` | Current playback position (seconds) |
| `TotalDurationSeconds` | Total playback time (seconds, based on `TempoMapExtractor`) |

### Events

| UnityEvent | Timing |
|------------|------------|
| `onPlaybackStarted` | Playback started |
| `onPlaybackPaused` | Paused |
| `onPlaybackStopped` | Stopped |
| `onPlaybackFinished` | Reached the end without looping |

### Usage example

```csharp
using jp.kshoji.unity.midi;
using UnityEngine;

public sealed class SmfPlaybackController : MonoBehaviour
{
    public SmfPlayer player;

    void Start()
    {
        MidiManager.Instance.InitializeMidi(() => player.Play());
    }

    public void OnSeek(float normalized)
    {
        player.Seek(player.TotalDurationSeconds * normalized);
    }
}
```

<div class="page" />

## MidiRecorder

Records live MIDI input (Note / CC / Program Change / Pitch Bend / Aftertouch / SysEx / system realtime) to a `Sequence` and saves it as a `.mid` file or a `MidiSequenceAsset`.

The current implementation uses its own `Time.time`-based timestamping (integration with the `SequencerImpl` recording API is a future extension).

### Setup

1. Attach `MidiRecorder` to a GameObject
2. Register with `MidiManager` via `registerWithMidiManager = true` (default)
3. Set `deviceIdFilter` / `channelFilter` as needed
4. Control recording with `StartRecording()` / `StopRecording()`

When using it via a filter chain, set `registerWithMidiManager = false` and place it under the upstream filter's `forwardTargets` (same as [Gameplay Components](gameplay.md)).

### Main settings

| Field | Description |
|------------|------|
| `tempoBpm` | Tempo at record time (used for tick calculation) |
| `ticksPerQuarter` | Resolution (PPQ, default 480) |
| `deviceIdFilter` | Devices to record (empty = all devices) |
| `channelFilter` | Channels to record (empty = all channels) |

### API

| Method | Description |
|----------|------|
| `StartRecording()` | Start recording |
| `StopRecording()` | Stop recording |
| `ClearRecording()` | Clear the recorded content |
| `GetSequence()` | Get the recorded `Sequence` |
| `SaveToFile(path)` | Save to a `.mid` file |
| `SaveToAsset(assetPath)` | Create a `MidiSequenceAsset` (Editor only) |

### Recording format

- SMF Format 1 (tempo track + data track)
- Recorded: Note On/Off, Control Change, Program Change, Pitch Bend, Channel/Poly Aftertouch, SysEx / System Common, MIDI realtime (Clock / Start / Stop, etc.)
- Raw UMP is not written to SMF (handled separately via `onMessage`)

### Usage example

```csharp
using jp.kshoji.unity.midi;
using UnityEngine;

public sealed class RecordingExample : MonoBehaviour
{
    public MidiRecorder recorder;
    public SmfPlayer player;
    public MidiSequenceAsset recordedAsset;

    public void RecordAndPlay()
    {
        recorder.StartRecording();
        // ... play MIDI ...
        recorder.StopRecording();

#if UNITY_EDITOR
        recordedAsset = recorder.SaveToAsset("Assets/Recorded.mid.asset");
        player.SetSequenceAsset(recordedAsset);
        player.Play();
#endif
    }
}
```

<div class="page" />

## TempoMapExtractor

A static utility that extracts tempo changes and time signatures as a time series from a `Sequence` or SMF data. It can be used for calculating `SmfPlayer.TotalDurationSeconds` or as a foundation for score generation.

### API

```csharp
using jp.kshoji.unity.midi.util;
using jp.kshoji.midisystem;

TempoMap map = TempoMapExtractor.Extract(sequence);
// or
TempoMap map = TempoMapExtractor.Extract(smfBytes);
TempoMap map = TempoMapExtractor.Extract("/path/to/file.mid");
```

### TempoMap properties / methods

| Name | Description |
|------|------|
| `TempoChanges` | List of tempo changes |
| `TimeSignatures` | List of time signatures |
| `TotalDurationSeconds` | Total playback time (seconds) |
| `TotalTicks` | Total number of ticks |
| `TicksPerQuarter` | Resolution |
| `GetBpmAt(tick)` | BPM at the given tick |
| `TickToSeconds(tick)` | Tick → seconds |
| `SecondsToTick(seconds)` | Seconds → tick |

For SMF without tempo meta-events, 120 BPM (500000 μs/qn) is treated as the default.

### Usage example

```csharp
var map = TempoMapExtractor.Extract(sequence);
Debug.Log($"Duration: {map.TotalDurationSeconds:F2}s, BPM at 0: {map.GetBpmAt(0)}");

foreach (var signature in map.TimeSignatures)
{
    Debug.Log($"{signature.TimeSeconds:F2}s -> {signature.Numerator}/{signature.Denominator}");
}
```

<div class="page" />

## MeasureTimeUtility

Bar / beat tick math and measure ↔ time conversion on a `TempoMap`.

Location: `Assets/MIDI/Scripts/Utilities/Smf/MeasureTimeUtility.cs`

| Method | Description |
|--------|-------------|
| `TicksPerBar(ppqn, numerator, denominator)` | Length of one bar in ticks |
| `TicksForBars(...)` | Length of N bars |
| `TicksPerBeat(...)` | Beat length (bar / numerator) for simple meters |
| `MeasureToStartSeconds` / `MeasureToEndSeconds` | 1-based measure → seconds |
| `TryResolveMeasureRange` / `TryGetMeasureRangeMs` | Inclusive measure ranges |
| `TimeSecondsToMeasure` | Seconds → 1-based measure |

Formula for bar ticks: `ppqn * 4 * numerator / denominator` (integer division).

<div class="page" />

## TupletUtility / SwingUtility

Location: `Assets/MIDI/Scripts/Utilities/Smf/TupletUtility.cs`, `SwingUtility.cs`

### TupletUtility

Rational tuplets on a fixed PPQN grid (e.g. 3:2 eighth-note triplets). Step ticks use integer division so index→tick→index round-trips for in-range indices.

| Method | Description |
|--------|-------------|
| `UnitTicks` / `GroupDurationTicks` / `StepTicks` | Unit, group span, and per-step ticks |
| `TickAtIndex` / `IndexAtTick` | Bidirectional mapping |
| `QuantizeToGroup` | Snap a tick onto the nearest tuplet step |
| `IsIndexTickReversible` | Verify round-trip for all indices |

### SwingUtility

Playback-time swing: delays even offbeats without rewriting edit ticks. `swingAmount` ≈ 0.33 yields a triplet-like feel.

| Method | Description |
|--------|-------------|
| `UnitTicksFromDivision(ppqn, division)` | Unit length (4 = quarter, 8 = eighth, …) |
| `ApplySwing(editTick, unitTicks, amount)` | Map edit tick → swung playback tick |
| `ApplySwing(..., ppqn, swingDivision, amount)` | Convenience overload |

<div class="page" />

## MidiMetaMessageFactory

Builds and reads common SMF `MetaMessage` payloads.

Location: `Assets/MIDI/Scripts/Utilities/Smf/MidiMetaMessageFactory.cs`

| Method | Description |
|--------|-------------|
| `CreateTempo(bpm)` | Set Tempo meta (μs per quarter) |
| `CreateTimeSignature(numerator, denominator, …)` | Time Signature meta |
| `CreateTrackName(name)` | Track / Sequence Name (UTF-8) |
| `DenominatorToExponent` / `ExponentToDenominator` | SMF power-of-two conversions |
| `TryReadTempoBpm(meta, out bpm)` | Read BPM from a Tempo meta |

<div class="page" />

## Source files

| File | Role |
|----------|------|
| `MidiSequenceAsset.cs` | SMF ScriptableObject |
| `UmpSequenceAsset.cs` | UMP `.midi2` ScriptableObject |
| `Editor/UmpImportUtility.cs` / `UmpAssetImportMenu.cs` | `.midi2` import |
| `SmfPlayer.cs` / `SmfPlayerState.cs` | SMF playback component |
| `MidiManagerReceiverBridge.cs` | Sequencer → MidiManager bridge |
| `MidiRecorder.cs` | Recording component |
| `MidiRecordingSession.cs` | Recording session (tick calculation / SMF export) |
| `TempoMapExtractor.cs` | Tempo-map extraction |
| `TempoMap.cs` / `TempoMapEntry.cs` / `TimeSignatureEntry.cs` | Data model |
| `MeasureTimeUtility.cs` | Measure / bar tick helpers |
| `TupletUtility.cs` / `SwingUtility.cs` | Tuplet and swing timing |
| `MidiMetaMessageFactory.cs` | SMF meta builders |

<div class="page" />

## Related docs

- [SMF / Sequencing (jp.kshoji.midisystem)](smf.md)
- [MIDI 2.0 / UMP](midi2.md)
- [Unity Ecosystem Integration — UMP sequence](integrations.md#ump-sequence-playback-dsp-sync)
- [Gameplay Components](gameplay.md)
- [MIDI 1.0 Runtime (MidiManager)](midi1.md)
- [Samples](samples.md)
