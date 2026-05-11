# 系统管理的权限请求

如果你不喜欢权限询问过程与 Activity 紧密耦合——回想一下你必须在 Activity 类中实现 `onRequestPermissionsResult()` 方法——那么有一种使用回调注册机制的替代方案。要使用它，你必须首先在 `build.gradle` 文件中添加两个额外的依赖项：

```groovy
implementation "androidx.activity:" +
"activity-ktx:1.4.0"
implementation "androidx.fragment:" +
"fragment-ktx:1.4.1"
```

然后，在 Activity 的初始化部分，你编写以下代码：



```kotlin
import androidx.activity.result.contract.ActivityResultContracts
...
class MainActivity : AppCompatActivity() {
    private lateinit var requestPermissionLauncher: ActivityResultLauncher
    ....
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        ...
        requestPermissionLauncher =
            registerForActivityResult(
                ActivityResultContracts.RequestPermission()
            ) { isGranted: Boolean ->
                if (isGranted) {
                    // 权限已授予，继续在应用中进行相关操作。
                    ...
                } else {
                    // 某项功能需要用户已拒绝的权限，向用户说明该功能不可用。
                    ...
                }
            }
    }
}
```

**注意**

此注册必须在 Activity 进入 `ACTIVE` 状态*之前*完成。在 `create()` 内部执行此操作自然是个不错的主意。

要实际检查并可能获取权限，你需要在代码中添加如下内容：

```kotlin
if(checkSelfPermission(Manifest.permission.CAMERA) !=
    PackageManager.PERMISSION_GRANTED) {
    if(shouldShowRequestPermissionRationale(
        Manifest.permission.CAMERA)) {
        // 向用户说明为什么你的应用需要此权限。
        // 视图中应显示一个链接到
        //   requestPermissionLauncher.launch(
        //        Manifest.permission.CAMERA
        //   )
        // 的按钮
        ...
    } else {
        // 向系统请求权限。
        requestPermissionLauncher.launch(
            Manifest.permission.CAMERA
        )
    }
} else {
    // 使用需要该权限的 API。
    ....
}
```

与 `Activity` 类的唯一联系是 `requestPermissionLauncher` 变量，因此很容易将权限相关代码提取到单独的类中。

## 获取特殊权限

在某些情况下，使用 `ActivityCompat.requestPermissions()` 不足以获取像 `SYSTEM_ALERT_WINDOW` 或 `WRITE_SETTINGS` 这样的特殊权限。对于这两种权限以及可能的其他权限，你需要采用不同的方法。

权限 `WRITE_SETTINGS` 必须使用特殊的 `Intent` 来获取，如下所示：

```kotlin
val backFromSettingPerm = 6183  // 任意合适的常量
if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.M) {
    val activity = this
    if (!Settings.System.canWrite(activity)) {
        // 这只是一个建议：向用户显示一个关于特殊权限的特殊对话框。
        // 重要的是启动 Activity
        AlertDialog dialog =
            new AlertDialog.Builder(activity)
                .setTitle(...)
                .setMessage(...)
                .setPositiveButton("确定", { dialog, id ->
                    val intent = Intent(
                        Settings.ACTION_MANAGE_WRITE_SETTINGS)
                    intent.data = Uri.parse("package:" +
                        getPackageName())
                    activity.startActivityForResult(intent,
                        backFromSettingPerm)
                }).setNegativeButton("取消",
                    { dialog, id ->
                        // ...
                    })
                .create();
        dialog.show();
        systemWillAsk = true;
    }
} else {
    // 像处理其他权限一样处理...
}
```

完成该 `Intent` 后，可以使用回调方法 `onActivityResult()` 继续 GUI 流程：

```kotlin
override
protected fun onActivityResult(requestCode:Int,
                               resultCode:Int, data:Intent) {
    if ((requestCode and 0xFFFF) == backFromSettingPerm) {
        if (resultCode == Activity.RESULT_OK) {
            // 相应地操作...
        }
    }
}
```

对于 `SYSTEM_ALERT_WINDOW` 权限，你可能必须遵循相同的方法，但在创建 `Intent` 时需使用 `ACTION_MANAGE_OVERLAY_PERMISSION` 代替。

**注意**

对于此特殊的 `SYSTEM_ALERT_WINDOW` 权限，如果应用是从 Google Play 商店安装的，Google Play 商店将自动授予该权限。对于本地开发和测试，你必须按描述使用 `Intent`。另请注意，对于 Android 11，此设置的策略已更新；请参阅 [`developer.android.com/about/versions/11/privacy/permissions`](https://developer.android.com/about/versions/11/privacy/permissions)。

## 功能需求与权限

在第 2 章中，我们看到通过 `AndroidManifest.xml` 中的 `<uses-feature>` 元素，你可以指定应用将使用哪些功能。此信息对于 Google Play 商店确定应用发布后可在哪些设备上运行很重要。然而，如果你指定了此需求，还有另一个重要方面需要考虑：此类需求会隐含哪些权限，以及根据使用的 API 级别将如何处理这些权限？

功能常量和 API 级别并非严格相关——例如，`android.hardware.bluetooth` 功能是在 API 级别 8 中添加的，但相应的蓝牙 API 是在 API 级别 5 中添加的。因此，一些应用在能够使用 `<uses-feature>` 声明来声明需要该 API 之前，就已经能够使用该 API 了。为了弥补这种差异，Google Play 假设某些与硬件相关的权限默认指示了底层硬件功能是必需的。例如，使用蓝牙的应用必须在 `<uses-permission>` 元素中请求 `BLUETOOTH` 权限，而对于针对较旧 API 级别的应用，Google Play 假设该权限声明意味着应用需要底层的 `android.hardware.bluetooth` 功能。表 7-2 列出了此类隐含功能需求的权限。

请注意，`<uses-feature>` 声明的优先级高于表 7-2 中权限所隐含的功能。对于这些权限中的任何一个，你可以通过在 `<uses-feature>` 元素中显式声明隐含的功能，并添加 `android:required="false"` 属性，来禁用基于该隐含功能的过滤。例如，要禁用基于 `CAMERA` 权限的任何过滤，你需要将其添加到清单文件中：

**表 7-2** 隐含功能需求的权限

| 类别 | 权限 | …隐含的功能 |
| --- | --- | --- |
| 蓝牙 | `BLUETOOTH` | `android.hardware.bluetooth` |
| | `BLUETOOTH_ADMIN` | `android.hardware.bluetooth` |
| 相机 | `CAMERA` | `android.hardware.camera` 和 `android.hardware.camera.autofocus` |
| 位置 | `ACCESS_MOCK_LOCATION` | `android.hardware.location` |
| | `ACCESS_LOCATION_EXTRA_COMMANDS` | `android.hardware.location` |
| | `INSTALL_LOCATION_PROVIDER` | `android.hardware.location` |
| | `ACCESS_COARSE_LOCATION` | `android.hardware.location` `android.hardware.location.network` (API 级别 < 21) |
| | `ACCESS_FINE_LOCATION` | `android.hardware.location` `android.hardware.location.gps` (API 级别 < 21) |
| 麦克风 | `RECORD_AUDIO` | `android.hardware.microphone` |
| 电话 | `CALL_PHONE` | `android.hardware.telephony` |
| | `CALL_PRIVILEGED` | `android.hardware.telephony` |
| | `MODIFY_PHONE_STATE` | `android.hardware.telephony` |
| | `PROCESS_OUTGOING_CALLS` | `android.hardware.telephony` |
| | `READ_SMS` | `android.hardware.telephony` |
| | `RECEIVE_SMS` | `android.hardware.telephony` |
| | `RECEIVE_MMS` | `android.hardware.telephony` |
| | `RECEIVE_WAP_PUSH` | `android.hardware.telephony` |
| | `SEND_SMS` | `android.hardware.telephony` |
| | `WRITE_APN_SETTINGS` | `android.hardware.telephony` |
| | `WRITE_SMS` | `android.hardware.telephony` |
| Wi-Fi | `ACCESS_WIFI_STATE` | `android.hardware.wifi` |
| | `CHANGE_WIFI_STATE` | `android.hardware.wifi` |
| | `CHANGE_WIFI_MULTICAST_STATE` | `android.hardware.wifi` |

## 使用终端处理权限

要查看设备上已注册的权限，你可以浏览系统设置应用中的应用列表，或者更简单的方式是，使用 ADB shell 在终端中获取各种与权限相关的信息。

为此，将硬件设备通过 USB 连接到你的笔记本电脑或 PC，打开终端，`cd` 到 SDK 安装目录中的 `platform-tools` 文件夹，在以下命令中查找你的设备：

```bash
./adb devices
```

然后输入：

```bash
./adb shell -s
```



