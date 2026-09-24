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

- `SampleMenuScene.unity`  
  他のサンプルシーンを一覧表示して開くハブシーン（`Window > MIDI > Samples/Open Sample Menu Scene`）。

### Unity エコシステム統合

場所: `Assets/MIDI/Samples/Integrations/Scenes/`

- `MidiAnimatorIntegrationSampleScene.unity`  
  `MidiAnimatorDriver` / `MidiBlendTreeDriver` による MIDI → Animator パラメータ駆動を示します。  
  CC1 でキューブの高さ、CC10/11 で回転、Note 60 でパルス演出が変化します。

- `MidiTimelineIntegrationSampleScene.unity`  
  `PlayableDirector` + `SmfPlayer` + `MidiPlaybackTrack` による Timeline 同期 SMF 再生を示します。  
  Play モード中に IMGUI から Timeline の Play / Pause / Seek を操作できます。

- `MidiVisualScriptingIntegrationSampleScene.unity`  
  `MidiVisualScriptingBridge` と Event Bus リスナーによる Visual Scripting 連携を示します。  
  Script Graph に **Events > MIDI** ノードを追加して同じ GameObject 上で拡張できます。

### ゲームプレイ

場所: `Assets/MIDI/Samples/Gameplay/`

- `Scenes/MidiGameplaySampleScene.unity`  
  ゲームプレイ向けコンポーネント（`MidiDeviceFilter` → `MidiChannelFilter` → `MidiInputRouter` / `MidiNoteTracker`）の配線と動作を示します。  
  Play モード中に IMGUI パネルから仮想デバイス経由の Note / CC / MIDI Start / SysEx シミュレーションが可能です。実機 MIDI コントローラーでも同様に動作します。

- `Scenes/MidiClockSyncSampleScene.unity`  
  外部 MIDI Clock 注入、`MidiClockSync` BPM 推定、`SmfPlayerClockAdapter` デモ。

- `Scenes/ChordScaleSampleScene.unity`  
  `ChordRecognition` / `MidiChordDetector` デモ。

- `Scenes/ChordPuzzleSampleScene.unity`  
  目標和音クイズ（`MidiChordDetector.targetChord`）デモ。

- `Scenes/ScalePracticeSampleScene.unity`  
  複数スケールタイプの練習デモ。

### ネットワーク / Input System / Chunity

場所: `Assets/MIDI/Samples/Integrations/`

- `Networking/Scenes/MidiNetworkJamSampleScene.unity`  
  UDP ベースの `MidiNetworkHub` / `MidiNetworkClient` デモ（`FEATURE_MIDI_NETWORK` 必須）。

- `Networking/Scenes/MidiMirrorNetworkSampleScene.unity`  
  Mirror Host/Client MIDI（`FEATURE_MIRROR` + Mirror 導入）。

- `Networking/Scenes/MidiNetcodeNetworkSampleScene.unity`  
  Netcode Host/Client MIDI（`FEATURE_NETCODE` + NGO 導入）。

- `Networking/Scenes/MidiWsnet2NetworkSampleScene.unity`  
  WSNet2 Create/Join MIDI（`FEATURE_WSNET2` + クライアント + サーバ起動）。

- `InputSystem/Scenes/InputSystemBridgeSampleScene.unity`  
  `MidiInputSystemBridge` + Synthetic Device デモ（`FEATURE_INPUT_SYSTEM` 必須）。

- `Chunity/Scenes/ChunityBridgeSampleScene.unity`  
  `MidiChuckBridge` + `MidiChuckPatchHost` デモ（外部 Chunity + `FEATURE_CHUNITY` 必須）。

- `Chunity/Scenes/ChunityWorkflowsSampleScene.unity`  
  Poly / SMF / Clock / Event→MIDI の各ワークフローデモ（`FEATURE_CHUNITY` 必須）。

- `Chunity/Scenes/ChunityPresetsSampleScene.unity`  
  `MidiChuckPreset` / `MidiChuckParameterBinder` と Filter / Gain 等のデモ（`FEATURE_CHUNITY` 必須。Timeline は `FEATURE_USE_TIMELINE` 任意）。

- `Chunity/Scenes/ChunityMicFxSampleScene.unity`  
  マイク adc + PitShift FX、CC1（Mod Wheel）でピッチシフト量を制御するデモ（`FEATURE_CHUNITY` 必須。Phase E）。

- `Chunity/Scenes/ChunityGeneratorWorkflowSampleScene.unity`  
  Main + Sub Generator + Bridge デモ（`FEATURE_CHUNITY_SCRIPTABLE_AUDIO` 必須）。

### Scriptable Audio

場所: `Assets/MIDI/Samples/Integrations/ScriptableAudio/`

- `Scenes/ScriptableAudioMetronomeSampleScene.unity`  
  メトロノーム / DSP ブートストラップデモ（`FEATURE_SCRIPTABLE_AUDIO`、Unity 6000.3+）。

- `Scenes/ScriptableAudioSequenceSampleScene.unity`  
  SMF シーケンス → DSP スケジュールデモ。

- `Scenes/ScriptableAudioUmpSequenceSampleScene.unity`  
  UMP シーケンス → DSP スケジュールデモ。

### Foundation

場所: `Assets/MIDI/Samples/Foundation/`

- `Scenes/FoundationSampleScene.unity`  
  デバイス選択・レイテンシ校正・Foundation UI シェルのデモ。

### Maestro / MPTK

場所: `Assets/MIDI/Samples/MPTK/`

- `Scenes/MptkIntegrationSampleScene.unity`  
  Maestro / MPTK 統合ハブ（`FEATURE_USE_MPTK` 必須。Pro 機能は `MPTK_PRO` が必要）。

### MIDI Tracker（別サンプル）

場所: `Assets/MIDITracker/`（`Assets/MIDI/Samples/` の外。コアプラグインに一方向依存）

- `Scenes/MidiTrackerSampleScene.unity`  
  パターン指向の MIDI トラッカーショーケース（編集、アレンジ、録音、SMF I/O、マルチプラットフォーム UX）。  
  [MIDI Tracker README](../../../../MIDITracker/README.md) および [MIDI Tracker 開発計画](../../../../MIDITracker/DEVELOPMENT_PLAN.md) を参照してください。

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

### Unity エコシステム統合

場所: `Assets/MIDI/Samples/Integrations/Scripts/`

- `MidiAnimatorIntegrationSampleScene.cs`  
  `MidiAnimatorDriver` / `MidiBlendTreeDriver` のランタイム構築、仮想 MIDI 入力、IMGUI 操作パネル

- `MidiTimelineIntegrationSampleScene.cs`  
  `TimelineAsset` / `MidiPlaybackTrack` / `SmfPlayer` のランタイム構築、Timeline 操作 UI

- `MidiVisualScriptingIntegrationSampleScene.cs`  
  `MidiVisualScriptingBridge` + `MidiVisualScriptingSampleFeedback` による Event Bus デモ

- `MidiIntegrationSampleFactory.cs`  
  デモ用 SMF アルペジオ生成、仮想デバイス登録の共通ヘルパー

### ゲームプレイ

場所: `Assets/MIDI/Samples/Gameplay/Scripts/`

- `MidiGameplaySampleScene.cs`  
  デモにおける主な役割:
  - フィルタチェーン（DeviceFilter → ChannelFilter）のランタイム構築
  - `MidiInputMap` / `MidiInputRouter` による NoteOn・CC・MIDI Start・SysEx バインディング
  - `MidiNoteTracker` による押下ノート追跡と和音検出
  - 仮想入力デバイス（`virtual:gameplay-sample`）への Inject によるハードウェアなしテスト
  - IMGUI によるフィルタ設定・イベントログ表示
- `MidiClockSyncSampleScene.cs` — Clock 同期デモ
- `ChordScaleSampleScene.cs` — 和音・スケール判定デモ
- `GameplaySampleFactory.cs` — 仮想 MIDI / Clock 注入ヘルパー

### ネットワーク / Input System

場所: `Assets/MIDI/Samples/Integrations/`

- `Networking/Scripts/MidiNetworkJamSampleScene.cs` — LAN MIDI 同期デモ（`#if FEATURE_MIDI_NETWORK`）
- `InputSystem/Scripts/InputSystemBridgeSampleScene.cs` — Input System ブリッジ（`#if FEATURE_INPUT_SYSTEM`）

### Foundation

場所: `Assets/MIDI/Samples/Foundation/Scripts/`

- `FoundationSampleScene.cs` — Foundation デモ

- `FileUtility.cs`, `AudioClipUtility.cs`（`Assets/MIDI/Samples/Scripts/` 配下）  
  サンプルで使用されるユーティリティスクリプト（ファイル操作、オーディオクリップヘルパー）。

<div class="page" />

## WebGL テンプレート

場所: `Assets/MIDI/Samples/WebGLTemplates/`

MIDI を有効にした WebGL 配信を想定した、WebGL ビルドテンプレートが含まれています。WebGL MIDI や BLE MIDI フローにおいて、一貫した HTML/JS の土台が必要な場合に使用してください。

<div class="page" />

## 推奨されるクイックテスト手順

### MIDI 1.0 基本（MidiSampleScene）

1. `MidiSampleScene.unity` を開きます。
2. `Window > MIDI > Monitor` を開きます（[エディタツール (MIDI モニター)](editor-tools.md)）。
3. Play モードに入ります。
4. MIDI デバイスを接続します（または RTP-MIDI / UDP MIDI 2.0 などのネットワークトランスポートを使用します）。
5. 以下を確認します:
- デバイス接続イベントが表示されること。
- ノートや CC イベントが受信されること（モニターの IN 列）。
- 送信によって対象デバイスで出力が行われること（モニターの OUT 列）。

### ゲームプレイ向けコンポーネント（MidiGameplaySampleScene）

1. `MidiGameplaySampleScene.unity` を開きます。
2. Play モードに入ります。
3. 画面上の IMGUI パネルで以下を試します:
   - **NoteOn C4 (ch0)** → Router の Launch バインディングが発火
   - **CC64 = 127 (ch0)** → Toggle バインディングが発火
   - **NoteOn C4 (ch1)** → ChannelFilter によりブロック（Router / Tracker に届かない）
   - **Add D4 + E4 (ch0)** → NoteTracker で 3 ノート和音検出
4. 実機 MIDI コントローラーを接続した場合も、ch0 の C4 / CC64 で同様に動作します。
5. フィルタチェーンの詳細は [ゲームプレイ向けコンポーネント](gameplay.md) を参照してください。

### Animator 統合（MidiAnimatorIntegrationSampleScene）

1. `MidiAnimatorIntegrationSampleScene.unity` を開きます。
2. Play モードに入ります。
3. IMGUI パネルで **CC1 = 127** → キューブが上昇することを確認します。
4. **Note 60** → パルス演出（スケール変化）を確認します。
5. **Program Change 5** → `Program` Int パラメータ更新と色相変化を確認します。
6. **MIDI Start** → `TransportStart` Trigger とパルス演出を確認します。
7. **SysEx** → Event Log に `onMessage SysEx` が表示されることを確認します（Animator パラメータは更新されません）。
8. **CC10 / CC11** → キューブが回転することを確認します。
9. 詳細は [Unity エコシステム統合](integrations.md) を参照してください。

### Timeline 統合（MidiTimelineIntegrationSampleScene）

1. `com.unity.timeline` パッケージがインストールされていることを確認します。
2. Project Settings に `FEATURE_USE_TIMELINE` が追加されていることを確認します。
3. `MidiTimelineIntegrationSampleScene.unity` を開きます。
3. Play モードに入り、**Play Timeline** をクリックします。
4. Director time と SmfPlayer time が同期することを確認します。
5. **Pause** / **Seek to 1.0 s** で SmfPlayer が追従することを確認します。

### Visual Scripting 統合（MidiVisualScriptingIntegrationSampleScene）

1. `com.unity.visualscripting` パッケージがインストールされていることを確認します。
2. Project Settings に `FEATURE_USE_VISUALSCRIPTING` が追加されていることを確認します。
3. `MidiVisualScriptingIntegrationSampleScene.unity` を開きます。
3. Play モードに入り、**NoteOn C4** / **CC1 = 127** でキューブの色が変化することを確認します。
4. 同 GameObject に Script Machine を追加し、**Events > MIDI** ノードでグラフを拡張できます。

### ネットワーク MIDI（MidiNetworkJamSampleScene）

1. Project Settings に `FEATURE_MIDI_NETWORK` を追加します。
2. `MidiNetworkJamSampleScene.unity` を開きます。
3. Play モードで Hub / Client のブロードキャスト操作を試します。

### ネットワーク MIDI — Mirror / Netcode / WSNet2

1. フレームワーク導入とシンボル設定（[統合 — Networking](integrations.md) / Networking README）。
2. `MidiMirrorNetworkSampleScene` / `MidiNetcodeNetworkSampleScene` / `MidiWsnet2NetworkSampleScene` を開く。
3. Mirror/Netcode: **Start Host** → 別インスタンスで **Client**。WSNet2: **Create Room** → **Join by Room#**（サーバ起動必須）。
4. Note / CC / Seek で同期を確認。

### MidiClockSync / 和音・スケール（Gameplay サンプル）

1. `MidiClockSyncSampleScene.unity` を開き、Play モードで Clock 注入と BPM 推定を確認します。
2. `ChordScaleSampleScene.unity` / `ChordPuzzleSampleScene.unity` / `ScalePracticeSampleScene.unity` で和音・スケール判定を確認します。
3. 詳細は [ジャンルキット](kits.md) および [ゲームプレイ向けコンポーネント](gameplay.md) を参照してください。

### Input System ブリッジ（InputSystemBridgeSampleScene）

1. Project Settings に `FEATURE_INPUT_SYSTEM` を追加します（`com.unity.inputsystem` が必要）。
2. `InputSystemBridgeSampleScene.unity` を開きます。
3. Play モードで Synthetic Device と双方向ブリッジを確認します。

### Chunity ブリッジ（ChunityBridgeSampleScene）

1. Chunity をインストールし、`Chunity.Runtime.asmdef.example` を Scripts ルートへ `Chunity.Runtime.asmdef` として配置します。
2. Project Settings に `FEATURE_CHUNITY` を追加します。
3. `Assets/MIDI/Samples/Integrations/Chunity/Scenes/ChunityBridgeSampleScene.unity` を開きます。
4. Play モードで Note On / Off ボタンから単音 SinOsc が鳴ることを確認します。

### Chunity ワークフロー（ChunityWorkflowsSampleScene）

1. 上記と同じ有効化手順を行います。
2. `ChunityWorkflowsSampleScene.unity` を開きます。
3. タブで Poly / SMF / Clock / Event / Bank / Array / Adv（HostAdvancer）/ Life（PatchLifecycle）を切り替え、各デモを確認します。

### Chunity プリセット（ChunityPresetsSampleScene）

1. 上記と同じ有効化手順を行います。
2. `Assets/MIDI/Samples/Integrations/Chunity/Scenes/ChunityPresetsSampleScene.unity` を開きます。
3. Soft / Bright / Pad プリセット切替と Filter / Gain などのスライダー、Note On を確認します。
4. （任意）`FEATURE_USE_TIMELINE` 有効時は Timeline マーカー API のデモボタンも表示されます。

### Chunity マイク FX（ChunityMicFxSampleScene）

1. 上記と同じ有効化手順を行います。
2. `Assets/MIDI/Samples/Integrations/Chunity/Scenes/ChunityMicFxSampleScene.unity` を開きます。
3. Play モードでマイク入力が有効なことを確認し、CC1 スライダーまたはボタンで PitShift のシフト量が変化することを確認します。
4. 実機 MIDI の CC1（Mod Wheel）も `MidiChuckBridge` 経由で同じパラメータに届きます。

### Chunity Scriptable Generator（ChunityGeneratorWorkflowSampleScene）

1. Chunity ブリッジと同じ前提に加え、[Chunity Optional パッチ手順](../../../Scripts/Integrations/Chunity/Optional/README.md) の `useBuiltInAudioFilter` パッチを適用します。
2. プロジェクトに無い場合は Unity パッケージ **`com.unity.collections`** をインストールします。
3. Project Settings に `FEATURE_CHUNITY_SCRIPTABLE_AUDIO` を追加します（`FEATURE_CHUNITY` も維持）。
4. `ChunityGeneratorWorkflowSampleScene.unity` を開き、Main + Sub Generator + Bridge（Note On と Near/Mid/Far による空間化）を確認します。

### Scriptable Audio パイプライン

1. Unity 6000.3+ を確認し、`FEATURE_SCRIPTABLE_AUDIO` を追加します（[統合](integrations.md) / [ビルドポストプロセス](build-postprocessing.md) を参照）。
2. `ScriptableAudioMetronomeSampleScene.unity`、`ScriptableAudioSequenceSampleScene.unity`、または `ScriptableAudioUmpSequenceSampleScene.unity` を開きます。
3. Play モードでメトロノームまたはシーケンスのスケジュール動作を確認します。

### Foundation（FoundationSampleScene）

1. `FoundationSampleScene.unity` を開きます。
2. Play モードでデバイス選択・レイテンシ校正・Foundation UI シェルを確認します。

### MIDI Tracker（MidiTrackerSampleScene）

1. `Assets/MIDITracker/Scenes/MidiTrackerSampleScene.unity` を開きます。
2. Play モードに入り、パターン編集 / 再生 / アレンジを試します（外部 MIDI 音源を推奨）。
3. 操作方法とフェーズ状況は [MIDI Tracker README](../../../../MIDITracker/README.md) を参照してください。

サンプルでイベントが受信されない場合:
- 正しいプラットフォームバックエンドが使用されているか確認してください。
- トランスポートの要件を確認してください（WebGL の権限、ネットワークトランスポートのファイアウォールなど）。

`MidiInputRouter` やフィルタを使う場合は、[ゲームプレイ向けコンポーネント](gameplay.md) の配線手順も参照してください。

SMF の再生・録音を試す場合は、[SMF ツール](smf-tools.md) の `SmfPlayer` / `MidiRecorder` を利用できます。

Edit モードで SMF を確認する場合は、[エディタツール](editor-tools.md) の SMF プレビューを利用できます。

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
- `Assets/MIDI/Samples/DocumentationExamples/MptkBootstrapExample.cs` — Bootstrap / SmfPlayer 連携 / 検証
- `Assets/MIDI/Samples/DocumentationExamples/MptkMidiEventPipelineExample.cs` — 合成前 rewrite（`MPTK_PRO`）
- `Assets/MIDI/Samples/DocumentationExamples/MptkWriterExternalExample.cs` — Writer / External 往復（`MPTK_PRO`）
- `Assets/MIDI/Samples/DocumentationExamples/MptkInnerLoopExample.cs` — InnerLoop 区間ループ（`MPTK_PRO`）
- `Assets/MIDI/Samples/DocumentationExamples/MptkListPlayerExample.cs` — プレイリスト（`MPTK_PRO`）
- `Assets/MIDI/Samples/DocumentationExamples/MptkSoundFontLoaderExample.cs` — 実行時 SoundFont
- `Assets/MIDI/Samples/DocumentationExamples/MptkDelayedNoteDispatcherExample.cs` — 遅延ディスパッチ
- `Assets/MIDI/Samples/DocumentationExamples/MptkFilePlayerChannelsExample.cs` — チャンネル劇場

MPTK 専用サンプルシーン:

- シーン: `Assets/MIDI/Samples/MPTK/Scenes/MptkIntegrationSampleScene.unity`（`FEATURE_USE_MPTK` 必須）
- スクリプト: `Assets/MIDI/Samples/MPTK/Scripts/MptkIntegrationSampleScene.cs`
- 合成前 rewrite GUI: `Assets/MIDI/Samples/MPTK/Scripts/MptkMidiEventPipelineSample.cs`（`MPTK_PRO`）
- Writer / External / Join GUI: `Assets/MIDI/Samples/MPTK/Scripts/MptkWriterExternalSample.cs`（`MPTK_PRO`）
- InnerLoop GUI: `Assets/MIDI/Samples/MPTK/Scripts/MptkInnerLoopSample.cs`（`MPTK_PRO`）
- List GUI: `Assets/MIDI/Samples/MPTK/Scripts/MptkListPlayerSample.cs`（`MPTK_PRO`）
- Phase D GUI: `Assets/MIDI/Samples/MPTK/Scripts/MptkPhaseDUtilitiesSample.cs`
- Spatializer GUI: `Assets/MIDI/Samples/MPTK/Scripts/MptkSpatializerSample.cs`（`MPTK_PRO` + MidiSpatializer prefab）
- Spatializer Example: `Assets/MIDI/Samples/DocumentationExamples/MptkSpatializerExample.cs`
- Timeline Marker Example: `Assets/MIDI/Samples/DocumentationExamples/MptkTimelineMarkerExample.cs`
- 出力プリセット: `Assets/MIDI/Scripts/Integrations/MPTK/Presets/MptkVirtualOutput.preset.asset`

Maestro / MPTK を使用している場合は、以下も参照してください:
- [Maestro / MPTK 統合](mptk.md)
