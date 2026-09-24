# Gameplay Components

This page describes the high-level components used to connect MIDI input to Unity game logic.

Namespace: `jp.kshoji.unity.midi`

Location: `Assets/MIDI/Scripts/Gameplay/`

<div class="page" />

## Overview

| Component | Role |
|----------------|------|
| `MidiInputMap` / `MidiInputRouter` | Fire `UnityEvent`s from MIDI conditions defined in a ScriptableObject |
| `MidiNoteTracker` | Manage per-channel held-note state |
| `MidiChannelFilter` | Forward only specific channels downstream |
| `MidiDeviceFilter` | Forward only specific devices downstream |
| `SmfPlayer` | Synchronized SMF / Sequence playback (see [SMF Tools](smf-tools.md)) |
| `MidiRecorder` | Record live MIDI input as SMF (see [SMF Tools](smf-tools.md)) |
| `MidiCcSmoother` | Exponential moving-average smoothing of CC values |
| `MidiCcButton` | Button detection from a CC threshold |
| `MidiClockSync` | Sync to an external MIDI Clock; provide BPM / beat / bar |
| `MidiClockOutput` | Send MIDI Clock as master |
| `SmfPlayerClockAdapter` | Follow `SmfPlayer` tempo to an external Clock |
| `MidiChordDetector` | Detect chord-name / scale-membership changes and match against a target chord (quiz) |

For Animator / Timeline / Visual Scripting integration, see [Unity Ecosystem Integration](integrations.md).

All of these use the event-handler mechanism of `MidiManager` (`IMidi*EventHandler`). Calling `InitializeMidi()` is the responsibility of the scene.

<div class="page" />

## MidiInputMap / MidiInputRouter

### Overview

Define mappings between MIDI messages and `UnityEvent`s in a `MidiInputMap` (ScriptableObject), and `MidiInputRouter` (MonoBehaviour) fires events when a condition matches. This lets you connect MIDI input to game events from the Inspector without writing code.

### Setup

1. Create a `MidiInputMap` from `Assets > Create > MIDI > Input Map`
2. Attach `MidiInputRouter` to a GameObject
3. Assign the created Input Map to the `Map` field
4. Set conditions and UnityEvents in the Input Map's `Bindings`
5. Call `MidiManager.InitializeMidi()` in your scene bootstrap

### Binding conditions

| Field | Description |
|------------|------|
| `messageType` | `MidiOutgoingMessageType` (NoteOn / CC / PitchWheel / Start / SysEx, etc.) |
| `group` | 0–15, `-1` = all groups |
| `deviceIdFilter` | Empty = all devices |
| `channel` | 0–15, `-1` = all channels (ignored for system triggers) |
| `noteNumber` | 0–127, `-1` = any (Note / Poly Aftertouch / system data-byte filter) |
| `minVelocity` / `maxVelocity` | NoteOn velocity range |
| `controllerNumber` | CC number |
| `minValue` / `maxValue` | CC / Aftertouch value range |
| `ccTriggerMode` | CC trigger method (see table below) |
| `programNumber` | 0–127, `-1` = any (for PC) |

#### Message types and events

| Category | messageType example | Fired event |
|----------|----------------|--------------|
| Performance | NoteOn/Off, CC, PitchWheel, Aftertouch | `onTriggered` + `onTriggeredWithValue` |
| Performance | ProgramChange, SongSelect, … | `onTriggered` + `onTriggeredWithValue` (`number`) |
| System trigger | TimingClock, Start, Stop, Reset, … | `onTriggered` only |
| Payload | SystemExclusive, SystemCommonMessage, Midi2RawUmp | `onMessage` only |

`MidiInputRouter` itself also has `raiseMessageEvents` / `onMessage`, letting you receive all incoming messages as a DTO (`MidiInputMessageEventArgs`). Use this for generic handling of SysEx / Raw UMP.

The legacy `MidiBindingMessageType` asset is migrated automatically via `FormerlySerializedAs` (PitchBend → PitchWheel).

#### CC trigger modes

| Mode | Behavior |
|--------|------|
| `OnChange` | Every time the value changes within range |
| `OnEnterRange` | The moment the value becomes `minValue` or higher |
| `OnExitRange` | The moment the value becomes `maxValue` or lower |
| `OnThreshold` | The moment the value becomes `minValue` or higher (for buttons) |

### Usage example

```csharp
// Configure MidiInputMap in the Inspector and wire game logic to the UnityEvent
// Example: NoteOn ch0 note60 → turn on a light
```

Configuration example for a CC button (fires once at 64 or higher):
- `messageType`: ControlChange
- `channel`: 0
- `controllerNumber`: 64
- `minValue`: 64
- `ccTriggerMode`: OnThreshold

### Notes

- Multiple bindings are evaluated with OR (fires if any one matches)
- If both `onTriggered` (no argument) and `onTriggeredWithValue` (int argument) are set, both fire
- When used via a filter, set `registerWithMidiManager = false` ([Combining with filters](#combining-with-filters))

<div class="page" />

## MidiNoteTracker

### Overview

Tracks the note numbers currently held (Note On received, Note Off not yet received) per MIDI channel. It can serve as a foundation for chord detection, simultaneous-press counting, key-release detection, and so on.

### Settings

| Field | Description |
|------------|------|
| `targetChannels` | Channels to track (empty = all channels) |
| `deviceIdFilter` | Empty = all devices |
| `registerWithMidiManager` | Whether to register directly with `MidiManager` (default true) |
| `onNoteStateChanged` | UnityEvent on note-state change |

### API

| Method | Description |
|----------|------|
| `IsNoteOn(channel, note)` | Whether the given note is held |
| `GetActiveNotes(channel)` | List of held notes |
| `GetActiveNoteCount(channel)` | Number of held notes |
| `GetTotalActiveNoteCount()` | Total across all channels |
| `HasAnyNoteOn()` | Whether any note is held |

On receiving CC 120 (All Sound Off) and CC 123 (All Notes Off), all notes on that channel are treated as Off.

### Usage example

```csharp
using jp.kshoji.unity.midi;

public sealed class ChordDetector : MonoBehaviour
{
    public MidiNoteTracker tracker;

    void Update()
    {
        if (tracker.GetActiveNoteCount(0) >= 3)
            Debug.Log("3 notes or more pressed on channel 0");
    }
}
```

<div class="page" />

## MidiChannelFilter / MidiDeviceFilter

### Overview

Filter components that pass through only specific MIDI channels or devices and forward events to downstream GameObjects. In addition to all MIDI 1.0 messages, they also forward Raw UMP (`IMidi2RawUmpEventHandler`). Use them to split routes between UI and gameplay, for example.

### Settings

**MidiChannelFilter**

| Field | Description |
|------------|------|
| `allowedChannels` | Allowed channels (empty = pass all) |
| `blockedChannels` | Blocked channels (takes priority over allowed) |
| `forwardTargets` | Array of forward-destination GameObjects |

**MidiDeviceFilter**

| Field | Description |
|------------|------|
| `allowedDeviceIds` | Allowed device IDs (empty = pass all) |
| `blockedDeviceIds` | Blocked device IDs |
| `allowVirtualDevices` | Allow virtual devices with the `virtual:` prefix |
| `forwardTargets` | Array of forward-destination GameObjects |

### Combining with filters

Example wiring to pass events to downstream components through filters:

```
[MidiManager]
    ↓
[MidiDeviceFilter]  ← registerWithMidiManager = true (chain entry point)
    ↓ forwardTargets
[MidiChannelFilter] ← registerWithMidiManager = false
    ↓ forwardTargets
[MidiInputRouter]   ← registerWithMidiManager = false
```

**Important:** When using a filter chain, set `registerWithMidiManager = false` on any `MidiChannelFilter` other than the entry point, and also set `registerWithMidiManager = false` on downstream `MidiInputRouter` and `MidiNoteTracker`. Registering twice causes events to be processed twice.

See `Assets/MIDI/Samples/Gameplay/Scenes/MidiGameplaySampleScene.unity` for a working example.

When using them directly without a filter, leaving `registerWithMidiManager = true` (the default) on the Router / Tracker is fine.

<div class="page" />

## MidiClockSync / Chord & scale detection

### MidiClockSync

Syncs to a MIDI Clock from an external sequencer, etc. BPM, beat, and bar events can be used from game logic or `SmfPlayerClockAdapter`.

| Property / API | Description |
|------------------|------|
| `pulsesPerQuarterNote` | Clock pulses per quarter note (MIDI standard 24) |
| `beatsPerBar` | Beats per bar (default 4) |
| `deviceIdFilter` | Empty = all devices |
| `EstimatedBpm` / `IsBpmStable` | Estimated BPM value and stability flag |
| `onBeat` / `onBar` | Beat / bar boundary |
| `onStarted` / `onStopped` | Start / Stop received |

```csharp
var clock = gameObject.AddComponent<MidiClockSync>();
clock.onBeat.AddListener(() => Debug.Log("Beat"));
clock.onBar.AddListener(() => Debug.Log("Bar"));
```

`MidiClockOutput` can send a 120 BPM Clock. `SmfPlayerClockAdapter` links the tempo or playback position of `SmfPlayer` to an external Clock in `Follow` / `Step` / `Free` mode.

### Chord & scale detection

Combine with `MidiNoteTracker` to estimate chord names and detect out-of-scale notes.

```csharp
using jp.kshoji.unity.midi.util;

var notes = noteTracker.GetActiveNotes(0);
var chord = ChordRecognition.Recognize(notes);
var inScale = ScaleUtility.AllNotesInScale(notes, rootNote: 0, MidiScaleType.Major);
```

`MidiChordDetector` notifies chord changes via `onChordChanged` and detects out-of-scale input via `onScaleViolation`. If you set `targetChord` and call `EvaluateNow()`, the match against the target chord (quiz) is notified via `onQuizCorrect` / `onQuizIncorrect`. `onAllNotesInScale` fires when all held notes are within the scale.

<div class="page" />

## MidiCcSmoother / MidiCcButton

Provide smoothing and threshold toggling of CC values. They can also be used from Animator integration, etc.

### MidiCcProcessorBase

Abstract base for CC processor components. Channel / device matching helpers and EMA / time-based smooth helpers (EMA delegates to `MidiControlSmoothing`).

Location: `Assets/MIDI/Scripts/Gameplay/MidiCcProcessorBase.cs`

| API | Description |
|-----|-------------|
| `MatchesChannel` / `MatchesDevice` | Filter helpers (−1 / empty = match all) |
| `ApplyEma(...)` | Delegates to `MidiControlSmoothing.ApplyEma` |
| `SmoothTowards(current, target, smoothingTime, deltaTime)` | Frame-based lerp toward a normalized target |

### MidiCcSmoother

| Item | Description |
|------|------|
| `configs[]` | Controller number, channel, and `smoothFactor` (0.01–1.0; smaller is smoother) |
| `onSmoothedValue[]` | UnityEvent of the normalized value (0–1) |
| `GetSmoothedNormalizedValue(index)` | Get the latest normalized value |

### MidiCcButton

| Item | Description |
|------|------|
| `onThreshold` / `offThreshold` | Threshold with hysteresis (default 64 / 63) |
| `onPressed` / `onReleased` | Press / release events |
| `IsPressed` | Current on-state |

<div class="page" />

## Source files

| File | Role |
|----------|------|
| `MidiInputMap.cs` | ScriptableObject definition |
| `MidiInputBinding.cs` | Binding definition |
| `MidiInputCondition.cs` | Condition-evaluation logic |
| `MidiInputBindingMigration.cs` | Legacy enum migration |
| `MidiInputMessageEventArgs.cs` | `onMessage` DTO |
| `MidiInputRouter.cs` | Event router |
| `MidiNoteTracker.cs` | Note-state management |
| `MidiNoteStateChangedEvent.cs` | State-change event args |
| `MidiFilterBase.cs` | Filter base class |
| `MidiChannelFilter.cs` | Channel filter |
| `MidiDeviceFilter.cs` | Device filter |
| `MidiEventForwarder.cs` | Event-forwarding helper |
| `SmfPlayer.cs` / `SmfPlayerState.cs` | SMF playback |
| `MidiRecorder.cs` | SMF recording |
| `MidiClockSync.cs` / `MidiClockState.cs` / `MidiClockOutput.cs` | MIDI Clock sync |
| `SmfPlayerClockAdapter.cs` | Tempo linkage between SMF and external Clock |
| `MidiChordDetector.cs` | Chord & scale detection (realtime detection + quiz) |
| `MidiCcProcessorBase.cs` | Shared base for CC processors |
| `MidiCcSmoother.cs` / `MidiCcButton.cs` | CC smoothing / threshold toggle |
| `Gameplay/Theory/ChordRecognition.cs` / `Gameplay/Theory/ScaleUtility.cs` | Chord-name estimation / scale utilities |
| `MidiManagerReceiverBridge.cs` | Sequencer → MidiManager bridge |

<div class="page" />

## Related docs

- [MIDI 1.0 Runtime (MidiManager)](midi1.md)
- [Utilities (MidiNoteUtility / MidiMessageBuilder)](utilities.md)
- [Editor Tools (MIDI Monitor)](editor-tools.md)
- [SMF Tools (SmfPlayer / MidiRecorder)](smf-tools.md)
- [Getting Started Guide](getting-started.md)
