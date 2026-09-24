# Embedded Third-Party Modules

This repository contains embedded modules and vendored sources used by transports.

This page helps you find their docs and licenses.

<div class="page" />

## RTP-MIDI-for-.NET (embedded module)

Location:
- `Assets/MIDI/Scripts/RTP-MIDI-for-.NET/`

Includes:
- its own `README.md`, `LICENSE`, and changelog
- an embedded documentation page under its `Documentation~/` folder

Unity integration is provided by:
- `Assets/MIDI/Scripts/MidiPlugin.RtpMidi.cs`

<div class="page" />

## mDNS / DNS-SD vendor dependencies (shared)

Location:
- `Assets/MIDI/Scripts/MdnsVendor/` (`jp.kshoji.mdns.vendor`)

Shared by Network MIDI 2.0 UDP discovery and RTP-MIDI Zeroconf.
If you modify or redistribute the package, ensure you comply with each component’s license.

See also:
- [Contacts / Support](contacts.md) (lists discovery dependencies and versions)

<div class="page" />

## VST3 Plugin Host (not bundled)

This MIDI package does **not** redistribute the VST3 SDK, `VstHostNative.dll`, VST host C#, or third-party `.vst3` plugins.

VST® is a registered trademark of Steinberg Media Technologies GmbH.

For VST3 hosting in Unity, use the separate package [Unity-VST3-Bridge](https://github.com/kshoji/Unity-VST3-Bridge) (`jp.kshoji.unity.vst3nativehost`) and its `NOTICE.md` / documentation. Short integration pointer: [Integrations — VST3 Plugin Host](integrations.md#vst3-plugin-host-separate-package).
