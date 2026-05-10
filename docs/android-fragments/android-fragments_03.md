# Android Fragments 简介

Android 碎片化问题与同时存在于世界上的许多不同版本的 Android 有关。虽然这与`Fragment`无关，但你会想了解如何在早于`Fragment`引入的旧版 Android 上使用`Fragment`。Google 通过使用兼容性库使其成为可能，因此第 5 章将向你展示如何使用它们。读完本章后，你将能够编写一个使用`Fragment`的应用程序，并使其在像 Froyo（Android 2.2）这样旧的设备上得到支持。

[www.it-ebooks.info](http://www.it-ebooks.info/)

## 引言

**xix**

这本迷你书以关于`AsyncTask`的一章作为结尾，`AsyncTask`是一个非常有用的结构，用于在应用程序后台执行工作，同时能够更新 UI（你猜对了，是在`Fragment`中渲染的 UI）。

整本书中，许多示例程序都附有代码清单进行解释。完整的示例程序都可以从我们的网站下载，因此你将能够轻松地跟上学习进度，并且拥有一套非常适合实验以及用于启动你自己应用程序的工作示例程序。

## 如何为 Android Fragments 做准备

虽然我们使用了最新的 Android 版本（5.0）来编写和测试 *Android Fragments*，但本书的内容与任何 Android 版本都相当独立。大多数（如果不是全部）示例程序和代码即使在未来的版本中也能正常工作。为了提高这些章节的可读性，除其他改进外，我们减少了典型的冗长源代码。取而代之的是，每个章节的源代码都可以在 `apress.com` 和我们的支持网站 `androidbook.com` 上获取。我们仍然在文本中包含源代码，但那是为了理解概念而需要查看的重要代码。

你将能够下载每个章节的源代码并直接将其加载到 `Eclipse` 中。如果你使用的是 `IntelliJ` 或其他编辑器，你可以解压每个章节，并通过手动将项目导入到你喜欢的 IDE 中来构建代码。

如果你正在使用我们在任何书籍（包括 *Android Fragments*）中涵盖的任何主题进行编程，请记住我们的网站

[androidbook.com](http://androidbook.com) 和 [satyakomatineni.com](http://SatyaKomatineni.com) 为每个主题设有专门的知识文件夹。这些知识文件夹记录了每个主题中的各种项目。例如，在本书中，你将看到在相应上下文中开发代码时所需的 Android API 链接。简而言之，我们经常使用这些网站来获取代码片段，并快速访问 Android API 链接。

## 如何联系我们

你可以通过我们的各自电子邮件地址轻松联系我们：Dave MacLean（邮箱：davemac327@gmail.com）和 Satya Komatineni（邮箱：satya.komatineni@gmail.com）。另外，请将此 URL 加入书签：[`www.androidbook.com`](http://www.androidbook.com)。在这里，你将找到源代码链接、可下载项目链接、来自读者的重要反馈、完整的联系信息、未来通知、勘误表、我们未来项目的新闻、阅读指南以及额外资源。

我们真心希望你喜欢我们的书，并欢迎你的反馈。

[www.it-ebooks.info](http://www.it-ebooks.info/)

---

