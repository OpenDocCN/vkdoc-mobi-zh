# 第 1 章：引言

在开始之前，我们需要快速讨论一下你的起点。

## 你需要具备哪些知识才能开始

你可能会问自己一个很自然的问题：这本书适合你吗？

是的，显然是的，因为你正在读它。不过，我会对你的能力做一些假设。



你了解 Java；你能编写、阅读并理解它。由于本书关注的是 Android 平台而非语言本身，我会在文中大量内联 Java 代码，并假设你能跟上进度。如果你的 Java 技能有些生疏，我建议你查阅 Apress 网站上丰富的 Java 资料（[`java.apress.com/`](http://java.apress.com)）。

熟悉其他移动平台会对你有所帮助。在阅读本书的过程中，我会将 Android 与其他移动软件开发工具包（SDK）进行对比。你完全不必是一名专业的移动开发者也能跟上节奏。

你需要具备出色的黑客技能。其实也不尽然，但如果你习惯于撸起袖子深入问题核心，那么读这本书时你会感到得心应手。

我将假设你在 Android 开发方面**毫无经验**。如果你已掌握基础知识，可以跳过第一章，直接关注后面更高级的主题。

这个要求列表并不长，但其中包含的几点内容能帮助你轻松地阅读本书而不至于抓狂。

理想情况下，我希望这本书对任何有兴趣为 Android 开发应用的人都能派上用场且富有价值。业余爱好者可以在这里为自己的梦想应用找到基础。移动游戏开发者能找到图形输出和用户输入的核心细节。多媒体和应用开发者能找到构建下一个杀手级应用所需的所有技巧、窍门和核心功能。如果你是一位商界人士，正考虑将现有应用移植到 Android 平台，你会在这里找到如何实现这一目标的无价信息。简而言之，无论你的目标、经验、时间或兴趣如何，这本书都能为你提供丰富的内容。

## 如何最好地利用本书

简单的答案是阅读它，但这对于不同的人可能意味着不同的方式。如果你是移动开发或 Android 的新手，最好把本书当作教程来读。逐章跟随，直到掌握所有入门所需的基础知识。

如果你是一位经验丰富的 Java 和移动开发者，但对 Android 不太熟悉，那么你可以在通读第一章了解概况后，把本书更多地当作参考手册来使用。

在整本书中，我将主要使用真实世界的示例来帮助你熟悉 Android。对于已经是资深 Android 开发者的人来说，本书可能吸引力不大。如我之前所说，我将从零基础开始，假设你对这个 SDK 毫无经验。本书将从简单的内容开始：闪屏、主菜单和一些简单的多媒体。随后我会深入讲解更高级的概念，如蓝牙、基于位置的服务、后台应用以及 Android 提供的其他令人兴奋的功能。

话不多说，现在就开始吧。

## 入门指南

首先需要安装 SDK。就我个人而言，我是在 Mac OS X 上使用 Eclipse 进行开发。所有屏幕截图、IDE 信息、技巧和窍门都将针对 Eclipse IDE。Android 开发者似乎考虑到了开源 IDE Eclipse，因为他们发布了简化设置和调试的插件。为简单起见，我使用 Eclipse 和开放手机联盟的 Android。我并不特别推崇这一组合。不过，我会花一点时间讲解如何下载和配置 Eclipse 以使其与 Android 集成。如果你已经可以运行 SDK，请直接跳到“Android 项目”部分。此外，你也可以在谷歌的 [SDK 安装页面](http://code.google.com/android/intro/installing.html#installingplugin)找到更详细的安装指南。



### 安装 Eclipse

同样，由于本书示例中会用到 Eclipse，请下载完整版，下载地址为 [`www.eclipse.org/downloads/`](http://www.eclipse.org/downloads)。

务必获取 Java EE 版本。该版本包含了一些框架，可供完整的 Google Eclipse 插件中的部分编辑器使用。安装 Eclipse；使用默认配置即可正常运行。

### 注意

无论是 Windows 还是 Mac 系统，建议将你的文件和 SDK 安装目录放在不含空格的文件夹中。许多工具（如 Ant 等）可能会因文件夹名称中的空格而出错。

### 获取 Android SDK

你可以在 Google 网站上找到 Android SDK，地址为 [`code.google.com/android/download.html`](http://code.google.com/android/download.html)。

下载它，将其保存到方便的位置，然后解压。我将 SDK 放在我的开发文件夹 `/Developer/AndroidSDK` 中。你也可以轻松地将 ZIP 文件放在文件系统的任何位置。只需记住存放位置，因为后续你需要告诉 Eclipse 这个位置。另外，如果你是 Windows 或 Linux 用户，建议将 Android 工具的路径添加到你的 `Path` 变量中。

### 安装 Eclipse 插件

在使用 Android 时，我喜欢带有快捷键（如 Eclipse IDE 中的快捷键）的图形用户界面。为了获得超越基本功能的使用体验，你需要下载 Android 开发者工具。若要从 Eclipse 内部安装它，请按照 Google 提供的指南操作，地址为 [`code.google.com/android/intro/installing.html#installingplugin`](http://code.google.com/android/intro/installing.html#installingplugin)。

如果你已经安装了旧版本的 SDK 和 Eclipse 插件，建议你现在就使用前面提到的链接，将其更新到 M5-RC15（或最新版本）。旧版本与最新版本之间发生了足够多的变化，这些细节变化可能会造成混淆。如果你正确遵循了指南，但在尝试安装 Android 编辑器时遇到错误，请返回并安装 Java EE Eclipse 的完整版本。基础 Java SDK 不包含 Android 插件所需的所有正确包。

别忘了将 Android 插件指向你解压 SDK 副本的位置。该路径在 Android 标签页的 `Windows/Preferences/Android` 中。

通过选择 `File` -> `New` -> `Android Project` 来创建一个新项目。为项目和 Activity 取一个你喜欢的简洁名称。你还需要在源包名中至少插入一个点号 (`.`)，例如 `apress.book.sample` 或 `crazy.flyingmonkey.application`。

如果你忘记提供由点号分隔的多个名称，Eclipse 会显示一个相当不友好的错误信息。我个人可以作证，当你大脑疲惫，比如正赶着交稿时，这可能会相当令人沮丧。

### Android 项目

现在，你已经成为自己第一个基本 Android 应用的骄傲拥有者。如果你跳过了上一节，导致目前并非如此，请立即创建一个新项目。默认情况下，新项目包含一个“Hello, World”的实现。在我看来，那些以解释“Hello, World”开头的书籍和文章并不特别有用。因此，我不会占用你的时间来剖析最基本 Android 应用的功能。然而，值得花些时间研究的是你新项目的内容、布局和文件。让我简要解释每个文件和目录如何构成一个可运行的 Android 项目（见表 1-1）。

**表 1-1. 基本 Android 项目中的文件**

| 文件名 | 用途 |
|---|---|
| `YourActivity.java` | 默认启动 Activity 的文件；后续将详细介绍。 |
| `R.java` | 包含所有资源常量 ID 的文件。 |
| `Android Library/` | 包含所有 Android SDK 文件的文件夹。 |
| `assets/` | |



### 多媒体及其他必需文件

#### `res/`

UI 所用资源的基础目录。

##### `res/drawable`

存放 UI 层需要渲染的图像文件的目录。

##### `res/layout`

所有 XML 格式的视图布局文件都存放在这里，后续将详细介绍。

***续***

