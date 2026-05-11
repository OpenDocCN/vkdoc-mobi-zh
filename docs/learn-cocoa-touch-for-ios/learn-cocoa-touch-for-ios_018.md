# App 包

App 包是一个目录，其中包含用户从 App Store 下载应用时附带的所有内容：图像、视频、可执行文件等等。App 包严格为只读，其完整性通过代码签名进行验证。由于代码签名的要求，更改 App 包中的任何内容都会使代码签名失效，导致应用无法使用。要从 App 包中获取文件，可以使用 `NSBundle` 类。以下是获取 App 包中 `Catalog.pdf` 文件路径的代码示例：

```
NSString *path = [[NSBundle mainBundle] pathForResource:@"Catalog"
ofType:@"pdf"];
```

只有当你将文件包含在应用中时，从 App 包加载文件才会生效。如果你有大量文件，或者它们特别大，你可能不希望将它们包含在应用包中。还有两个主要的文件存储位置：Documents 目录和 Caches 目录。

### Documents 目录

iOS 上的 Documents 目录是存储用户生成文件的主要位置。放置在 Documents 目录中的文件会在设备使用 iCloud 或 iTunes 备份时一同备份，因此将不易重新生成的文件存储在 Documents 中以避免丢失非常重要。要获取 Documents 目录的路径，请使用 `NSSearchPathForDirectoriesInDomains` 函数：

```
NSArray *documentsDirectories =
NSSearchPathForDirectoriesInDomains(NSDocumentDirectory,
NSUserDomainMask,
YES);
```

`NSSearchPathForDirectoriesInDomains` 返回一个路径数组，要获取路径，只需获取数组的第一个对象即可。第一个参数是最重要的指定项；在此例中，它指定了你正在查找 Documents 目录。

### Caches 目录

Caches 目录在几个关键方面与 Documents 目录不同。

首先，它不会被备份。如果用户更换新手机并迁移数据，放置在 Caches 中的文件将被删除。由于这一限制，只能将可以重新创建或重新下载的文件放入 Caches 目录，因为这些文件随时可能消失。iOS 5 增加了一项功能：当设备内存不足时，系统会自动清理缓存，这让那些依赖持久化文件的应用程序大为恼火，不过苹果后来增加了一种方法，可以标记 Caches 中的文件，使其不会被系统自动清除，这将在本书后续部分介绍。获取 Caches 目录的方式与获取 Documents 目录类似：

```
NSArray *cacheDirectories =
NSSearchPathForDirectoriesInDomains(NSCachesDirectory,
NSUserDomainMask,
YES);
```

与 Documents 目录一样，这会返回一个包含一个目录的数组。

## Core Data

跨文件系统管理文件、在 SQLite 中操作数据库以及将对象移入和移出内存是一项繁重的工作，而且很难做到完美。你可能过于激进（或不够激进）地从内存中移除对象，数据库代码可能未充分优化，文件系统访问也可能是一团乱麻，毫无组织。数据持久化也是一个常见问题。几乎所有应用都需要管理对象、存储对象并查找对象。苹果对此问题的解决方案是 Core Data，这是一个在 Mac OS X 和 iOS 上可用的框架，可为你管理数据持久化。Core Data 可以将应用的数据保存到 SQLite 数据库（默认方式）、XML 或二进制格式，或者仅仅保存在内存中；Core Data 还可以自动将图像和视频等大文件保存到外部文件中，以提高数据库性能。Core Data 的核心是托管对象模型，这是一个描述数据的文档。通过使用 Xcode 中的可视化布局，你可以定义类及它们之间的关系。例如，一个追踪图书的应用可能有一个 `Book` 类和一个 `Author` 类。使用 Core Data，你可以创建一个如图 4-3 所示的对象关系图。

**图 4-3.** *在 Core Data 中编辑 Book 和 Author 类*

此配置将创建这两个类。`Book` 类将具有 `pageCount`、`synopsis` 和 `title` 属性，以及一个 `Author` 的 `NSSet`。`Author` 类将具有 `firstName` 和 `lastName` 属性，以及一个 `Book` 的 `NSSet`。这本身就已使 Core Data 成为一个方便的工具，仅用于对象建模。Core Data 还为你管理对象生命周期，从创建对象到将其保存到磁盘。最后，Core Data 可以为你搜索对象，甚至自动管理表格视图以显示搜索结果。它是一个强大的工具。有关更多信息，有一些优秀的书籍介绍了 Core Data：《More iOS 5 Development》和《Pro Core Data for iOS, Second Edition》。

## 总结

在本章中，我们讨论了如何处理数据。无论你是在应用中使用键值观察来传递数据，还是发送 `NSNotification` 通知，都有多种方法可以将对象指针从 A 点传递到 B 点。我们还讨论了使用 `NSUserDefaults` 存储偏好设置和简单的键值对以实现数据持久化。对于大量数据，我们介绍了创建属性列表，以及使用 `NSCoding` 协议来存储归档数据。现在我们已经介绍了如何处理数据，下一章我们将介绍如何让用户与数据进行交互。

