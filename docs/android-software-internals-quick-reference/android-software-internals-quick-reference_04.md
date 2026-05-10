# 权限

Android 中的权限遵循最小权限模型，即特定类型的功能仅授予明确请求这些功能的应用程序。权限主要分为两种类型：清单权限和运行时权限（后者通常属于“危险”权限类别）。运行时权限和清单权限都必须在 Android 清单中声明；但此外，运行时权限还必须在运行时请求，通过对话框（如图 3-1 所示）提示用户是否同意应用程序使用所述功能。运行时权限是在 API 级别 23 中实现的；然而，在此之前，用户会在安装前看到所有运行时权限。

![../images/509502_1_En_3_Chapter/509502_1_En_3_Fig1_HTML.jpg](img/509502_1_En_3_Fig1_HTML.jpg)

**图 3-1** 用户看到的运行时权限示例

如前所述，权限根据其对用户、Android 系统或设备上其他应用程序构成的潜在风险被赋予权限类型。所有权限类型的摘要见表 3-1。^(⁵)

**表 3-1** 权限类型

| 权限类型 | 风险 | 描述 |
| --- | --- | --- |
| 普通 | 低 | 默认权限类型。为应用程序提供独立的应用程序级功能，例如 `BLUETOOTH`、`NFC` 和 `INTERNET`。 |
| 危险 | 高 | 为应用程序提供对私有数据或设备控制方面的访问权限，例如 `WRITE_EXTERNAL_STORAGE`、`ACCESS_FINE_LOCATION` 和 `CAMERA`。与普通权限类型不同，运行时权限请求必须由用户（在运行时通过点击按钮）接受，系统才会授予权限（或者在 API 级别 23 之前的设备上在安装时接受）。 |
| 签名 | 关键 | 仅当请求权限的应用程序与声明该权限的应用程序使用相同的证书签名时，才会授予此权限。这通常用于将权限类型限制为系统/预安装的应用程序。这些权限通常授予对系统和其他应用程序的大规模控制权，或绕过 Android 安全机制。其中包括 `MANAGE_EXTERNAL_STORAGE`、`READ_LOGS` 和 `CAPTURE_AUDIO_OUTPUT`。 |

除了这些类型之外，还可以向权限应用其他标志，包括 `privileged` 和 `development`，它们分别指授予系统和开发应用程序的权限。^(⁶)

最后，在识别哪些应用程序可以执行哪些操作时，还需要考虑另外两种权限类型：

* **硬限制权限** - 设备上的任何应用程序都不能持有此权限，除非安装程序应用已将该权限列入允许列表。
* **软限制权限** - 设备上的任何应用程序都不能以其完整形式持有此权限，除非安装程序应用已将该权限列入允许列表。

在调试设备时，可以使用 `adb` 授予通常对标准应用程序不可用的权限（例如具有签名权限类型的权限）。例如，对于 `READ_LOGS` 权限，可以使用以下命令来授予该权限：`adb shell pm grant <Package ID> android.permission.READ_LOGS`。要检索应用程序的包 ID，请参阅第 5 章或第 12 章。

清单权限示例：

```
<uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION" />
```

运行时权限请求示例：

```
if (Build.VERSION.SDK_INT >= 23) {
    // 除非设置了正确的清单权限，否则不会显示通知
    ActivityCompat.requestPermissions(this, new String[]{Manifest.permission.WRITE_EXTERNAL_STORAGE}, 1234);
}
```

在撰写本文时，Android 规范^(⁷) 列出了 166 种不同的清单权限。表 3-2 显示了这些权限中最常见的一个子集，以及它们的 API 和权限级别。

**表 3-2** Android 权限

| 名称 | 权限 | API 级别 | 描述 |
| --- | --- | --- | --- |
| `ACCESS_COARSE_LOCATION` | 危险 | 29+ | 通过非 GPS 提供程序（如网络提供商）访问大致位置。 |
| `ACCESS_FINE_LOCATION` | 危险 | 1+ | 通过 GPS 和网络提供商访问特定位置。 |
| `ACCESS_NETWORK_STATE` | 普通 | 1+ | 访问 `ConnectivityManager`，除其他职责外，负责监控网络连接（Wi-Fi-GPRS、UMTS 等）。 |
| `ACCESS_WIFI_STATE` | 普通 | 1+ | 访问 `WifiManager`，负责查看已配置的网络列表、当前活动网络以及接入点扫描结果。 |
| `ANSWER_PHONE_CALLS` | 危险 | 26+ | 允许应用程序接听来电。 |
| `BATTERY_STATS` | 签名\|特权\|开发 | 1+ | 允许收集电池信息和统计数据。 |
| `BLUETOOTH` | 普通 | 1+ | 允许与蓝牙设备配对和连接。 |
| `CALL_PHONE` | 危险 | 1+ | 允许应用程序在不向拨号器应用发送 Intent（因此无需通过 UI 通知用户）的情况下拨打电话。 |
| `CALL_PRIVILEGED` | 不允许第三方应用使用 | 1+ | `CALL_PHONE` 权限的限制较少版本，允许应用程序拨打任何电话号码，包括紧急号码。 |
| `CAMERA` | 危险 | 1+ | 提供对设备相机的访问权限。 |
| `CAPTURE_AUDIO_OUTPUT` | 禁止第三方应用使用 | 19+ | 允许不受限制地访问设备麦克风以录制音频。 |
| `CHANGE_WIFI_STATE` | 普通 | 1+ | 允许应用程序修改设备的网络配置，并连接和断开 Wi-Fi 接入点。 |
| `DELETE_PACKAGES` | 禁止第三方应用使用 | 1+ | 允许删除应用程序。从 Android 7 开始，如果请求删除的应用程序与安装它的应用程序不同，则需要用户确认。 |
| `INSTALL_PACKAGES` | 禁止第三方应用使用 | 1+ | 允许应用程序安装另一个应用程序。 |
| `INTERNET` | 普通 | 1+ | 允许应用程序打开网络套接字。 |
| `MANAGE_EXTERNAL_STORAGE` | 签名\|appop\|预安装 | 30+ | 由于 Android 10 中实现了作用域存储（应用程序拥有沙盒化的外部存储），此权限允许广泛管理外部存储空间。 |
| `NFC` | 普通 | 9+ | 允许通过 NFC（近场通信）进行 I/O 操作。 |
| `PROCESS_OUTGOING_CALLS` | 危险且硬限制 | 1–29 | 允许应用程序查看和重定向所有去电。 |
| `READ_CONTACTS` | 危险 | 1+ | 允许读取用户的联系人信息。 |
| `READ_EXTERNAL_STORAGE` | 危险，软限制 | 16+ | 允许应用程序从外部存储读取数据。在 API 19 之后，读取应用程序的作用域存储不需要此权限。截至 API 级别 29 强制实施作用域存储，此权限仅提供只读访问权限。 |
| `READ_LOGS` | 禁止第三方应用使用 | 1+ | 允许读取系统日志信息，例如由 `DropBoxManager` 创建的日志。 |
| `READ_PHONE_NUMBERS` | 危险 | 26+ | 允许应用程序读取设备的所有电话号码。默认情况下，此权限对即时应用程序开放。 |
| `READ_PHONE_STATE` | 危险 | 1+ | 包括 `READ_PHONE_NUMBERS` 权限。允许访问设备/电话信息，例如当前蜂窝网络。 |
| `READ_SMS` | 危险且硬限制 | 1+ | 允许读取设备已收到的 SMS 消息。 |
| `REBOOT` | 禁止第三方应用使用 | 1+ | 允许重启设备。 |
| `RECEIVE_BOOT_COMPLETED` | 普通 | 1+ | 允许应用程序接收 `Intent.ACTION_BOOT_COMPLETED` Intent。该 Intent 在系统启动完成后广播。 |
| `RECEIVE_MMS` | 危险且硬限制 | 1+ | 允许应用程序监控传入的 MMS 消息。 |
| `RECEIVE_SMS` | 危险且硬限制 | 1+ | 允许应用程序监控传入的 SMS 消息。 |
| `RECORD_AUDIO` | 危险 | 1+ | 允许应用程序录制音频。 |
| `REQUEST_DELETE_PACKAGES` | 普通 | 26+ | 允许应用程序请求删除自身或另一个应用程序。这将需要用户交互。 |
| `REQUEST_INSTALL_PACKAGES` | 签名 | 23+ | 允许应用程序请求安装另一个应用程序。这将需要用户交互。 |
| `SEND_SMS` | 危险且硬限制权限 | 1+ | 允许发送 SMS 消息。 |
| `SET_PREFERRED_APPLICATIONS` | 普通 | 1–15 | 允许应用程序修改用户首选的（默认）应用程序——包括 Web 浏览器和安装程序。 |
| `SET_TIME` | 禁止第三方应用使用 | 8+ | 允许设置系统时间。 |
| `USE_BIOMETRIC` | 普通 | 28+ | 允许使用设备支持的硬件。 |
| `USE_FINGERPRINT` | 普通 | 23–28 | 允许使用设备指纹硬件。在 Android API 级别 28 中被 `USE_BIOMETRIC` 权限取代。 |
| `WAKE_LOCK` | 普通 | 1+ | 允许应用程序启动 `PowerManager` 唤醒锁，这可以阻止设备休眠或屏幕变暗。 |
| `WRITE_EXTERNAL_STORAGE` | 危险 | 4+ | 允许写入设备外部存储。同时授予 `READ_EXTERNAL_STORAGE` 权限。在 API 19 之后，在应用程序的作用域存储中进行读/写操作不需要此权限。截至 API 级别 29 强制实施作用域存储，此权限仅允许应用程序读取其自己的存储数据。 |

