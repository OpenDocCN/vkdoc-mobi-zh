# 文件包装器概述

*文件包装器*（`NSFileWrapper`）对象是对存储在一个或多个文件中数据的抽象。文件包装器有三种类型：常规文件包装器、目录包装器和链接包装器。从概念上讲，它们分别等同于单个数据文件、文件系统目录和文件系统符号链接。文件包装器允许你的应用描述一个命名的数据块集合，这些数据块组织在一个分层的命名目录结构中。如果这听起来像文件和文件夹，那就对了。当你的 `UIDocument` 存储在文件 URL 中时，这些文件包装器恰好会变成文件和文件夹。但通过维持这种抽象，`UIDocument` 可以同样轻松地通过网络传输此数据集合，或将包装器转换为数据库的记录。

### 使用包装器

使用文件包装器并不十分困难。一个*常规文件包装器* 代表一个字节数组，就像 `NSData` 一样。一个*目录文件包装器*（或简称为*目录包装器*）可以包含任意数量的其他文件包装器。

包装器与文件/文件夹的一个显著区别是，包装器不需要有唯一的名称。包装器有一个首选名称和一个键。它的*键*是唯一标识包装器的字符串，就像文件名唯一标识一个文件一样。它的*名称*或*首选名称*是它希望被识别的字符串。当你创建一个包装器时，你为其分配一个首选名称。如果你随后将其添加到目录包装器中，并且其首选名称是唯一的，那么它的键和首选名称将是相同的。然而，如果已经存在一个或多个同名的包装器，目录包装器将为刚刚添加的包装器生成一个唯一的键。换句话说，向同一个目录包装器添加多个同名的包装器是有效的。请注意，添加包装器并不会像在文件系统中那样替换或覆盖同名的现有包装器。如果你想再次引用它，你需要跟踪它的键。

你的 `contentsForType(_:,error:)` 函数将创建一个包含所有其他常规文件包装器的单一目录包装器（`docWrapper`）。其中会有一个包含数据模型对象归档版本的常规文件包装器。每个包含图片的项目都会将其图像作为另一个文件包装器存储。你需要修改 `MyWhatsit`，以便在用户添加图片时将图像存储到文档中，并在需要时从文档中获取图像。

### 增量文档更新

将文档组织成包装器为你的应用带来一个显著特性：增量文档加载和更新。如果用户向你的 MyStuff 应用添加了 100 个项目，你的文档包（当保存到文件系统时）将包含一个包含 101 个文件的文件夹：一个归档文件和 100 个图像文件。如果用户用更好的图片替换了其星盘的图片，则只需要更新一个文件包装器。`UIDocument` 理解这一点。当需要再次保存文档时，`UIDocument` 将只重写包中的那一个文件。这使得对大型文档的更新非常快速和高效。这些对你的应用来说都是很好的特性。

同样，文件包装器的数据只有在被请求时才会被读取。换句话说，文件包装器是惰性的。当你打开一个由文件包装器构建的 `UIDocument` 时，每个单独包装器的数据都会保留在原处，直到你的应用需要使用它。对于你的图像来说，这意味着你的应用在启动时不必读取所有 100 个图像文件。它可以只检索当前需要的图像。这同样意味着你的应用可以快速启动，并仅执行显示界面所需的最少工作。

### 构建你的包装器

选择 `ThingsDocument.swift` 文件。首先添加两个常量和两个实例变量。

```swift
let thingsPreferredName = "things.data"
let imagePreferredName = "image.png"

var docWrapper = NSFileWrapper(directoryWithFileWrappers: [:])
var things = [MyWhatsit]()
```

这两个常量定义了归档后的 `MyWhatsit` 对象以及添加到目录包装器中的任何图像的首选包装器名称。`docWrapper` 实例变量是包含所有其他包装器的单一目录包装器。实际上，`docWrapper` 就是你的文档数据。`things` 变量是构成你数据模型的 `MyWhatsit` 对象数组。

**注意：** 稍后，你将用新的 `ThingsDocument` 替换 `MasterViewController` 中的 `things` 数组。文档对象将成为你视图控制器的数据模型。

现在添加关键的 `contentsForType(_:,error:)` 函数。

```swift
override func contentsForType(typeName: String, 
                        error outError: NSErrorPointer) -> AnyObject? {
    if let wrapper = docWrapper.fileWrappers[thingsPreferredName] as? NSFileWrapper {
        docWrapper.removeFileWrapper(wrapper)
    }
    let thingsData = NSKeyedArchiver.archivedDataWithRootObject(things)
    docWrapper.addRegularFileWithContents(thingsData, preferredFilename: thingsPreferredName)
    return docWrapper
}
```

当 `UIDocument` 想要创建或保存你的文档时，会调用此函数。第一步处理第二种情况，即覆盖现有包装器；它检查 `things.data` 子包装器是否已存在，如果存在则将其删除。请记住，添加另一个 `things.data` 包装器不会替换前一个。

下一步是将所有 `MyWhatsit` 对象归档（序列化）到一个可移植的 `NSData` 对象中。我将在下一节解释这是如何发生的。生成的数据对象随后被传递给 `addRegularFileWithContents(_:,preferredFilename:)` 函数。这是一个便捷方法，它创建一个包含 `thingsData` 字节的新常规文件包装器，并以首选名称将其添加到目录包装器中。此方法让你无需显式编写这些步骤。

最后，你将包含文档中所有数据的目录包装器返回给 `UIDocument`。现在你可能会问：“但是所有图像数据呢？它们是在哪里创建的？” 这是一个非常好的问题。图像数据由同一目录包装器中的其他常规文件包装器表示。当文档首次创建时，没有图像，因此目录包装器只包含 `things.data`。当用户向数据模型添加图片时，每个图像都会向 `docWrapper` 添加一个新的包装器。当你的文档再次保存时，包含图像的这些文件包装器已经在 `docWrapper` 中了！每个常规文件包装器都知道自己是否已被修改或更新，而 `UIDocument` 足够智能，能够判断哪些文件需要写入，哪些已经是最新的。

### 解读你的包装器

打开文档时，会执行与上述过程相反的操作。`UIDocument` 获取保存在文档中的数据，然后调用你的 `loadFromContents(_:,ofType:,error:)` 函数。此函数的任务是将文档数据转换回你的数据模型。请在你的 `contentsForType(_:,error:)` 函数之后立即添加此函数。

```swift
override func loadFromContents(contents: AnyObject,
                        ofType typeName: String,
                         error outError: NSErrorPointer) -> Bool {
    if let contentWrapper = contents as? NSFileWrapper {
        if let thingsWrapper = contentWrapper.fileWrappers[thingsPreferredName]
                                                     as? NSFileWrapper {
            if let data = thingsWrapper.regularFileContents {
                things = NSKeyedUnarchiver.unarchiveObjectWithData(data) as [MyWhatsit]
                for thing in things {
                    thing.imageStorage = self
                }
                docWrapper = contentWrapper
                return true
            }
        }
    }
    return false
}
```


好的，作为高级文档工程师和翻译员，我将严格按照您提供的注意事项和示例，将给定的英文文本翻译成中文。

译文如下：


