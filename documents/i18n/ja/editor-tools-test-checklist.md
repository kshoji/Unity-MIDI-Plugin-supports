# エディタ拡張 動作確認チェックリスト

コミット `33b2c6053118d8e33dd785ee797693348d76407c` から `HEAD` までの差分で追加・更新された Unity Editor 拡張の動作確認用チェックリストです。

> **注:** 本チェックリストは [サンプルシーン動作確認チェックリスト](sample-scene-test-checklist.md) と併用することを推奨します。Play 専用ツールの確認には `MidiSampleScene` や `MidiGameplaySampleScene` 等の Play 対応シーンが必要です。

<div class="page" />

## 共通の事前準備

- [ ] Unity エディタでプロジェクトを開き、**Console にコンパイルエラーがない**
- [ ] `Window > MIDI` メニュー配下に各項目が表示される
- [ ] Play 専用ツールの確認前に、`MidiManager.InitializeMidi()` を呼ぶサンプルシーンを用意する
- [ ] 必要に応じて `Window > MIDI > Monitor` を併用し IN/OUT を確認する

### Scripting Define Symbols 一覧

| シンボル | 対象エディタ拡張 |
|----------|------------------|
| `FEATURE_USE_MPTK` + Maestro / MPTK アセット | SMF Preview の **Preview Audio (MPTK)** |
| `FEATURE_USE_TIMELINE` + `com.unity.timeline` | Timeline 統合エディタ（マーカー生成、Clip / Marker Inspector 等） |
| `FEATURE_USE_VISUALSCRIPTING` + `com.unity.visualscripting` | Visual Scripting メニュー、ノード登録 |

<div class="page" />

## 追加・更新された Editor 拡張一覧

### コアエディタツール（`Window > MIDI`）

| # | ツール | メニュー | モード | 主なソース |
|---|--------|----------|--------|------------|
| 1 | MIDI モニター（更新） | `Window > MIDI > Monitor` | Play 専用 | `MidiMonitorWindow.cs` 他 |
| 2 | 仮想 MIDI コントローラー（新規） | `Window > MIDI > Virtual Controller` | Play 専用 | `VirtualMidiControllerWindow.cs` |
| 3 | SMF プレビュー（新規） | `Window > MIDI > SMF Preview` | Edit 専用 | `SmfPreviewWindow.cs` |
| 4 | SMF インポート（新規） | `Assets > Import MIDI File...` | Edit 専用 | `SmfImportUtility.cs` |
| 5 | Project Settings（新規） | `Edit > Project Settings > MIDI` | Edit | `MidiProjectSettingsProvider.cs` |

### Unity 統合エディタ

| # | ツール | メニュー | 前提 |
|---|--------|----------|------|
| 6 | 統合サンプルアセット生成 | `Window > MIDI > Samples > Generate Integration Sample Assets` | 不要 |
| 7 | Animator Driver Inspector | Inspector（`MidiAnimatorDriver`） | 不要 |
| 8 | Timeline マーカー生成 | `Window > MIDI > Timeline > Generate Markers From Sequence Asset` | `FEATURE_USE_TIMELINE` |
| 9 | Timeline Notification Receiver 追加 | `Window > MIDI > Timeline > Add Notification Receiver To Director` | `FEATURE_USE_TIMELINE` |
| 10 | Visual Scripting ヘルパー | `Window > MIDI > Visual Scripting/*` | `FEATURE_USE_VISUALSCRIPTING` |

<div class="page" />

## 推奨テスト順序

1. **Project Settings** — 初回インポート時の自動生成、設定 UI
2. **SMF Preview / Import** — Edit モードで完結する機能
3. **MIDI Monitor** — Play 中の IN/OUT 捕捉・フィルタ・エクスポート
4. **Virtual Controller** — Monitor と併用して送信確認
5. **SMF Preview Audio (MPTK)** — MPTK 有効時の Play モード試聴
6. **Unity 統合エディタ** — Animator / Timeline / Visual Scripting

<div class="page" />

## ツール別チェックリスト

### 1. Project Settings（`Edit > Project Settings > MIDI`）

**目的:** グローバル MIDI 設定の管理、初回アセット自動生成

- [ ] プロジェクトを開くと `Assets/MIDI/Resources/MidiProjectSettings.asset` が自動生成される
- [ ] **Devices** セクション: Default Input / Output Device Id を編集・保存できる
- [ ] **Bluetooth MIDI**: Auto Scan / Timeout を変更できる
- [ ] **RTP-MIDI**: Port / Session Name を変更できる
- [ ] **Debug**: Log Level / Enable MIDI Monitor On Play を変更できる
- [ ] **Development**: Development Build Only Verbose Log を変更できる
- [ ] Play モード中、接続デバイス一覧から Default Device をドロップダウン選択できる
- [ ] `enableMidiMonitorOnPlay = true` のとき Play 開始で Monitor が自動オープンする

---

### 2. SMF Preview（`Window > MIDI > SMF Preview`）

**目的:** Edit モードでの `.mid` プレビュー・エクスポート

#### ファイル読み込み

- [ ] ウィンドウを開き、ドロップエリア「Drop .mid file here」が表示される
- [ ] `.mid` / `.midi` をドラッグ＆ドロップ → サマリ（Format / Ticks/Quarter / Tracks / Length）が表示される
- [ ] `Assets > Import MIDI File...` からファイル選択 → SMF Preview が開き読み込まれる
- [ ] 不正ファイル読み込み時、HelpBox にエラーメッセージが表示される

#### 内容表示

- [ ] **Tracks** 一覧でトラック選択 → **Events** 一覧が切り替わる
- [ ] Events 列（Tick / Time / Type / Ch / Detail）が正しく表示される
- [ ] **Note Roll (text)** フォールドアウト → ノート一覧が表示される

#### エクスポート

- [ ] **Export as MidiSequenceAsset** → Project 内に `.asset` 保存、Ping 表示
- [ ] エクスポートした `MidiSequenceAsset` を `SmfPlayer` や Timeline Clip で参照できる
- [ ] **Export as JSON** → 外部 `.json` 保存、ステータスメッセージ表示

#### オーディオプレビュー（MPTK）

**前提:** `FEATURE_USE_MPTK`、Maestro / MPTK アセット

- [ ] **Preview Audio (MPTK)** → Play モードに入り SMF が再生開始
- [ ] Console に `[SMF Preview Playback] Playing ...` ログ
- [ ] **Stop Preview** → 再生停止、`SmfPreviewPlaybackHost` が破棄される
- [ ] 再生完了後、ホスト GameObject が自動破棄される

**MPTK 無効時:**

- [ ] Audio Preview セクションに Info HelpBox（MPTK 必須）が表示され、ボタンは出ない

---

### 3. MIDI Monitor（`Window > MIDI > Monitor`）

**目的:** Play 中の MIDI IN/OUT リアルタイム表示・ログエクスポート

**前提:** Play モード（Edit モードではメッセージは記録されない）

#### 基本表示

- [ ] `MidiSampleScene` 等を Play し、Monitor を開く
- [ ] IN: 実機入力または Inject で Note On/Off / CC / PC が表示される
- [ ] OUT: `MidiSend` や Virtual Controller からの送信が表示される
- [ ] Time / Dir / Device / Ch / Type / Detail 列が表示される
- [ ] Note 系 Detail に音名（例: `C4 (60)`）が含まれる
- [ ] デバイス接続 / 切断イベントが Device タイプで表示される

#### ツールバー

- [ ] **Clear** → ログがクリアされる
- [ ] **Auto Scroll** → 新規行追加時に末尾へスクロール
- [ ] **Max Lines** 変更 → 保持行数が制限される（1–10000）

#### フィルタ

- [ ] **Direction**: All / IN / OUT
- [ ] **Device**: 接続デバイスで絞り込み
- [ ] **Ch**: チャンネル絞り込み
- [ ] **Type**: NoteOn / NoteOff / CC / PC / Device
- [ ] **Search**: Detail / Device 部分一致検索
- [ ] フィルタ設定を閉じて再度開く → EditorPrefs に保存・復元される

#### 列リサイズ

- [ ] 列ヘッダー右端をドラッグ → 列幅変更
- [ ] 変更後ウィンドウを閉じて再度開く → 列幅が維持される

#### エクスポート

- [ ] **Export CSV** → フィルタ適用後のログを `.csv` 保存
- [ ] **Export TXT** → フィルタ適用後のログを `.txt` 保存
- [ ] **Export SMF** → フィルタ適用後の OUT イベントを `.mid` 保存
- [ ] エクスポート不可（イベントなし）時、エラーメッセージが status に表示される
- [ ] エクスポートした SMF を SMF Preview で再読み込みできる

#### ライフサイクル

- [ ] Play 終了後もログ内容がウィンドウ上に残る
- [ ] 再度 Play → 新規メッセージが追加される（Clear で消去可能）

---

### 4. Virtual MIDI Controller（`Window > MIDI > Virtual Controller`）

**目的:** 物理デバイスなしで Note / CC / PC / Pitch Bend を送信

**前提:** Play モード、`MidiManager.InitializeMidi()` 済み

#### Edit モード

- [ ] Edit モードでは HelpBox「Play mode only」が表示され操作不可

#### Play モード — デバイス

- [ ] 出力デバイス一覧が表示される（Project Settings デフォルト / 接続デバイス / `editor:virtual-controller`）
- [ ] 未接続時 `editor:virtual-controller` 仮想出力が自動登録される
- [ ] Channel / Group / Velocity 設定が反映される

#### Play モード — 鍵盤

- [ ] 2 オクターブ鍵盤（C3–B4）クリック → Note On
- [ ] マウス離し → Note Off
- [ ] Monitor OUT に Note On/Off が表示される

#### Play モード — CC / その他

- [ ] 16 個の CC スライダー操作 → CC 送信（Monitor OUT 確認）
- [ ] CC 番号設定が EditorPrefs に保存・復元される
- [ ] Program Change / Pitch Bend 送信（UI セクション）
- [ ] **All Notes Off** → CC 123 全チャンネル送信
- [ ] **Panic** → CC 120 + 123 全チャンネル送信

#### Play 終了

- [ ] Play 終了時、押下中ノートが All Off される

---

### 5. 統合サンプルアセット生成

**メニュー:** `Window > MIDI > Samples > Generate Integration Sample Assets`

- [ ] メニュー実行 → 完了ダイアログ表示
- [ ] `Assets/MIDI/Samples/Integrations/Resources/MidiAnimatorSample.controller` が生成される
- [ ] Controller に Height / Pulse / BlendX / BlendY パラメータが含まれる
- [ ] 再実行時、既存 Controller が上書き再生成される

---

### 6. MidiAnimatorDriver Inspector

**目的:** Animator バインディングの編集、Play 中ライブ値表示

**確認手順:**

1. `MidiAnimatorIntegrationSampleScene` を開く、または `MidiAnimatorDriver` を持つ GameObject を選択
2. Inspector を確認

- [ ] Animator / Mapping / CC Smoother / Register With MidiManager フィールド表示
- [ ] Mapping 未設定時、Bindings 配列が編集可能
- [ ] **Add Binding** / **Remove Last** でバインディング追加・削除
- [ ] Mapping 設定時、Bindings は Asset 側を使用する HelpBox 表示
- [ ] Animator 未設定時、HelpBox で警告
- [ ] Play モード中、バインド先 Animator パラメータのライブ値が表示される

---

### 7. Timeline 統合エディタ

**前提:** `FEATURE_USE_TIMELINE`、`com.unity.timeline`

#### MidiPlaybackClip Inspector

- [ ] Timeline 上の Midi Playback Clip 選択 → Sequence Asset / Tempo BPM / Output Channel / Mute / Solo 編集
- [ ] Sequence Asset 設定時、Tracks 数 HelpBox 表示

#### MidiPlaybackClip Timeline 表示

- [ ] Clip 表示名が Sequence Asset 名に連動
- [ ] Mute / Solo 時 `(Muted)` / `(Solo)` サフィックス表示

#### マーカー Inspector / Timeline 表示

- [ ] `MidiBarMarker` — Bar Number / 拍子、Timeline ツールチップ
- [ ] `MidiTempoMarker` — BPM、Timeline ツールチップ
- [ ] `MidiMarker` / `MidiSignalEmitter` — 送信メッセージ編集、ツールチップ

#### Generate Markers From Sequence Asset

**手順:**

1. Project で `MidiSequenceAsset` を選択
2. Timeline ウィンドウで PlayableDirector を開く
3. `Window > MIDI > Timeline > Generate Markers From Sequence Asset` 実行

- [ ] 小節マーカー（MidiBarMarker）が生成される
- [ ] テンポ変更マーカー（MidiTempoMarker）が生成される
- [ ] Undo で元に戻せる
- [ ] Asset 未選択 / Director 未割当時、ダイアログで案内

#### Add Notification Receiver To Director

- [ ] Timeline で Director 選択後メニュー実行
- [ ] `MidiTimelineNotificationReceiver` コンポーネントが追加される
- [ ] 既存時は重複追加されない

---

### 8. Visual Scripting ヘルパー

**前提:** `FEATURE_USE_VISUALSCRIPTING`

**メニュー:** `Window > MIDI > Visual Scripting/*`

#### 自動登録

- [ ] プロジェクト読み込み後、`jp.kshoji.midi.visualscripting` が Visual Scripting Node Library に自動追加される

#### Open Visual Scripting Settings

- [ ] `Project Settings > Visual Scripting` が開く

#### Regenerate MIDI Nodes

- [ ] 実行 → Console に `[MIDI Visual Scripting] Node library regeneration requested.`（成功時）
- [ ] Script Graph に **Events > MIDI** ノードカテゴリが表示される

#### Show Node Categories

- [ ] ダイアログに MIDI/Events, MIDI/Send, MIDI/Playback, MIDI/Utility, MIDI/Note が表示される

#### ノード動作（併用確認）

- [ ] `MidiVisualScriptingIntegrationSampleScene` を Play
- [ ] Script Graph 経由で Note On / CC イベントが実行される

---

## クロスツール連携確認

以下は複数エディタ拡張の連携を確認する統合テストです。

### A. Virtual Controller → Monitor

- [ ] Play 中 Virtual Controller で Note 送信 → Monitor OUT に表示

### B. SMF Preview → MidiSequenceAsset → Timeline

- [ ] SMF Preview で `.mid` 読み込み → MidiSequenceAsset エクスポート
- [ ] Timeline の Midi Playback Clip に割当 → Play で同期再生（`MidiTimelineIntegrationSampleScene`）

### C. SMF Preview → MPTK 試聴

- [ ] SMF Preview で **Preview Audio (MPTK)** → Play モード自動開始 → 音声出力
- [ ] Monitor で OUT メッセージ確認（MPTK 仮想出力）

### D. Monitor → SMF 再インポート

- [ ] Play 中 MIDI 操作 → Monitor に記録 → **Export SMF**
- [ ] エクスポート `.mid` を SMF Preview で読み込み → イベント内容が一致

### E. Project Settings → Virtual Controller

- [ ] Default Output Device Id を変更 → Virtual Controller のデバイス一覧に反映

<div class="page" />

## 追加されたソースファイル（参考）

| ファイル | 役割 |
|----------|------|
| `MidiMonitorWindow.cs` | MIDI モニター UI（CSV/TXT/SMF エクスポート、列リサイズ） |
| `MidiMonitorLifecycle.cs` | Play 開始フック、Monitor 自動オープン |
| `MidiMonitorLogBuffer.cs` | ログバッファ |
| `MidiMonitorSettings.cs` | フィルタ・列幅 EditorPrefs |
| `MidiMonitorSmfExportUtility.cs` | Monitor ログ → SMF 変換 |
| `VirtualMidiControllerWindow.cs` | 仮想コントローラー UI |
| `VirtualMidiControllerState.cs` | CC 番号・デバイス ID EditorPrefs |
| `UI/PianoKeyboardElement.cs` | エディタ鍵盤 UI |
| `UI/CcSliderBankElement.cs` | CC スライダーバンク UI |
| `SmfPreviewWindow.cs` | SMF プレビューウィンドウ |
| `SmfPreviewModel.cs` | プレビューデータモデル |
| `SmfImportUtility.cs` | SMF 読み込み / エクスポート |
| `SmfPreviewAudioPlayback.cs` | MPTK Play モード試聴 |
| `MidiProjectSettingsProvider.cs` | Project Settings UI |
| `MidiProjectSettingsBootstrap.cs` | 初回 Settings アセット生成 |
| `Integrations/MidiIntegrationSampleSetup.cs` | 統合サンプル Controller 生成 |
| `Integrations/Animator/MidiAnimatorDriverEditor.cs` | Animator Driver Inspector |
| `Integrations/Timeline/*` | Timeline Clip / Marker エディタ、マーカー生成 |
| `Integrations/VisualScripting/MidiVisualScriptingMenu.cs` | VS ノード登録メニュー |

<div class="page" />

## 関連ドキュメント

- [エディタツール](editor-tools.md)
- [サンプルシーン動作確認チェックリスト](sample-scene-test-checklist.md)
- [Unity エコシステム統合](integrations.md)
- [SMF ツール](smf-tools.md)
- [Maestro / MPTK 統合](mptk.md)
