# 第 4 章：在应用中保存内容

单例的另一个常见用途是管理资源，例如网络连接。与其让应用中的每个视图控制器都从网络加载数据，不如通过一个集中点来管理这些操作，从而避免同时尝试加载多个连接。这对性能和电池寿命都有好处。稍后我们将介绍一些网络相关的技术。

## 将数据持久化到文件

到目前为止，本章已经介绍了在应用中移动数据的各种方法。它们都有一个共同的缺点：无法以应用之外的生命周期保存数据。当你的应用退出时，键值观察通知将停止触发，系统通知无法到达你的代码，你的委托也会消失。理想情况下，你应该在应用退出前将用户数据保存到磁盘，并在应用启动时加载进来，这样用户的工作就能跨越应用会话而持久存在。有几种保存数据的方法，每种方法都有其优势和局限性。我们从最简单的方法开始：用户默认设置（User Defaults）。

我们还将介绍使用归档（Archiving）将数据保存到文件，以及直接写入文件的方法。

### NSUserDefaults

如果你是一位 Mac 老用户，那么你可能曾经想让某些应用启用隐藏偏好设置。例如，要在 Finder 中查看隐藏文件，技巧是打开终端并执行以下命令：`defaults write com.apple.finder AppleShowAllFiles TRUE`

重启 Finder，一切就绪。Mac 上的 `defaults` 命令是与 Mac OS X 的用户默认设置系统交互，而该系统在 iOS 上也存在。在 Mac 上，你的家目录中，应用偏好设置可以在 `~/Library/Preferences/` 目录下找到。每个文件对应一个应用的偏好设置，它们包含键-值对，就像 `NSDictionary` 一样。`NSUserDefaults` 为你管理这些文件，让你无需手动保存、加载和管理，从而可以设置持久化的值。在 iOS 上同样简单。要将值保存到名为 `userName` 的键中，只需调用 `NSUserDefaults` 单例：

```
[[NSUserDefaults standardUserDefaults] setObject:@"Jeff" forKey:@"userName"];
```

从那时起，除非你删除该键或用户删除应用，否则你可以同样轻松地随时获取 `userName` 的值：

[www.it-ebooks.info](http://www.it-ebooks.info/)

