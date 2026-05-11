# 第 6 章：集成网络与 Web 服务

**图 6-5.** *设置应用中的 Twitter 设置页面，已配置 Twitter 账户*

一旦你设置好 Twitter 账户，我们就可以开始编写代码了。我们要做的第一件事是创建一个 Twitter 控制器对象。这个控制器，我们将把它实现为单例，将处理所有需要与 Twitter 服务器通信的方法。通过将其放在一个单独的类中，我们可以恰当地封装这些方法。在 Xcode 中，选择 File → New → File… 或按 `⌘+N` 创建一个新文件。在左侧栏选中 Cocoa Touch，然后选择 Objective-C Class。点击 Next，将类命名为`LCTTwitterController`，作为`NSObject`的子类。点击 Next，并将文件保存到磁盘。打开头文件（`LCTTwitterController.h`），并添加以下加粗显示的方法声明：

[www.it-ebooks.info](http://www.it-ebooks.info/)

