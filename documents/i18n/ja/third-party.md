# 組み込み済みサードパーティモジュール

このリポジトリには、トランスポートで使用される組み込みモジュールや外部ソースが含まれています。

このページでは、それらのドキュメントやライセンスの場所を案内します。

<div class="page" />

## RTP-MIDI-for-.NET (組み込みモジュール)

場所:
- `Assets/MIDI/Scripts/RTP-MIDI-for-.NET/`

内容:
- 独自の `README.md`、`LICENSE`、および更新履歴（changelog）が含まれています。
- `Documentation~/` フォルダ内にドキュメントページが含まれています。

Unity への統合は以下によって提供されます:
- `Assets/MIDI/Scripts/MidiPlugin.RtpMidi.cs`

<div class="page" />

## mDNS / DNS-SD ベンダー依存関係（共有）

場所:
- `Assets/MIDI/Scripts/MdnsVendor/`（`jp.kshoji.mdns.vendor`）

Network MIDI 2.0 UDP ディスカバリと RTP-MIDI Zeroconf で共有します。
パッケージを修正または再配布する場合は、各コンポーネントのライセンスに従ってください。

参照:
- [連絡先 / サポート](contacts.md) (検索用依存関係の一覧とバージョンを記載しています)

<div class="page" />

## VST3 プラグインホスト（非同梱）

本 MIDI パッケージは VST3 SDK・`VstHostNative.dll`・VST ホスト C#・第三者 `.vst3` を **再配布しません**。

VST® は Steinberg Media Technologies GmbH の登録商標です。

Unity 上で VST3 をホストする場合は、別パッケージ [Unity-VST3-Bridge](https://github.com/kshoji/Unity-VST3-Bridge)（`jp.kshoji.unity.vst3nativehost`）とその `NOTICE.md`／文書を参照してください。短い案内: [統合 — VST3 プラグインホスト](integrations.md#vst3-プラグインホスト別パッケージ)。
