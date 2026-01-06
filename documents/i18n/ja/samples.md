# サンプル

このページでは、以下のディレクトリに含まれるサンプルコンテンツについて説明します:

- `Assets/MIDI/Samples/`

<div class="page" />

## シーン

場所: `Assets/MIDI/Samples/Scenes/`

- `MidiSampleScene.unity`  
  `MidiManager` と MIDI 1.0 イベントハンドラインターフェースを使用した、MIDI 1.0 の送受信ワークフローを示します。  
  有効な場合、出力を MPTK バックエンドの仮想出力デバイスにルーティングするトグルが含まれています。

- `Midi2SampleScene.unity`  
  `Midi2Manager` と MIDI 2.0 ハンドラインターフェースを使用した、MIDI 2.0 / UMP ワークフローを示します。  
  有効な場合、MIDI 2.0 の送信内容を MPTK MIDI 1.0 仮想シンクにミラーリングするトグルが含まれています。

<div class="page" />

## スクリプト

場所: `Assets/MIDI/Samples/Scripts/`

- `MidiSampleScene.cs`  
  デモにおける主な役割:
  - MIDI の初期化
  - ハンドラオブジェクトの登録
  - 受信メッセージへの反応（例: ノートオン/オフのログ出力）
  - テストメッセージの送信
  - (オプション) 仮想デバイスを介した MPTK への出力ルーティング

- `Midi2SampleScene.cs`  
  デモにおける主な役割:
  - MIDI 2.0 の初期化
  - UMP ハンドラの登録
  - デコードされたイベントの表示
  - UMP メッセージの送信例
  - (オプション) MIDI 1.0 仮想シンクを介した MPTK への送信ミラーリング

- `FileUtils.cs`, `AudioClipUtils.cs`  
  サンプルで使用されるユーティリティスクリプト（ファイル操作、オーディオクリップヘルパー）。

<div class="page" />

## WebGL テンプレート

場所: `Assets/MIDI/Samples/WebGLTemplates/`

MIDI を有効にした WebGL 配信を想定した、WebGL ビルドテンプレートが含まれています。WebGL MIDI や BLE MIDI フローにおいて、一貫した HTML/JS の土台が必要な場合に使用してください。

<div class="page" />

## 推奨されるクイックテスト手順

1. いずれかのサンプルシーンを開きます。
2. Play モードに入ります。
3. MIDI デバイスを接続します（または RTP-MIDI / UDP MIDI 2.0 などのネットワークトランスポートを使用します）。
4. 以下を確認します:
- デバイス接続イベントが表示されること。
- ノートや CC イベントが受信されること。
- 送信によって対象デバイスで出力が行われること。

サンプルでイベントが受信されない場合:
- 正しいプラットフォームバックエンドが使用されているか確認してください。
- トランスポートの要件を確認してください（WebGL の権限、ネットワークトランスポートのファイアウォールなど）。

<div class="page" />

## ドキュメントで参照されているソースコード実装例

ドキュメントの各ページでは、以下の「最小限で具体的な」スクリプトを参照しています:

- `Assets/MIDI/Samples/DocumentationExamples/Midi1QuickStartExample.cs`
- `Assets/MIDI/Samples/DocumentationExamples/Midi2QuickStartExample.cs`
- `Assets/MIDI/Samples/DocumentationExamples/MpeOutputExample.cs`
- `Assets/MIDI/Samples/DocumentationExamples/SmfPlaybackExample.cs`
- `Assets/MIDI/Samples/DocumentationExamples/RtpMidiTransportExample.cs`

MPTK 関連の例:

- `Assets/MIDI/Samples/DocumentationExamples/MptkVirtualOutputSinkExample.cs`
- `Assets/MIDI/Samples/DocumentationExamples/MptkToMidiManagerInputExample.cs`

Maestro / MPTK を使用している場合は、以下も参照してください:
- [Maestro / MPTK 統合](mptk.md)