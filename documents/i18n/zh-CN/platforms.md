# 平台与限制

## 功能矩阵（按平台）

| 平台 | 蓝牙 MIDI | USB MIDI | 网络 MIDI (RTP-MIDI) | Nearby Connections MIDI | 应用间 MIDI | USB MIDI 2.0 | 网络 MIDI 2.0 (UDP MIDI 2.0) |
| ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- |
| iOS | ○ | ○ | ○ | ○ | ○ | ○ | △ (实验性) |
| Android | ○ | ○ | △ (实验性) | ○ | ○ | ○ | △ (实验性) |
| UWP (通用 Windows 平台) | - | ○ | △ (实验性) | - | ○ | - | △ (实验性) |
| 独立 macOS / Unity 编辑器 macOS | ○ | ○ | ○ | ○ | ○ | ○ | △ (实验性) |
| 独立 Linux / Unity 编辑器 Linux | ○ | ○ | △ (实验性) | - | ○ | ○ | △ (实验性) |
| 独立 Windows / Unity 编辑器 Windows | - | ○ | △ (实验性) | - | ○ | - | △ (实验性) |
| WebGL | ○ | ○ | - | - | - | - | - |

图例：
- ○ 已支持
- △ 已支持，但处于实验性/受限状态
- \- 不支持

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
  - 参见：[构建后处理与脚本定义符号](build-postprocessing.md)

### iOS / macOS
- 支持的 iOS：12.0 或更高版本。
- 蓝牙 MIDI：仅支持主机模式 (Central)。

### UWP
- 支持的 UWP 目标版本：10.0.10240.0 或更高版本。
- 目前不支持 MIDI 2.0 (USB)。
- 不支持蓝牙 MIDI。
- RTP-MIDI 需要启用相应能力 (Capability)：
  - `Project Settings > Player > Capabilities > PrivateNetworkClientServer`

### Windows
- 不支持蓝牙 MIDI。
- 不支持 MIDI 2.0 (USB)。

### Linux
- 当 MIDI 1 和 MIDI 2 共存于一个 USB 设备时，可能只能找到 MIDI 2 端口。

### WebGL
- 设备支持取决于操作系统/浏览器的 WebMIDI 环境。
- WebGL 无法使用原始 UDP/TCP 套接字，因此无法使用 RTP-MIDI / UDP MIDI 2.0。
- WebGL 访问其他服务器时可能会受到 `UnityWebRequest` 限制；请使用 `StreamingAssets` 存放 `.mid` 和其他内容。
- WebGL 无法处理 USB MIDI 2.0 设备及网络 MIDI 2.0；除剪辑文件 (Clip file) 读写外，MIDI 2.0 运行时功能不可用。
- WebGL 模板可能需要暴露 `unityInstance`（参见 [传输协议与平台说明](transports.md)）。
