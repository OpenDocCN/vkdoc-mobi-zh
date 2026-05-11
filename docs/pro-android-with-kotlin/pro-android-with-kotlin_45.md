# 拖放实现指南

由于所有拖拽源和可能的放置目标都会获知各种拖拽状态，您可以自由地通过不同的视图外观来直观地表达这些状态。

一旦发生放置操作，监听器便会收到通知，您可以自由地对此类放置事件做出响应。

还可以不专门使用拖放监听器，而是重写参与拖放操作的视图的某些特定方法。这样做的缺点是必须在布局描述中使用自定义视图，这会使代码可读性降低。此外，从架构角度来看，视图会过多地了解正在*对其*发生的事情，这属于外部关注点，因此最好由外部对象来处理。因此，我们采用监听器方法，并在接下来的章节中描述此方法的具体操作步骤。

## 定义拖放数据

如果您的拖放操作定义了某种从视图所代表的对象到另一个对象的数据传输，您可以按如下方式定义 `ClipData` 对象：

```
val item = ClipData.Item(myView.tag.toString())
val dragData = ClipData(myView.tag.toString(),
arrayOf(MIMETYPE_TEXT_PLAIN), item)
```

构造函数的第一个参数是剪贴数据的用户可读标签，第二个参数通过分配适当的 MIME 类型来描述内容类型，`item` 参数表示要传输的数据，此处是视图的 `tag` 属性给出的字符串。其他项类型为 `Intents` 和 `URIs`，需在 `ClipData.Item` 的构造函数中指定。

## 定义拖拽阴影

拖拽阴影是拖拽进行时绘制在手指下方的可见元素。您可以按如下方式在对象中定义阴影：

```
class DragShadow(val resources: Resources, val resId:Int,
view: ImageView) : View.DragShadowBuilder(view) {
val rect = Rect()
// 定义回调，将拖拽阴影
// 尺寸和触摸点信息发送回
// 系统。
override
fun onProvideShadowMetrics(size: Point, touch: Point) {
val width = view.width
val height = view.height
rect.set(0, 0, width, height)
// 通过 size 参数返回给系统。
size.set(width, height)
// 触摸点位于中间位置
touch.set(width / 2, height / 2)
}
// 定义回调，在画布上绘制拖拽阴影
override
fun onDrawShadow(canvas: Canvas) {
canvas.drawBitmap(
BitmapFactory.decodeResource(
resources, resId),
null, rect, null)
}
}
```

此示例绘制了一个位图资源，但您也可以在此处执行任何其他操作。

## 开始拖拽

对于 API 级别 24 及以上版本，请在充当拖拽源的视图对象上调用 `startDragAndDrop()`，否则调用 `startDrag()`。示例如下：

```
theView.setOnTouchListener { view, event ->
if(event.action == MotionEvent.ACTION_DOWN) {
val shadow = DragShadow(resources,
R.the_dragging_image, theView)
val item = ClipData.Item(frog.tag.toString())
val dragData = ClipData(frog.tag.toString(),
arrayOf(MIMETYPE_TEXT_PLAIN), item)
if (Build.VERSION.SDK_INT >=
Build.VERSION_CODES.N) {
theView.startDragAndDrop(dragData, shadow,
null, 0)
} else {
theView.startDrag(dragData, shadow,
null, 0)
}
}
true
}
```

如果拖拽操作*不*与数据类型关联，您也可以将 `dragData` 设置为 `null`。在这种情况下，您无需构建 `ClipData` 对象。

## 监听拖拽事件

拖放操作中发生的完整事件集由拖放监听器管理。示例如下：

```
class MyDragListener : View.OnDragListener {
override
fun onDrag(v: View, event: DragEvent): Boolean {
var res = true
when(event.action) {
DragEvent.ACTION_DRAG_STARTED -> {
when(v.tag) {
"DragSource" -> { res = false
/*不是放置接收器*/ }
"OneTarget" -> {
// 可以明显改变
// 可能的放置接收器
}
}
}
DragEvent.ACTION_DRAG_ENDED -> {
when(v.tag) {
"OneTarget" -> {
// 可以明显改变
// 可能的放置接收器
}
}
}
DragEvent.ACTION_DROP -> {
when(v.tag) {
"OneTarget" -> {
// 视觉上还原放置
// 接收器 ...
}
}
Toast.makeText(v.context, "已放置！",
Toast.LENGTH_LONG).show()
}
DragEvent.ACTION_DRAG_ENTERED -> {
when(v.tag) {
"OneTarget" -> {
// 可以明显改变
// 可能的放置接收器
}
}
}
DragEvent.ACTION_DRAG_EXITED -> {
when(v.tag) {
"OneTarget" -> {
// 视觉上还原放置
// 接收器 ...
}
}
}
}
return res
}
}
```

您可以看到我们正在监听拖拽开始和结束事件，以及阴影进入或退出可能的放置区域的事件和放置事件。如前所述，您如何响应所有这些事件完全取决于您。

> **注意**  
> 在示例中，我们使用分配给视图的 `tag` 属性来标识该视图为拖放操作的一部分。实际上，您也可以使用 ID 或任何其他您能想到的方式。

剩下的就是，例如，在 Activity 的 `onCreate()` 回调中，将监听器注册到所有参与拖放操作的视图：

```
override fun onCreate(savedInstanceState: Bundle?) {
super.onCreate(savedInstanceState)
setContentView(R.layout.activity_main)
theView.setOnTouchListener ...
val dragListener = MyDragListener()
theView.setOnDragListener(dragListener)
otherView.setOnDragListener(dragListener)
...
}
```

## 多点触控

Android 操作系统中的多点触控事件处理起来出奇地简单。您只需重写 `View` 或 `ViewGroup` 元素的 `onTouchEvent()` 方法。在 `onTouchEvent()` 内部，您获取掩码后的动作并对其进行处理：

```
frog.setOnTouchListener { view, event ->
true
}
```

> **注意**  
> 在旧版 Android 中，您通常通过 `event.action` 来分发动作。对于多点触控手势，最好使用 `maskedAction` 进行操作——详见下文。

在监听器内部，您通过 `event.actionMasked` 获取掩码后的动作，并将其传递给 `when(){ .. }` 语句。

现在，神奇之处在于这个监听器会被所有手指（此处称为*指针*）连续调用。要了解当前注册了多少根手指，请使用 `event.pointerCount`；如果您想知道事件属于哪根手指，请使用 `val index = event.actionIndex`。因此，起点如下：

```
theView.setOnTouchListener { view,event ->
fun actionToString(action:Int) : String = mapOf(
MotionEvent.ACTION_DOWN to "按下",
MotionEvent.ACTION_MOVE to "移动",
MotionEvent.ACTION_POINTER_DOWN to "指针按下",
MotionEvent.ACTION_UP to "抬起",
MotionEvent.ACTION_POINTER_UP to "指针抬起",
MotionEvent.ACTION_OUTSIDE to "外部",
MotionEvent.ACTION_CANCEL to "取消").
getOrDefault(action,"")
val action = event.actionMasked
val index = event.actionIndex
var xPos = -1
var yPos = -1
Log.d("LOG", "动作是 " +
actionToString(action))
if (event.pointerCount > 1) {
Log.d("LOG", "多点触控事件")
// 当前屏幕接触点的坐标，
// 相对于响应的 View 或 Activity。
xPos = event.getX(index).toInt()
yPos = event.getY(index).toInt()
} else {
// 单点触控事件
Log.d("LOG", "单点触控事件")
xPos = event.getX(index).toInt()
yPos = event.getY(index).toInt()
}
// 做更多事情...
true
}
```

## 画中画模式

从 Android 8.0 或 API 级别 26 开始，存在一种画中画模式，其中 Activity 会缩小并固定到屏幕边缘。当 Activity 正在播放视频，并且您希望在另一个 Activity 出现时视频继续播放时，这尤其有用。

要启用画中画模式，请在 `AndroidManifest.xml` 中的 `<activity>` 内添加以下属性：



```xml
<code_block>
android:resizeableActivity="true"
android:supportsPictureInPicture="true"
android:configChanges=
"screenSize|smallestScreenSize|
screenLayout|orientation"
```
</code_block>

然后，在你的应用中尽可能方便的地方，通过以下方式启动画中画模式：

```xml
enterPictureInPictureMode()
```

如果进入或退出画中画模式，你可能希望更改布局，之后再恢复原状。为此，可以重写 `onPictureInPictureModeChanged(isInPictureInPictureMode : Boolean, newConfig : Configuration)` 方法并做出相应反应。

## 文字转语音

文字转语音框架允许将文本转换为音频，可以直接发送到音频硬件，也可以发送到文件。使用相应的 `TextToSpeech` 类很简单，但应确保所有必要的资源都已加载。为此，应触发一个 action 为 `TextToSpeech.Engine.ACTION_CHECK_TTS_DATA` 的 `Intent`。预期它会返回 `TextToSpeech.Engine.CHECK_VOICE_DATA_PASS`；如果没有返回，则调用另一个 action 为 `TextToSpeech.Engine.ACTION_INSTALL_TTS_DATA` 的 `Intent`，让用户安装文字转语音数据。

一个执行所有上述操作的示例 Activity 如下所示：

```kotlin
class MainActivity : AppCompatActivity() {
    companion object {
        val MY_DATA_CHECK_CODE = 42
    }
    var tts: TextToSpeech? = null
    override
    fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)
        val checkIntent = Intent()
        checkIntent.action = TextToSpeech.Engine.ACTION_CHECK_TTS_DATA
        startActivityForResult(checkIntent, MY_DATA_CHECK_CODE)
    }
    fun go(view: View) {
        tts?.run {
            language = Locale.US
            val myText1 = "Did you sleep well?"
            val myText2 = "It's time to wake up."
            speak(myText1, TextToSpeech.QUEUE_FLUSH, null)
            speak(myText2, TextToSpeech.QUEUE_ADD, null)
        }
    }
    override
    fun onActivityResult(requestCode: Int, resultCode: Int, data: Intent) {
        if (requestCode == MY_DATA_CHECK_CODE) {
            if (resultCode == TextToSpeech.Engine.CHECK_VOICE_DATA_PASS) {
                // 成功，创建 TTS 实例
                tts = TextToSpeech(this, { status ->
                    // 如果愿意，可以做一些事情
                })
            } else {
                // 数据缺失，安装它
                val installIntent = Intent()
                installIntent.action = TextToSpeech.Engine.ACTION_INSTALL_TTS_DATA
                startActivity(installIntent)
            }
        }
    }
}
```

这个示例还包含一个 `go()` 方法，例如，可以通过按钮点击来触发它。它会生成一些语音并立即发送到扬声器。如果你想将音频写入文件，可以使用 `tts.synthesizeToFile()` 方法。更多细节将在 `TextToSpeech` 的在线文档中提供。

## 10. Firebase

Firebase 是谷歌云及（离岸）开发平台的名称。在本章中，我们将讨论云数据存储和基于云的消息传递。

> **注意**
>
> Firebase 的功能远不止消息传递和数据存储——请查阅在线文档（例如，[`https://firebase.google.com/products-build`](https://firebase.google.com/products-build)）、Firebase 控制台中的信息以及 Android Studio 提供的信息，来了解还可以做什么。

## Firebase 云存储

你可以使用云存储 API 将文件保存到 Google Cloud Storage 并从中读取文件。你需要拥有一个已激活 Firebase 的 Google 账户，并通过 [`https://console.firebase.google.com`](https://console.firebase.google.com) 设置一个 Firebase 项目。

> **注意**
>
> “文件”这个概念的含义非常广泛——它可以是来自设备内存的一系列字节、一个字节流，或者来自设备存储的一个实际文件。

本书中逐字复制应用设置和存储配置文档的价值不大。相反，只需遵循 [`https://firebase.google.com/docs/storage`](https://firebase.google.com/docs/storage) 上给出的详细说明，尤其是其中的 [`https://firebase.google.com/docs/storage/android/start`](https://firebase.google.com/docs/storage/android/start)。

## Firebase 云消息传递

Firebase 云消息传递 (FCM) 是一个基于云的消息代理，你可以使用它向各种设备（包括其他操作系统，如 Apple iOS）发送和接收消息。其思路如下：你在 Firebase 控制台中注册一个应用，此后便可以在连接的设备中接收和发送消息，包括在其他设备上你的应用的其他安装实例。

> **注意**
>
> Firebase 云消息传递 (FCM) 是 *Google Cloud Messaging* (GCM) 的后续版本。

要从 Android Studio 内部启动 FCM，请在你打开的项目中，转到 **Tools ➤ Firebase**。选择 **Cloud Messaging**，然后选择 **Set up Firebase Cloud Messaging**。如果按照那里的说明进行操作，你将最终使用两项服务：

*   一个 `FirebaseInstanceIdService` 的子类，你将在其中接收消息令牌。该类大致如下所示：

```kotlin
class MyFirebaseInstanceIdService : FirebaseInstanceIdService() {
    override
    fun onTokenRefresh() {
        // 获取更新的 InstanceID 令牌。
        val refreshedToken = FirebaseInstanceId.getInstance().token
        Log.d(TAG, "Refreshed token: " + refreshedToken!!)
        // 如果你想向此应用实例发送消息
        // 或在服务器端管理此应用的订阅，
        // 请将 Instance ID 令牌发送到你的应用服务器。
        sendRegistrationToServer(refreshedToken)
    }
}
```

并且它在 `AndroidManifest.xml` 中有一个相应的条目：

你首次启动连接到 Firebase 的应用时在此处收到的令牌非常重要——你需要它来使用基于 Firebase 的通信渠道。并且该令牌会不定期地自动更新，因此你需要找到一种方法，以便在此服务中收到令牌时可靠地存储它。为了方便起见：除非你实现了存储令牌的方法，否则一定要保存你在日志中收到的令牌，因为恢复丢失的令牌会导致烦人的管理工作。

*   另一个用于接收基于 FCM 消息的服务。它可能如下所示：

```kotlin
class MyFirebaseMessagingService : FirebaseMessagingService() {
    override
    fun onMessageReceived(remoteMessage: RemoteMessage) {
        // ...
        // 检查消息是否包含数据负载。
        if (remoteMessage.data.size > 0) {
            Log.d(TAG, "Message data payload: " + remoteMessage.data)
            // 实现一个逻辑：
            // 对于长时间运行的任务（10 秒或更长）
            // 使用 Firebase Job Dispatcher。
            scheduleJob()
            // ...否则在 10 秒内处理消息
            // handleNow()
        }
        // 消息包含通知负载？
        remoteMessage.notification?.run {
            Log.d(TAG, "Message Notification Body: " + body)
        }
    }
    private fun handleNow() {
        Log.e("LOG","handleNow()")
    }
    private fun scheduleJob() {
        Log.e("LOG","scheduleJob()")
    }
}
```

这同样在 `AndroidManifest.xml` 中有一个相应的条目：

为了使这一切正常工作，你需要在你的 Google 账户中激活 Firebase。有几种选择。对于高流量的消息传递服务，你需要购买一个套餐。不过，免费版本（截至 2018 年 3 月）将为你提供远超开发和测试所需的强大功能。如果一切设置正确，你可以使用基于 Web 的 Firebase 控制台测试向正在运行的应用发送消息，并查看消息到达日志中。对于发送消息，建议的解决方案是以应用服务器的形式设置一个可信环境。这超出了本书的范围，但 Firebase 在线文档为你提供了各种入门指导。

## 11. 开发

与前几章相比，本章涉及更贴近开发事项的问题。我们在此讨论的主题与特定 Android OS API 的耦合度较低——我们更关心的是如何利用 Kotlin 方法最佳地实现技术需求。本章末尾附有一节内容，介绍如何将 Kotlin 代码转录为可为 `WebView` 控件提供服务的 JavaScript 代码。



