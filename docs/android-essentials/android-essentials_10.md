# 第 2 章：应用程序

Android 中的应用程序由其清单文件的内容定义。每个 Android 应用程序都通过 `AndroidManifest.xml` 声明其所有 activity、入口点、通信层、权限和 intent。四个基本构建块组合在一起，构成了一个丰富的 Android 应用程序：

- **Activity**：Android 应用程序最基本的构建块
- **Intent receiver**：一个为处理特定任务而启动的反应式对象
- **Service**：一个没有用户界面的后台进程
- **Content provider**：一个用于处理和存储数据的基本超类框架

在本章中，我将通过具体的功能示例来分解每一个特定部分。首先是 activity，这是独立 Android 应用程序的核心构建块。

## 行动起来

所有传统的 Android 移动应用程序都将围绕 activity 展开。如果你有过其他移动平台的经验，Android 的 activity 与 BREW 的 applet 或 Java ME 的 midlet 非常相似。然而，它们之间有几个非常重要的区别。

### Android 与 Java ME 和 BREW 的对比

在绝大多数情况下，BREW 应用程序由单个 applet 组成。该 applet 通过接收和发送事件与手机的其他部分通信。另一方面，你可以将 Java ME 应用程序视为 `Midlet` 类的扩展。midlet 具有用于启动、停止、暂停、按键处理以及执行手机与应用程序之间任何其他交互的函数。一个 Java ME 应用程序通常由一个 midlet 组成。

Android 应用程序可以通过 `AndroidManifest.xml` 文件在手机上注册任意数量的 activity。Android 的多 activity 架构可能是 Android 开发与其他手机 SDK 开发之间的主要区别。这一点使得编写模块化、分区的代码变得容易得多。在 BREW 和 Java ME 中，开发者通常会在 midlet 或 applet 的范围内实现大部分功能。而在 Android 中，你可以编写 activity、内容处理器、intent receiver 或 service 来处理几乎任何事情。一旦你编写了一个用于编辑文本文件的 activity，你就可以通过发送和接收 intent 动作，在所有未来的应用程序中引用这个 activity。这并不是说在 BREW 或 Java ME 中无法实现这样的架构。只是必须在 Java、C 或 C++ 层面，或者在 BREW 中通过繁琐的扩展来完成，而无法像 Android 那样平滑地内置于应用程序框架中。

### 功能

就像 midlet 一样，activity 使用一系列函数与外部世界交互。基本上，你的 activity 必须重写 `onCreate` 方法。你希望重写的其他函数包括 `onStop`、`onPause`、`onResume` 和 `onKeyDown`。正是这几个函数让你能够将 activity 与整个 Android 手机绑定在一起。

默认情况下，在 Eclipse 中创建的新 Android 应用程序会实现一个“Hello, World”应用程序。我将向你展示如何从这个基础应用程序升级为一个功能完整的启动画面。

### 打造启动画面

如果你想将此启动画面示例作为自己 Android 应用程序的起点，或者想以更被动的方式跟着学习，可以从 `www.apress.com` 的下载区下载打包好的版本。在本示例中，由于是你的第一个示例，我将通过一系列小步骤来讲解。我会精确分解需要编写的内容，从添加新的图像资源到修改 XML 布局文件。在后续章节中，示例将不会细化到如此细节。这应该是唯一一个读起来像面向初学者的教程的章节。

#### 添加图像资源

首先，你需要一个示例启动画面图像。我提供的这个“社交尴尬”型启动画面不会赢得任何奖项，但对于当前在移动领域似乎不断涌现的社交网络应用热潮，这是一个不错的调侃。

要添加这个新资源，我将 `menu_background.jpg` 放在了 `res/drawable` 目录下。确保 `R.java` 中已添加一个新的 ID。它应该像这样：

`public static final int menu_background=0x7f020001;`

现在，这是你从代码中加载和绘制图像的方式。我们将在下一章关于用户交互的内容中再回到这个概念。

#### 创建 XML 布局文件

既然你有了图像资源，就可以将其添加到 XML 布局文件中。这些文件存放在 `res/layout/` 目录下，目前你应该有一个名为 `main.xml` 的文件。添加一个名为 `splash.xml` 的新 XML 文件，并将 `main.xml` 的内容复制进去。然后，修改该文件，移除 `<TextView>` 标签，并添加一个如下所示的 `<ImageView>` 标签：

```
<ImageView android:src="@drawable/menu_background"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent">
</ImageView>
```

使用 Android 的 XML 布局对象简单直接。正如我提到的，`/res` 目录中的文件可以通过前面展示的 `@` 符号进行引用，例如 `android:src="@drawable/menu_background"`。此外，`layout_width` 和 `layout_height` 决定了图像视图的大小。请确认你的新布局文件已添加到 `R.java` 中。它应显示如下：

`public static final int splash=0x7f030001;`

#### 绘制启动画面

现在启动画面已定义，是时候激活并绘制它了。你现有的 Android activity 正在绘制 `main.xml`，因此你需要切换到新的启动布局。要进行切换，将方法 `onCreate` 中的以下代码：

`setContentView(R.layout.main);`

更改为：

`setContentView(R.layout.splash);`

运行应用程序，观察你新创建的启动画面生效。如果你到目前为止遇到了错误，请检查所有名称是否匹配。如果图像没有显示，请确保它已正确放置在 `res/drawable` 文件夹中，并且 `splash.xml` 引用了正确的名称和文件。

#### 时机几乎就是一切

启动画面现在正在渲染，但仅有启动画面还不足以...



