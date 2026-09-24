# エディタとライフサイクルに関する注意点

このページでは、エディタ上とビルド後で MIDI の動作が異なって見える原因となる、Unity 特有の挙動について説明します。

<div class="page" />

## マネージャーの生存期間と `DontDestroyOnLoad`

`MidiManager` は、Play モード中に `MidiManager.Instance` に初めてアクセスした際、`DontDestroyOnLoad` が設定された GameObject として作成されます。

実用上の影響:
- 通常、プレイセッションごとに 1 回初期化を行います。
- Unity が再生中でないときに、エディタツールからマネージャーを呼び出さないでください。

<div class="page" />

## Unity エディタの Play モードの切り替え

このプロジェクトにはエディタ用フックが含まれており、Play モードの開始/終了に合わせてトランスポートがクリーンに開始/停止するようになっています。

もし以下のような現象が発生する場合:
- Play モード停止後も MIDI が届き続ける
- デバイスが適切に閉じられない

以下の点を確認してください:
- 終了時に MIDI 終了処理 (`TerminateMidi` / `TerminateMidi2`) を呼び出しているか。
- スクリプトがプレイセッションを越えて静的参照（static reference）を保持し続けていないか。

<div class="page" />

## メインスレッドへのディスパッチ

一部のトランスポート（ネットワークトランスポートなど）はバックグラウンドスレッドでメッセージを受信します。受信したイベントは、メインスレッドセーフな方法で Unity に転送されます。

実用上の影響:
- イベントハンドラのコールバックは、Unity のメインスレッドで実行される前提で実装してください。
- コールバック内での重い処理は避け、必要に応じて処理をキューイングしてください。

<div class="page" />

## EventSystem に関する注意

シーン内に既に Unity の `EventSystem` が存在する場合、重複して作成しないようにしてください。
重複の警告やエラーが出る場合は、適宜初期化処理を調整してください（[スタートガイド](getting-started.md) を参照）。

<div class="page" />

## MIDI モニター (エディタツール)

Unity エディタには、Play モード中の MIDI 入出力をリアルタイム表示する **MIDI モニター** が含まれています。

```
Window > MIDI > Monitor
```

### 動作

- Play モード開始時に `[MIDI Monitor Proxy]` が自動生成され、`MidiManager` にイベントハンドラとして登録されます。
- Play モード終了時に Proxy は自動破棄されます。
- 出力メッセージは `MidiManager` のエディタ専用フック（`#if UNITY_EDITOR`）経由で捕捉されます。

### 注意点

- モニターは Play モード専用です。Edit モードではメッセージは表示されません。
- Proxy の生成は `MidiManager.InitializeMidi()` とは独立しています。MIDI 初期化前でも Proxy は登録されますが、メッセージを受信するには通常どおり `InitializeMidi()` が必要です。
- ログバッファは Play 終了後もウィンドウ上に残ります。次回 Play 時に **Clear** で消去できます。

詳細は [エディタツール (MIDI モニター)](editor-tools.md) を参照してください。