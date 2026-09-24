# 平台与限制

## 功能矩阵（按平台）

| 平台 | 蓝牙 MIDI | USB MIDI | 网络 MIDI (RTP-MIDI) | Nearby Connections MIDI | 应用间 MIDI | USB MIDI 2.0 | 网络 MIDI 2.0 (UDP MIDI 2.0) | Scriptable Audio（可选） |
| ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- |
| iOS | ○ | ○ | ○ | ○ | ○ | ○ | △ (实验性) | ○ * |
| Android | ○ | ○ | △ (实验性) | ○ | ○ | ○ | △ (实验性) | ○ * |
| UWP (通用 Windows 平台) | - | ○ | △ (实验性) | - | ○ | △ (有限制) | △ (实验性) | ○ * |
| 独立 macOS / Unity 编辑器 macOS | ○ | ○ | ○ | ○ | ○ | ○ | △ (实验性) | ○ * |
| 独立 Linux / Unity 编辑器 Linux | ○ | ○ | △ (实验性) | - | ○ | ○ | △ (实验性) | ○ * |
| 独立 Windows / Unity 编辑器 Windows | - | ○ | △ (实验性) | - | ○ | △ (有限制) | △ (实验性) | ○ * |
| WebGL | ○ | ○ | - | - | - | - | - | - |

图例：
- ○ 已支持
- △ 已支持，但处于实验性/受限状态
- \- 不支持

\* Scriptable Audio 集成需要 **Unity 6000.3+**、脚本定义 `FEATURE_SCRIPTABLE_AUDIO` 及非 WebGL 构建目标。参见 [构建后处理](build-postprocessing.md#scriptable-audio-pipeline-集成可选)。

<div class="page" />

## 限制与要求

### Android
- USB MIDI：API Level 12 (Android 3.1) 或更高版本。
- 蓝牙 MIDI：API Level 18 (Android 4.3) 或更高版本。
- 应用间 MIDI：API Level 23 (Android 6.0) 或更高版本。
- MIDI 2.0：API Level 23 (Android 6.0) 或更高版本。
- 如果使用 **Mono** 构建：
  - 可能会出现延迟问题
  - 可能仅支持 `armeabi-v7a`
  - 推荐：使用 **IL2CPP**  
    `Project Settings > Player > Configuration > Scripting Backend`
- Nearby Connections MIDI：
  - 需要 API Level 28 (Android 9) 或更高版本
  - 且应使用 API Level 33 (Android 13) 或更高版本进行编译

#### 蓝牙 MIDI 从机模式 (仅限 Android)
除了作为 BLE MIDI **主机 (Central)**（扫描/连接设备）外，Android 还可以作为 BLE MIDI **从机 (Peripheral)**（将您的应用广播为 MIDI 设备）。

注意：
- 仅限 Android。
- 请参阅 [MIDI 1.0 (MidiManager)](midi1.md) 了解相关的 API 入口。
- 仍需遵守平台权限和蓝牙状态要求（参见 [构建后处理](build-postprocessing.md)）。

### Meta Quest (Oculus Quest)
Meta Quest 设备运行 Android，因此上述 **Android** 要求同样适用。

实践建议（参考手册）：
- 在 Quest 上使用 **USB MIDI** 可能需要在 Android 构建后处理期间启用 USB 设备 Intent 过滤器。
  - 参见：[构建后处理与脚本定义符号](build-postprocessing.md)（Quest USB MIDI 章节）
- **蓝牙 MIDI** 设备的发现/配对可以使用 Android 的伴生设备 (Companion Device) 工作流。
  - 启用脚本定义符号：`FEATURE_ANDROID_COMPANION_DEVICE`
  - Unity 6+ 且 Application Entry Point 使用 **GameActivity** 时，后处理会选择 `BleMidiUnityGamePlayerActivity`（见 [构建后处理](build-postprocessing.md)）
  - 参见：[构建后处理与脚本定义符号](build-postprocessing.md)

### iOS / macOS
- 支持的 iOS：12.0 或更高版本。
- 蓝牙 MIDI：仅支持主机模式 (Central)。

### UWP
- 支持的 UWP 目标版本（MIDI 1.0）：10.0.10240.0 或更高版本。
- MIDI 2.0 (USB)：**有限制支持**（`UwpMidi2Plugin` / `Midi2Plugin.Uwp.cs`），通过 `InitializeMidi2`。
  - 运行时要求：
    - 操作系统：**Windows 11 24H2 或更高**（内部版本 **26100+**，含 25H2 / 26H1），且已启用 Windows MIDI Services
    - 设备需另行安装 **Windows MIDI Services SDK Runtime and Tools**（[get-latest](https://microsoft.github.io/MIDI/get-latest/) 或 `winget install Microsoft.WindowsMIDIServicesSDK`）
    - 体系结构：**x64 或 ARM64**（不支持 x86）
    - 引用 `Assets/MIDI/Plugins/WSA/Microsoft.Windows.Devices.Midi2.winmd`（原生 WinRT；不要使用 NetProjection）
    - **仅 UWP 播放器构建**（`UNITY_WSA && !UNITY_EDITOR`）。Unity 编辑器 / Standalone Windows 中不可用
  - 在 Windows 10、Windows 11 23H2 及更早版本，或未安装 MIDI Services / SDK Runtime 时，初始化会跳过并输出警告日志，但仍调用完成回调（不会崩溃）
  - MIDI 1.0（`WindowsMidiPlugin`）与 MIDI 2.0（`UwpMidi2Plugin`）为独立后端，同一物理设备可能在**两个 API** 下同时出现，且设备 ID 空间不同（经典 MIDI1 ID 与 Windows MIDI Services 的 `EndpointDeviceId`）。请按用途仅使用 MIDI1 或仅使用 MIDI2
  - 热插拔由 Watcher 处理。没有针对 UWP Suspend / `OnApplicationPause` 的专用重新初始化；长时间挂起后建议 `TerminateMidi2` 再 `InitializeMidi2`
  - UWP 构建还会同时初始化 `UdpMidi2Plugin`（实验性）
- 不支持蓝牙 MIDI。
- RTP-MIDI 需要启用相应能力 (Capability)：
  - `Project Settings > Player > Capabilities > PrivateNetworkClientServer`

### Windows（独立版 / Unity 编辑器）
- 不支持蓝牙 MIDI。
- MIDI 2.0 (USB)：**有限制支持**（`WindowsMidi2Plugin` / `Midi2Plugin.Windows.cs`），通过 `InitializeMidi2` 与原生 **`Midi2Native.dll`**（**方法 C** — C++/WinRT）。
  - 运行时要求：
    - 操作系统：**Windows 11 24H2 或更高**（内部版本 **26100+**，含 25H2 / 26H1），且已启用 Windows MIDI Services
    - 设备需另行安装 **Windows MIDI Services SDK Runtime and Tools**（[get-latest](https://microsoft.github.io/MIDI/get-latest/) 或 `winget install Microsoft.WindowsMIDIServicesSDK`）
    - 体系结构：**x64 或 ARM64**（不支持 x86）。Unity 编辑器 Play Mode 使用 **x64** DLL
    - 原生插件：`Assets/MIDI/Plugins/Windows/x86_64/Midi2Native.dll` 与 `.../ARM64/Midi2Native.dll`。请勿将 `WinRT.Runtime`、NetProjection 或 Standalone 用的 Midi2 `.winmd` 放入托管 `Assets`
    - **独立 Windows 与 Unity 编辑器 Windows**（`UNITY_EDITOR_WIN || (UNITY_STANDALONE_WIN && !UNITY_EDITOR)`）。UWP 的 USB MIDI 2.0 仍为 **方法 B**（`UwpMidi2Plugin` + WSA winmd）— 另一后端；勿为 Standalone 启用 WSA winmd
  - 在 Windows 10、Windows 11 23H2 及更早版本，或未安装 MIDI Services / SDK Runtime 时，初始化会跳过并输出警告日志，但仍调用完成回调（不会崩溃）
  - MIDI 1.0（`WindowsMidiPlugin` / WinMM）与 MIDI 2.0（`WindowsMidi2Plugin`）为独立后端，同一物理设备可能在**两个 API** 下同时出现，且设备 ID 空间不同（WinMM ID 与 Windows MIDI Services 的 `EndpointDeviceId`）。请按用途仅使用 MIDI1 或仅使用 MIDI2
  - 热插拔由 Watcher 处理。编辑器退出 Play Mode 时通过 `PlayModeStateChanged` 调用 `TerminateMidi`（原生 SDK 关闭有引用计数保护）
  - 在独立版 / 编辑器中使用 `InitializeMidi2` 时，除 USB MIDI 2.0 外还会初始化 `UdpMidi2Plugin`（实验性）

### Linux
- 当 MIDI 1 和 MIDI 2 共存于一个 USB 设备时，可能只能找到 MIDI 2 端口。

### WebGL
- 设备支持取决于操作系统/浏览器的 WebMIDI 环境。
- WebGL 无法使用原始 UDP/TCP 套接字，因此无法使用 RTP-MIDI / UDP MIDI 2.0。
- WebGL 访问其他服务器时可能会受到 `UnityWebRequest` 限制；请使用 `StreamingAssets` 存放 `.mid` 和其他内容。
- WebGL 无法处理 USB MIDI 2.0 设备及网络 MIDI 2.0；除剪辑文件 (Clip file) 读写外，MIDI 2.0 运行时功能不可用。
- WebGL 模板可能需要暴露 `unityInstance`（参见 [传输协议与平台说明](transports.md)）。
- **WebGL 上不编译 Scriptable Audio 集成**（asmdef 排除 WebGL 平台）。`ScriptableAudioUtility.IsAvailable` 为 `false`。
- **Maestro / MPTK（WebGL，可选）：** Maestro 2.16+ 文档标明正式支持 WebGL。预期 **Core player 为旧模式**（WebGL 上强制关闭 `MPTK_Core`）。内容优先放 `StreamingAssets`，或为 `MidiExternalPlayer` / 远程 SoundFont 使用 **符合 CORS** 的主机。WebGL 上的 `MPTKWriter` 偏直接播放。**有 MPTK ≠ 本插件所有集成都可用**（Scriptable Audio / Network MIDI / MIDI 2.0 运行时仍如上受限）。详见 [Maestro / MPTK 集成](mptk.md)。

<div class="page" />

### Scriptable Audio Pipeline（可选集成）

| 要求 | 值 |
|------|-----|
| Unity 版本 | 6000.3 LTS 或更高 |
| 脚本定义 | `FEATURE_SCRIPTABLE_AUDIO` |
| 包 | `com.unity.burst`、`com.unity.collections`（使用完整仓库时已包含） |
| WebGL | 不支持 — 集成 asmdef 排除，`#if !UNITY_WEBGL` 守卫 |
| 未定义符号 / Unity 6.2 及更早 | 不编译集成代码；核心 MIDI 插件不变 |

不满足要求时项目仍应能无错误编译。参见 [构建后处理 — Scriptable Audio Pipeline 集成](build-postprocessing.md#scriptable-audio-pipeline-集成可选)。

### Chunity / Chunity Scriptable Generator（可选集成）

| 项目 | 内容 |
|------|------|
| Chunity 运行时 | **不捆绑**。由使用者另行安装 |
| 脚本定义 | `FEATURE_CHUNITY`（必需）。使用 Generator 时另需 `FEATURE_CHUNITY_SCRIPTABLE_AUDIO` |
| 包（Generator） | `com.unity.collections`（asmdef 引用 `Unity.Collections`） |
| Generator 补丁 | 对 Chunity 本体的 `useBuiltInAudioFilter` 最小补丁（[Chunity Optional 补丁说明](../../../Scripts/Integrations/Chunity/Optional/README.md)） |
| WebGL | 遵循 Chunity 自身的限制。Generator 不受支持，将回退到 FilterRead / WebChucK |
| Unity 版本（Generator） | 预期 6000.3+（与本体 Scriptable Audio 集成同一系列） |

有关详细信息，请参阅 [Unity 生态系统集成 — Chunity](integrations.md#chunity-chuck-集成) 和 [构建后处理](build-postprocessing.md#chunity-chuck-集成可选)。
