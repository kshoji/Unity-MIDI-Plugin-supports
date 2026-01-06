# MIDI-CI / 機能ネゴシエーション

このプロジェクトには、MIDI Capability Inquiry (MIDI-CI) 形式のワークフローに対する試験的なサポートが含まれています。

実装場所:
- `Assets/MIDI/Scripts/MidiCapabilityNegotiator.cs`

<div class="page" />

## MIDI-CI とは

Capability Negotiator (機能ネゴシエーター) は、受信した SysEx を監視し、デバイス間ネゴシエーションフロー（識別情報の交換や機能の問い合わせ）に参加します。これにより、アプリケーションはサポートされている機能を検出し、動作を交渉（ネゴシエーション）することができます。

MIDI-CI は相手デバイスの実装や SysEx のサポート状況に大きく依存するため、高度な機能として扱ってください。

<div class="page" />

## 実用上の注意点

- MIDI-CI は SysEx を使用します。一部のプラットフォームやトランスポートでは、SysEx に制限があったり、特別な権限が必要な場合があります。
- MIDI-CI に依存する場合は、ターゲットとなるすべてのプラットフォームとトランスポート (USB/BLE/ネットワーク) でテストを行ってください。

<div class="page" />

## 次に読むべきドキュメント

- [MIDI 1.0 (MidiManager)](midi1.md) (SysEx の送受信)
- [トランスポートとプラットフォームの注意点](transports.md)