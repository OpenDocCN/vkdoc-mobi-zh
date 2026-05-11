# 在 macOS 上实现文档：NSDocument

macOS 上文档的核心是 `NSDocument` 类。就像 iOS 中的 `UIDocument` 一样，它是一个抽象类，你需要为你的应用创建其子类。有三个类相互协作，为你的应用提供文档功能。它们分别是：

*   `NSDocument` 是你为你的应用创建子类的抽象类。
*   `NSDocumentController` 是特定于应用的类，用于管理文档的打开和关闭。
*   `NSWindowController` 是管理显示文档窗口的类。

macOS 和 `NSDocument` 与 iOS 和 `UIDocument` 之间的这些类并非一一对应；然而，`NSDocument` 和 `UIDocument` 之间的相似性，以及 `NSDocumentController` 和 `UIDocumentController` 之间的相似性很重要（但也仅仅是相似罢了）。

## iOS UIDocuments 与 macOS NSDocuments 的区别

iOS 文档和 macOS 文档之间的最大区别在于，在 macOS 上，文档是系统环境的一部分，而在 iOS 上，文档是每个应用环境的一部分。这背后有原因（其中许多原因反映了两大操作系统的演进过程），但重要的是你的应用运行和文档存在的环境这一方面。

换句话说，用于管理文档的工具（例如，创建和保存文档）是 macOS 的一部分，而在 iOS 上，它们是诸如 `UIDocumentBrowserViewController` 等工具的一部分，而不是 iOS 本身的一部分。



