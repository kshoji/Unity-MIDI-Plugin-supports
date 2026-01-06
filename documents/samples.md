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

- `FileUtils.cs`, `AudioClipUtils.cs`  
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

If a sample doesn’t receive events:
- confirm the correct platform backend is in use,
- check transport requirements (permissions on WebGL, firewall for network transports, etc.).

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

If you’re using Maestro / MPTK, see:
- [Maestro / MPTK Integration](mptk.md)