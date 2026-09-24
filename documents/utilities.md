# Utilities (MidiNoteUtility / MidiMessageBuilder)

This page describes the runtime utilities that assist with MIDI 1.0 send/receive and related helpers.

Namespace: `jp.kshoji.unity.midi.util`

Location:
- `Assets/MIDI/Scripts/Utilities/MusicTheory/MidiNoteUtility.cs`
- `Assets/MIDI/Scripts/Utilities/Messaging/MidiMessageBuilder.cs`
- `Assets/MIDI/Scripts/Utilities/Messaging/PitchBendUtility.cs`
- `Assets/MIDI/Scripts/Utilities/Messaging/MidiControlSmoothing.cs`
- `Assets/MIDI/Scripts/Utilities/Messaging/AutomationInterpolation.cs`
- `Assets/MIDI/Scripts/Utilities/Smf/` — `TempoMapExtractor`, `MeasureTimeUtility`, `TupletUtility`, `SwingUtility`, `MidiMetaMessageFactory` (details in [SMF Tools](smf-tools.md))
- `Assets/MIDI/Scripts/Editor/UI/PianoKeyboardElement.cs` (`MidiKeyboardLogic`)

<div class="page" />

## MidiNoteUtility

A static utility that provides conversion between MIDI note numbers and note names, octave calculation, semitone transposition, and more. It is a pure C# implementation with no dependency on Unity or the MIDI plugin.

### Key constants

| Constant | Value | Description |
|------|-----|------|
| `MiddleC` | 60 | C4 in General MIDI |
| `MinNote` | 0 | Minimum note number |
| `MaxNote` | 127 | Maximum note number |

### API overview

| Method | Description | Example |
|----------|------|-----|
| `ToNoteName(noteNumber, useSharps)` | Convert a note number to a note name | `60` → `"C4"` |
| `TryParseNoteName(name, out noteNumber)` | Convert a note name to a note number | `"C#4"` → `61` |
| `GetOctave(noteNumber)` | Get the octave number | `60` → `4` |
| `GetPitchClass(noteNumber)` | Get the pitch class (0–11) | `64` → `4` (E) |
| `Transpose(noteNumber, semitones)` | Transpose by semitones (clamped to 0–127) | `(60, 12)` → `72` |
| `FromPitchClassAndOctave(pitchClass, octave)` | Compute a note number from pitch class and octave | `(0, 4)` → `60` |
| `FormatNoteWithNumber(noteNumber)` | Show the note name together with its number | `60` → `"C4 (60)"` |

### Usage example

```csharp
using jp.kshoji.unity.midi.util;

// Note number → note name
Debug.Log(MidiNoteUtility.ToNoteName(60));           // "C4"
Debug.Log(MidiNoteUtility.ToNoteName(61, useSharps: false)); // "Db4"

// Note name → note number
if (MidiNoteUtility.TryParseNoteName("C#4", out var note))
    Debug.Log(note);  // 61

// Transpose
var transposed = MidiNoteUtility.Transpose(60, 7);    // G4 (67)
```

### Octave convention

This utility uses the standard MIDI octave notation.

```
octave = (noteNumber / 12) - 1
```

Examples: note number 60 → C4, 0 → C-1, 127 → G9

<div class="page" />

## MidiMessageBuilder (MidiSend)

Wraps the send API of `MidiManager` in a fluent interface, providing chainable MIDI message sending. Internally it calls `MidiManager.Instance.SendMidi*`.

### Entry points

| Method | Description |
|----------|------|
| `MidiSend.To(deviceId)` | Get a builder targeting the specified device |
| `MidiSend.ToFirstOutput()` | Get a builder targeting the first output device |

If `deviceId` is empty, an `ArgumentException` is thrown; if no output device exists, an `InvalidOperationException` is thrown.

### Builder methods

| Method | Description |
|----------|------|
| `Group(int)` | Set the MIDI 2.0 group (0–15, default 0) |
| `Channel(int)` | Set the MIDI channel (0–15, default 0) |
| `NoteOn(note, velocity)` | Send a Note On (velocity defaults to 127) |
| `NoteOff(note, velocity)` | Send a Note Off (velocity defaults to 0) |
| `ControlChange(controller, value)` | Send a Control Change |
| `ProgramChange(program)` | Send a Program Change |
| `PitchWheel(amount)` | Send pitch bend (14-bit, 0–16383; center 8192) |
| `PitchWheelNormalized(value)` | Send pitch bend from a normalized float (via `PitchBendUtility`) |
| `SystemExclusive(byte[])` | Send SysEx |
| `AllNotesOff()` / `AllNotesOff(channel)` | Send All Notes Off (CC 123) on all or one channel |

Each method returns `this`, so calls can be chained.

### Usage example

```csharp
using jp.kshoji.unity.midi.util;

// Single send
MidiSend.To(deviceId).Channel(0).NoteOn(60, 100);

// Send a chord in sequence
MidiSend.To(deviceId).Channel(0)
    .NoteOn(60, 127)
    .NoteOn(64, 127)
    .NoteOn(67, 127);

// Send a CC to the first output device
MidiSend.ToFirstOutput().Channel(0).ControlChange(1, 64);
```

### Relationship with MidiManager

`MidiSend` is a thin wrapper over `MidiManager`. It behaves the same as the existing `SendMidiNoteOn` and similar methods. In the Unity editor, sent messages appear as OUT in the [MIDI Monitor](editor-tools.md).

For the detailed send API, see also "Sending MIDI 1.0 messages" in [MIDI 1.0 Runtime (MidiManager)](midi1.md).

<div class="page" />

## PitchBendUtility

14-bit pitch-bend helpers (center = 8192). Pure C#; no Unity dependency.

Location: `Assets/MIDI/Scripts/Utilities/Messaging/PitchBendUtility.cs`

| Member | Description |
|--------|-------------|
| `Min` / `Max` / `Center` | `0` / `16383` / `8192` |
| `Clamp(amount)` | Clamp to 0–16383 |
| `FromNormalized(normalized, zeroToOne)` | Map float → 14-bit (`zeroToOne`: 0–1, or bipolar −1…+1) |
| `ToNormalized(amount)` | Map 14-bit → 0–1 (center ≈ 0.5) |
| `Split` / `Combine` | LSB/MSB (7+7 bit) pack/unpack |

Semitone-range conversion is not included yet (see Future features in [index](index.md)).

```csharp
var amount = PitchBendUtility.FromNormalized(0.75f);
PitchBendUtility.Split(amount, out var lsb, out var msb);
MidiSend.To(deviceId).Channel(0).PitchWheel(amount);
```

<div class="page" />

## MidiControlSmoothing

Shared EMA (exponential moving average) helpers used by `MidiCcSmoother` and sequencer output stages.

Location: `Assets/MIDI/Scripts/Utilities/Messaging/MidiControlSmoothing.cs`

| Method | Description |
|--------|-------------|
| `ApplyEma(previous, raw, smoothFactor)` | EMA; `smoothFactor` 0.01–1.0 (smaller = smoother) |
| `ApplyEmaToMidi7(...)` | EMA then round/clamp to 0–127 |
| `ApplyEmaToMidi14(...)` | EMA then round/clamp to 0–16383 (pitch bend) |

<div class="page" />

## AutomationInterpolation

Evaluates ordered automation breakpoints (normalized 0–1 values) at a tick.

Location: `Assets/MIDI/Scripts/Utilities/Messaging/AutomationInterpolation.cs`

| API | Description |
|-----|-------------|
| `CurveKind.Linear` / `Smooth` | Linear lerp or smoothstep between points |
| `Evaluate(points, tick, curve)` | Value at tick; before first / after last clamps to endpoint; empty → 0 |

<div class="page" />

## MidiKeyboardLogic

A keyboard-layout utility shared between the editor's virtual MIDI controller keyboard and the `MidiKeyboardElement` on the runtime UI surface.

Location: `Assets/MIDI/Scripts/Editor/UI/PianoKeyboardElement.cs` (`MidiKeyboardLogic`)

| API | Description |
|-----|------|
| `IsBlackKey(note)` | Determine whether the note is a black key |
| `CountWhiteKeysBefore(note)` | Number of white keys before the specified note |
| `CountWhiteKeys(start, end)` | Number of white keys within a range |
| `DefaultStartNote` / `DefaultEndNote` | Default 2-octave range (C3–B4) |

For details on the runtime UI, see [Genre Kits](kits.md).

<div class="page" />

## TempoMapExtractor

A static utility that extracts tempo changes and time signatures from an SMF (`Sequence`) as a time series. It can be used for `SmfPlayer` playback-time calculation and as a foundation for generating scores and timelines.

Location: `Assets/MIDI/Scripts/Utilities/Smf/TempoMapExtractor.cs`

### API

| Method | Description |
|----------|------|
| `Extract(Sequence)` | Build a tempo map from a Sequence |
| `Extract(byte[] smfData)` | Extract from SMF binary |
| `Extract(string filePath)` | Extract from a `.mid` file |

### TempoMap

| Member | Description |
|----------|------|
| `TempoChanges` | Tempo changes (Tick / seconds / BPM) |
| `TimeSignatures` | Time signatures |
| `TotalDurationSeconds` | Total playback duration |
| `GetBpmAt(tick)` | BPM at the specified Tick |
| `TickToSeconds(tick)` | Tick → seconds |
| `SecondsToTick(seconds)` | seconds → Tick |

For detailed usage examples, see [SMF Tools](smf-tools.md). The same folder also provides:

| Utility | Role |
|---------|------|
| `MeasureTimeUtility` | Bar length in ticks (`TicksPerBar` / `TicksPerBeat`), measure ↔ time |
| `TupletUtility` | Rational tuplet step ticks (e.g. 3:2); reversible index↔tick |
| `SwingUtility` | Playback-only swing delay on even offbeats |
| `MidiMetaMessageFactory` | Build/read Tempo, Time Signature, Track Name meta events |

<div class="page" />

## MidiScaleUtility

A static utility that determines whether a note belongs to a specified scale. It is also used by the **Is Note In Scale** node in Visual Scripting.

### MidiScaleType

| Value | Description |
|----|------|
| `Major` | Major scale |
| `NaturalMinor` | Natural minor scale |
| `HarmonicMinor` | Harmonic minor scale |
| `MajorPentatonic` | Major pentatonic |
| `MinorPentatonic` | Minor pentatonic |
| `Blues` | Blues |
| `Dorian` | Dorian |
| `Mixolydian` | Mixolydian |
| `Chromatic` | Chromatic |

### API

| Method | Description |
|----------|------|
| `IsNoteInScale(noteNumber, rootNote, scaleType)` | Whether the note is contained in the scale |
| `GetIntervals(scaleType)` | Array of semitone intervals for the scale |

```csharp
using jp.kshoji.unity.midi.util;

// D4 (62) is contained in the C major scale (root C4=60)
bool inScale = MidiScaleUtility.IsNoteInScale(62, 60, MidiScaleType.Major); // true
```

<div class="page" />

## ScaleUtility / ChordRecognition

`ScaleUtility` extends `MidiScaleUtility` to determine scale membership for multiple notes at once.

| Method | Description |
|----------|------|
| `AllNotesInScale(notes, rootNote, scaleType)` | Whether all notes are in the scale |
| `AnyNoteOutsideScale(notes, rootNote, scaleType)` | Whether any note is outside the scale |
| `GetIntervals(scaleType)` | Alias for `MidiScaleUtility.GetIntervals` |

`ChordRecognition.Recognize(activeNotes)` infers a chord name (`C`, `Cm7`, `Cmaj7`, etc.) from the set of held notes. It supports the main triad and seventh-chord patterns.

```csharp
var notes = noteTracker.GetActiveNotes(0);
var chord = ChordRecognition.Recognize(notes);
var inScale = ScaleUtility.AllNotesInScale(notes, rootNote: 60, MidiScaleType.Major);
```

For detailed real-time detection, see `MidiChordDetector` in [Gameplay Components](gameplay.md).

<div class="page" />

## Related docs

- [MIDI 1.0 Runtime (MidiManager)](midi1.md)
- [SMF Tools (SmfPlayer / MidiRecorder)](smf-tools.md)
- [Editor Tools (MIDI Monitor)](editor-tools.md)
- [Getting Started](getting-started.md)
