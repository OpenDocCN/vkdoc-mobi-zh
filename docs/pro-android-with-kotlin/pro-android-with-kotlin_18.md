# Android 广播

Android 广播是遵循发布-订阅模式的消息。它们在整个 Android 操作系统中发送，其内部机制由 Android 系统隐藏，因此发布者和订阅者都只能看到一个用于发送和接收消息的简洁异步接口。广播可以由 Android 系统本身、标准应用以及系统上安装的任何其他应用发布。同样，任何应用都可以被配置或编程为接收其感兴趣的广播消息。与活动类似，广播可以是显式或隐式路由的——具体使用哪种方式由广播发送者决定。

广播接收器可以在 `AndroidManifest.xml` 文件中声明，也可以通过编程方式声明。从 Android 8.0 或 API 级别 26 开始，Android 开发者放弃了在 XML 和编程方式声明隐式 `Intent` 的广播接收器之间通常存在的对称性。原因是出于对后台运行进程施加限制的总体思路——这类进程，尤其是与广播相关的进程，会导致 Android 系统负载过高，使设备运行速度显著变慢，并导致用户体验不佳。因此，现在在 `AndroidManifest.xml` 内部声明广播接收器仅限于较少的用例。请参见下一节“隐式广播”。

> **注意**
>
> 我们希望编写也能在 Android 8.0 及更高版本上运行的现代应用。因此，请认真对待隐式 `Intent` 的这些广播限制，让您的应用适应这些限制。

## 显式广播

显式广播是一种以明确指定唯一接收器的方式发布的广播。这通常仅在广播发布者和订阅者属于同一个应用时才有意义，或者较少情况下，当它们属于同一个应用集合且彼此之间存在强功能依赖关系时才有意义。

此外，本地广播和远程广播之间存在差异：本地广播接收器*必须*位于同一个应用中，它们运行速度快，并且不能在 `AndroidManifest.xml` 内部声明。相反，必须使用编程注册方法来注册本地广播接收器。此外，你必须使用

```
// 发送本地广播
LocalBroadcastManager.getInstance(Context).
sendBroadcast(...)
```

来发送本地广播消息。另一方面，远程广播接收器*可以*位于同一个应用中，它们速度较慢，并且可以使用 `AndroidManifest.xml` 来声明它们。要发送远程广播，你可以编写

```
// 发送远程广播（此应用或其他应用）
sendBroadcast(...)
```

> **注意**
>
> 出于性能原因，应优先选择本地广播而非远程广播。不能使用 `AndroidManifest.xml` 声明本地接收器这一明显缺点影响并不大，因为从 Android 8.0 或 API 级别 26 开始，在清单文件中声明广播接收器的用例无论如何都已经受到限制了。

### 显式本地广播

要向同一应用内的本地广播接收器发送本地广播消息，你可以编写

```
val intent = Intent(this, MyReceiver::class.java)
intent.action = "de.pspaeth.simplebroadcast.DO_STH"
intent.putExtra("myExtra", "myExtraVal")
Log.e("LOG", "Sending broadcast")
LocalBroadcastManager.getInstance(this).
sendBroadcast(intent)
Log.e("LOG", "Broadcast sent")
```

其中 `MyReceiver` 是接收器类：

```
class MyReceiver : BroadcastReceiver() {
override
fun onReceive(context: Context?, intent: Intent?) {
Toast.makeText(context, "Intent Detected.",
Toast.LENGTH_LONG).show()
Log.e("LOG", "Received broadcast")
Thread.sleep(3000)
// 当然也可以是实际工作...
Log.e("LOG", "Broadcast done")
}
}
```

对于本地广播，接收器*必须*在代码内部声明。为避免资源泄漏，我们在 `onCreate()` 中创建并注册接收器，并在 `onDestroy()` 中注销它：

```
class MainActivity : AppCompatActivity() {
private var bcReceiver:BroadcastReceiver? = null
override fun onCreate(savedInstanceState: Bundle?) {
super.onCreate(savedInstanceState)
// ...
bcReceiver = MyReceiver()
val ifi:IntentFilter =
IntentFilter("de.pspaeth.myapp.DO_STH")
LocalBroadcastManager.getInstance(this).
registerReceiver(bcReceiver, ifi)
}
override fun onDestroy() {
super.onDestroy()
// ...
LocalBroadcastManager.getInstance(this).
unregisterReceiver(bcReceiver)
}
}
```

### 显式远程广播

我们已经指出，可以将*远程*类型的广播消息发送到其他应用或接收器所在的同一应用。区别在于数据的发送方式——对于远程消息，数据通过 IPC 通道传输。现在，要将此类远程广播消息发送到同一应用，你可以编写

```
val intent = Intent(this, MyReceiver::class.java)
intent.action = "de.pspaeth.myapp.DO_STH"
intent.putExtra("myExtra", "myExtraVal")
sendBroadcast(intent)
```

在接收端，对于远程消息，接收器*必须*在清单文件中声明：

```
<receiver android:name=".MyReceiver" />
```

审视本地广播和远程广播之间的差异时，牢记以下几点会很有帮助：

-   **显式本地广播**  
    发送者使用显式接收器类，接收器必须通过编程方式声明，并且发送者和接收者都使用 `LocalBroadcastManager` 来发送消息和注册接收器。

-   **显式远程广播**  
    发送者使用显式接收器类，接收器必须在 `AndroidManifest.xml` 中声明。

对于负责处理已接收广播的类，与显式本地广播的类相比没有区别：

```
class MyReceiver : BroadcastReceiver() {
override
fun onReceive(context: Context?, intent: Intent?) {
// 处理传入的广播...
}
}
```

### 发送到其他应用的显式广播

显式广播的发送者和接收者可以位于不同的应用中。为了使其工作，我们不能再使用之前使用的 Intent 构造器：

```
val intent = Intent(this, MyReceiver::class.java)
intent.action = "de.pspaeth.myapp.DO_STH"
// 添加其他参数...
sendBroadcast(intent)
```

这是因为接收类（此处为 `MyReceiver`）不在类路径中。然而，我们可以使用另一种构造方式

```
val intent = Intent()
intent.component = ComponentName("de.pspaeth.xyz",
"de.pspaeth.xyz.MyReceiver")
intent.action = "de.pspaeth.simplebroadcast.DO_STH"
// 添加其他参数...
sendBroadcast(intent)
```

其中，`ComponentName` 的第一个参数是接收方应用的包名字符串，第二个参数是该应用中的类名。

> **警告**
>
> 除非你是在向同样由你自己构建的应用发送广播，否则这种发送显式广播的方式用途有限。其他应用的开发者可能很容易决定更改类名，从而导致你与该应用之间使用广播进行的通信中断。

## 隐式广播

隐式广播是具有未定义数量潜在接收器的广播。对于显式广播，我们了解到必须使用指向接收组件的构造器来构建相应的 `Intent`：`val intent = Intent(this, TheReceiverClass::class.java)`。与此相反，对于隐式广播，我们不再指定接收者，而是提供哪些组件可能对其感兴趣的提示。例如，在

```
val intent = Intent()
intent.action = "de.pspaeth.myapp.DO_STH"
sendBroadcast(intent)
```

中，我们实际上表达了以下含义：“向所有对动作 'de.pspaeth.myapp.DO_STH' 感兴趣的接收器发送一条广播消息。”然后，Android 系统会确定哪些组件有资格接收此类广播消息——这可能不会找到接收者，也可能找到一个或许多个实际接收者。

在开始编写隐式广播程序之前，你必须做出三项决定：

1.  **我们是否想要监听系统广播？**



Android 预定义了大量的广播消息类型。在您随 Android Studio 一起安装的 Android SDK 中，更确切地说，在`SDK_INST_DIR/platforms/VERSION/data/broadcast_actions.txt`路径下，您可以找到系统广播操作的列表。如果我们想要监听此类消息，只需按照下文“接收隐式广播”和“监听系统广播”章节所述，编写相应的广播接收器即可。在在线文本伴读的“系统广播”章节中，您将找到系统广播的完整列表。

## 如何对广播消息类型进行分类？

广播发送者和广播接收者基于 Intent 过滤器匹配来加入，这与 Activity 的方式相同，如第 3 章“Intent 过滤器”部分所述。广播的分类同样分为三类：首先，您有一个必须的“操作”说明符；其次，一个“类别”说明符；第三，一个“数据和类型”说明符。您可以使用这些来定义过滤器。我们将在下文描述此匹配过程。

## 我们使用本地广播还是远程广播？

如果所有广播都完全在您的应用内部发生，您应该使用本地广播来发送和接收消息。对于隐式广播，这种情况可能不常见，但对于大型复杂应用，这完全是可以接受的。如果涉及系统广播或来自其他应用的广播，则*必须*使用远程广播。后者是大多数示例中的默认情况，因此您会经常看到这种模式。

## Intent 过滤器匹配

广播接收器通过声明*操作*、*类别*和*数据说明符*来表示它们接受哪些广播。

我们首先讨论*操作*。从表面上看，它们只是一些字符串，没有任何语法限制。但更仔细地观察，我们会发现它们首先是一组或多或少严格定义的预定义操作名称。我们已将它们全部列在第 3 章的“Intent 过滤器”部分。此外，您也可以定义自己的操作。惯例是使用您的包名，加上一个点号，然后跟上操作说明符。您不必强制遵守此惯例，但强烈建议这样做，这样您的应用就不会与其他应用冲突。在不指定其他过滤器条件的情况下，指定了与过滤器中特定操作相匹配的发送者将到达所有匹配的接收器：

- 要使 Intent 过滤器匹配，接收方指定的*操作*必须与发送方指定的*操作*匹配。对于隐式广播，一个广播可能寻址零个、一个或多个接收器。
- 一个接收器可以指定多个过滤器。如果其中一个过滤器包含指定的*操作*，则该特定过滤器将匹配该广播。

示例如表 5-1 所示。

**表 5-1 操作匹配**

| 接收器 | 发送器 | 匹配 |
| :--- | :--- | :--- |
| 单个过滤器 操作 = `com.xyz.ACTION1` | 操作 = `com.xyz.ACTION1` | 是 |
| 单个过滤器 操作 = `com.xyz.ACTION1` | 操作 = `com.xyz.ACTION2` | 否 |
| 两个过滤器 操作 = `com.xyz.ACTION1` 操作 = `com.xyz.ACTION2` | 操作 = `com.xyz.ACTION1` | 是 |
| 两个过滤器 操作 = `com.xyz.ACTION1` 操作 = `com.xyz.ACTION2` | 操作 = `com.xyz.ACTION3` | 否 |

除了*操作*之外，还可以使用*类别*说明符来限制 Intent 过滤器。我们有一些预定义的类别，列在第 3 章的“Intent 过滤器”部分，但您同样可以定义自己的类别。并且与*操作*一样，对于您自己的*类别*，您应该遵循命名惯例，即在类别名称前加上应用包名。在 Intent 匹配过程中，一旦找到*操作*的匹配项，发送方声明的所有类别也必须出现在接收方的 Intent 过滤器中，匹配才能继续。

- 一旦 Intent 过滤器中的*操作*与广播匹配，并且该过滤器也包含发送方指定的类别列表，则只有该过滤器会匹配该广播。

示例如表 5-2 所示（仅一个过滤器——如果有多个过滤器，则匹配基于“或”的方式进行）。

**表 5-2 类别匹配**

| 接收方操作 | 接收方类别 | 发送方 | 匹配 |
| :--- | :--- | :--- | :--- |
| `com.xyz.ACT1` | `com.xyz.cate1` | 操作 = `com.xyz.ACT1` | 是 |
| `com.xyz.ACT1` | - | 操作 = `com.xyz.ACT1` 类别 = `com.xyz.cate1` | 否 |
| `com.xyz.ACT1` | `com.xyz.cate1` | 操作 = `com.xyz.ACT1` 类别 = `com.xyz.cate1` | 是 |
| `com.xyz.ACT1` | `com.xyz.cate1` | 操作 = `com.xyz.ACT1` 类别 = `com.xyz.cate1` 类别 = `com.xyz.cate2` | 否 |
| `com.xyz.ACT1` | `com.xyz.cate1` `com.xyz.cate2` | 操作 = `com.xyz.ACT1` 类别 = `com.xyz.cate1` 类别 = `com.xyz.cate2` | 是 |
| `com.xyz.ACT1` | `com.xyz.cate1` `com.xyz.cate2` | 操作 = `com.xyz.ACT1` 类别 = `com.xyz.cate1` | 是 |
| `com.xyz.ACT1` | 任意 | 操作 = `com.xyz.ACT2` 类别 = 任意 | 否 |

最后，*数据和类型*说明符允许过滤数据类型。这样的说明符可以是以下之一：

- **类型**：MIME 类型，例如 `text/html` 或 `text/plain`
- **数据**：数据 URI，例如 `http://xyz.com/type1`
- **数据**和**类型**：两者皆有

其中`data`元素允许通配符匹配。详细信息见“接收隐式广播”部分。

- 假定*操作*和*类别*匹配，如果发送方指定的 MIME 类型包含在接收方允许的 MIME 类型列表中，则*类型*过滤器元素匹配。
- 假定*操作*和*类别*匹配，如果发送方指定的数据 URI 与接收方允许的数据 URI 列表中的任何一个匹配（可能应用通配符匹配），则*数据*过滤器元素匹配。
- 假定*操作*和*类别*匹配，如果 MIME 类型和数据 URI 都匹配，即都包含在接收方指定的列表中，则*数据和类型*过滤器元素匹配。

示例如表 5-3 所示（仅一个过滤器——如果有多个过滤器，则匹配基于“或”的方式进行）。

**表 5-3 数据匹配**

| 接收方类型 | 接收方 URI (`.*` = 任意字符串) | 发送方 | 匹配 |
| :--- | :--- | :--- | :--- |
| `text/html` | - | 类型 = `text/html` | 是 |
| `text/html` `text/plain` | - | 类型 = `text/html` | 是 |
| `text/html` `text/plain` | - | 类型 = `image/jpeg` | 否 |
| - | `http://a.b.c/xyz` | 数据 = `http://a.b.c/xyz` | 是 |
| - | `http://a.b.c/xyz` | 数据 = `http://a.b.c/qrs` | 否 |
| - | `http://a.b.c/xyz/.*` | 数据 = `http://a.b.c/xyz/3` | 是 |
| - | `http://.*/xyz` | 数据 = `http://a.b.c/xyz` | 是 |
| - | `http://.*/xyz` | 数据 = `http://a.b.c/qrs` | 否 |
| `text/html` | `http://a.b.c/xyz/.*` | 类型 = `text/html` 数据 = `http://a.b.c/xyz/1` | 是 |
| `text/html` | `http://a.b.c/xyz/.*` | 类型 = `image/jpeg` 数据 = `http://a.b.c/xyz/1` | 否 |



### 活跃监听与待命监听

问题在于，一个应用必须处于何种状态才能接收隐式广播。如果我们希望广播接收器仅在系统中注册，并且只在匹配的广播到达时按需启动，则必须在应用的清单文件中指定该监听器。然而，对于隐式广播，这不能随意进行，只能针对“监听系统广播”一节中列出的预定义系统广播进行。

**注意：**  
此对隐式意图过滤器的限制是在 Android 8.0（即 API 级别 26）中引入的。在此之前，可以在清单文件中指定任何隐式过滤器。

但是，如果你在应用内部以编程方式启动广播监听器，并且该应用正在运行，则可以定义任意数量的隐式广播监听器，并且对于广播是来自系统、你的应用还是其他应用没有限制。同样，对可用的*操作*或*类别*名称也没有限制。

由于监听开机完成事件被包含在清单文件允许的监听器列表中，因此你可以自由地在其中以 Activity 或 Service 形式启动应用，并在这些应用内部注册任何隐式监听器。这意味着你可以合法地规避从 Android 8.0 开始施加的限制。只需注意，如果出现资源短缺，此类应用可能会被 Android 操作系统杀死，因此你必须采取适当的预防措施。

### 发送隐式广播

要准备发送隐式广播，你需要指定操作、类别、数据和附加数据，如下所示：

```
val intent = Intent()
intent.action = "de.pspaeth.myapp.DO_STH"
intent.addCategory("de.pspaeth.myapp.CATEG1")
intent.addCategory("de.pspaeth.myapp.CATEG2")
// ... 更多类别
intent.type = "text/html"
intent.data = Uri.parse("content://myContent")
intent.putExtra("EXTRA_KEY", "extraVal")
intent.flags = ...
```

只有操作是强制性的；其他所有项都是可选的。现在，要发送广播，你可以编写

```
sendBroadcast(intent)
```

用于远程广播，或者

```
LocalBroadcastManager.getInstance(this).sendBroadcast(intent)
```

用于本地广播。其中的“this”必须是 `Context` 或其子类——如果代码来自 Activity 或 Service 类内部，它将完全按此处所示的方式工作。

对于远程消息，还有一个变体，可以一次将广播发送给一个适用的接收器：

```
...
sendOrderedBroadcast(...)
...
```

这使得接收器顺序接收消息，并且每个接收器可以通过使用 `BroadcastReceiver.abortBroadcast()` 来取消将消息转发给队列中的下一个接收器。

### 接收隐式广播

要接收隐式广播，对于有限的一组广播类型（参见“监听系统广播”一节），你可以在 `AndroidManifest.xml` 中指定一个 `BroadcastListener`，如下所示：

```
...

```

此处显示的 `<data>` 元素只是一个例子——所有可能性在第 3 章的“意图过滤器”部分中都有介绍。

相比之下，在你的代码中以编程方式添加隐式广播监听器则不受限制：

```
class MainActivity : AppCompatActivity() {
private var bcReceiver:BroadcastReceiver? = null
override fun onCreate(savedInstanceState: Bundle?) {
super.onCreate(savedInstanceState)
// ...
bcReceiver = MyReceiver()
val ifi:IntentFilter =
IntentFilter("de.pspaeth.myapp.DO_STH")
registerReceiver(bcReceiver, ifi)
}
override fun onDestroy() {
super.onDestroy()
// ...
unregisterReceiver(bcReceiver)
}
}
```

此处显示的 `MyReceiver` 是 `android.content.BroadcastReceiver` 类的一个实现。

### 监听系统广播

要监听系统广播——请参阅在线文本配套资料的“系统广播”部分中的列表——你可以像之前展示的那样使用编程注册。对于大多数系统广播，你不能使用清单文件注册方式，这是由于自 Android 8.0（即 API 级别 26）以来对后台执行施加的限制。然而，对于其中一些广播，你也可以使用清单文件来指定监听器：

- **`ACTION_LOCKED_BOOT_COMPLETED`**、**`ACTION_BOOT_COMPLETED`**：应用可能需要这些来安排作业、闹钟等。
- **`ACTION_USER_INITIALIZE`**、**`"android.intent.action.USER_ADDED"`**、**`"android.intent.action.USER_REMOVED"`**：由特权权限保护，因此用例有限。
- **`"android.intent.action.TIME_SET"`**、**`ACTION_TIMEZONE_CHANGED`**、**`ACTION_NEXT_ALARM_CLOCK_CHANGED`**：时钟应用所需。
- **`ACTION_LOCALE_CHANGED`**：语言区域已更改，应用可能需要在此时更新其数据。
- **`ACTION_USB_ACCESSORY_ATTACHED`**、**`ACTION_USB_ACCESSORY_DETACHED`**、**`ACTION_USB_DEVICE_ATTACHED`**、**`ACTION_USB_DEVICE_DETACHED`**：USB 相关事件。
- **`ACTION_CONNECTION_STATE_CHANGED`**、**`ACTION_ACL_CONNECTED`**、**`ACTION_ACL_DISCONNECTED`**：蓝牙事件。
- **`ACTION_CARRIER_CONFIG_CHANGED`**、**`TelephonyIntents.ACTION__SUBSCRIPTION_CHANGED`**、**`TelephonyIntents.SECRET_CODE_ACTION`**：OEM 电话应用可能需要接收这些广播。
- **`LOGIN_ACCOUNTS_CHANGED_ACTION`**：某些应用需要用它来为新建和已更改的账户设置计划操作。
- **`ACTION_PACKAGE_DATA_CLEARED`**：由操作系统的“设置”应用清除数据。正在运行的应用可能对此感兴趣。
- **`ACTION_PACKAGE_FULLY_REMOVED`**：如果某些应用被卸载且其数据被移除，可能需要通知相关应用。
- **`ACTION_NEW_OUTGOING_CALL`**：拦截拨出的电话。
- **`ACTION_DEVICE_OWNER_CHANGED`**：某些应用可能需要接收它，以便了解设备的安全状态已更改。
- **`ACTION_EVENT_REMINDER`**：由日历提供程序发送，以将事件提醒发布到日历应用。
- **`ACTION_MEDIA_MOUNTED`**、**`ACTION_MEDIA_CHECKING`**、**`ACTION_MEDIA_UNMOUNTED`**、**`ACTION_MEDIA_EJECT`**、**`ACTION_MEDIA_UNMOUNTABLE`**、**`ACTION_MEDIA_REMOVED`**、**`ACTION_MEDIA_BAD_REMOVAL`**：应用可能需要了解用户与设备的物理交互。
- **`SMS_RECEIVED_ACTION`**、**`WAP_PUSH_RECEIVED_ACTION`**：短信接收应用所需。

### 为广播增加安全性

广播消息的安全性由*权限*系统处理，这将在第 7 章中更详细地描述。在以下章节中，我们区分显式广播和隐式广播。

#### 保护显式广播

对于非本地广播，即不使用 `LocalBroadcastManager`，可以在接收方*和*发送方两侧指定权限。对于后者，广播发送方法有包含权限说明符的重载版本：

```
...
val intent = Intent(this, MyReceiver::class.java)
...
sendBroadcast(intent, "com.xyz.theapp.PERMISSION1")
...
```

这表示向受 `"com.xyz.theapp.PERMISSION1"` 保护的接收器发送广播。当然，你应该在此处写入你自己的包名并使用适当的权限名称。

相比之下，不指定权限发送广播

```
...
val intent = Intent(this, MyReceiver::class.java)
...
sendBroadcast(intent)
...
```

可能会寻址到有权限保护和无权限保护的接收器。这意味着在发送方指定权限并不旨在告诉接收方发送方以任何方式受到保护。

要为接收方添加权限，我们首先需要在应用级别的 `AndroidManifest.xml` 中声明使用它：

```
...
<application ...
```

接下来，我们在同一个清单文件中将其显式添加到接收器元素中

其中 `MyReceiver` 是 `android.content.BroadcastReceiver` 的一个实现。



由于这是自定义权限，你需要在清单文件中声明它：

```xml
...
```

`<permission>` 元素还支持一些其他属性（参见在线配套文本中的“清单顶级条目”章节），例如保护级别。这些属性的详细说明及其影响将在第 7 章中深入讲解。对于非自定义权限，你无需使用 `<permission>` 元素。

**警告**

如果在发送方指定了权限，但接收方没有匹配的权限，尝试发送广播时会静默失败。同时也不会有任何日志记录，因此使用发送方权限时需格外小心。

如果通过 `LocalBroadcastManager` 使用本地广播，则无法在发送方或接收方指定权限。

## 保护隐式广播

与非本地显式广播类似，隐式广播的权限既可以在发送方指定，也可以在接收方指定。在发送方，你可以这样写：

```kotlin
val intent = Intent()
intent.action = "de.pspaeth.myapp.DO_STH"
// ... 更多 intent 坐标
sendBroadcast(intent, "com.xyz.theapp.PERMISSION1")
```

这表示向所有同时受 `"com.xyz.theapp.PERMISSION1"` 保护的匹配接收方发送广播。当然，你应该在这里使用自己的包名和合适的权限名称。与隐式广播的常规发送方-接收方匹配流程一样，添加权限相当于增加了一个额外的匹配条件。因此，如果存在多个候选接收方，在查看 Intent 过滤器时，只有同时提供了该权限标志的接收方才会被选中来实际接收此广播。

隐式广播还需要注意的另一件事是，在 `AndroidManifest.xml` 中声明权限使用。因此，为了让发送方能够使用该权限，需要在清单文件中添加：

```xml
<uses-permission android:name="com.xyz.theapp.PERMISSION1" />
```

与显式广播相同，不带权限规范的广播发送：

```kotlin
...
sendBroadcast(intent)
...
```

可能会同时寻址到有权限保护和无权限保护的接收方。这意味着，在发送方指定权限并不意味着告知接收方发送方受到了任何形式的保护。

为了让接收方能够获取此类广播，必须在代码中添加权限，示例如下：

```kotlin
private var bcReceiver: BroadcastReceiver? = null
override fun onCreate(savedInstanceState: Bundle?) {
    super.onCreate(savedInstanceState)
    ...
    bcReceiver = object : BroadcastReceiver() {
        override fun onReceive(context: Context?,
                                intent: Intent?) {
            // 接收到广播时执行某些操作...
        }
    }
    val ifi: IntentFilter =
        IntentFilter("de.pspaeth.myapp.DO_STH")
    registerReceiver(bcReceiver, ifi,
        "com.xyz.theapp.PERMISSION1", null)
}
override fun onDestroy() {
    super.onDestroy()
    unregisterReceiver(bcReceiver)
}
```

此外，你必须在接收方的清单文件中同时定义并使用该权限：

```xml
<permission android:name="com.xyz.theapp.PERMISSION1" />
...
<uses-permission android:name="com.xyz.theapp.PERMISSION1" />
```

再次强调，对于非自定义权限，你无需使用 `<permission>` 元素。更多关于权限的信息，请参见第 7 章。

**注意**

作为提升安全性的附加手段，在适用情况下，你可以使用 `Intent.setPackage()` 来限制可能的接收方。

## 从命令行发送广播

对于可通过*安卓调试桥*（ADB）连接的设备（参见第 19 章“SDK 平台工具”部分），你可以在开发电脑上使用 shell 命令发送广播消息。示例如下：

```bash
./adb shell am broadcast -a de.pspaeth.myapp.DO_STH \
    de.pspaeth.simplebroadcast MyReceiver
```

这会将操作 `"de.pspaeth.myapp.DO_STH"` 发送给包 `"de.pspaeth.simplebroadcast"` 中的指定接收器 `"MyReceiver"`（因此这是一个显式广播消息）。

要获取这种广播发送方式的完整说明，可以按如下方式使用 shell：

```bash
./adb shell
am
```



