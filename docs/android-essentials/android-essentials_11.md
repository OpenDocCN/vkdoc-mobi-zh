# Android 开发核心要素

## 启动画面计时

烦人的应用程序，所以你需要进入主菜单。你将使用一个简单内联定义的线程来完成计时。在线程之前初始化了几个常量，我已经包含在内。为了完整性，我包含了`onCreate`方法的全部内容。代码清单 2-1 展示了我的带计时线程的代码。

***代码清单 2-1. 为启动画面计时***

```java
long m_dwSplashTime = 3000;
boolean m_bPaused = false;
```

**Android 开发核心要素**

![图](img/index-19_1.png)

```java
boolean m_bSplashActive = true;

public void onCreate(Bundle icicle)
{
    super.onCreate(icicle);
    // 绘制启动画面
    setContentView(R.layout.splash);

    // 非常简单的计时器线程
    Thread splashTimer = new Thread()
    {
        public void run()
        {
            try
            {
                // 等待循环
                long ms = 0;
                while(m_bSplashActive && ms < m_dwSplashTime)
                {
                    sleep(100);
                    // 仅当程序运行时推进计时器
                    if(!m_bPaused)
                        ms += 100;
                }
                // 前进到下一个屏幕
                startActivity(new Intent(
                    "com.google.app.splashy.CLEARSPLASH"));
            }
            catch(Exception e)
            {
                Log.e("Splash", e.toString());
            }
            finally
            {
                finish();
            }
        }
    };
    splashTimer.start();
}
```

**Android 开发核心要素**

**13**

![图](img/index-20_1.png)

终于要进入 Java 代码部分了。这个简单线程将运行直到时间计数器超过`m_dwSplashTime`。虽然还有其他实现计时器的方法，但我喜欢这个方法的两个原因：它可以被暂停。计时器仅当`m_bPaused`标志为`false`时才会推进。正如你稍后将看到的，如果你的活动的`onPause`方法被调用，挂起计时器很容易。这对启动画面来说并不总是必需的，但对于其他基于定时的操作却很重要。

移动到下一个屏幕就像将`m_bSplashActive`标志设置为`false`一样简单。如果按这种方式实现，前进到下一个屏幕不需要先移动再取消更传统的计时器。

有了这段代码，你应该会看到启动画面持续你以毫秒为单位设置的`m_dwSplashTime`时间。当时间到了或者用户通过按键中断启动画面时，`startActivity`将被调用（我稍后会解释）。这个函数会将用户移动到下一个章节中将成为主菜单的界面。`finish`会关闭启动活动，这样用户从主菜单按返回键时就不会回到它。你需要实现一个接受`CLEARSPLASH`意图动作的活动。同时，让我们回顾一些你想要重写的其他重要活动方法。

## 暂停、恢复、冲洗、重复

当你的活动被来电、短信或其他中断挂起时，暂停启动计时器非常简单，如下所示：

```java
protected void onPause()
{
    super.onPause();
    m_bPaused = true;
}
```

**Android 开发核心要素**

**14**

![图](img/index-21_1.png)

与大多数这些重写的方法一样，你需要在做其他任何事情之前调用超类。如果你回顾计时器线程，你会看到负责跟踪经过时间的`ms`计数器在`m_bPaused`为`true`时不会推进。此时，我相信你已经能猜到`onResume`会是什么样子：

```java
protected void onResume()
{
    super.onResume();
    m_bPaused = false;
}
```

这里毫无意外。当你的应用程序恢复时，计时器线程将恢复向`ms`计数器添加时间。

## 基本按键处理

活动中的按键处理通过重写`onKeyDown`方法来实现。我们将使用这个函数允许用户取消你初生的启动画面。正如你在本节开头计时器线程中看到的，你已经通过`m_bSplashActive`变量在计时器循环中设置了一个退出条件。为了退出，你只需重写`onKeyDown`方法，将启动标志设置为`false`。以下是需要添加的代码：

```java
public boolean onKeyDown(int keyCode, KeyEvent event)
{
    // 如果按下任何键，清除启动画面
    super.onKeyDown(keyCode, event);
    m_bSplashActive = false;
    return true;
}
```



### **Android 核心要点**

**15**

![index-22_1.png](img/index-22_1.png)

#### **补充练习**

如果你想将这个启动屏打造成一个功能完整的版本，还需要添加两个元素。这给了我一个绝佳的理由来给你布置一些作业。试着添加以下两项功能：

-   允许用户仅在点击“确定”或中间键时跳过启动屏。
-   让返回键退出应用程序，而不是将其放入后台。

提示：要完成这些任务，你需要查阅 Android SDK 文档中的按键码映射。

#### **明确 Intent**

在完成启动屏之前，还有一件事需要做。我相信你一定对之前那个 `startActivity` 方法调用感到好奇。这意味着是时候在这个有限的上下文中，简要地谈谈 Intent 了。*Intent* 是一个对象，它充当两个或多个 Activity、内容处理器、Intent 接收器或服务之间的通信事件。在本例中，你将使用 Intent `com.google.app.splashy.CLEARSPLASH` 来调用 `startActivity`。当调用 `startActivity` 时，Android 会搜索其所有清单文件，查找已注册了上述 `CLEARSPLASH` Intent 动作的节点。恰好，你将添加一个名为 `MainMenu` 的 Activity，它就会注册处理这样一个 Intent。

为了创建将成为主菜单的 Activity，在你的现有源码包中添加一个名为 `MainMenu` 的新类。接下来，确保它继承 `Activity` 类，实现 `onCreate` 方法，并在 `R.layout.main` 布局上调用 `setContentView`。此时，你需要打开 `AndroidManifest.xml` 并向其中添加一个新的 Activity 元素。在

**16**

**Android 核心要点**

![index-23_1.png](img/index-23_1.png)

启动屏的 `</activity>` 结束标签之后，你应该插入以下内容：

```xml
<activity android:name=".MainMenu"
          android:label="@string/app_name">
    <intent-filter>
        <action android:name="com.google.app.splashy.CLEARSPLASH"/>
        <category android:name="android.intent.category.DEFAULT"/>
    </intent-filter>
</activity>
```

将 Activity 的名称定义为 `.MainMenu`。这将会告诉 Android 加载并运行哪个 Java 类。在 `intent-filter` 标签内，注册 `com.apress.splash.CLEARSPLASH` Intent 动作。实际上，这个 Intent 的名称可以是 `beef.funkporium.swizzle`，只要该名称在 `startActivity` 调用和前面列出的 Android 清单片段中保持一致，一切就会正常运转。

#### **运行应用**

如果你到目前为止已经认真学习了，那么此时运行你的应用程序，结果应该是启动屏会显示几秒钟，然后由你新创建的主菜单 Activity 获取焦点。并且，一旦应用程序进入主菜单，就不应该再能回到启动屏。如果遇到问题，请确保你的新主菜单已列在清单文件和 `R.java` 中。同时，确保你在新的 Intent 中绘制了正确的布局文件。

#### **Activity 的生命周期**

Activity 的生命周期在谷歌文档中有详尽的介绍。不过，既然我在回顾构成一个 Activity 的基本要素，就不能错过这个重要信息。现在，有了你的启动屏，你应该已经准备好继续前进了。

**Android 核心要点**

**17**

![index-24_1.png](img/index-24_1.png)

为了便于解释，我还向启动屏 Activity 中添加了以下函数：

```java
protected void onStop()
{
    super.onStop();
}

protected void onDestroy()
{
    super.onDestroy();
}
```

如果你在新创建的启动 Activity 中的所有函数内设置断点，并以调试模式运行它，你会看到断点按以下顺序被触发：

1.  `onCreate`
2.  `onStart`
3.  `onResume`
4.  此时，你的 Activity 正在运行。三秒钟后，计时器线程结束，并使用清除启动屏的 Intent 调用 `startActivity`。接下来，它会调用 `finish`，这告诉 Android 关闭启动屏 Activity。
5.  `onPause`
6.  `onStop`
7.  `onDestroy`

这是一个 Activity 从开始到结束的通用生命周期。你可以在谷歌文档 [`code.google.com/android/reference/android/app/Activity.html`](http://code.google.com/android/reference/android/app/Activity.html) 中找到关于 Android Activity 生命周期和时间的更全面阐述。你甚至还会看到一个漂亮的图表。本质上，手机利用上述函数的组合来向你提示应用中可能发生的重大事件：启动、关闭、暂停和恢复。正如我之前提到的，Activity

**18**

**Android 核心要点**

![index-25_1.png](img/index-25_1.png)

将是任何传统应用程序的核心构建块；它们让你能够控制屏幕并接收用户输入。后续章节将更深入地探讨用户交互。

#### **迄今为止**

到目前为止，我探讨了 Activity 如何集成到手机中，它们如何启动和停止，以及它们如何在基本层面上进行通信。我演示了如何显示一个简单的 XML 视图屏幕，以及如何通过在按键事件发生时或设定的时间结束时，在两个 Activity 之间进行切换。短期内，你需要进一步了解 Android 如何使用 Intent、Intent 接收器和 Intent 过滤器进行通信。为此，你需要另一个示例应用程序。

### **创建 Intent 接收器**

*Intent 接收器* 是 Android 中为数不多的几个名副其实的东西之一。它的作用是等待注册过的 Intent 动作（Android 版的 BREW 风格通知）。我将使用一个不太具有生产价值的应用程序来演示 Intent 接收器的一个较为棘手的任务：接收并响应传入的短信。

#### **设置场景**

让我为你描绘一个场景。一天下午，你回到办公桌前，发现自己遭遇了一场“Hello Kitty”袭击。你的办公桌从地毯到天花板，到处都贴满了这个人类已知最令人厌烦的 icon 的可爱粉色图案。你知道是谁干的，复仇的时候到了。你还知道，你们工程副总裁对某首歌深恶痛绝，那种炽热的仇恨无与伦比。那首歌就是《La Bamba》。你决定通过在你同事的 Android 手机上安装一个潜伏应用程序来报复他。我将向你展示如何制作一个 Android 应用程序，它能对特定短信做出响应，播放一个音频文件，以达到最大的羞辱效果。

**Android 核心要点**

**19**

![index-26_1.png](img/index-26_1.png)

这将使你新结下的仇敌成为你们副总裁强烈愤怒的对象。同时，你也要让你的受害者知道他中招了……并给他一个关闭声音的机会。这个恶作剧应用程序需要一个 Intent 接收器、一个 Activity、一个服务，以及这三者之间通信的方式。想象一下，当你同事的手机开始播放你们工程副总裁最痛恨的那首歌时，他会多么惊讶。

##### **这有什么实际用途吗？**

这是个很好的问题。虽然表面上这看起来可能不是最实用的应用程序，但我相信，只要稍加想象，你就能为这个小小的恶作剧应用程序找到各种重要的实际用途，从推送邮件通知到内部应用通信。此外，到目前为止内容一直过于严肃。我们将分四个阶段进行。在每个阶段，你都会学到更多关于 Intent 接收器、服务以及所有这些应用程序组件之间交互的知识：

-   在短信到达时接收通知
-   打开短信内容并查找特定的有效载荷
-   在短信到达时启动一个 Activity，并意识到这次启动是由 Intent 接收器发起的
-   启动一个播放音频文件的新服务

##### **使用 Intent 接收器**



在开始构建意图接收器之前，你需要花一点时间了解为何要使用它。意图接收器几乎不占用内存、不需要链接或额外开销。当活动（Activity）必须在启动时加载所有繁重的导入类时，意图接收器则没有这些负担。由于某些类型的意图可能会以极高的频率到达（例如网络状态更新），一个轻量级的 **Android 基础** 对象必须首先对数据进行解析。如果此时适合唤醒一个更大的用户界面进程或繁重的后台服务，意图接收器就应该采取这样的行动。

**提示**  
意图接收器可能会被频繁地启动和关闭（取决于它们监听的内容）；请尽量让它们保持轻量，并尽可能少地使用库。如果你在处理某个特定事件时插入了过多开销，导致手机运行缓慢，用户是不会高兴的。

### 构建意图接收器

首先，你需要为这个恶作剧小应用创建一个新项目。在源码目录中，创建一个新类，它将作为新的意图接收器。初步来看，它应该像这样：

```
public class PrankSMSReceiver extends IntentReceiver
{
    public void onReceiveIntent
    (Context context, Intent intent)
    {
        return;
    }
}
```

现在你已经设置好了这个类，接下来需要告诉 Android 你希望接收短信事件。你需要修改 `AndroidManifest.xml` 文件，以获取权限并注册 `RECEIVE_SMS` 意图动作。

### 权限

运营商、用户甚至开发者可能都不希望让 Android 应用随意访问手机和网络的特权层。因此，Google 在 Android 中引入了 **Android 基础** 权限的概念（所有有移动开发经验的开发者都应该认识它）。要能够接收短信，你需要通知手机你已经获得接收权限。

由于在 Android 中，权限是针对特定清单中的所有元素声明的，你需要在 `<manifest>` 声明标签之后添加权限行（代码清单 2-2）。示例应用将命名为 `PrankApp`，其主活动称为 `PrankActivity`。对于原创性，笔者不指望获得任何命名奖项。

**代码清单 2-2. 添加接收短信的权限**

```xml
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    package="com.apress.book.PrankApp">
    <uses-permission android:name="android.permission.RECEIVE_SMS" />
```

如果没有权限标记，Android 在收到短信时就不会启动你的应用。后面我还会介绍其他需要涉及的权限。与此同时，你可以在 [Android 官方文档：http://code.google.com/android/reference/android/Manifest.permission.html](http://code.google.com/android/reference/android/Manifest.permission.html) 中找到所有权限的列表。

### 也把短信发给我！

现在你已经获得了与手机短信层交互的权限，接下来需要告诉手机在收到新短信时该做什么。为此，你必须像之前一样打开 `AndroidManifest.xml` 文件。你需要在现有活动旁边添加一个新的意图接收器。代码清单 2-3 展示了需要插入的内容（为了便于参考，我保留了 `</activity>` 标签）。**Android 基础**

**代码清单 2-3. 注册新的意图接收器以接收传入短信**

```xml
</activity>
<receiver android:name="PrankSMSReceiver" android:enabled="true">
    <intent-filter>
        <action android:name="android.provider.Telephony.SMS_RECEIVED" />
        <category android:name="android.intent.category.DEFAULT" />
    </intent-filter>
</receiver>
```

每当手机收到一条传入短信时，这就是你接收意图通知所需的一切。



## 一窥意图接收器的实际运行

实现这一点比听起来要困难一些。必须有进程正在运行，DDMS（调试器应用程序）才能附加到它。但在大多数情况下，除非有新事件触发，否则你不希望应用程序运行。解决方法是利用 Eclipse IDE 玩一点“花招”。

随着你进入后续步骤，这个过程可能会变得稍微复杂一些。

在你的 `onReceiveIntent()` 方法内部设置一个断点。开始调试应用程序，让模拟器停留在“Hello World, PrankActivity”屏幕上。

在 Eclipse 中，切换到 DDMS 视图。你可以按几次 Command+F8 或选择菜单 `Window` → `Open Perspective` → `DDMS` 来完成此操作。

图 2-1 展示了它的样子。

