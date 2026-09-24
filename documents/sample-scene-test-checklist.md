# Sample Scene Verification Checklist

Checklist for verifying the remaining sample scenes.

> **Note:** `MptkIntegrationSampleScene.unity` additionally requires `FEATURE_USE_MPTK`.

<div class="page" />

## Common prerequisites

Items to confirm for every scene:

- [ ] Open the scene and enter **Play mode** (no console errors)
- [ ] Open `Window > MIDI > Monitor` and check IN/OUT as needed
- [ ] Enable the relevant **Scripting Define Symbols** (see table below)
- [ ] The IMGUI panel appears and responds to button input

### Scripting Define Symbols

| Symbol | Target scene |
|----------|------------|
| `FEATURE_USE_TIMELINE` + `com.unity.timeline` | MidiTimelineIntegrationSampleScene |
| `FEATURE_USE_VISUALSCRIPTING` + `com.unity.visualscripting` | MidiVisualScriptingIntegrationSampleScene |
| `FEATURE_MIDI_NETWORK` | MidiNetworkJamSampleScene |
| `FEATURE_INPUT_SYSTEM` + `com.unity.inputsystem` | InputSystemBridgeSampleScene |

<div class="page" />

## Scene list

| # | Category | Scene | Path |
|---|----------|--------|------|
| 1 | Gameplay foundation | MidiGameplaySampleScene | `Assets/MIDI/Samples/Gameplay/Scenes/MidiGameplaySampleScene.unity` |
| 2 | Unity integration | MidiAnimatorIntegrationSampleScene | `Assets/MIDI/Samples/Integrations/Scenes/MidiAnimatorIntegrationSampleScene.unity` |
| 3 | Unity integration | MidiTimelineIntegrationSampleScene | `Assets/MIDI/Samples/Integrations/Scenes/MidiTimelineIntegrationSampleScene.unity` |
| 4 | Unity integration | MidiVisualScriptingIntegrationSampleScene | `Assets/MIDI/Samples/Integrations/Scenes/MidiVisualScriptingIntegrationSampleScene.unity` |
| 5 | Gameplay | ChordPuzzleSampleScene | `Assets/MIDI/Samples/Gameplay/Scenes/ChordPuzzleSampleScene.unity` |
| 6 | Gameplay | ChordScaleSampleScene | `Assets/MIDI/Samples/Gameplay/Scenes/ChordScaleSampleScene.unity` |
| 7 | Gameplay | MidiClockSyncSampleScene | `Assets/MIDI/Samples/Gameplay/Scenes/MidiClockSyncSampleScene.unity` |
| 8 | Gameplay | ScalePracticeSampleScene | `Assets/MIDI/Samples/Gameplay/Scenes/ScalePracticeSampleScene.unity` |
| 9 | Input System | InputSystemBridgeSampleScene | `Assets/MIDI/Samples/Integrations/InputSystem/Scenes/InputSystemBridgeSampleScene.unity` |
| 10 | Networking | MidiNetworkJamSampleScene | `Assets/MIDI/Samples/Integrations/Networking/Scenes/MidiNetworkJamSampleScene.unity` |
| 11 | Foundation | FoundationSampleScene | `Assets/MIDI/Samples/Foundation/Scenes/FoundationSampleScene.unity` |

<div class="page" />

## Recommended test order

1. **Foundation:** MidiGameplaySampleScene
2. **Unity integrations:** Animator → Timeline → Visual Scripting
3. **Cross-cutting:** ClockSync → Chord/Scale → InputSystem
4. **Networking / Foundation:** MidiNetworkJamSampleScene → FoundationSampleScene

<div class="page" />

## Per-scene checklist

### 1. MidiGameplaySampleScene

**Path:** `Assets/MIDI/Samples/Gameplay/Scenes/MidiGameplaySampleScene.unity`  
**Purpose:** DeviceFilter → ChannelFilter → Router / NoteTracker pipeline

- [ ] After Play starts, IMGUI shows pipeline state
- [ ] **NoteOn C4 (ch0)** → Router Launch binding fires (log / counter update)
- [ ] **CC64 = 127 (ch0)** → Toggle binding fires
- [ ] **NoteOn C4 (ch1)** → blocked by ChannelFilter (does not reach Router / Tracker)
- [ ] **Add D4 + E4 (ch0)** → 3-note chord detection message
- [ ] SmfPlayer playback / MidiRecorder recording sections work
- [ ] With a hardware MIDI controller, C4 / CC64 on ch0 behave the same way

---

### 2. MidiAnimatorIntegrationSampleScene

**Path:** `Assets/MIDI/Samples/Integrations/Scenes/MidiAnimatorIntegrationSampleScene.unity`  
**Purpose:** Drive Animator parameters from MIDI

- [ ] **CC1 = 127** → cube rises
- [ ] **Note 60** → pulse effect (scale change)
- [ ] **CC10 / CC11** → cube rotates
- [ ] Same changes via both virtual MIDI Inject and hardware input

---

### 3. MidiTimelineIntegrationSampleScene

**Path:** `Assets/MIDI/Samples/Integrations/Scenes/MidiTimelineIntegrationSampleScene.unity`  
**Requires:** `FEATURE_USE_TIMELINE`, `com.unity.timeline`

- [ ] **Play Timeline** → SMF playback starts
- [ ] Director time and SmfPlayer time stay in sync
- [ ] **Pause** → SmfPlayer pauses
- [ ] **Seek to 1.0 s** → SmfPlayer follows

---

### 4. MidiVisualScriptingIntegrationSampleScene

**Path:** `Assets/MIDI/Samples/Integrations/Scenes/MidiVisualScriptingIntegrationSampleScene.unity`  
**Requires:** `FEATURE_USE_VISUALSCRIPTING`, `com.unity.visualscripting`

- [ ] **NoteOn C4** → cube color changes
- [ ] **CC1 = 127** → visual feedback changes
- [ ] Events arrive via Event Bus / Script Graph

---

### 5. ChordPuzzleSampleScene

**Path:** `Assets/MIDI/Samples/Gameplay/Scenes/ChordPuzzleSampleScene.unity`  
**Purpose:** Chord-name quiz (MidiScaleQuiz)

- [ ] Target chord is shown on screen
- [ ] **Hold C Major / C Minor / Cmaj7** → recognized chord appears in the log
- [ ] **Check Answer** → Correct / Incorrect judgment
- [ ] On correct answer, **Next Puzzle** advances to the next challenge
- [ ] **Release All** releases notes

---

### 6. ChordScaleSampleScene

**Path:** `Assets/MIDI/Samples/Gameplay/Scenes/ChordScaleSampleScene.unity`  
**Purpose:** ChordRecognition + MidiChordDetector + MidiScaleQuiz

- [ ] **Hold C Major** → Chord = C, C Major scale = OK
- [ ] **Hold C Minor** → Chord = Cm
- [ ] **Add Out-of-scale Note (61)** → Out of scale display / log
- [ ] **Evaluate Quiz Target = C** → quiz judgment
- [ ] **Release All** resets state

---

### 7. MidiClockSyncSampleScene

**Path:** `Assets/MIDI/Samples/Gameplay/Scenes/MidiClockSyncSampleScene.unity`  
**Purpose:** External MIDI Clock sync and BPM estimation

- [ ] **Inject Start** → IsPlaying = true
- [ ] **Inject 24 Timing Clocks** → BPM estimate updates, Bar/Beat advances
- [ ] **Inject Stop** → stops
- [ ] **Set Adapter Mode: Step / Follow** → SmfPlayerClockAdapter mode switches
- [ ] onBeat events appear in the log

---

### 8. ScalePracticeSampleScene

**Path:** `Assets/MIDI/Samples/Gameplay/Scenes/ScalePracticeSampleScene.unity`  
**Purpose:** Membership checks for all scale types

- [ ] Switching scale type (Major / Minor, etc.) updates the In scale display
- [ ] **Add C4 (60)** → In scale: Yes (when Major is selected)
- [ ] **Add C#4 (61)** → In scale: No, Scale violation log
- [ ] **Add E4 (64)** → chord recognition display updates
- [ ] **Release All** clears state

---

### 9. InputSystemBridgeSampleScene

**Path:** `Assets/MIDI/Samples/Integrations/InputSystem/Scenes/InputSystemBridgeSampleScene.unity`  
**Requires:** `FEATURE_INPUT_SYSTEM`

- [ ] After Play starts, a Synthetic Device is created
- [ ] MIDI Inject reflects into Input System state
- [ ] Input Action → MIDI send (bidirectional bridge)
- [ ] Confirm IN/OUT in Monitor

---

### 10. MidiNetworkJamSampleScene

**Path:** `Assets/MIDI/Samples/Integrations/Networking/Scenes/MidiNetworkJamSampleScene.unity`  
**Requires:** `FEATURE_MIDI_NETWORK`

- [ ] Connect via Hub / Client loopback or two instances
- [ ] In Broadcast mode, MIDI events inject into the Client-side virtual device
- [ ] Switch Merge / Playback modes (if UI is available)
- [ ] Confirm Client-side IN in Monitor

---

### 11. FoundationSampleScene

**Path:** `Assets/MIDI/Samples/Foundation/Scenes/FoundationSampleScene.unity`  
**Purpose:** Device selection, latency calibration, Foundation UI

- [ ] After Play starts, the Foundation UI shell appears
- [ ] Device selection UI lists connected devices
- [ ] Latency calibration flow works
- [ ] Settings persistence (if present) works

<div class="page" />

## Related documents

- [Samples](samples.md)
- [Genre kits](kits.md)
- [Unity Ecosystem Integrations](integrations.md)
- [Gameplay Components](gameplay.md)
