# Getting Started (Install, Initialize, Send/Receive)

## Install

1. Import the `.unitypackage` from the Asset Store.
    - If the package was updated over an older version, check:
      `Assets/MIDI/Plugins/Android`
      and remove any older/duplicate `.aar` files if present.
2. Switch to your target platform (iOS / Android / Standalone / WebGL / UWP).
3. Build and run the sample scenes:
    - `Assets/MIDI/Samples/Scenes/`

If you previously installed optional dependencies (e.g., Nearby), update them to latest.

<div class="page" />

## MIDI 1.0 quick start (receive + optional BLE scan)

Recommended lifecycle:
```csharp
using UnityEngine;
using jp.kshoji.unity.midi;

public sealed class Midi1QuickStart : MonoBehaviour, IMidiDeviceEventHandler, IMidiNoteOnEventHandler
{
    private void Awake()
    {
        MidiManager.Instance.RegisterEventHandleObject(gameObject);

        MidiManager.Instance.InitializeMidi(() =>
        {
#if (UNITY_ANDROID || UNITY_IOS || UNITY_WEBGL) && !UNITY_EDITOR
            // BLE MIDI only (where supported): start scan after init completes.
            MidiManager.Instance.StartScanBluetoothMidiDevices(0);
#endif
        });
    }

    private void OnDestroy()
    {
        MidiManager.Instance.TerminateMidi();
    }

    public void OnMidiInputDeviceAttached(string deviceId)
        => Debug.Log($"Input attached: {deviceId}");

    public void OnMidiInputDeviceDetached(string deviceId)
        => Debug.Log($"Input detached: {deviceId}");

    public void OnMidiOutputDeviceAttached(string deviceId)
        => Debug.Log($"Output attached: {deviceId}");

    public void OnMidiOutputDeviceDetached(string deviceId)
        => Debug.Log($"Output detached: {deviceId}");

    public void OnMidiNoteOn(string deviceId, int group, int channel, int note, int velocity)
        => Debug.Log($"NoteOn dev:{deviceId} ch:{channel} note:{note} vel:{velocity}");
}
```

## MIDI 1.0 send

Direct `MidiManager` call:

```csharp
// Send Note On
MidiManager.Instance.SendMidiNoteOn(
    "deviceId",
    0 /*group*/,
    0 /*channel*/,
    60 /*note*/,
    127 /*velocity*/
);
```

Using the Fluent API (`MidiSend`):

```csharp
using jp.kshoji.unity.midi.util;

MidiSend.To("deviceId").Channel(0).NoteOn(60, 127);

// Send to the first output device
MidiSend.ToFirstOutput().Channel(0).ControlChange(1, 64);
```

See [Utilities (MidiNoteUtility / MidiMessageBuilder)](utilities.md) for details.

You can get output device IDs from:
- `MidiManager.Instance.OutputDeviceIdSet` (type: `HashSet<string>`)

<div class="page" />

## Debugging in the Editor

During Play Mode, use the **MIDI Monitor** to check MIDI input/output messages in real time.

```
Window > MIDI > Monitor
```

See [Editor Tools (MIDI Monitor)](editor-tools.md) for details.

## Optional: Unity-MCP (AI / Cursor)

Install [Unity-MCP](https://github.com/IvanMurzak/Unity-MCP) (`com.ivanmurzak.unity.mcp`) in the project, open **Window → AI Game Developer**, then from Cursor:

1. Play Mode → `midi-init` → `midi-devices-list` → `midi-send-note` (C4) → `midi-monitor-read`

Built Player: place `MidiMcpRuntimeBootstrap`, set Host to the LAN MCP Server, wire components in the Editor first (control tools only on Player). Networking (`FEATURE_MIDI_NETWORK`): Editor `midi-net-hub-client-setup` → Player `midi-net-rtt`. MIDI 2.0: `midi2-devices-list` / `midi2-send-ump`. See [MCP README](../Scripts/Integrations/Mcp/README.md), [sample prompts](unity-mcp-sample-prompts.md), and [define matrix](unity-mcp-define-matrix.md). VST host tools live in [Unity-VST3-Bridge](https://github.com/kshoji/Unity-VST3-Bridge) (desktop Player; not Android MCP).

## MIDI 2.0 quick start
```csharp
using UnityEngine;
using jp.kshoji.unity.midi;

public sealed class Midi2QuickStart : MonoBehaviour, IMidi2DeviceEventHandler, IMidi2NoteOnEventHandler
{
    private void Awake()
    {
        MidiManager.Instance.RegisterEventHandleObject(gameObject);
        MidiManager.Instance.InitializeMidi2(() => { });
    }

    private void OnDestroy()
    {
        MidiManager.Instance.TerminateMidi2();
    }

    public void OnMidi2InputDeviceAttached(string deviceId)
        => Debug.Log($"MIDI2 In attached: {deviceId}");

    public void OnMidi2InputDeviceDetached(string deviceId)
        => Debug.Log($"MIDI2 In detached: {deviceId}");

    public void OnMidi2OutputDeviceAttached(string deviceId)
        => Debug.Log($"MIDI2 Out attached: {deviceId}");

    public void OnMidi2OutputDeviceDetached(string deviceId)
        => Debug.Log($"MIDI2 Out detached: {deviceId}");

    public void OnMidi2NoteOn(string deviceId, int group, int channel, int note, int velocity, int attributeType, int attributeData)
        => Debug.Log($"MIDI2 NoteOn dev:{deviceId} g:{group} ch:{channel} note:{note} vel:{velocity}");
}
```

## Where to look next

- [Gameplay Components (InputMap / NoteTracker / Filter)](gameplay.md)
- [SMF Tools (SmfPlayer / MidiRecorder / TempoMapExtractor)](smf-tools.md)
- [Utilities (MidiNoteUtility / MidiMessageBuilder)](utilities.md)
- [Editor Tools (MIDI Monitor)](editor-tools.md)
- [Unity-MCP integration](../Scripts/Integrations/Mcp/README.md) · [sample prompts](unity-mcp-sample-prompts.md) · [define matrix](unity-mcp-define-matrix.md)
- [Platforms & Limitations](platforms.md)
- [Build PostProcessing & Scripting Define Symbols](build-postprocessing.md)
- [Transports & Platform Notes](transports.md)
- [Samples](samples.md)
