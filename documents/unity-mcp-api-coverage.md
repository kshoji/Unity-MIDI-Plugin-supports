# Unity-MCP API Coverage Matrix

Pinned: `com.ivanmurzak.unity.mcp` **0.89.0**  
Related: [define matrix](unity-mcp-define-matrix.md) · [sample prompts](unity-mcp-sample-prompts.md) · [MCP README](../Scripts/Integrations/Mcp/README.md)

Legend: **Direct tool** · **Generic tool** · **Raw only** · **Diagnostic only** · **Out of scope**

## MIDI 1.0 (`MidiOutgoingMessageType`)

| Enum / API | Coverage | Tool |
|------------|----------|------|
| NoteOn / NoteOff | Direct | `midi-send-note` |
| ControlChange | Direct | `midi-send-cc` |
| ProgramChange | Direct | `midi-send-pc` |
| ChannelAftertouch, PolyphonicAftertouch, PitchWheel | Generic | `midi-send-message` |
| SysEx / SystemCommon / SingleByte / MTC / Song* / Tune / Clock / Start/Continue/Stop / ActiveSensing / Reset / Misc / Cable | Generic | `midi-send-message` |
| All Sound/Notes Off | Direct | `midi-panic` |
| Virtual inject (all above) | Generic | `midi-virtual-device-inject` (+ convenience `midi-virtual-device`) |
| Device name/vendor/product | Direct | `midi-device-info` |
| Midi2RawUmp (on MIDI1 enum) | Raw only | `midi2-send-ump` |

## MIDI 2.0 / UMP (`MidiManager` send / inject)

| Category | Coverage | Tool |
|----------|----------|------|
| Initialize / Terminate / status | Direct | `midi2-lifecycle` |
| Device list | Direct | `midi2-devices-list` |
| Channel voice (Note/AT/CC/PC/PB/Per-Note/RPN/NRPN relative) | Direct | `midi2-send-channel-voice` |
| Utility + MIDI1 System on UMP path | Direct | `midi2-send-system` |
| SysEx data | Direct | `midi2-send-data` |
| Flex: tempo / time sig / key / text | Direct | `midi2-send-flex-data` |
| Flex: metronome / chord name structs | Raw only | `midi2-send-ump` (structured MCP deferred — complex structs) |
| Stream: EndpointDiscovery / StartOfClip / EndOfClip | Direct | `midi2-send-stream` |
| Stream: Endpoint Info/Identity/Name/Product/FunctionBlock/Config | Raw only | `midi2-send-ump` |
| Raw UMP escape hatch | Raw only | `midi2-send-ump` |
| Virtual register + inject | Direct | `midi2-virtual-device` |
| UMP monitor filter | Direct | `midi2-monitor-read` |

## UMP Sequence

| API | Coverage | Tool |
|-----|----------|------|
| UmpSequencer play/seek/loop | Direct | `ump-sequencer-control` |
| UmpSequencer record/save | Direct | `ump-recorder-control` |
| Sequence ↔ UmpSequence | Direct | `ump-sequence-convert` |
| Import `.midi2` asset | Direct | `ump-import-asset` |

## Transport

| API | Coverage | Tool |
|-----|----------|------|
| RTP-MIDI start/stop/connect | Direct | `midi-rtp-session` |
| BLE scan/advertise | Direct | `midi-ble-control` (platform-gated) |
| Nearby discover/advertise | Direct | `midi-nearby-control` |
| UDP MIDI 2 / UMP Endpoint | Direct | `midi2-udp-session` (secrets never echoed) |
| Aggregate status | Diagnostic | `midi-transport-status` |
| `FEATURE_ANDROID_COMPANION_DEVICE` | Diagnostic only | `midi-features-status` / docs (build post-process, not a runtime transport tool) |

## Foundation / Gameplay

| API | Coverage | Tool |
|-----|----------|------|
| MidiDeviceSelection | Direct | `midi-device-selection` |
| MidiLatencyCalibrator | Direct | `midi-latency-calibration` |
| MidiOutputRoutingPreset | Direct | `midi-output-routing` |
| MidiClockSync / MidiClockOutput | Direct | `midi-clock-sync` / `midi-clock-output` |
| ChordRecognition / MidiChordDetector | Direct | `midi-chord-state` |
| Note/scale theory | Direct | `midi-theory-utility` (+ existing `midi-note-utility`) |
| SmfPlayer loop/gain/transpose full edit | Out of scope | Use `smf-player-control` + Inspector; add `smf-player-configure` later if needed |
| InputMap binding CRUD (all message types) | Out of scope | Existing `midi-setup-router` covers create/append; full list/update/remove deferred |
| Animator mapping list/update/remove | Out of scope | Existing `midi-animator-add-*` remains append-oriented |

## Monitor

| Item | Coverage | Notes |
|------|----------|-------|
| includeRaw / sinceTimestamp | Direct | `midi-monitor-read` |
| MidiMonitorMessageType expansion (Channel AT / Poly AT / Sys Common/Realtime split) | Out of scope | Would require core classification changes in `MidiMonitorMessageType` producers; Detail string remains source of truth |
| Device attach/detach Resource | Out of scope | Prefer `midi-device-info` + Play Mode device lists |

## MPTK Free extras

| Tool | Coverage |
|------|----------|
| `midi-mptk-stream-command` | Direct (`MptkUtility`) |
| `midi-mptk-midi2-device` | Direct |
| `midi-mptk-mpe-zone` | Direct (`ConfigureZone`) |
| `midi-mptk-delayed-dispatch` | Direct |
| `midi-mptk-global-settings` | Direct (`MPTK_RunInBackground` / `MPTK_AudioListener`) |
| `midi://mptk/setup-report` | ValidateSetup + diagnostics (voice stats / SoundFont / global knobs) |
| `midi-mptk-distance-audio` | Direct (`MptkDistanceAudioSettings` / DistanceAttenuation · Orientation) |
| `midi-mptk-bank-program` | Direct (+ `action=list-drums` for drum preset catalog) |
| `midi://mptk/soundfont` | SoundFont name + drum preset catalog |
| `midi-mptk-velocity-attenuation` | Direct (**experimental** `MPTK_VelocityAttenuation`; default 960) |

## MPTK Pro extras

| Tool | Coverage |
|------|----------|
| `midi-mptk-effects-configure` | Direct (`MptkSynthEffectsController` / SoundFont filter·reverb·chorus) |
| `midi-mptk-voice-lifecycle` | Direct (`MptkVoiceLifecycle` / `PauseVoices`·`ResumeVoices`) |
| `midi-mptk-chord-progression` | Direct (`MptkChordProgressionPlayer` / Maestro progression presets) |

## MIDI-CI

| Item | Coverage |
|------|----------|
| Discovery | Direct (`midi-ci-discover`) |
| Profile / Property / Protocol Negotiation | Out of scope until library APIs exist |

## Scriptable Audio / Network (Editor vs Player)

| Tool | Host |
|------|------|
| `midi-sa-bootstrap-*` | **Editor** (wiring) |
| `midi-sa-transport` / `midi-sa-clock-mode` / `midi-sa-validate` | **Player** control (existing generators) |
| `midi-net-hub-client-setup` / `midi-net-bridge-setup` / `midi-wsnet2-sync-defines` | **Editor** (wiring / defines) |
| `midi-net-discovery-status` / `midi-net-rtt` | **Player** control (`ensure*` create is Editor-only) |

## VST3

| Item | Coverage |
|------|----------|
| Host operations | Out of scope in this repo | See [Unity-VST3-Bridge](https://github.com/kshoji/Unity-VST3-Bridge); MIDI Meta detects package only. Desktop Player MCP tools live in that package; **Android device MCP is not a VST3 target**. |

## Safety metadata

| Tool | Note |
|------|------|
| `midi2-devices-list` | `ReadOnlyHint` removed (initialize side effect) |
| `mpe-zone-status` | `ReadOnlyHint` removed (setupIfMissing) |
| `midi-net-discovery-status` | `ReadOnlyHint` removed (ensure*) |
| `midi-net-rtt` | `ReadOnlyHint` removed (ping) |
| Destructive paths | `confirm=true` on define enable, asset overwrite (UMP convert/save, routing preset) |
