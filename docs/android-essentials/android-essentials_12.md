# Android 要点

*图 2-1. DDMS 透视图*

在左侧窗口的“设备”(Devices) 标签页中，你应该会看到你的应用程序旁边有一个绿色的小虫子图标高亮显示。这是 DDMS 在告诉你，调试器已附加到你的 `PrankApp` 进程。再往下一点是“模拟器控制”(Emulator Control) 标签页。你将在这里发送短信。首先输入任意电话号码，选择“发送短信”(SMS)，输入一条测试消息，然后点击“发送”(Send)。

如果你的清单文件配置正确，你应该会看到 Eclipse 切换回“调试”(Debug) 透视图，并在你新设置的断点处暂停。

**注意**

如果没有任何反应，首先请确保你已正确设置权限。如果权限不正确，你应该会在 DDMS 屏幕底部的 LogCat 标签页上看到一条权限失败的消息。此外，请确保你的应用程序正在运行，并且在“设备”(Devices) 标签页上旁边有绿色的调试图标。如果其他方法都失败了，请将你的项目与我随书附带的示例项目进行比较。

如果你到目前为止正确地遵循了步骤，那么每当有短信发送到手机时，你的意图的 `onReceiveIntent()` 函数就会被调用。接下来，你需要弄清楚如何检索短信的内容。

### 短信里有什么？

遗憾的是，到目前为止，Android 关于接收和过滤短信的文档充其量只是令人困惑。我怀疑这并不是一个需要优先告知开发者的特性。虽然我不同意这种优先级安排，但这给了我一个填补空白的机会。

以下是你正在监听新短信时的 `onReceiveIntent()` 方法现在的样子：

```java
public void onReceiveIntent(Context context, Intent intent)
{
    SmsMessage msg[] = Telephony.Sms.Intents.getMessagesFromIntent(intent);
    for (int i = 0; i < msg.length; i++)
    {
        String msgTxt = msg[i].getMessageBody();
        if (msgTxt.equals("0xBADCAT0_Fire_The_Missiles!"))
        {
            //在这里开始恶作剧
        }
    }
    return;
}
```

你还需要导入两个库才能使其工作：

```java
import android.telephony.gsm.SmsMessage;
import android.provider.Telephony;
```

在 `Telephony` 库中隐藏着 `getMessagesFromIntent()` 调用，它将返回一个短信息数组。剩下要做的就是提取这些短信中的有效载荷。你要寻找用于触发恶作剧活动的特殊代码是文本 `"0xBADCAT0_Fire_The_Missiles!"`。它必须是一个足够独特的组合，以免被意外触发。你肯定不希望你的恶作剧出差错，过早地惊动“受害者”。

**注意**

在 Android 中，那些文档记录不完善的功能很可能尚未完成。由于接收短信息的文档几乎不存在，你应该预料到短信处理方式会有所变化。很可能整体方法会类似，但可以确定的是，在 SDK 达到最终版本之前，某些细节将会改变。这个示例更多的是关于学习如何使用意图接收器，而不是关于文本消息通信的细节。

## 触发 Activity



### Android 核心要点

请务必记住，intent 接收器的生命周期仅限于 `onReceiveIntent` 方法调用的持续时间。一旦离开该函数，Android 系统就可以随时终止运行你应用的进程。任何异步功能若在此之后启动，都将以混乱的方式消亡。如果你想在该方法内执行除简单处理以外的任何操作，就需要启动一个服务（service）或活动（activity）。由于你既要播放音乐，又要提醒受害者他们已中招，因此需要启动一个活动。实现方式如下：

```
if (msgTxt.equals("0xBADCAT0_Fire_The_Missiles!"))
{
    //启动 Activity
    Intent startActivity = new Intent();
    startActivity.setLaunchFlags(Intent.NEW_TASK_LAUNCH);
    startActivity.setAction("com.apress.START_THE_MUSIC");
    context.startActivity(startActivity);
}
```

你在这里看到的唯一陌生代码是 `setLaunchFlags` 中新增的 `NEW_TASK_LAUNCH`。每当你想要发送一个 intent action 来启动一个新的 activity 时，都需要这样做。

此外，正如你在闪屏示例应用中所做的那样，你必须在 activity 的 intent 过滤器中添加一个新的 action。这个过程看起来应该很熟悉：

```
<intent-filter>
    <action android:name="android.intent.action.MAIN" />
    <action android:name="com.apress.START_THE_MUSIC" />
    <category android:name="android.intent.category.LAUNCHER" />
    <category android:name="android.intent.category.DEFAULT" />
</intent-filter>
```

现在，如果你已将前面的代码正确添加到位，当从 DDMS 透视图发送一条 SMS 消息时，你应该会看到你的应用出现在前台，并且屏幕上自豪地显示着“Hello World, PrankActivity”文本。

### 设置活动

在进入更邪恶的音乐播放服务之前，还有最后一部分需要添加：将 activity 设置为能正确响应由你的 SMS intent 接收器发送的 action。如果应用是正常启动的，你需要立即将其关闭。同样，你不能让恶作剧的受害者从菜单中启动它而过早发现你的计划。为此，你需要检索启动 intent，并调用 `getAction` 方法来找出它是在何种情况下启动的。`PrankActivity` 的 `onCreate` 方法现在应如代码清单 2-4 所示。

#### 代码清单 2-4. 针对特定 Intent Action 启动

```
public void onCreate(Bundle icicle)
{
    super.onCreate(icicle);
    Intent i = getIntent();
    String action = i.getAction();
    if (action != null &&
        action.equals("com.apress.START_THE_MUSIC"))
    {
        setContentView(R.layout.pranked);
        //此处我们需要启动音乐服务
    }
    else
        finish();
}
```

首先，你获取启动了你的 activity 的 intent。拿到 intent 后，你可以通过 `getAction` 方法检索调用 action。这将返回一个包含启动事件的字符串，你可以将其与你之前在 XML 中列出的已知音乐 action 进行比对。如果你的 activity 是通过正常方式（从菜单或启动调试器）启动的，action 字符串将为 `null`。如果是这种情况，你需要立即使用 `finish` 方法关闭 activity。

**注意**：`onCreate` 方法仅在应用首次启动时被调用。如果你启动了应用然后退出（使用返回键），应用仍将在后台运行。此时，如果你发送 SMS 消息，你的 activity 会回到前台，但其 `onStart` 方法会被调用，而 `onCreate` 不会被调用。如果你正在跟着做，可以随意将你自己示例中的功能移到 `onStart` 方法中。

## 今天你想羞辱谁？

尽管可能有点大材小用，但你还是会使用 Android 的 `Service` 对象之一来处理音乐播放。我这样做有两个原因：


这是一个绝佳的机会，可以在简单、不复杂的环境中演示某项服务的使用。

它可能会让你在不显示应用界面的情况下启动音乐，从而使你的恶作剧应用更加“阴险”。

## 对服务的疑虑

为什么要使用服务？本质上，它是一个独立于用户界面、作为分离进程运行的对象。当开发者希望某项功能（无论是网络相关还是多媒体相关）能够独立运行时，它是最理想的选择。例如音频播放、后台网络事务，以及邪恶的恶作剧应用。

尽管服务允许多个应用绑定（即建立通信通道），但你这里将把它作为一个简单的后台进程来使用。再次强调，服务的用途远不止你在此处应用的简单场景。

**Android 要点**

**29**

![](img/index-36_1.png)

### 创建服务

在源包中添加一个新类。我再次将其命名为 `PrankService`（原创性方面就不加分了）。在最基本层面，服务必须重写 `onBind` 方法。要使服务类能够编译，它至少需要像代码清单 2-5 所示。

#### 代码清单 2-5. 精简版服务

```java
public class PrankService extends Service
{
    public IBinder onBind(Intent intent)
    {
        return null;
    }
}
```

在这个示例中，你不会使用服务交互中的 `onBind` 方法，而只是从主 Activity 中启动和停止服务。为此，你需要在 `Service` 类中再重写两个方法：

- `onStart(int startId, Bundle arguments)`
- `onDestroy()`

当 `onStart` 被调用时，你将开始播放示例媒体文件。当服务被销毁时，你将显式地停止它。这并非绝对必要，但为了便于解释，稍后我们会进一步说明。

### 启动服务

启动一个新服务应该与启动一个 Activity 类似。由于你之前已经接触过几次，这里就直接给出代码，让你自行理解。

**30**

**Android Essentials**

![](img/index-37_1.png)

回想一下之前列出的 `PrankActivity` 中的 `onCreate` 方法。只需用下面这行代码替换注释“我们需要在此处启动音乐服务”：

```java
startService(new Intent
("com.apress.START_AUDIO_SERVICE"), null);
```

同样，这段代码应该看起来很熟悉。启动 Activity 与启动服务之间的唯一区别（除了方法调用不同）在于，可以随 Intent 传递一个 *Bundle*（本质上是一个映射或哈希表）参数。该 Bundle 将被传递给服务的 `onCreate` 方法。

### 启动音乐

与 BREW 和 Java ME 一样，Android 使媒体播放（至少是简单的播放/停止操作）变得非常简单易用。当服务的 `onStart` 函数被调用时，你将加载并播放来自 `/res/raw` 目录的测试音频文件。首要任务是复制一个示例音频文件到 `/res/raw` 目录（如果你没有这个目录，请自行创建）。

接下来，将你准备好的、尊重版权的音频文件放入 `raw` 文件夹。如果你使用 Eclipse，应该向 `R.raw` 添加一个对应元素。在你的案例中，它是 `R.raw.test`。

现在你有了可引用的音乐文件，可以按如下方式向 `PrankService` 添加过程调用：

```java
public void onStart(int startId, Bundle arguments)
{
    MediaPlayer p;
    super.onStart(startId, arguments);
    player = MediaPlayer.create(this, R.raw.test);
    player.start();
}
```

请记住，`onStart` 是一个被重写的方法，因此你需要首先调用父类的同名函数，否则 Android 会报错。此时，你将使用 `MediaPlayer` 静态类

**Android Essentials**

**31**

![](img/index-38_1.png)

创建一个新的媒体播放器对象。由于服务是 Activity 的子类，



## 上下文类

你需要传递一个指向当前上下文的指针，以及代表测试媒体的静态变量。此时，你可以调用 `play`，然后一切便已就绪。通过这项服务，音频应在后台持续播放，直到你的主 Activity 调用了 `stopService` 方法。

当 `stopService` 被调用时，将触发以下方法：

```java
public void onDestroy()
{
    super.onDestroy();
    player.stop();
}
```

### 仁慈之举

既然你是一个善良的恶作剧者，你会给受害者留一条出路。正如你之前所见，Activity 是由意图接收器触发的，而 Activity 随后会启动服务。正如你刚刚看到的，服务负责播放那段会让你的虚拟工程副总裁恼火的噪音。同样，因为你执行报复时心怀仁慈，所以你必须构建一种让受害者关掉音乐的方式。

在你的 `PrankActivity` 中添加以下方法即可实现你的仁爱之举：

```java
public boolean onKeyDown(int keyCode, KeyEvent event)
{
    stopService(new Intent(
        "com.apress.START_AUDIO_SERVICE"));
    finish();
    return true;
}
```

### 清单配置

以下是清单文件的内容：

```xml
<service android:name=".PrankService">
    <intent-filter>
        <action android:name=
            "com.apress.START_AUDIO_SERVICE" />
        <category android:name=
            "android.intent.category.DEFAULT" />
    </intent-filter>
</service>
```

### 报复的禅意与艺术

通过使用一个狡黠的小型恶作剧应用程序，我们探索了意图、意图接收器、服务和 Activity 如何在一个高级的、主要运行在后台的应用程序中协同工作。下面我将逐步介绍你做了什么以及是如何实现的。

#### 实施步骤

你做了以下几件事：

1.  你使用了一个具有适当权限的意图接收器，以及一个系统级的短信意图，使得每当手机收到短信时，你的 `PrankSMSReciever` 对象就会被实例化。如果你的意图接收器检测到一个非常特定的短信内容，它就会通过发送一个意图来启动你的 Activity 作为响应。

2.  这个名为 `PrankActivity` 的 Activity 会监听由 `PrankSMSReceiver` 发送的特定意图动作。当它接收到那个精确的意图动作时，你的 Activity 会向受害者显示一条“逮到你了”的消息。同时，该 Activity 会发送一个意图来启动一项服务。如果在任何时候受害者/用户按下了手机上的某个按键，应用程序将退出，音乐服务也会终止。

3.  名为 `PrankService` 的服务类会监听 `PrankActivity` 的意图，一旦收到即开始播放一个令人讨厌的、预定义的音频文件。它会持续播放，直到被 `PrankActivity` 对 `stopService` 方法的调用所终止。

**注意**  
此示例应用程序不处理手机自带的短信应用程序。由于所有意图接收器都会收到传入意图的通知，你的应用程序将与 Android 的短信收件箱应用程序争夺用户注意力。在实际产品中，这可能需要一个可观的计时器，以及一个更微妙的触发文本内容，而不是像 `“0xBADCAT0_Fire_The_Missiles!”` 这样直白。

## 更多恶作剧思路

以下是你可以自行探索和扩展这个恶作剧应用程序的几种方法：

-   将 Activity 从循环中移除，变得更“邪恶”。直接从意图接收器启动 `PrankService`。不要给受害者关闭音乐的任何途径。

-   添加一个不同的文本内容来停止音乐。这个练习与上一个练习结合进行会非常棒。

-   自定义你的“讨回公道”消息。创建一个触发服务的前缀，以及一个由主应用 Activity 展示的消息内容。使用意图内的额外数据将此内容从意图接收器传递给 Activity。嘲弄，有时需要精准微调。

这些只是一些让你更好地学习 Android 各个组件的方式。


