# Android 基础

**5**

![](img/index-12_1.png)

### 表 1-1. 续

| 文件名 | 用途 |
|---|---|
| `res/values` | 存放字符串和配置文件的位置。 |
| `AndroidManifest.xml` | 向外部操作系统描述应用程序的文件。 |

显然，你早已超越了`Hello_World.c`的时代。随着学习的深入，我会在文中提及这些文件。如果你在阅读后续章节时遇到困难，请参考此表。接下来，我将花几分钟时间解析几个关键点。

在各种文件中，最核心且可能最令人困惑的就是 Android 清单文件。它是你的应用程序与外部世界之间的桥梁。在这个文件中，你需要为 Activity 注册 intent（下一章我会详细介绍 intents 和 activities 及其工作原理）。为了让你具备进一步开发的基础，代码清单 1-1 展示了创建第一个项目时的 Android 清单文件内容。

### 代码清单 1-1. `AndroidManifest.xml`

```xml
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
          package="apress.book.sample">
    <application android:icon="@drawable/icon">
        <activity android:name=".SampleApp"
                  android:label="@string/app_name">
            <intent-filter>
                <action android:name="android.intent.action.MAIN" />
                <category android:name="android.intent.category.LAUNCHER" />
            </intent-filter>
        </activity>
    </application>
</manifest>
```

**6**

**Android 基础**

![](img/index-13_1.png)

XML 声明之后是带有 schema URL 的`<manifest>`标签。其属性之一是你的源包名称。在这个示例中，它是`apress.book.sample`。

接下来是应用程序声明以及应用图标的路径。`@drawable`表示法表明该项目可以在`res/drawable`文件夹中找到。此时，该应用程序仅包含一个 Activity：`SampleApp`。Activity 名称是硬编码的，但其标签（即用户在 Android 应用程序菜单中看到的内容）定义在`res/values/strings.xml`文件中。`@string`表示法则从上述 XML 文件中的`app_name`项获取值。

现在来看 intent 过滤器。我将在下一章详细讲解 intent 过滤器，但目前你只需要知道`android.intent.action.MAIN`和`android.intent.category.LAUNCHER`是预定义的字符串，它们告诉 Android 在启动你的应用程序时应该激活哪个 Activity。

这就是最简形式的清单文件的所有内容。为了节省篇幅，我尽量不再重复写出整个清单文件。如果后续插入新元素时你需要回顾上下文，请折起本页，以便随时查阅。

**注意**

> 养成尽可能将字符串、资源和屏幕布局文件放入`res`文件夹的习惯。前期多花些时间处理这些文件可能会增加一些工作量，但一旦你开始将应用程序移植到不同的语言、屏幕尺寸和功能集时，前期的准备时间将得到加倍的回报。我相信这在计算机手册中你经常读到，但值得重复强调。前期的一点规划和额外投入，可以为项目后期的移植和调试节省数周时间。这对于移动设备开发尤为重要，因为每个应用程序可能包含多个独立的构建环境。

**Android 基础**

**7**

![](img/index-14_1.png)

接下来在重要文件列表中，是`R.java`。这个文件存放了所有资源（包括图形资源和可绘制资源）的引用标识号。向`res/drawable`中添加新图形应该会



