# 前置要求

如果你主要使用 `Xamarin.Forms`，除了需要具备一定的 C# 基础外，应该能够轻松掌握本书内容。然而，如果你想深入研究 `Xamarin.Android`、`Xamarin.iOS` 或 `Xamarin.Forms` 的自定义渲染器，请注意以下事项：

本书并非 iOS 入门教程。虽然它对关键主题进行了初步介绍，然后进入“进阶”内容，但你仍需参考其他资料来全面掌握 iOS 和 `Xamarin.iOS` 的基础知识。请查阅 `Xamarin.iOS` 入门指南，了解以下主题：
- 使用 `Xamarin Studio` 或 `Visual Studio` 创建 `Xamarin.iOS` 解决方案
- `Xcode` 界面生成器或 `Xamarin Designer for iOS`
- 故事板（Storyboard）和跳转（Segue）
- `UIView` 和 `UIViewController`
- 基本 UI 控件：`UILabel`、`UITextField`、`UIButton` 和 `UIImageView`
- 图片与屏幕尺寸
- 本地资源
- 手势操作

本书也并非 Android 入门教程。虽然它对关键主题进行了初步介绍，然后进入“进阶”内容，但你仍需参考其他资料来全面掌握 Android 和 `Xamarin.Android` 的基础知识。请查阅 `Xamarin.Android` 入门指南，了解以下主题：
- 使用 `Xamarin Studio` 或 `Visual Studio` 创建 `Xamarin.Android` 解决方案
- `Xamarin Designer for Android`
- 活动（Activity）、XML 布局和视图（View）
- 活动生命周期
- 基本 UI 控件：`TextView`、`EditText`、`Button` 和 `ImageView`
- 片段（Fragment）
- 图片与屏幕尺寸
- 本地资源
- 手势操作

## 本书未涵盖的内容

Xamarin 平台是一个庞大的项目，涵盖了多个操作系统的技术和 API。若要详尽地阐述所有这些内容至足够实用的程度，需要数千页而非数百页的篇幅，这意味着一本书无法面面俱到。以下内容不在本书的讨论范围之内：
- 本书正文未涉及 `Xamarin.Forms` 的可扩展应用程序标记语言（XAML），但提供了完整的、可下载的 XAML 代码示例。
- 集成开发环境（IDE），包括 `Visual Studio` 和 `Xamarin Studio`。
- UI 设计器工具，包括 `Xcode` 界面生成器、`Xamarin Designer for iOS` 和 `Xamarin Designer for Android`。
- 部分关于 `Xamarin.Android` 的入门主题（参见“前置要求”）
- 部分关于 `Xamarin.iOS` 的入门主题（参见“前置要求”）

## Windows Phone

`Xamarin.Forms` 应用程序可以在 Windows Phone 上运行。Windows Phone 项目可以构建为支持 Silverlight 或 WinRT。本书是为 Windows Phone Silverlight 实现编写的，但书中大部分内容也适用于 WinRT（并且指出了部分 WinRT 的差异）。

## 系统需求

无论你是在 Mac 还是 Windows 工作站上进行开发，都需要下载并运行 Xamarin 统一安装程序，网址为 `http://xamarin.com/download`。这将使你能够安装和配置 Xamarin 平台，包括 Xamarin Android SDK、Xamarin iOS SDK、`Xamarin Studio`，以及适用于 `Visual Studio` 的 Xamarin 插件。

以下是在 Xamarin 开发中的操作系统和软件要求。

### Mac

要在 Mac 上构建 Xamarin 应用程序，必须满足以下要求。
- 在 OS X 上使用 `Xamarin.Forms` 需要 `Xamarin Studio 5+`。
- 开发 iOS 应用程序：
  - 最新版本的 `Xcode`
  - Mac OS X 10.9.3+（Mavericks）或 10.10+
- 无法在 Mac 上开发 Windows Phone 应用程序。

### Windows

要在 Windows 工作站上构建 Xamarin 应用程序，必须满足以下要求。
- 最新版本的 `Xamarin Studio` 或 `Visual Studio 2012+`（非 Express 版）
- Windows 7+
- 对于 iOS 开发，需要一台联网的 Mac。
- 对于 Windows Phone 开发，你需要 Windows Phone SDK。
- 要使用 PCL（可移植类库），你需要 `Visual Studio 2013+` 或可移植参考库程序集 4.6。若要在不使用 `Visual Studio` 的情况下通过 `Xamarin Studio` 使用 PCL，请下载可移植库工具 2。

### Xamarin.Forms

要使用 `Xamarin.Forms`，你的应用程序构建必须面向以下平台。
- iOS 6.1+
- Android 4.0+
- Windows Phone 8（在撰写本文时，以及之后的更新版本）

## 勘误

作者、技术审阅者以及 Apress 的众多工作人员已尽最大努力查找并消除本书文本和代码中的所有错误。即便如此，仍难免会留下一两处瑕疵。为了让你及时了解情况，Apress 的书籍页面（`www.apress.com/9781484202159`）上有一个“勘误”选项卡。如果你发现任何尚未报告的错别字或错误代码，请通过电子邮件 `support@apress.com` 告知我们。

## 客户支持

Apress 希望听取你的意见——你喜欢什么、不喜欢什么，以及你认为下次可以做得更好的地方。你可以将意见发送至 `feedback@apress.com`。请务必在邮件中注明书名。

## 联系作者

你可以在 Twitter 上关注我（`@lexiconsystems`）、阅读我的博客（`www.mobilecsharpcafe.com`），或发送电子邮件至 `dan@lexiconsystemsinc.com` 联系我。

如果你需要 Xamarin 产品的常规技术支持，请访问 Xamarin 支持页面 `http://xamarin.com/support` 或 Xamarin 论坛 `http://forums.xamarin.com/`。



