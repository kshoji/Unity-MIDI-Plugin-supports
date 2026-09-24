# サンプルシーン動作確認チェックリスト

残存サンプルシーンの動作確認用チェックリストです。

> **注:** `MptkIntegrationSampleScene.unity` は別途 `FEATURE_USE_MPTK` が必要です。

<div class="page" />

## 共通の事前準備

各シーンで共通して確認しておく項目:

- [ ] シーンを開いて **Play モード** に入れる（コンソールにエラーが出ない）
- [ ] `Window > MIDI > Monitor` を開き、必要に応じて IN/OUT を確認
- [ ] 該当する **Scripting Define Symbols** が有効（下表参照）
- [ ] IMGUI パネルが表示され、ボタン操作に応答する

### Scripting Define Symbols 一覧

| シンボル | 対象シーン |
|----------|------------|
| `FEATURE_USE_TIMELINE` + `com.unity.timeline` | MidiTimelineIntegrationSampleScene |
| `FEATURE_USE_VISUALSCRIPTING` + `com.unity.visualscripting` | MidiVisualScriptingIntegrationSampleScene |
| `FEATURE_MIDI_NETWORK` | MidiNetworkJamSampleScene |
| `FEATURE_INPUT_SYSTEM` + `com.unity.inputsystem` | InputSystemBridgeSampleScene |

<div class="page" />

## シーン一覧

| # | カテゴリ | シーン | パス |
|---|----------|--------|------|
| 1 | ゲームプレイ基盤 | MidiGameplaySampleScene | `Assets/MIDI/Samples/Gameplay/Scenes/MidiGameplaySampleScene.unity` |
| 2 | Unity 統合 | MidiAnimatorIntegrationSampleScene | `Assets/MIDI/Samples/Integrations/Scenes/MidiAnimatorIntegrationSampleScene.unity` |
| 3 | Unity 統合 | MidiTimelineIntegrationSampleScene | `Assets/MIDI/Samples/Integrations/Scenes/MidiTimelineIntegrationSampleScene.unity` |
| 4 | Unity 統合 | MidiVisualScriptingIntegrationSampleScene | `Assets/MIDI/Samples/Integrations/Scenes/MidiVisualScriptingIntegrationSampleScene.unity` |
| 5 | ゲームプレイ | ChordPuzzleSampleScene | `Assets/MIDI/Samples/Gameplay/Scenes/ChordPuzzleSampleScene.unity` |
| 6 | ゲームプレイ | ChordScaleSampleScene | `Assets/MIDI/Samples/Gameplay/Scenes/ChordScaleSampleScene.unity` |
| 7 | ゲームプレイ | MidiClockSyncSampleScene | `Assets/MIDI/Samples/Gameplay/Scenes/MidiClockSyncSampleScene.unity` |
| 8 | ゲームプレイ | ScalePracticeSampleScene | `Assets/MIDI/Samples/Gameplay/Scenes/ScalePracticeSampleScene.unity` |
| 9 | Input System | InputSystemBridgeSampleScene | `Assets/MIDI/Samples/Integrations/InputSystem/Scenes/InputSystemBridgeSampleScene.unity` |
| 10 | ネットワーク | MidiNetworkJamSampleScene | `Assets/MIDI/Samples/Integrations/Networking/Scenes/MidiNetworkJamSampleScene.unity` |
| 11 | Foundation | FoundationSampleScene | `Assets/MIDI/Samples/Foundation/Scenes/FoundationSampleScene.unity` |

<div class="page" />

## 推奨テスト順序

1. **基盤:** MidiGameplaySampleScene
2. **Unity 統合:** Animator → Timeline → Visual Scripting
3. **横断基盤:** ClockSync → Chord/Scale → InputSystem
4. **ネットワーク / Foundation:** MidiNetworkJamSampleScene → FoundationSampleScene

<div class="page" />

## シーン別チェックリスト

### 1. MidiGameplaySampleScene

**パス:** `Assets/MIDI/Samples/Gameplay/Scenes/MidiGameplaySampleScene.unity`  
**目的:** DeviceFilter → ChannelFilter → Router / NoteTracker パイプライン

- [ ] Play 開始後、IMGUI にパイプライン状態が表示される
- [ ] **NoteOn C4 (ch0)** → Router の Launch バインディングが発火（ログ / カウンタ更新）
- [ ] **CC64 = 127 (ch0)** → Toggle バインディングが発火
- [ ] **NoteOn C4 (ch1)** → ChannelFilter でブロック（Router / Tracker に届かない）
- [ ] **Add D4 + E4 (ch0)** → 3 ノート和音検出メッセージ
- [ ] SmfPlayer 再生 / MidiRecorder 録音セクションが動作する
- [ ] 実機 MIDI コントローラー接続時も ch0 の C4 / CC64 で同様に動作

---

### 2. MidiAnimatorIntegrationSampleScene

**パス:** `Assets/MIDI/Samples/Integrations/Scenes/MidiAnimatorIntegrationSampleScene.unity`  
**目的:** MIDI → Animator パラメータ駆動

- [ ] **CC1 = 127** → キューブが上昇
- [ ] **Note 60** → パルス演出（スケール変化）
- [ ] **CC10 / CC11** → キューブが回転
- [ ] 仮想 MIDI Inject と実機入力の両方で同様の変化

---

### 3. MidiTimelineIntegrationSampleScene

**パス:** `Assets/MIDI/Samples/Integrations/Scenes/MidiTimelineIntegrationSampleScene.unity`  
**前提:** `FEATURE_USE_TIMELINE`、`com.unity.timeline`

- [ ] **Play Timeline** → SMF が再生開始
- [ ] Director time と SmfPlayer time が同期
- [ ] **Pause** → SmfPlayer が一時停止
- [ ] **Seek to 1.0 s** → SmfPlayer が追従

---

### 4. MidiVisualScriptingIntegrationSampleScene

**パス:** `Assets/MIDI/Samples/Integrations/Scenes/MidiVisualScriptingIntegrationSampleScene.unity`  
**前提:** `FEATURE_USE_VISUALSCRIPTING`、`com.unity.visualscripting`

- [ ] **NoteOn C4** → キューブの色が変化
- [ ] **CC1 = 127** → ビジュアルフィードバックが変化
- [ ] Event Bus / Script Graph 経由でイベントが届く

---

### 5. ChordPuzzleSampleScene

**パス:** `Assets/MIDI/Samples/Gameplay/Scenes/ChordPuzzleSampleScene.unity`  
**目的:** 和音名当てクイズ（MidiScaleQuiz）

- [ ] 画面上に Target chord が表示される
- [ ] **Hold C Major / C Minor / Cmaj7** → 認識和音がログに出る
- [ ] **Check Answer** → Correct / Incorrect 判定
- [ ] 正解時 **Next Puzzle** で次の課題へ
- [ ] **Release All** でノート解除

---

### 6. ChordScaleSampleScene

**パス:** `Assets/MIDI/Samples/Gameplay/Scenes/ChordScaleSampleScene.unity`  
**目的:** ChordRecognition + MidiChordDetector + MidiScaleQuiz

- [ ] **Hold C Major** → Chord = C、C Major scale = OK
- [ ] **Hold C Minor** → Chord = Cm
- [ ] **Add Out-of-scale Note (61)** → Out of scale 表示 / ログ
- [ ] **Evaluate Quiz Target = C** → クイズ判定
- [ ] **Release All** で状態リセット

---

### 7. MidiClockSyncSampleScene

**パス:** `Assets/MIDI/Samples/Gameplay/Scenes/MidiClockSyncSampleScene.unity`  
**目的:** 外部 MIDI Clock 同期・BPM 推定

- [ ] **Inject Start** → IsPlaying = true
- [ ] **Inject 24 Timing Clocks** → BPM 推定値が更新、Bar/Beat が進む
- [ ] **Inject Stop** → 停止
- [ ] **Set Adapter Mode: Step / Follow** → SmfPlayerClockAdapter モード切替
- [ ] onBeat イベントがログに出る

---

### 8. ScalePracticeSampleScene

**パス:** `Assets/MIDI/Samples/Gameplay/Scenes/ScalePracticeSampleScene.unity`  
**目的:** 全スケールタイプの所属判定

- [ ] スケールタイプ（Major / Minor 等）切替 → In scale 表示が変わる
- [ ] **Add C4 (60)** → In scale: Yes（Major 選択時）
- [ ] **Add C#4 (61)** → In scale: No、Scale violation ログ
- [ ] **Add E4 (64)** → 和音認識表示が更新
- [ ] **Release All** でクリア

---

### 9. InputSystemBridgeSampleScene

**パス:** `Assets/MIDI/Samples/Integrations/InputSystem/Scenes/InputSystemBridgeSampleScene.unity`  
**前提:** `FEATURE_INPUT_SYSTEM`

- [ ] Play 開始後 Synthetic Device が生成される
- [ ] MIDI Inject → Input System 状態に反映
- [ ] Input Action → MIDI 送信（双方向ブリッジ）
- [ ] Monitor で IN/OUT を確認

---

### 10. MidiNetworkJamSampleScene

**パス:** `Assets/MIDI/Samples/Integrations/Networking/Scenes/MidiNetworkJamSampleScene.unity`  
**前提:** `FEATURE_MIDI_NETWORK`

- [ ] Hub / Client のループバックまたは 2 インスタンスで接続
- [ ] Broadcast モードで MIDI イベントが Client 側仮想デバイスへ注入
- [ ] Merge / Playback モード切替（UI があれば）
- [ ] Monitor で Client 側 IN を確認

---

### 11. FoundationSampleScene

**パス:** `Assets/MIDI/Samples/Foundation/Scenes/FoundationSampleScene.unity`  
**目的:** デバイス選択・レイテンシ校正・Foundation UI

- [ ] Play 開始後、Foundation UI シェルが表示される
- [ ] デバイス選択 UI が接続デバイスを一覧表示する
- [ ] レイテンシ校正フローが動作する
- [ ] 設定の永続化（あれば）が動作する

<div class="page" />

## 関連ドキュメント

- [サンプル](samples.md)
- [ジャンルキット](kits.md)
- [Unity エコシステム統合](integrations.md)
- [ゲームプレイ向けコンポーネント](gameplay.md)
