# iCloud 存储

你可以将文档存储在云端，这与第 18 章中将属性列表值存储在云端的方式非常相似。当然，文档的处理会稍微复杂一些。

苹果的指南建议你提供一个设置选项，允许用户将所有文档放在云端，或者完全不放在云端。不建议采用逐个文档处理的方式。当用户改变主意时，你的应用需要负责将本地存储的文档移动（复制）到云端，或者反向操作。这并非一项简单的任务，它涉及多任务处理，而这要等到第 24 章才会讲到。

一旦文档进入云端，你打开、修改和保存文档的方式与在本地沙盒中基本相同。你为 `contentsForType(_:,error:)` 和 `loadFromContents(_:,ofType:,error:)` 编写的所有代码都不需要修改（前提是你编写正确）；你只需使用不同的 URL 即可。实际上，“云”文档的数据是本地存储在设备上的。任何变更都会在后台与 iCloud 存储服务器同步，但你始终保留数据的本地副本，这既是为了速度，也是为了防止与云端的网络连接中断。

本地文档和云端文档之间存在一些细微（甚至不那么细微）的差异。其中一个重要的差异就是变更。云端文档的变更可能随时发生。用户可以在另一台设备上自由编辑同一个文档，而网络中断可能会延迟这些变更到达你的应用。

通常，你的应用会监听 `UIDocumentStateChangedNotification` 通知。如果 iCloud 服务检测到版本冲突（本地版本与服务端版本），你的文档状态会变为 `UIDocumentStateInConflict`。这时，你的应用需要比较两个文档，并决定保留哪些内容、丢弃哪些内容。你可以询问用户意见，也可以让应用自动处理。

**注** Homer the Pigeon 应用提供了一个基于云的 `UIDocument` 存储和同步示例。与 MyStuff 类似，Homer 允许用户将图像附加到保存的位置，导致数据量过大，无法使用通用的键值存储进行共享。你可以从 `https://github.com/JamesBucanek/Pigeon` 下载 Homer 的源代码。

要了解更多关于 iCloud 文档的信息，请从 *Document-Based App Programming Guide for iOS* 开始阅读，你可以在 Xcode 的文档和 API 参考窗口中找到它。这是一本很好的读物，如果你计划进行更多涉及 `UIDocument` 的开发，我强烈建议你仔细阅读。其中“Managing the Life Cycle of a Document”和“Resolving Document Version Conflicts”这两章直接涵盖了云存储的内容。

