# プラットフォームと制限事項

## 機能対応表 (プラットフォーム別)

| プラットフォーム | Bluetooth MIDI | USB MIDI | ネットワーク MIDI (RTP-MIDI) | Nearby Connections MIDI | アプリ間 MIDI | USB MIDI 2.0 | ネットワーク MIDI 2.0 (UDP MIDI 2.0) |
| ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- |
| iOS | ○ | ○ | ○ | ○ | ○ | ○ | △ (試験的) |
| Android | ○ | ○ | △ (試験的) | ○ | ○ | ○ | △ (試験的) |
| Universal Windows Platform | - | ○ | △ (試験的) | - | ○ | - | △ (試験的) |
| Standalone macOS / Unity Editor macOS | ○ | ○ | ○ | ○ | ○ | ○ | △ (試験的) |
| Standalone Linux / Unity Editor Linux | ○ | ○ | △ (試験的) | - | ○ | ○ | △ (試験的) |
| Standalone Windows / Unity Editor Windows | - | ○ | △ (試験的) | - | ○ | - | △ (試験的) |
| WebGL | ○ | ○ | - | - | - | - | - |

記号の説明:
- ○ 対応
- △ 対応しているが試験的 / 制限あり
- - 未対応

<div class="page" />

## 制限事項 / 要件

### Android
- USB MIDI: API レベル 12 (Android 3.1) 以上。
- Bluetooth MIDI: API レベル 18 (Android 4.3) 以上。
- アプリ間 MIDI: API レベル 23 (Android 6.0) 以上。
- MIDI 2.0: API レベル 23 (Android 6.0) 以上。
- **Mono** でビルドする場合:
  - レイテンシ（遅延）の問題が発生する可能性があります。
  - `armeabi-v7a` のみをサポートする場合があります。
  - 推奨: **IL2CPP** を使用してください。  
    `Project Settings > Player > Configuration > Scripting Backend`
- Nearby Connections MIDI:
  - API レベル 28 (Android 9) 以上が必要。
  - かつ API レベル 33 (Android 13) 以上でコンパイルされている必要があります。

#### Bluetooth MIDI ペリフェラルモード (Android のみ)
BLE MIDI **セントラル**（デバイスのスキャンと接続）として動作するだけでなく、Android は BLE MIDI **ペリフェラル**（アプリを MIDI デバイスとして公開）としても動作できます。

注意点:
- これは Android 限定の機能です。
- API エントリポイントについては [MIDI 1.0 (MidiManager)](midi1.md) を参照してください。
- プラットフォームの権限と Bluetooth の状態が適用されます（[ビルド設定](build-postprocessing.md) を参照）。

### Meta Quest (Oculus Quest)
Meta Quest デバイスは Android で動作するため、上記の **Android** の要件が適用されます。

実用上の注意点:
- Quest での **USB MIDI** は、Android ビルド後の処理で USB デバイスインテントフィルタを有効にする必要がある場合があります。
  - 参照: [ビルド後の処理とスクリプト定義シンボル](build-postprocessing.md) (Quest USB MIDI のセクション)
- **Bluetooth MIDI** デバイスの検索とペアリングには、Android の Companion Device ワークフローを使用できます。
  - スクリプト定義シンボルを有効化: `FEATURE_ANDROID_COMPANION_DEVICE`
  - 参照: [ビルド後の処理とスクリプト定義シンボル](build-postprocessing.md)

### iOS / macOS
- サポートされている iOS: 12.0 以上。
- Bluetooth MIDI: セントラルモードのみ。

### UWP
- サポートされている UWP ターゲットバージョン: 10.0.10240.0 以上。
- MIDI 2.0 (USB) は現在サポートされていません。
- Bluetooth MIDI はサポートされていません。
- RTP-MIDI を使用するには、以下の Capability を有効にする必要があります:
  - `Project Settings > Player > Capabilities > PrivateNetworkClientServer`

### Windows
- Bluetooth MIDI はサポートされていません。
- MIDI 2.0 (USB) はサポートされていません。

### Linux
- 1 つの USB デバイスに MIDI 1.0 と MIDI 2.0 が共存している場合、MIDI 2.0 ポートのみが検出されることがあります。

### WebGL
- デバイスのサポート状況は OS/ブラウザの WebMIDI 環境に依存します。
- WebGL は生の UDP/TCP ソケットを使用できないため、RTP-MIDI / UDP MIDI 2.0 は利用できません。
- WebGL では、他のサーバーへの `UnityWebRequest` アクセスが制限される場合があります。`.mid` ファイルなどのコンテンツには `StreamingAssets` を使用してください。
- WebGL は USB MIDI 2.0 デバイスやネットワーク MIDI 2.0 を処理できません。MIDI 2.0 ランタイム機能は、クリップファイルの読み書きを除いて利用できません。
- WebGL テンプレートで `unityInstance` を公開する必要がある場合があります（[トランスポートとプラットフォームの注意点](transports.md) を参照）。