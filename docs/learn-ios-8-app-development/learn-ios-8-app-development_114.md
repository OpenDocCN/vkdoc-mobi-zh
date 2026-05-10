# Markdown 排版后的内容

`contents` 参数是封装了文档数据的对象。它始终与您从 `contentsForType(_:,error:)` 返回的（同一类）对象相同。如果您采用第一种方法并返回了一个单一的 `NSData` 对象，那么 `contents` 参数将包含一个 `NSData` 对象，其中包含相同的数据。由于 `MyStuff` 选择了使用文件封装技术，`contents` 是一个与您之前返回的目录封装器等效的对象。

第一步是将 `contents` 保存到 `docWrapper` 中；您后续需要它来读取图像封装器以及稍后再次保存文档。该方法的其余部分会找到包含已归档 `MyWhatsit` 对象数组的 `things.data` 封装器。它会立即检索存储在该封装器中的数据并进行解档，从而重新创建您的数据模型对象。

如果成功，`loadFromContents(_:,ofType:,error:)` 函数必须返回 `true`；如果在解释文档时出现问题，则返回 `false`。如果封装器包含一个 `things.data` 封装器，并且该封装器中的数据已成功转换回 `MyWhatsit` 对象数组，则该方法假定文档有效并返回 `true`。

至此，保存并随后打开新文档所需的工作，差不多就完成了。这里有一个明显的漏洞：`MyWhatsit` 对象数组无法归档！让我们现在就来修复它。

## 其他存储方案

可供您使用的最后两种文档存储方案是 Core Data 和自己动手（DIY）。DIY 是我不太喜欢的一种方案。它应该是您的最后选择，因为您将被迫处理 `UIDocument` 通常为您处理的所有常见和异常任务。我的建议是尽最大努力让前三种方案之一生效。如果那行不通，您可以自行处理文档存储。请查阅 `UIDocument` 文档的“高级重写”部分。

最有趣的文档解决方案之一是 Core Data。iOS 包含一个快速高效的、具有语言级支持的**关系型数据库引擎（SQLite）**。Core Data 远远超出了本书的讨论范围，但如果您的应用数据更适合存储在数据库中而不是文本文件中，它将是一个非常强大的工具。（很遗憾我没有足够的篇幅，因为 `MyStuff` 本来会是一个完美的 Core Data 应用。）

使用 Core Data 的一个巨大优势是，文档管理基本上为您完成了。您只需使用 `UIManagedDocument` 类（`UIDocument` 的一个子类），无需做太多其他工作。本章中您需要编写代码来支持的许多功能——增量文档更新、惰性文档加载、数据模型对象的归档和解档、后台文档加载和保存、云同步等等——都由 `UIManagedDocument` “免费”提供。

当然，前提是您必须首先将应用基于 Core Data。您的数据模型对象必须是 `NSManagedObjects`，您必须为数据库设计模式，并且您需要了解面向对象数据库（OODB）技术的方方面面。但除此之外（！），就很简单了。

## 归档对象

在第 18 章 中，您学习了关于**序列化**的全部内容。序列化将属性列表对象图转换为字节流（XML 或二进制格式），这些字节流可以存储在文件中、与其他进程交换、传输到其他计算机系统等等。在接收端，这些字节被转换回等效的属性列表对象集，以备使用。

归档是序列化的“大姐”。*归档* 将一组都采用 `NSCoding` 协议的对象图进行序列化（计算机科学术语）。这比属性列表对象的集合要大得多。² 更重要的是，您可以在自己开发的类中采用 `NSCoding` 协议。这样，您的自定义对象就可以与其他对象一起归档。这正是您的 `MyWhatsit` 类需要发生的事情。

### 采用 NSCoding

归档对象图的第一步是确保每个对象都采用 `NSCoding` 协议。如果有一个对象没有采用，您需要将它从图中移除，或者修改它以使其采用。在 `MyWhatsit.swift` 中，修改类声明，使其采用 `NSCoding`（新代码以粗体显示）。

```
class MyWhatsit: NSObject, NSCoding {
```

**注意**  要采用 `NSCoding`，首先要使您的类成为 Objective-C 基类 `NSObject` 的子类。这样做与在第 8 章 中使您的类与键值观察兼容的原因相同；`NSObject` 定义了 `NSCoding` 依赖的关键方法。请注意，Swift 的 `@objc` 关键字可以达到同样的效果。

`NSCoding` 协议要求一个类实现一个初始化器 `init(coder:)` 和一个实例函数 `encodeWithCoder(_:)`。初始化器从先前归档的数据中创建一个新对象。该函数从现有对象创建归档数据。这两个过程都通过一个 `NSCoder` 对象进行。`NSCoder` 对象负责序列化（编码）和后续的反序列化（解码）您对象的属性。

编码器使用一个键来标识对象的每个属性值。现在通过将这些常量添加到您的 `MyWhatsit` 类中来定义这些键。

```
let nameKey = "name"
let locationKey = "location"
```

现在您可以编写初始化器函数了。

```
required init(coder decoder: NSCoder) {
    name = decoder.decodeObjectForKey(nameKey) as String
    location = decoder.decodeObjectForKey(locationKey) as String
}
```

`init(coder:)` 使用编码器对象中存储的值初始化新对象的所有属性。在这种情况下，这两个值都是字符串对象。除了对象之外，编码器对象还可以直接编码整型、浮点型、布尔型和其他原始类型。UIKit 为 `NSCoder` 添加了扩展，用于编码点、矩形、尺寸、仿射变换和其他常用的数据结构。现在编写与初始化器镜像的函数。

```
func encodeWithCoder(coder: NSCoder) {
    coder.encodeObject(name, forKey: nameKey)
    coder.encodeObject(location, forKey: locationKey)
}
```

相反方向的转换由您的 `encodeWithCoder(_:)` 函数提供。此函数将其持久属性的当前值保存在编码器对象中。您的 `MyWhatsit` 对象现在已经准备好参与归档过程。

#### 子类化一个 <NSCODING> 类

当您子类化一个已经采用 `NSCoding` 的类时，处理方式会略有不同。您的 `init(coder:)` 函数将如下所示：

```
required init(coder decoder: NSCoder) {
    super.init(coder: decoder)
    // 子类解码代码写在这里
}
```

而您的 `encodeWithCoder(_:)` 函数应该如下所示：

```
override func encodeWithCoder(coder: NSCoder) {
    super.encodeWithCoder(coder)
    // 子类编码代码写在这里
}
```

您的父类已经对其属性进行了编码和解码。您的子类必须允许父类完成此操作，然后对子类中定义的任何其他属性进行编码和解码。

### 归档和取消归档对象

一旦您的类采用了 `NSCoding`，它就准备好被归档了。当您想将对象扁平化为字节时，请使用如下代码：

```
let data = NSKeyedArchiver.archivedDataWithRootObject(myObject)
```



`NSKeyedArchiver`类是归档引擎。它创建一个`NSCoder`对象，然后调用根对象（`things`）的`encodeWithCoder(_:)`函数。该对象负责将其内容保存在编码器对象中。很可能会为它所引用的对象调用`encodeObject(_:,forKey:)`函数。这些对象随后会收到`encodeWithCoder(_:)`调用，这个过程会重复，直到所有对象都被编码完成。唯一的限制是每个涉及的对象都必须采用`NSCoding`。

当你想要取回对象时，可以使用`NSKeyedUnarchiver`类，如下所示：

```
myObject = NSKeyedUnarchiver.unarchiveObjectWithData(data)
```

在编码过程中，编码器记录了每个对象的类。解码器随后使用该信息来调用对象的`init(coder:)`初始化器。结果对象与原始编码对象具有相同的类和属性值。

**注意** 键控归档的前身是顺序归档。你可能会偶尔看到对顺序归档的引用，但在 iOS 中并未使用。

## 归档序列化对决

既然你已将序列化（属性列表）和归档（`NSCoding`对象）都加入了你的技术储备，我想花点时间对两者进行比较和对比。表 19-1 总结了它们的主要特性。

表 19-1. 序列化与归档对比

| 特性 | 序列化 | 归档 |
| --- | --- | --- |
| 对象图 | 仅限属性列表对象 | 采用`NSCoding`的对象 |
| XML | 是 | 否 |
| 可移植性 | Cocoa 或 Cocoa Touch 应用，或任何能解析 XML 版本的系统 | 仅限包含所有原始类的其他进程 |
| 编辑器 | 是 | 否 |

属性列表在可存储内容上限制更多，但在存储、共享和编辑方式上弥补了这一点。当你的值需要被其他进程理解时，尤其是那些不包含你自定义类的进程，请使用属性列表。一个例子是你在第 18 章中创建的设置 bundle。“设置”应用永远不会包含你的任何自定义 Objective-C 类，但你仍然能够使用属性列表定义、交换这些设置并将其整合到你的应用中。属性列表是值的“通用”语言。

相比之下，归档可以编码大量类，并且你可以通过采用`NSCoding`协议将自己的类添加到这个列表中。你在 Interface Builder 中创建的所有内容都使用键控归档进行编码。当你在应用中加载 Interface Builder 文件时，`NSKeyedUnarchiver`正在负责将该文件翻译回你定义的对象。归档极其灵活且适用范围广，这就是它成为在文档中存储数据模型对象的首选技术的原因。

为什么我们不把所有事情都用归档？解档时，归档中记录的每个类都必须存在。所以，别想着用另一个不包含你`MyWhatsit`类的应用或程序来读取你的 MyStuff 文档——这是做不到的。归档在大多数情况下是不透明的。没有像属性列表那样的通用归档编辑器，也没有将归档数据转换为 XML 文档的功能。

## 序列化，遇见归档

现在你已经对归档和序列化的优缺点有了感觉，我要向你展示一个结合两者的非常实用的技巧。（你可能已经想到了，但至少可以假装惊讶一下。）`NSData`是一个属性列表对象。对一组`NSCoding`对象进行归档的结果是一个`NSData`对象。你知道这要做什么吗？

通过先将你的对象归档成一个`NSData`对象，你可以将非属性列表对象存储在属性列表中，比如用户默认设置！你的代码如下所示：

```
let userDefaults = NSUserDefaults.standardUserDefaults()
let data = NSKeyedArchiver.archivedDataWithRootObject(dataModel)
userDefaults.setObject(data, forKey: "data_model")
```

你做的是将你的数据模型对象归档成一个`NSData`对象，该对象可以存储在属性列表中。要检索它们，反转这个过程即可。

```
let modelData = userDefaults.objectForKey("data_model") as NSData
dataModel = NSKeyedUnarchiver.unarchiveObjectWithData(modelData) as DataClass
```

这种技术的缺点与归档技术本身的缺点相同。检索对象的进程必须能够解档它们。此外，任何检查你属性值的编辑器或其他程序只会看到一团数据。与你之前在 Pigeon 中将`MKAnnotation`对象转换为字典的技术相比，那些属性列表值（位置的名称、经度和纬度）很容易解释，甚至可以被其他程序编辑。

**注意** 不要滥用这种技术。像`NSUserDefaults`和`NSUbiquitousKeyValueStore`这样的服务被设计用来存储*少量*信息。不要通过存储你用归档器创建的数兆字节的`NSData`对象来滥用它们。

我觉得关于归档和属性列表已经讲得够多了。是时候回到让 MyStuff 实现文档化的工作上来了。

## 文档，遇见你的数据模型

我们讲到哪了？哦，对了，你创建了一个`UIDocument`类并编写了所有必要的代码，将你的数据模型对象转换为文档数据并再转换回来。下一步是让你的`ThingsDocument`对象成为`MasterViewController`的数据模型。

你这样做的原因是你的文档需要感知*任何*对数据模型的更改，我将在接下来的“跟踪更改”部分中进行解释。目前，你的视图控制器是操作你数据模型（`things`数组）的对象。这不是好的 MVC 设计；你的控制器在做一些本属于数据模型的工作。但之前这还不至于糟糕到需要创建另一个数据模型类来封装对`things`数组的更改。随着引入文档，我们已经越过了那条线，所以是时候重构了。正如我在第 8 章结束时所说，为了保持代码简洁，稍微偏离 MVC 路径是可以接受的。只要知道你在哪里偏离了，并准备好在自己陷入困境时回到正轨。

**注意** 在一个“大型”应用中，你可能会创建一个独立于`UIDocument`类的自定义数据模型类。然后文档和视图控制器都会观察数据模型对象的更改。用 MVC 术语来说，你会有一个*数据模型*和一个*数据模型控制器*（文档对象）。文档和视图控制器都会连接到同一个数据模型对象。对于 MyStuff，我让你将数据模型和文档合并成一个类，这与之前数据模型和视图控制器纠缠在一起是同样的原因。这简化了设计并减少了你需要编写的代码量。

你当前的`MasterViewController`正在使用一个数组作为其数据模型对象。该数组对象提供了视图控制器用于管理它的一些方法，具体来说是计数、添加和移除数组中的对象。`UIDocument`没有任何这些方法——因为它不是数据模型。通过复制视图控制器所需的功能，将其变成一个数据模型。选择`ThingsDocument.swift`并添加以下函数：

```
var whatsitCount: Int {
    return things.count
}

func whatsitAtIndex(index: Int) -> MyWhatsit {
    return things[index]
}

func indexOfWhatsit(thing: MyWhatsit) -> Int? {
    for (index,value) in enumerate(things) {
        if value === thing {
            return index
        }
    }
    return nil
}

func removeWhatsitAtIndex(index: Int) {
    things.removeAtIndex(index)
}
```



