# MPE (MIDI Polyphonic Expression)

このページでは、以下のコンポーネントによって提供される MPE サポートについて説明します:

- `Assets/MIDI/Scripts/MpeManager.cs`
- `Assets/MIDI/Scripts/IMpeEventHandler.cs`

MPE は、MIDI 1.0 形式のメッセージおよび `MidiManager` の上のレイヤーとして実装されています。

<div class="page" />

## このレイヤーの役割

### 入力 (MPE → 統合コールバック)
- マネージャーチャンネル（ロワーゾーンは 0、アッパーゾーンは 15）のコントロール・チェンジを介して送信される **MPE 設定メッセージ (MCM)** を監視します。
- **ロワー/アッパーゾーン**、メンバーチャンネル、およびゾーンの状態に関する内部モデルを構築します。
- メンバーチャンネルでイベントが発生すると、それを **MPE イベント** として MPE 専用のハンドラインターフェースに転送し、**ゾーンマネージャーチャンネル** をゾーン識別子として報告します。

### 出力 (統合呼び出し → MPE チャンネル割り当て)
- `SendMpeNoteOn`/`SendMpeNoteOff` などのメソッドを提供し、以下の処理を行います:
  - ノートごとに適切なメンバーチャンネルを選択。
  - 必要に応じて、最近使用したチャンネルを再利用。
  - チャンネルごとのアクティブなノートを追跡。
  - 新しいノートを演奏する前に、ゾーン全体のパラメータを適用。

<div class="page" />

## ゾーンとチャンネル (概念)

- **ロワーゾーン**: マネージャーチャンネル = 0、メンバーチャンネル = 1..N
- **アッパーゾーン**: マネージャーチャンネル = 15、メンバーチャンネル = (15-N)..14

デバイスは片方または両方のゾーンを設定できますが、両方のゾーンを合わせたメンバーチャンネルの合計は MIDI チャンネルの範囲内に収まる必要があります。

<div class="page" />

## 出力 API: MpeManager

`MpeManager` はシングルトン形式のマネージャー (`MpeManager.Instance`) で、以下を提供します:

### ゾーンの設定
- `SetupMpeZone(deviceId, managerChannel, memberChannelCount)`

このメソッドは以下の処理を行います:
- `managerChannel` が `0` または `15` であることを検証。
- `memberChannelCount` を 0..15 の範囲に制限。
- 内部のゾーン状態を更新。
- `MidiManager` を介して MPE 設定用の RPN/CC シーケンスをデバイスに送信。

### MIDI モードの変更
- `ChangeMidiMode(deviceId, channel, mode)`

サポートされているモード:
- `3`: Omni Off, Poly
- `4`: Omni Off, Mono

(これら以外の値は無視されます。)

### イベントの送信
- `SendMpeNoteOn(deviceId, channel, note, velocity)`
- `SendMpeNoteOff(deviceId, channel, note, velocity)`
- `SendMpePolyphonicAftertouch(deviceId, channel, note, pressure)`
- `SendMpeControlChange(deviceId, channel, function, value)`
- `SendMpeProgramChange(deviceId, channel, program)`
- `SendMpeChannelAftertouch(deviceId, channel, pressure)`
- `SendMpePitchWheel(deviceId, channel, amount)`
- その他、SysEx やシステムメッセージ用の便利なラッパー。

#### チャンネル選択の挙動 (概要)
対象デバイスで MPE が有効な場合:

- ノートイベントは、選択された **メンバーチャンネル** にルーティングされます。
- 同じノートが既にいずれかのメンバーチャンネルで演奏されている場合、再演奏の前にノート・オフを送信して停止させることがあります。
- ノートを開始する前に、ゾーン全体のパラメータが選択されたチャンネルに適用されます:
  - コントローラー
  - ポリフォニック・アフタータッチのキャッシュ
  - プログラム
  - チャンネル・アフタータッチ
  - ピッチ

MPE が設定されていない場合、MpeManager は指定されたチャンネルに直接送信する動作にフォールバックします。

<div class="page" />

## 入力 API: MPE イベントハンドラ

ゾーン設定が既知の場合、受信した MIDI 1.0 メッセージは MPE として解釈されます。

代表的な MPE ハンドラインターフェースは以下の通りです:

- `IMpeNoteOnEventHandler`
- `IMpeNoteOffEventHandler`
- `IMpeControlChangeEventHandler`
- `IMpePitchWheelEventHandler`
- `IMpeZoneDefinedEventHandler`
- など。

入力レイヤーは以下を報告します:
- `deviceId`
- **ゾーンマネージャーチャンネル**
- イベントパラメータ (ノート、ベロシティ、コントローラー/値など)

<div class="page" />

## 実用的な使用パターン

1. 出力デバイスを MPE 用に設定します:
- `MpeManager.Instance.SetupMpeZone(...)` を呼び出します。
2. `MidiManager.SendMidiNoteOn/Off` ではなく `SendMpeNoteOn/Off` を使用してノートを送信します。
3. デバイスの設定が動的に変更される場合に対応するため、`IMpeZoneDefinedEventHandler` を実装してゾーンの変更に反応するようにします。

<div class="page" />

## ソースコード実装例 (MPE 出力)

以下を使用してください:

- `Assets/MIDI/Samples/Scripts/DocumentationExamples/MpeOutputExample.cs`

これを GameObject にアタッチして Play モードに入ります。
以下の動作が行われます:
- MIDI 1.0 の初期化。
- ロワーゾーンの MPE レイアウトを設定。
- MPE チャンネル割り当てを使用してテストノートを送信。