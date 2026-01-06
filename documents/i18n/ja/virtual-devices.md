# 仮想デバイスとイベント注入

このプロジェクトは、ハードウェアやネットワークデバイスと同じデバイスリストおよびイベントパイプラインに参加する「仮想（ソフトウェア）」MIDI デバイスをサポートしています。

これはオプションの統合機能（例: Maestro / MPTK）で使用されていますが、以下のような用途にも役立ちます:
- 自動テスト
- シミュレーションツール
- 他の入力ソースの `MidiManager` への橋渡し
- ソフトウェア上の「ループバック」デバイスの作成

実装場所:
- `Assets/MIDI/Scripts/MidiManager.VirtualDevices.cs`

<div class="page" />

## 仮想デバイスとは

**仮想デバイス**は `deviceId` 文字列（命名規則は自由です）によって識別されます。登録後は以下のリストに表示されるようになります:

- `MidiManager.Instance.DeviceIdSet`
- `MidiManager.Instance.InputDeviceIdSet`
- `MidiManager.Instance.OutputDeviceIdSet`

また、実際のデバイスと同様に、接続/切断のコールバックが実行されます。

<div class="page" />

## 登録 / 解除

仮想デバイスを入力用、出力用、またはその両方として登録します:

- `RegisterVirtualMidiDevice(deviceId, input: true/false, output: true/false)`
- `UnregisterVirtualMidiDevice(deviceId)`

推奨事項:
- `virtual:...` や `mptk:...` のような分かりやすいプレフィックスを使用してください。
- 意図的に複数のグループをモデル化する場合を除き、`group = 0` を使用してください。

<div class="page" />

## イベントの注入 (「デバイスが送信したことにする」)

仮想デバイスは、マネージャーの内部パイプラインに MIDI メッセージを注入できます:

- ノート・オン/オフ
- CC、プログラム・チェンジ
- アフタータッチ、ピッチホイール
- SysEx
- システム・コモン / リアルタイム (該当する場合)

重要な注意点:
- **注入によってハードウェアやバックエンドに送信されることはありません。**
- 注入は、あたかも `deviceId` から届いたかのようにソフトウェア生成の入力を扱うためのものです。

「仮想入力ソースとしての MPTK」はこの仕組みで動作しています。

参照:
- [Maestro / MPTK 統合](mptk.md)