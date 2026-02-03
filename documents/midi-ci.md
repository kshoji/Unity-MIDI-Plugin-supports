# MIDI-CI / Capability Negotiation

This project includes experimental support for MIDI Capability Inquiry (MIDI-CI) style workflows.

Implementation location:
- `Assets/MIDI/Scripts/MidiCapabilityNegotiator.cs`

<div class="page" />

## What it is

A capability negotiator listens to incoming SysEx and participates in device-to-device negotiation flows (identity/capability exchange) so applications can discover supported features and negotiate behaviors.

Because MIDI-CI depends heavily on the peer device’s implementation and SysEx support, treat this as an advanced feature.

<div class="page" />

## Practical notes

- MIDI-CI uses SysEx; some platforms/transports restrict SysEx or require special permissions.
- If you rely on MIDI-CI, test on every target platform and transport (USB/BLE/network).

<div class="page" />

## Where to look next

- [MIDI 1.0 (MidiManager)](midi1.md) (SysEx send/receive)
- [Transports & Platform Notes](transports.md)
