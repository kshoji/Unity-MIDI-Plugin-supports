# エディタツール

このページでは、Unity エディタ上で MIDI 開発を支援するツール群について説明します。

| ツール | モード | メニュー |
|--------|--------|----------|
| MIDI モニター | Play 専用 | `Window > MIDI > Monitor` |
| 仮想 MIDI コントローラー | Play 専用 | `Window > MIDI > Virtual Controller` |
| SMF プレビュー | Edit 専用 | `Window > MIDI > SMF Preview` |
| Project Settings | Edit | `Edit > Project Settings > MIDI` |

<div class="page" />

# MIDI モニター

Play モード中に MIDI 入出力メッセージをリアルタイムで一覧表示するエディタウィンドウです。DAW の MIDI モニターに相当する機能を提供し、開発時のデバッグ効率を向上させます。

> 注意: このツールは **Play モード専用** です。Edit モード（非 Play）では MIDI メッセージは表示されません。

### 開き方

```
Window > MIDI > Monitor
```

<div class="page" />

## 表示内容

| 列 | 内容 |
|----|------|
| Time | Play 開始からの相対時刻 (`mm:ss.fff`) |
| Dir | `IN`（入力）/ `OUT`（出力） |
| Device | デバイス ID（省略表示、ツールチップで全文表示） |
| Ch | MIDI チャンネル |
| Type | メッセージ種別 |
| Detail | ノート番号、CC 番号、音名など |

### 初期リリースで表示されるメッセージ

**入力 (IN)**
- Note On / Note Off
- Control Change (CC)
- Program Change (PC)
- デバイス接続 / 切断イベント

**出力 (OUT)**
- Note On / Note Off
- Control Change (CC)
- Program Change (PC)

Note 系メッセージの Detail 列には、`MidiNoteUtility` による音名（例: `C4 (60)`）も表示されます。

<div class="page" />

## ツールバー

| 操作 | 説明 |
|------|------|
| **Clear** | ログをクリア |
| **Export** | フィルタ適用後のログを CSV ファイルにエクスポート |
| **Auto Scroll** | 新しいメッセージが追加されたときに自動スクロール |
| **Max Lines** | 保持する最大行数（デフォルト 1000、最大 10000） |
| **Ch 1-16** | チャンネル表示を 0–15 / 1–16 で切り替え |

<div class="page" />

## フィルタ

| フィルタ | 選択肢 |
|----------|--------|
| Direction | All / IN / OUT |
| Device | 接続中デバイス一覧（All = 全デバイス） |
| Ch | All / 0–15（または 1–16 表示） |
| Type | All / NoteOn / NoteOff / CC / PC / Device |
| Search | Detail 列・Device 列の部分一致検索 |

フィルタ設定は `EditorPrefs` に保存され、次回ウィンドウを開いた際も維持されます。

<div class="page" />

## 動作の仕組み

### 入力メッセージの捕捉

Play モード開始時に `[MIDI Monitor Proxy]` GameObject が自動生成され、`MidiManager` にイベントハンドラとして登録されます。受信した MIDI イベントはモニターのログバッファに追加されます。

### 出力メッセージの捕捉

`MidiManager` の送信メソッド（Note On/Off、CC、PC）実行時に、エディタ専用フック（`#if UNITY_EDITOR`）経由で OUT ログが記録されます。`MidiSend`（Fluent API）からの送信も同様に表示されます。

### Play モード終了時

Proxy GameObject は自動的に破棄され、`MidiManager` からの登録も解除されます。ログバッファの内容は Play 終了後もウィンドウ上に残ります（次回 Play 時に Clear 可能）。

<div class="page" />

## 推奨される使い方

1. `Window > MIDI > Monitor` を開く
2. サンプルシーン（`Assets/MIDI/Samples/Scenes/`）を Play
3. MIDI デバイスを接続するか、スクリプトから `MidiSend.To(...).NoteOn(...)` で送信
4. IN / OUT ログを確認

送信テストの例:

```csharp
using jp.kshoji.unity.midi.util;

// Play 中に実行
MidiSend.ToFirstOutput().Channel(0).NoteOn(60, 127);
```

<div class="page" />

# 仮想 MIDI コントローラー

物理 MIDI デバイスなしで、エディタ上から Note / CC / Program Change / Pitch Bend を送信できるシミュレータです。

### 開き方

```
Window > MIDI > Virtual Controller
```

### 機能

- 2 オクターブ鍵盤（C3–B4）
- 16 個の CC スライダー（CC 番号は EditorPrefs に保存）
- チャンネル (1–16) / グループ / ベロシティ設定
- 出力デバイス選択（未接続時は `editor:virtual-controller` 仮想出力を自動作成）
- All Notes Off / Panic (CC 120 + 123)

### 使い方

1. サンプルシーン等で `MidiManager.InitializeMidi()` を呼び出すシーンを Play
2. `Window > MIDI > Virtual Controller` を開く
3. 鍵盤または CC スライダーを操作
4. `Window > MIDI > Monitor` で OUT メッセージを確認

<div class="page" />

# SMF プレビュー / インポート

`.mid` ファイルを Edit モードで読み込み、トラック・イベント一覧を確認し、`MidiSequenceAsset` へエクスポートできます。

### 開き方

```
Window > MIDI > SMF Preview
Assets > Import MIDI File...
```

### 機能

- `.mid` ファイルのドラッグ＆ドロップ読み込み
- トラック一覧 / イベント一覧（Tick、時刻、種別、Detail）
- テンポ・拍子・トラック名などのメタ情報表示
- テキストベースのノート一覧（Note Roll）
- `MidiSequenceAsset` / JSON エクスポート

エクスポートした `MidiSequenceAsset` は [SMF ツール](smf-tools.md) の `SmfPlayer` で再生できます。

<div class="page" />

# Project Settings

MIDI Plugin のグローバル設定を `Edit > Project Settings > MIDI` から管理します。

### 設定項目

| セクション | 項目 |
|------------|------|
| Devices | `defaultInputDeviceId` / `defaultOutputDeviceId`（空 = 自動選択） |
| Bluetooth MIDI | `autoScanBleOnInit`、`bleScanTimeoutMs`（0 = 無制限） |
| RTP-MIDI | `rtpMidiPort`（既定 5004）、`rtpMidiSessionName` |
| Debug | `logLevel`（None / Error / Warning / Info / Verbose）、`enableMidiMonitorOnPlay` |
| Development | `developmentBuildOnlyVerboseLog` |

初回インポート時に `Assets/MIDI/Resources/MidiProjectSettings.asset` が自動生成されます。`MidiManager.InitializeMidi()` 完了時に BLE 自動スキャンおよび RTP-MIDI サーバー起動設定が反映されます。

<div class="page" />

## 将来のエディタ機能（未実装）

| 機能 | 概要 |
|------|------|
| デバイスブラウザ | `Window > MIDI > Device Browser` — デバイス一覧、Vendor/Product ID、テスト送信、ID コピー |
| Scene View デバッグオーバーレイ | Play 中にシーン上へ MIDI 状態を表示 |
| レイテンシ計測（RTT ツール） | エディタ送信→受信の往復 UI（BLE / RTP-MIDI 評価向け）。ランタイムのタップ校正は Foundation の `MidiLatencyCalibrator` で既に利用可能 |

<div class="page" />

## ソースファイル

| ファイル | 役割 |
|----------|------|
| `MidiMonitorWindow.cs` 等 | MIDI モニター |
| `VirtualMidiControllerWindow.cs` | 仮想コントローラー |
| `SmfPreviewWindow.cs` / `SmfImportUtility.cs` | SMF プレビュー |
| `MidiProjectSettings.cs` | 設定 ScriptableObject |
| `MidiProjectSettingsProvider.cs` | Project Settings UI |
| `MidiManager.ProjectSettings.cs` | ランタイム連携 |

<div class="page" />

## 関連ドキュメント

- [SMF ツール (SmfPlayer / MidiRecorder)](smf-tools.md)
- [ユーティリティ (MidiNoteUtility / MidiMessageBuilder)](utilities.md)
- [エディタとライフサイクルに関する注意点](editor-and-lifecycle.md)
- [スタートガイド](getting-started.md)
- [サンプル](samples.md)
