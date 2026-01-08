# Unity MIDI Plugin — Documentation Index

This documentation describes the contents of `Assets/MIDI`, how the runtime API is structured, and how to use MIDI 1.0, MIDI 2.0 (UMP), MPE, and related transports in Unity.

## NotebookLM
We have created an AI that registers this manual.  
If you have any questions, please contact us here first. (A Google account is required.)  
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
- `Scripts/midisystem/`  
  Standard MIDI File (SMF) reader/writer + sequencing model (`Sequence`, `Track`, messages).
- `Scripts/UmpSequencer/`  
  UMP sequence utilities (clip/container read/write, sequencer, SMF↔UMP converter).
- `Scripts/RTP-MIDI-for-.NET/`  
  RTP-MIDI implementation (embedded module + its own docs).
- `Scripts/UdpMidi2Discovery/`  
  Discovery dependencies for UDP MIDI 2.0 workflows.
- `Samples/`  
  Example scenes and scripts.

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
2. Implement one or more event handler interfaces.
3. Register handler objects with the manager.
4. Initialize the manager (or ensure it’s present in the scene).
5. Test using the sample scenes in `Assets/MIDI/Samples/Scenes`.
