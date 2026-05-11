# 与 Fragment 通信

Activity 可以通过使用 Fragment 管理器并根据其 ID 或标签查找 Fragment 来与其 Fragment 进行通信：

```
val fragm = supportFragmentManager.findFragmentByTag("fragmTag")
// 或 val fragm = supportFragmentManager.findFragmentById(fragmId)
```

但 Fragment 也可以与其 Activity 通信。这可能表明应用设计不佳，因为 Fragment 应该是自包含的实体。如果你仍然需要，可以在 Fragment 类内部使用 `getActivity()`，或者在 Kotlin 中直接使用：

```
activity
```

来访问它。并且从那里，Fragment 甚至可以获取其他 Fragment 的引用。

## 应用微件

应用微件是一类特殊的应用，用于在其他应用（尤其是主屏幕）中显示信息性消息和/或控制器。应用微件作为特殊的广播接收器实现，因此一旦回调方法完成其工作，并且过后几秒钟，它们就有可能被 Android 操作系统终止。如果你需要运行较长的进程，请考虑从应用微件内部启动*服务*。

要开始创建应用微件，请在 `AndroidManifest.xml` 中编写，更确切地说，是作为 `<application>` 的子元素。

接下来，在资源中创建元数据：在 `res/xml` 中创建一个新文件 `example_appwidget_info.xml` 并写入内容。

剩下的就是创建广播监听器类——为此，最简单的方法是继承 `android.appwidget.AppWidgetProvider` 类，并例如编写：

```
class ExampleAppWidgetProvider : AppWidgetProvider() {
    override fun onUpdate(context: Context,
                          appWidgetManager: AppWidgetManager,
                          appWidgetIds: IntArray) {
        // 为属于此提供者的每个
        // 应用微件执行此循环过程
        for (appWidgetId in appWidgetIds) {
            // 这只是一个示例，你可以在此处
            // 做其他事情...
            // 创建一个 Intent 启动 MainActivity
            val intent = Intent(context, MainActivity::class.java)
            val pendingIntent = PendingIntent.getActivity(context, 0, intent, 0)
            // 为按钮附加监听器
            val views = RemoteViews(context.getPackageName(),
                                    R.layout.appwidget_provider_layout)
            views.setOnClickPendingIntent(R.id.button, pendingIntent)
            // 通知 AppWidgetManager 对应用微件
            // 执行更新
            appWidgetManager.updateAppWidget(appWidgetId, views)
        }
    }
}
```

一个*应用微件提供者*可以为多个应用微件提供服务；这就是为什么我们需要在设置中遍历循环。由于这需要一个布局文件 `appwidget_provider_layout.xml`，我们可以在 `res/layout` 中创建它，并例如编写内容。

> **注意**
> 并非所有布局容器和视图都允许出现在应用微件的布局文件中——你可以使用 `FrameLayout`、`LinearLayout`、`RelativeLayout`、`GridLayout`、`AnalogClock`、`Button`、`Chronometer`、`ImageButton`、`ImageView`、`ProgressBar`、`TextView`、`ViewFlipper`、`ListView`、`GridView`、`StackView` 和 `AdapterViewFlipper` 之一。
> 用户仍需通过长按应用图标并将其放置在主屏幕上来决定激活应用微件。因为并非所有用户都知道这一点，应用的功能不应依赖于它是否被设置为主屏幕上的应用微件。

应用微件可以附加一个配置 Activity。当用户尝试将应用微件放置在主屏幕上时，会调用这个特殊的 Activity。然后，用户可以例如被询问一些关于外观或功能的设置。要安装这样一个 Activity，请在 `AndroidManifest.xml` 中编写。

这里显示的 Intent 过滤器很重要——它告诉系统此 Activity 的特殊性质。

然后，必须将应用微件配置 Activity 添加到 XML 配置文件 `example_appwidget_info.xml` 中：添加以下内容：

```
android:configure="full.class.name.ExampleAppWidgetConfigure"
```

作为额外的属性，并使用配置类的完整限定名。

配置 Activity 本身需要按如下方式返回应用微件 ID：

```
class ExampleAppWidgetConfigure : AppCompatActivity() {
    var awi: Int = 0
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_conf)
        awi = intent.extras.getInt(AppWidgetManager.EXTRA_APPWIDGET_ID)
        Toast.makeText(this, "" + awi, Toast.LENGTH_LONG).show()
        // 做更多配置工作...
    }
    fun goBack(view: View) {
        // 只是一个示例...
        val data = Intent()
        data.putExtra(AppWidgetManager.EXTRA_APPWIDGET_ID, awi)
        setResult(RESULT_OK, data)
        finish()
    }
}
```

> **注意**
> 应用微件的 `onUpdate()` 方法在配置 Activity 退出时*第一次*不会被调用——如果需要，配置 Activity 有责任相应地调用应用微件管理器上的 `updateAppWidget()` 方法。

## 拖放

Android 支持任何类型的 UI 元素和布局的拖放。其思路如下：

- 应用定义一个手势来标识拖放操作的开始。你可以完全自由地定义这个手势——通常的候选是触摸或触摸并移动操作，但你也可以以编程方式启动拖放操作。

- 如果拖放指定了某种数据传输，你可以将一个 `android.content.ClipData` 类型的数据片段分配给拖放操作。

- 参与此拖放操作的视图（包括*任何*拖放源和*所有*可能的放置目标）会被分配一个自定义的 `View.OnDragListener` 对象。

- 当拖放进行中时，拖动的目标会通过一个移动的*阴影*对象进行视觉呈现。你可以自由定义它——它可以是一个星星、一个方框或任何类似于拖动源的图形。你只需告诉系统如何绘制它——拖放操作期间的定位由 Android 操作系统自动处理。由于拖动源在拖放操作期间保持原位，布局始终保持静态，并且拖动不会阻碍布局管理器的布局操作。

- 当拖放阴影进入可能的放置目标区域时，监听器会收到通知，你可以通过例如更改放置候选对象的视觉外观来做出反应。



