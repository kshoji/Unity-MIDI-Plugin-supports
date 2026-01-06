# トランスポートとプラットフォームの注意点

このページでは、`Assets/MIDI` において MIDI データがプラットフォームやネットワークプロトコルを介してどのように転送（トランスポート）されるかについて説明します。

プラットフォームごとの機能対応表や OS/バージョンの要件については、以下を参照してください:
- [プラットフォームと制限事項](platforms.md)

<div class="page" />

## マネージャーによるトランスポートの選択方法 (重要)

ほとんどのプラットフォームにおいて、`MidiManager`/`Midi2Manager` は **複数のバックエンドを並列に初期化** します（例: プラットフォーム標準の MIDI + RTP-MIDI、および有効な場合は Nearby など）。

実用上の影響:
- **送信のファンアウト (拡散)**: 1 回の `SendMidi...()` 呼び出しが、それをサポートする *初期化済みの各バックエンド* に転送される可能性があります。
- **ターゲットの指定**: 常に、到達させたい特定の `deviceId` に対して送信するようにしてください。同じ論理的なエンドポイントが複数のバックエンドに現れる可能性があるため注意が必要です。
- **デバイスの接続/切断イベントの集約**: アクティブなすべてのバックエンドからのデバイス ID が、マネージャーのデバイスセットに集約されます。

これは設計通りの動作であり、各トランスポートは 1 つの API サーフェスを共有する「プロバイダー」として扱われます。

<div class="page" />

## プラットフォーム固有のネイティブ MIDI (Plugins/ + MidiPlugin.*.cs)

ほとんどのプラットフォームでは、ネイティブプラグイン（または WebGL 用の JS プラグイン）と、`IMidiPlugin` を実装した C# ラッパーを使用します。

場所:
- ネイティブバイナリ: `Assets/MIDI/Plugins/<Platform>/...`
- C# ラッパー: `Assets/MIDI/Scripts/MidiPlugin.<Platform>.cs`

### Windows
- ラッパー: `MidiPlugin.Windows.cs`
- 追加ヘルパー: `Assets/MIDI/Scripts/win32/` (P/Invoke およびポートの抽象化)

### macOS / iOS / Android / Linux
- 各プラットフォーム用のラッパーが存在し、それぞれのネイティブライブラリを呼び出します。

### Android デバイス (Meta Quest を含む)
Android ベースのヘッドセット (Meta Quest など) は同じ Android トランスポートレイヤーを使用しますが、実用的なセットアップ項目がいくつかあります:

- **Meta Quest での USB MIDI** は、ビルド後の処理で USB デバイスインテントフィルタを有効にする必要がある場合があります。
- **Meta Quest での Bluetooth MIDI デバイスのペアリング** は、一般的に Android の Companion Device ワークフロー（オプションの機能フラグ）を介して行われます。

参照:
- [ビルド後の処理とスクリプト定義シンボル](build-postprocessing.md) (Meta Quest USB + `FEATURE_ANDROID_COMPANION_DEVICE`)
- [プラットフォームと制限事項](platforms.md) (Android API レベルに関する注意点)

### WebGL
- ラッパー: `MidiPlugin.WebGL.cs`
- JS ライブラリ: `Assets/MIDI/Plugins/WebGL/*.jslib`

重要な注意点:
- ブラウザの権限と WebMIDI のサポート状況に依存します。
- WebGL では生の UDP/TCP ソケットを使用できないため、**RTP-MIDI** および **UDP MIDI 2.0** は利用できません。
- WebGL は現在、USB MIDI 2.0 デバイスやネットワーク MIDI 2.0 を処理できません。MIDI 2.0 は実質的にファイルワークフロー（MIDI クリップの読み書きなど）に限定されます。
- ブラウザや CORS ルールの制約により、`UnityWebRequest` で外部サーバーのリソースにアクセスできない場合があります。`.mid` などのファイルは `StreamingAssets` に配置してください。

#### WebGL テンプレートの要件: `unityInstance` の公開
一部の WebGL フローでは、JS から Unity ランタイムインスタンスに `unityInstance` としてアクセスできる必要があります。

WebGL テンプレートの `index.html` を修正して、作成されたインスタンスをグローバル変数に代入するようにしてください:
```javascript
var unityInstance = null;
script.onload = () => {
    createUnityInstance(canvas, config, (progress) => {
        progressBarFull.style.width = 100 * progress + "%";
    }).then((unityInst) => {
        unityInstance = unityInst;
    });
};
```

または:
- `Assets/MIDI/Samples/WebGLTemplates` を `Assets/WebGLTemplates` にコピーします。
- その後、以下で `Default-MIDI` または `Minimal-MIDI` を選択します:
  `Project Settings > Player > Resolution and Presentation > WebGL Template`

(Unity マニュアル参照: WebGL テンプレート)

<div class="page" />

## RTP-MIDI (ネットワーク MIDI 1.0)

RTP-MIDI (Apple Network MIDI 形式) は、純粋な .NET 実装を介してサポートされています:

- モジュール: `Assets/MIDI/Scripts/RTP-MIDI-for-.NET/`
- アダプター: `Assets/MIDI/Scripts/MidiPlugin.RtpMidi.cs` (`IMidiPlugin` を実装)

主な機能:
- UDP ポートでリスニングする 1 つ以上の RTP-MIDI サーバーを起動。
- リモートエンドポイントへの接続/切断。
- MIDI 1.0 メッセージの送受信。

制限事項:
- 誤り訂正プロトコル (**RTP MIDI Journaling**) はサポートされていません。

スレッディング:
- 受信した RTP-MIDI イベントは、メインスレッドセーフなディスパッチ機構を使用してマネージャーに転送されます。

<div class="page" />

## UDP MIDI 2.0 (ネットワーク UMP)

UDP MIDI 2.0 サポートは以下によって提供されます:

- `Assets/MIDI/Scripts/Midi2Plugin.Udp.cs`
- 検索用ヘルパー/依存関係: `Assets/MIDI/Scripts/UdpMidi2Discovery/`

(`Midi2Manager` を介して公開される) 機能:
- UDP MIDI 2.0 サーバーの実行。
- LAN 上のサーバーの検索。
- クライアントの接続/切断。
- UMP メッセージの送受信。
- セッションレベルのコマンド（ping/再送/リセット/NAK/終了）の処理。

参照:
- [MIDI 2.0 / UMP (Midi2Manager)](midi2.md)

<div class="page" />

## 「Nearby」トランスポート (Google Nearby Connections)

`nearby/` モジュールと、対応するプラグインラッパーがあります:

- `Assets/MIDI/Scripts/nearby/`
- `Assets/MIDI/Scripts/MidiPlugin.Nearby.cs`

このトランスポートは、同じ MIDI 1.0 API を通じて公開される代替の MIDI リンクレイヤーです。

セットアップに関する注意点:
- Nearby 依存パッケージの追加が必要です（[ビルド設定](build-postprocessing.md) を参照）。
- Android での API レベルの制約があります（[プラットフォームと制限事項](platforms.md) を参照）。

ランタイムに関する注意点:
- 有効な場合、Nearby バックエンドは Unity のプレイヤーループからの定期的な更新を必要とします。トランスポートがコンパイルに含まれている場合、プロジェクトはマネージャーを介してこれを接続します。

<div class="page" />

## ソースコード実装例 (RTP-MIDI トランスポート)

以下を使用してください:

- `Assets/MIDI/Samples/Scripts/DocumentationExamples/RtpMidiTransportExample.cs`

これを GameObject にアタッチして Play モードに入ります。RTP-MIDI サーバーが起動します。
その後、OS やデバイスの RTP-MIDI ルーティングツールを使用して接続してください（操作方法はプラットフォームによって異なります）。