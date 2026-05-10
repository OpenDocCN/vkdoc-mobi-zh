# 控制器对象与自定义

大多数控制器对象将是 `NSApplication` 和 `NSDocument` 的自定义类或子类。

控制器实现应用程序的操作；提供自定义操作最终会涉及实现你自己的方法。

一个需要考虑的问题是：决定将应用程序的操作实现在控制器的子类中，还是实现在委托中。

- 如果需要重写控制器的方法，则创建一个子类。
- 如果扩展控制器类以添加新的实例变量是有意义的，则创建一个子类。
- 如果仅实现自定义操作方法，则在委托中实现它们。
- 如果需要一组可以“插入”到不同控制器中以创建自定义行为的操作，则在委托中实现它们。
- 如果你的解决方案有混合需求，可以同时实现两者：创建控制器的子类并附加一个委托。

创建 `NSApplication` 和 `NSDocument` 的子类有几个步骤，因为这些对象是由框架创建的——分别由 `NSApplicationMain()` 函数和 `NSDocumentController` 创建。如果你对它们进行子类化，必须告诉框架要实例化哪个类。

## 创建自定义 NSApplication

要创建 `NSApplication` 的子类，请执行以下操作：

1. 定义 `NSApplication` 的一个子类。
2. 在应用程序的 `Info.plist` 文件中，将 Principal Class (`NSPrincipalClass`) 值设置为步骤 1 中创建的类名。

`NSApplicationMain()` 函数在启动时读取 `Info.plist` 属性文件，并使用该值创建单个应用程序对象。你可以使用 `+[NSApplication sharedApplication]` 或通过全局变量 `NSApp` 获取对该应用程序对象的引用。

如果你创建了 `NSApplication` 的子类，可以通过将 NIB 文档中的 Application 占位符对象的类更改为你的自定义类，直接在 Interface Builder 中访问其输出口和操作。

### 创建 NSApplication 委托：

1. 定义你的类。用你的应用程序委托方法和操作填充它。
2. 在 Interface Builder 中，打开 `MainMenu` NIB 文档，并创建自定义对象的实例。通过从库中拖入一个通用 Object 并将其类更改为步骤 1 中创建的类来完成此操作。
3. 将 Application 占位符对象的 `delegate` 输出口连接到你的委托对象。

当 `MainMenu` NIB 文档在应用程序启动期间加载时，NIB 连接将导致创建委托对象的一个实例，并将其设置为应用程序对象的委托。

你的委托对象可以选择接收许多通知，这些通知对于在启动时和关闭前执行额外的初始化和内务处理特别方便。最常实现的委托方法是 `-applicationWillFinishLaunching:`、`-applicationDidFinishLaunching:` 和 `-applicationWillTerminate:`。

## 创建自定义 NSDocument

要使用 `NSDocument` 的子类，请遵循以下步骤：

1. 定义 `NSDocument` 的一个子类。
2. 在应用程序的 `Info.plist` 文件中，找到 Document Types (`CFBundleDocumentTypes`) 集合中与你自定义类关联的文档类型对应的条目。大多数应用程序只编辑一种文档类型。将 Cocoa NSDocument Class (`NSDocumentClass`) 值更改为步骤 1 中创建的类名。

每当创建一个新的 `NSDocument` 对象时，`NSDocumentController` 将使用它从 `DocumentType` 记录中找到的类名来实例化正确的 `NSDocument` 子类。也可以通过子类化 `NSDocumentController` 并重写其 `-documentClassForType:` 方法以编程方式实现，但这属于特殊情况。

### 创建文档委托，请遵循以下步骤：

1. 定义你的类。用你的文档委托方法和操作填充它。
2. 在 Interface Builder 中，打开与你的文档关联的 NIB 文档（其名称由文档的 `windowNibName` 属性定义）。创建自定义对象的实例。通过从库中拖入一个通用 Object 并将其类更改为步骤 1 中创建的类来完成此操作。
3. 将 File's Owner 占位符对象的 `delegate` 输出口连接到你的委托对象。当 NIB 加载时，`NSDocument` 对象是其所有者。如果你还创建了 `NSDocument` 的自定义子类，则可能需要将 File's Owner 占位符的类更改为你的自定义文档子类。

当 `NSDocumentController` 创建一个新的文档窗口时，它首先创建 `NSDocument` 或你的自定义 `NSDocument` 子类的一个实例。然后，它查询该对象以获取应加载的 NIB 文档的名称，以创建用户界面。加载 NIB 也会创建你的委托实例，并将其连接到文档对象。

或者，你可以重写自定义 `NSDocument` 类的初始化，并以编程方式创建和附加委托对象。但只有当你在 NIB 加载之前就需要委托对象时，这才有优势。

## NSController 控制器

Cocoa 框架提供了 `NSController` 的几个具体子类：`NSObjectController`、`NSArrayController`、`NSTreeController` 和 `NSDictionaryController`。每个子类都实现了将现有对象集合绑定到视图、管理选择、编辑单个值、插入新对象、删除对象以及对该集合中的对象进行排序所需的所有功能。

在使用 `NSController` 对象时，只需记住一条规则：大多数 `NSController` 不会观察其集合。核心集合类（如 `NSArray`）不符合键值观察（Key-Value Observing）规范。如果你对受管理的集合进行了更改，请通过控制器进行。

控制器提供了用于在集合中插入、删除和更改对象的方法。这些方法还会执行所有必要的通知和内务处理。

你几乎不需要创建自己的 `NSController` 子类。如果你确实需要，该过程在 Cocoa Bindings 编程主题指南中有所解释。

### 关于 TicTacToe

本章使用了如图 20-16 所示的 TicTacToe 应用程序，来说明设计和实现模型-视图-控制器（Model-View-Controller）设计的许多方面。然而，仍有一些零碎细节可能会使 Cocoa 应用程序开发新手感到困惑，值得阐明。

**图 20-16.** TicTacToe 应用程序

### Info.plist

应用程序的 `Info.plist` 文档对其功能和自定义至关重要。`Info.plist` 文件是一个属性文件，包含许多键值：

- 程序应用程序对象的类：始终是 `NSApplication` 的子类，这是在程序启动期间创建的对象。
- 主 NIB 文件的名称：这是在程序初始化期间加载的 NIB 文件。它通常包含所有核心用户界面元素，如菜单栏，并且经常包含全局对象的实例，如应用程序委托。
- 应用程序的标识符：这将其唯一标识给外界。TicTacToe 的应用程序标识符是 `com.apress.learnobjc.TicTacToe`。
- 包含应用程序图标的文件名。
- 文档类型记录的列表：每条记录将一个文件类型（扩展名）映射到实现它的 `NSDocument` 类。它还向 Launch Services 提供文档的用户可读描述及其图标。

`Info.plist` 还可以包含默认偏好设置、版本号、用户可读的版权信息等其他内容。基本上，`Info.plist` 包含了应用程序的所有公共元数据。

### 撤销



