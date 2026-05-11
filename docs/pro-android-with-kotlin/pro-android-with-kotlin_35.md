# 保护 Android 应用中的敏感数据

在应用开发过程中，保护敏感数据是一项重要任务。随着手持设备上用于银行、通讯等敏感日常任务的应用程序日益增多，安全性在过去一段时间里变得越来越重要，并且未来也将持续如此。作为开发者，你必须顺应这一演变趋势，采取一切可能的预防措施，负责任地处理应用用户的数据。

全面处理每一个可能的安全方面是一项具有挑战性的任务，单凭这一点就足以写满一整本书。幸运的是，有大量在线资源可供你查阅，以获取 Android 操作系统安全相关的最新信息。只需注意筛选掉不恰当的信息即可。一个不错的起点是 Android 操作系统在线资源中与安全相关的主题——截至 2022 年年中，相关的两个页面 URL 是 [`https://developer.android.com/design-for-safety#security`](https://developer.android.com/design-for-safety%2523security) 和 [`https://developer.android.com/guide/topics/permissions/overview`](https://developer.android.com/guide/topics/permissions/overview)。如果你在阅读本书时这些链接失效了，可以在你常用的搜索引擎中输入“Android best practices security”和“Android best practices permissions”，你就能轻松找到这些资源。

话虽如此，我们仍然需要深入探讨 Android 操作系统内部的*权限*系统，因为一旦你的应用涉及敏感数据，作为开发者，这绝对是你必须熟练掌握的部分。权限通过使用预定义的权限或自行定义权限，为使用系统数据和功能增加了安全性。在 `AndroidManifest.xml` 中，你可以添加自定义权限，并声明你的应用想要使用的权限。根据权限类型的不同，你必须在代码中添加对话框，告知用户需要哪些权限，并请求用户确认以访问受保护的资源或功能。

## 权限类型

根据所需的保护级别，权限有几种不同的类型：

*   **正常**：此级别对应低级别的安全敏感信息。系统会自动授予已安装应用此类权限，而无需明确询问用户，但该权限会在应用安装前列在包描述中。并且可以通过使用系统设置应用按需显式查询。

*   **危险**：此级别对应高级别的安全敏感信息。在运行时，系统会询问用户是否允许使用该权限。一旦某个应用被允许，该授权将被保存，用户可能不会再被询问，直到应用被重新安装，或者通过使用系统设置应用显式撤销该权限。

*   **签名**：此级别对应极高安全级别的敏感信息。只有使用与定义该权限的应用相同的证书签名的应用才能获取该权限。系统会检查签名是否匹配，然后自动授予权限。此级别仅对由同一开发者开发的一组应用有意义。

*   **特殊**：仅在少数用例中，系统通过非标准获取方法授予对某些系统资源的访问权限。这类特殊权限由平台定义。

*   **特权**或**系统**：仅用于系统映像应用。你通常不应该也不需要使用它们。

权限被归入权限组。其背后的理念是，一旦用户接受了来自 G1 组权限 A 的请求，就无需再对同一 G1 组的另一个权限 B 进行权限查询。从用户体验的角度来看，只有当我们谈论*危险*类型权限时，权限组才会产生影响——*正常*权限的权限组没有影响。

> **注意**
>
> 权限到权限组的映射可能会随着 Android 的未来版本而改变——因此你的应用不应依赖这种映射。从开发的角度来看，你应该忽略权限组，除非你定义了自己的权限和权限组。

## 定义权限

Android 操作系统包含许多由各种内置应用或操作系统本身定义的权限。此外，作为开发者，你可以定义自己的权限来保护应用或应用的部分功能。你在 `AndroidManifest.xml` 中定义此类权限：

```
<permission android:name="string"
            android:protectionLevel=["normal" | "dangerous" | "signature" | ...]
            android:label="string"
            android:icon="drawable resource"
            android:description="string" />
```

这些属性的含义在在线文本伴侣的“清单顶级条目”部分有描述。至少，你必须提供 `name` 和 `protectionLevel` 属性，但添加 `label` 和/或 `icon` 以及描述来帮助用户理解该权限的作用，当然也是个好主意。

如果你需要将权限分组，可以使用以下两种方法之一：

1.  使用 `<permission-group>` 元素，并向 `<permission>` 添加 `permissionGroup` 属性——参见在线文本伴侣中的“清单顶级条目”部分。

2.  使用 `<permission-tree>` 元素并相应地为你的权限命名——再次参见在线文本伴侣中的“清单顶级条目”部分。

如果你随后获取了某个组的权限，则同一组中的同级权限将被隐式地包含在授权中。

> **警告**
>
> 为了遵守安全准则并使你的应用设计清晰稳定，请将自己定义的权限数量保持在绝对最低限度。

## 使用权限

要使用权限，请在 `AndroidManifest.xml` 文件中添加一个或多个 `<uses-permission>` 元素：

```
<uses-permission android:name="string"
                 android:maxSdkVersion="integer" />
```

`name` 属性指定权限名称，`maxSdkVersion` 指定此权限要求将生效的最高 API 级别。引入这个特殊的 `<uses-permission-sdk-23>` 元素的原因是 Android 6.0 中权限语义的重大变更。

问题是：我们如何知道自己的应用具体需要哪些权限？答案有三部分：

*   Android Studio 会告诉你应用需要某个权限。例如，如果你编写：

```kotlin
val uri = CallLog.Calls.CONTENT_URI
val cursor = contentResolver.query(
uri, null, null, null, null)
```

Android Studio 会告诉你需要某个特定的权限——见图 7-1。

*   在开发和测试期间，你的应用崩溃了，在日志中你会看到类似这样的条目：

```
Caused by: java.lang.SecurityException: Permission
Denial: opening provider
com.android.providers.contacts.CallLogProvider
from
ProcessRecord{faeda9c 4127:de.pspaeth.cp1/u0a96}
(pid=4127, uid=10096) requires
android.permission.READ_CALL_LOG or
android.permission.WRITE_CALL_LOG
```

*   系统权限列表会告诉你，对于某个特定任务需要某个特定的权限。参见表 7-1（未详尽列出）。

为了强制某个 Activity 或整个应用受到自定义权限的保护，你可以在 `permission` 属性中声明该权限，如下所示：

```xml
<activity android:name=".MyActivity"
          android:permission="com.example.myapp.PERMISSION_NAME"
          ...>
</activity>
```

或者在 `<application>` 元素内部进行类似声明。

### 图 7-1
Android Studio 的界面，其中包含几行代码。它表示了权限要求。

*图 7-1：Android Studio 提示权限要求*（图片未显示）

### 表 7-1：系统权限

| 权限 | 组 | 描述 |
| --- | --- | --- |
| ... | ... | ... |



`READ_CALENDAR` | `CALENDAR` | 允许读取日历。清单条目：`android.permission.READ_CALENDAR`

`WRITE_CALENDAR` | `CALENDAR` | 允许写入日历。清单条目：`android.permission.READ_CALENDAR`

`CAMERA` | `CAMERA` | 允许访问摄像头。清单条目：`android.permission.CAMERA`

`READ_CONTACTS` | `CONTACTS` | 从联系人表格中读取。清单条目：`android.permission.READ_CONTACTS`

`WRITE_CONTACTS` | `CONTACTS` | 写入联系人表格。清单条目：`android.permission.WRITE_CONTACTS`

`GET_ACCOUNTS` | `CONTACTS` | 允许从*帐户服务*中列出账户。清单条目：`android.permission.GET_ACCOUNTS`

`ACCESS_FINE_LOCATION` | `LOCATION` | 允许应用访问精确位置。清单条目：`android.permission.ACCESS_FINE_LOCATION`

`ACCESS_COARSE_LOCATION` | `LOCATION` | 允许应用访问大致位置。清单条目：`android.permission.ACCESS_COARSE_LOCATION`

`RECORD_AUDIO` | `MICROPHONE` | 允许录音。清单条目：`android.permission.RECORD_AUDIO`

`READ_PHONE_STATE` | `PHONE` | 允许读取电话状态（设备的电话号码、当前蜂窝网络信息、任何正在进行的通话状态，以及设备上注册的 `PhoneAccounts` 列表）。清单条目：`android.permission.READ_PHONE_STATE`

`READ_PHONE_NUMBERS` | `PHONE` | 允许读取设备的电话号码。清单条目：`android.permission.READ_PHONE_NUMBERS`

`CALL_PHONE` | `PHONE` | 允许应用在不通过拨号器用户界面的情况下发起电话呼叫。清单条目：`android.permission.CALL_PHONE`

`ANSWER_PHONE_CALLS` | `PHONE` | 允许接听来电。清单条目：`android.permission.ANSWER_PHONE_CALLS`

`READ_CALL_LOG` | `PHONE` | 从通话记录表格中读取。清单条目：`android.permission.READ_CALL_LOG`

`WRITE_CALL_LOG` | `PHONE` | 写入通话记录表格。清单条目：`android.permission.WRITE_CALL_LOG`

`ADD_VOICEMAIL` | `PHONE` | 允许添加语音邮件。清单条目：`com.android.voicemail.permission.ADD_VOICEMAIL`

`USE_SIP` | `PHONE` | 允许使用 SIP 服务。清单条目：`android.permission.USE_SIP`

`PROCESS_OUTGOING_CALLS` | `PHONE` | 允许应用在拨出电话时查看正在拨打的号码，并可选择将呼叫重定向到另一个号码或中止呼叫。清单条目：`android.permission.PROCESS_OUTGOING_CALLS`

`BODY_SENSORS` | `SENSORS` | 允许应用访问用户用于测量体内状况的传感器数据。清单条目：`android.permission.BODY_SENSORS`

`SEND_SMS` | `SMS` | 允许发送短信。清单条目：`android.permission.SEND_SMS`

`RECEIVE_SMS` | `SMS` | 允许接收短信。清单条目：`android.permission.RECEIVE_SMS`

`READ_SMS` | `SMS` | 允许读取短信。清单条目：`android.permission.READ_SMS`

`RECEIVE_WAP_PUSH` | `SMS` | 允许接收 WAP 推送消息。清单条目：`android.permission.RECEIVE_WAP_PUSH`

`RECEIVE_MMS` | `SMS` | 允许接收彩信。清单条目：`android.permission.RECEIVE_MMS`

`READ_EXTERNAL_STORAGE` | `STORAGE` | 允许从外部存储读取。仅在 API 级别低于 19 时需要。清单条目：`android.permission.READ_EXTERNAL_STORAGE`

`WRITE_EXTERNAL_STORAGE` | `STORAGE` | 允许写入外部存储。仅在 API 级别低于 19 时需要。清单条目：`android.permission.WRITE_EXTERNAL_STORAGE`

## 获取权限

对“危险”权限的询问发生在应用运行时。这使得权限系统相当灵活——你的应用用户可能永远不使用其中的某些部分，因此不请求不必要的权限可以避免无谓的困扰。这种运行时方法的缺点是需要更多的编程工作——权限询问必须包含在你的代码中。为此，在需要权限之前的任何合适位置，你可以添加以下代码：

```kotlin
val activity = this
val perm = Manifest.permission.CAMERA
val cameraPermReturnId = 7239 // 任何合适的常量
val permissionCheck = ContextCompat.checkSelfPermission(
activity, perm)
if (permissionCheck !=
PackageManager.PERMISSION_GRANTED) {
// 是否应向用户显示解释？
if (ActivityCompat.
shouldShowRequestPermissionRationale(
activity, perm)) {
// 向用户显示解释
// *异步*——不要阻塞
// 此线程等待用户响应！
// 用户看完解释后，再次尝试请求
// 权限。
val dialog = AlertDialog.Builder(activity) ...
.create()
dialog.show()
} else {
// 无需解释，可以直接请求
// 权限。
ActivityCompat.requestPermissions(activity,
arrayOf(perm), cameraPermReturnId)
// cameraPermReturnId 是一个应用定义的
// int 常量。回调方法将获取
// 请求的结果。
}
}
```

这段代码执行以下操作：

*   首先，我们检查权限是否已被授予。如果之前已授予权限，除非应用被重新安装或权限被明确撤销，否则不会再次询问用户。
*   `ActivityCompat.shouldShowRequestPermissionRationale()` 检查是否应向用户显示理由。其背后的理念是，如果用户多次拒绝了权限询问请求，可能是因为他们不了解该权限的必要性。在这种情况下，应用有机会向用户说明更多关于该权限需求的信息。`shouldShowRequestPermissionRationale()` 返回 `true` 的频率取决于 Android 操作系统。这里的示例显示了一个对话框。你当然可以根据需要采取任何方式来告知用户。
*   `ActivityCompat.requestPermissions(...)` 最终执行权限询问。这是异步执行的，因此该调用会立即返回。

一旦调用 `ActivityCompat.requestPermissions(...)`，Android 操作系统会在你的应用之外询问用户是否授予该权限。其结果将通过一个异步回调方法返回，如下所示：

```kotlin
override
fun onRequestPermissionsResult(
requestCode: Int, permissions: Array,
grantResults: IntArray) {
when (requestCode) {
cameraPermReturnId -> {
// 如果请求被取消，结果
// 数组为空。这里我们知道它
// 只能有一个条目
if ((grantResults.isNotEmpty()
&& grantResults[0] ==
PackageManager.PERMISSION_GRANTED)) {
// 权限已被授予
// 进行相应处理...
} else {
// 权限被拒绝
// 进行相应处理...
}
return
}
// 添加其他 'when' 分支以检查
// 此应用可能请求的其他权限。
else -> {
// 忽略所有其他请求。
// 或者做任何你认为合适的处理。
}
}
}
```

此方法需要在 `android.content.Activity` 类内部实现。在其他上下文中这是不可行的。

