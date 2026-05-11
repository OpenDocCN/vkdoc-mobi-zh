# 应用清单

在任何安卓应用中都能看到的一个非常重要的中央应用配置文件是`AndroidManifest.xml`。它描述了应用并声明了应用的所有组成部分。这样一个清单文件的大纲可能如下所示：

```
...
```

根条目`<manifest>`最重要的属性是`package`。它声明了应用的 ID，如果你计划发布应用，它必须是全球唯一的 ID。一个好的做法是使用你或你公司的反向域名，然后加上一个唯一的应用标识符，如下所示。

`<manifest>`的所有可能属性在表 2-1 中进行了描述。请注意，对于最简单的应用，你只需要`package`属性和一个`<application>`子元素。

**表 2-1** 清单的主要属性

| 名称 | 描述 |
| --- | --- |
| `android:installLocation` | 定义安装位置：使用`"internalOnly"`表示仅安装到内部存储；`"auto"`表示让操作系统决定，倾向于使用内部存储（用户之后可以在系统设置中切换）；或`"preferExternal"`表示让操作系统决定，倾向于使用外部存储。默认值为`"internalOnly"`。请注意，为此目的使用外部存储会有一系列限制，参见在线文档`<manifest>`。对于现代通常拥有大量空闲内部存储的设备，你永远不需要在此指定`"preferExternal"`。 |
| `package` | 应用的全局唯一 ID。一个类似`"abc.def.ghi.[...]"`的字符串，其中非点号字符可以包含字母 A-Z 和 a-z、数字 0-9 以及下划线。不要在点号后使用数字！这也是默认的进程名称和默认的任务亲和性。有关这些含义，请参见文本配套资料。请注意，一旦你的应用发布，你就无法在 Google Play 商店中更改这个包名。没有默认值，你必须设置此属性。 |
| `android:sharedUserId` | 自 API 级别 29 起已弃用。分配给应用的安卓操作系统用户 ID 的名称。 |
| `android:sharedUserLabel` | 自 API 级别 29 起已弃用。仅当你同时设置了`"sharedUserId"`时，才能在此为共享用户 ID 设置一个用户可读的标签。 |
| `android:targetSandboxVersion` | 值为`"1"`或`"2"`。作为安全级别使用。从安卓 8.0 或 API 级别 26 开始，对于即时应用，你*必须*将其设置为`"2"`。与`"1"`相比，使用`"2"`时，不同应用之间不能再共享用户 ID，并且`usesClearTextTraffic`（参见文本配套资料）的默认值设置为 false。 |
| `android:versionCode` | 应用的内部版本号。此编号不向用户显示，仅用于版本比较。请使用一个大于 1 的整数。默认值为`undefined`。 |
| `android:versionName` | 用户可见的版本字符串。可以是字符串本身，也可以是指向字符串资源的指针（`"@string/..."`）。除了通知用户之外，它不用于其他任何用途。 |

可以作为`<manifest>`元素子元素的所有元素都在在线文本配套资料的“清单顶级条目”部分列出。其中最重要的`<application>`描述了应用，并在在线文本配套资料的“应用声明”部分进行了详细讨论。

### 活动

*活动*代表了应用的用户界面入口点。任何需要以直接方式与用户进行功能交互的应用——通过让用户输入内容或以图形方式告知用户应用的功能状态——都会向系统暴露至少一个*活动*。我们之所以说*功能性地*，是因为以通知方式告知用户事件也可以通过*Toasts*或*状态栏*的通知来实现，这不需要活动。

应用可以有零个、一个或多个活动，它们通过以下两种方式之一启动：

1.  *主活动*，在`AndroidManifest.xml`中声明，通过启动应用来启动。这有点类似于传统应用的`main()`函数。

2.  所有活动都可以配置为由显示或隐式*Intent*启动，如`AndroidManifest.xml`中所配置。Intent 既是类的对象，也是安卓中一个卓越的概念。对于显式 Intent，通过触发 Intent，一个组件告知它需要由某个特定应用的专用组件来完成某事。对于隐式 Intent，组件仅告知需要做什么，而不指定应由哪个组件来完成——由安卓操作系统或用户决定哪个应用或组件能够完成这样的隐式请求。

从用户角度来看，活动表现为可以从应用启动器中启动的屏幕，无论是标准启动器还是专门的第三方启动器应用。并且，一旦它们运行起来，它们也会被列在任务栈中，用户在使用返回按钮时会看到它们。

**注意** 活动是一个非常重要的概念，建议也参阅在线文档，网址为 [`https://developer.android.com/guide/components/activities/intro-activities`](https://developer.android.com/guide/components/activities/intro-activities) 和 [`https://developer.android.com/reference/android/app/Activity`](https://developer.android.com/reference/android/app/Activity)。本章只能展示这个庞大主题的一部分内容。



## 声明 Activity

要声明一个 Activity，在 `AndroidManifest.xml` 文件中，编写如下代码

```
...
...
```

如这个特定示例所示，你可以用点号开头来命名，这会导致自动添加应用的包名。在这种情况下，Activity 的完整名称是 "com.example.myapp.ExampleActivity"。或者你也可以直接写完整名称：

```
...
...
```

可以添加到 `<activity>` 元素的所有属性，都列在在线配套文本的 "Activity 相关的 Manifest 条目" 部分中。

可以作为子元素放入 `activity` 元素中的元素如下：

- **intent-filter**

一个意图过滤器。详细信息请参见 "意图过滤器" 部分。你可以指定零个、一个或多个意图过滤器。

- **layout**

从 Android 7.0 开始，你可以在此处指定多窗口模式下的布局属性，例如

```
你当然可以使用自己的数值。属性 `defaultWidth` 和 `defaultHeight`
指定默认尺寸，`gravity` 定义了
Activity 在自由模式下的初始放置位置，而 `minHeight` 和 `maxHeight` 则表示
最小尺寸。
```

- **meta-data**

一个任意的名称-值对，格式为 `<meta-data android:name = "..." android:resource = "..." android:value = "..." />`。可以有多个这样的条目，它们会被放入一个 `android.os.Bundle` 元素中，该元素可作为 `PackageItemInfo.metaData` 访问。

### 注意事项

编写一个没有任何 Activity 的应用是可能的——该应用仍然可以作为内容提供者提供服务、广播接收器和数据内容。作为应用开发者，你需要牢记一点：用户不一定理解这种没有用户界面的组件到底是做什么的。在大多数情况下，建议提供一个非常简单的、仅用于提供信息的主 Activity，这有助于改善用户体验。不过，在企业环境下，提供没有 Activity 的应用是可以接受的。

## 启动 Activity

Activity 可以通过两种方式之一启动。首先，如果 Activity 被标记为应用的可启动主 Activity，那么可以从应用启动器中启动它。要将一个 Activity 声明为可启动的主 Activity，需在 `AndroidManifest.xml` 中编写如下代码：

```
...
...
```

`android.intent.action.MAIN` 告诉 Android 这是主 Activity，并且它会被放置到任务栈的底部。`android.intent.category.LAUNCHER` 告诉 Android 它必须被列在启动器中。

其次，Activity 可以通过来自同一应用或任何其他应用的 `Intent` 进行启动。为了实现这一点，你需要在清单文件中声明一个意图过滤器：

```
...
...
```

对应于此意图过滤器、实际启动该 Activity 的代码如下所示：

```kotlin
val intent = Intent()
intent.action = "com.example.myapp.ExampleActivity.START_ME"
startActivity(intent)
```

对于来自其他应用的调用，不能设置 `exported="false"` 标志。过滤器中的 `android.intent.category.DEFAULT` 类别说明确保了即使启动代码中没有设置类别，该 Activity 也可以启动。

在前面的例子中，我们使用了一个*显式* `Intent` 来调用 Activity。我们精确地告诉了 Android 要调用哪个 Activity，并且期望恰好有一个 Activity 通过其意图过滤器被这样寻址。另一种类型的 `Intent` 称为*隐式* `Intent`，与精确调用一个 Activity 相反，它的作用是告诉系统我们*想要*做什么，而不指定*哪个*应用或哪个组件能够完成它。这种隐式调用的例子如下所示：

```kotlin
val intent = Intent(Intent.ACTION_SEND)
intent.type = "text/plain"
intent.putExtra(Intent.EXTRA_TEXT, "Give me a Quote")
startActivity(intent)
```

这段代码的作用是 "调用一个能够处理 `Intent.ACTION_SEND` 动作、接收 MIME 类型为 `text/plain` 的文本，并传递文本 'Give me a quote' 的 Activity。" Android 操作系统随后会向用户展示一个来自本应用或其他应用的、能够接收这种 `Intent` 的 Activity 列表。Activity 可以有关联的数据。只需使用 `Intent` 类中某个重载的 `putExtra(...)` 方法即可。

## Activity 与任务

启动的 Activity 在任务栈中的实际行为由以下属性决定：

- `taskAffinity`
- `launchMode`
- `allowTaskReparenting`
- `clearTaskOnLaunch`
- `alwaysRetainTaskState`
- `finishOnTaskLaunch`

这些属性在 `<activity>` 元素的属性中给出，以及 `Intent` 调用的标志：

- `FLAG_ACTIVITY_NEW_TASK`
- `FLAG_ACTIVITY_CLEAR_TOP`
- `FLAG_ACTIVITY_SINGLE_TOP`

你可以在 `Intent.flags = Intent.<FLAG>` 中指定它们，其中 `<FLAG>` 是列表中的一个。如果 Activity 属性与调用者标志冲突，则以调用者标志为准。

属性或标志的名称已经暗示了其用途。关于它们的更多详细信息，请参阅在线文档。

## Activity 返回数据

如果你通过以下方式启动一个 Activity：

```kotlin
startActivityForResult(intent:Intent, requestCode:Int)
```

这意味着你期望被调用的 Activity 在返回时能返回一些内容。在被调用的 Activity 中，你需要使用如下结构：

```kotlin
val intent = Intent()
intent.putExtra(...)
intent.putExtra(...)
setResult(Activity.RESULT_OK, intent)
finish()
```

在 `.putExtra(...)` 方法调用中，你可以添加任何需要从该 Activity 返回的数据。例如，你可以将这些代码行添加到 `onBackPressed()` 事件处理方法中。

对于 `setResult()` 的第一个参数，你可以使用以下任一值：

- **`Activity.RESULT_OK`**，如果你想告诉调用者，被调用的 Activity 已成功完成其任务。
- **`Activity.RESULT_CANCELED`**，如果你想告诉调用者，被调用的 Activity 未成功完成其任务。你仍然可以通过 `.putExtra(...)` 添加额外信息来说明哪里出错了。
- **`Activity.RESULT_FIRST_USER + N`**，其中 N 可以是 0、1、2 等任意数字，用于定义你想要的任何自定义结果码。实际上 N 没有限制（最大值为 2³¹ − 1）。

请注意，如果你有工具栏，还需要处理返回按钮按下的事件。一种可能性是在 `onCreate()` 方法中添加如下代码行：

```kotlin
setSupportActionBar(toolbar)
supportActionBar!!.setDisplayHomeAsUpEnabled(true)
// 工具栏上的导航按钮与 BACK 按钮功能不同，
// 更准确地说，它不会调用 onBackPressed() 方法。
// 我们添加一个监听器来自己处理
toolbar.setNavigationOnClickListener { onBackPressed() }
```

当被调用的 `Intent` 按照前述方式返回时，需要通知调用组件这一事件。这是异步完成的，因为 `startActivityForResult()` 会立即返回，而不会等待被调用的 Activity 结束。尽管如此，捕获此事件的方式是通过重写 `onActivityResult()` 方法：

```kotlin
override
fun onActivityResult(requestCode:Int, resultCode:Int,
data:Intent) {
// 处理 'requestCode' 和 'resultCode'
// 返回的数据在 'data' 中
}
```

`requestCode` 是在 `startActivityForResult()` 中设置的任何值，作为 `requestCode`；而 `resultCode` 是在被调用的 Activity 中作为 `setResult()` 的第一个参数写入的值。

### 注意事项

在某些设备上，无论之前设置的值是什么，`requestCode` 的最高有效位都会被设置为 1。为了安全起见，你可以在 `onActivityResult()` 内部使用 Kotlin 结构 `val requestCodeFixed = requestCode and 0xFFFF`。



