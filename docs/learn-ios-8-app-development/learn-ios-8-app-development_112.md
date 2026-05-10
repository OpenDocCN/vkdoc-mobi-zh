# 总结

现在不能再指责 Pigeon 是个“鸟脑子”应用了！它不仅能记住用户保存的位置，还能记住他们上次设置的地图样式和跟踪模式。通过这个过程，你学习了如何将属性列表值存储到用户默认设置中，如何将非属性列表对象转换为适合存储的对象，以及如何将它们取出来。更重要的是，你理解了存储和检索这些值的最佳时机。

你学习了如何处理用户默认值缺失的情况，以及如何创建和注册一组默认值。你还使用了用户默认设置来保存视图控制器的状态，这为你的应用带来了持久性。你是通过利用内置于 iOS 中的强大视图控制器恢复功能来实现这一点的。

你还飞向了云端，使用 iCloud 存储服务共享和同步更改。iCloud 集成为你的应用增加了一个引人注目的维度，任何拥有多个 iOS 设备的用户都会欣赏这一点。如果这还不够，你还定义了用户可以在你的应用之外访问的设置。

在创建符合用户期望的应用方面，你又迈出了重要的一步。但这只是一小步。用户默认设置，尤其是通用键值存储，只适用于少量信息。要学习如何存储“大数据”，请进入下一章。

## 练习

你可能已经注意到上一个版本的 Pigeon 中存在一个缺陷——我巧妙地通过在“设置”应用中更改“同步位置”设置之前让你在 Xcode 中停止应用来回避了这个问题。基于你目前对应用状态的理解，问题应该很明显了。

Pigeon 只在首次启动时检查 `.CloudSync` 的值。如果 Pigeon 用户切换到“设置”应用，更改了“同步位置”设置，然后立即返回 Pigeon，Pigeon 很可能仍在运行。它会被移到后台状态并暂停一段时间，但在用户返回时会重新激活。这个 bug 在于 Pigeon 不会再次检查 `.CloudSync` 的值，因此不知道它已经发生了变化。

有几种方法可以解决这个问题。一种方法是在应用委托函数 `applicationWillEnterForeground(_:)` 中添加代码。我选择的解决方案是观察由 `NSUserDefaults` 发布的 `NSUserDefaultsDidChangeNotification`。请记住，设置束中的值会对应用的用户默认设置进行更改，你可以通过通知中心观察这些更改。

你可以在 `Learn iOS Development Projects` ![image](img/arrow.jpg) `Ch 18` ![image](img/arrow.jpg) `Pigeon E1` 文件夹中找到我对这个问题的解决方案。看看你是否能想出第三个——非常相似但更有针对性——的解决方案。（提示：阅读 `applicationWillEnterForeground(_:)` 方法的文档。）

## 第 19 章

博士，你说的是存储

如果你希望你的 iOS 应用存储超过少量零碎信息，则需要文档。iOS 提供了一个强大的文档框架，将数据存储带入了 21 世纪。iOS 文档（`UIDocument`）类负责处理或让你轻松实现现代功能，如自动保存、版本控制和云存储。在这个过程中，你最终将学习如何归档对象。在本章中，你将执行以下操作：

*   创建自定义文档对象
*   使用文档对象作为应用的数据模型
*   学习如何归档和解归档数据模型对象
*   设计一个可以增量加载或保存的文档
*   处理异步文档加载
*   管理文档更改和自动保存

你会发现很多关于如何使用 `UIDocument` 的“操作指南”，因为坦率地说，它是一个使用起来很复杂的类。有很多活动部件，而且有不止一些细节你必须注意。这导致许多开发者忽略 `UIDocument` 并“自行构建”他们的文档存储解决方案。我恳请你不要那样做。征服 `UIDocument` 并没那么难，而且回报是丰厚的。



`UIDocument` 乍看之下可能让你感到不知所措——直到你理解*为什么* `UIDocument` 要以这种方式工作。一旦你明白了它架构背后的设计逻辑以及它为你完成的任务，你需要编写的代码就会变得合情合理。因此，本章的重点不仅在于"如何做"，更在于"为什么这样做"。学完本章后，你将能像专业人士一样使用 `UIDocument`。

## 文档概述

*文档*一词有多种含义，但在此上下文中，*文档*指的是包含用户生成内容的数据文件（或包），你的应用可以打开、修改并最终将其保存到持久化存储中。我们都习惯了桌面计算机系统上的文档。在移动设备中，文档的概念虽然退居次要位置，但依然存在，且形式大同小异。在 Pages 文字处理应用等少数应用中，熟悉的文档隐喻占据核心位置：你启动应用，看到一系列有名称的文档，选择其中一个进行操作；文档打开后，你便开始输入。在其他应用中，你使用单个文档的事实并不那么明显，有些应用甚至完全隐藏了文档的运作机制。你可以为你的应用选择以上任意一种方式。iOS 为你所需的任何界面提供了必要的工具，但不会强制规定某一种。

这种灵活性使你可以完全在后台为应用添加文档存储和管理功能，可以松散地耦合到用户界面，也可以沿用桌面计算机系统上传统的文档隐喻。无论你如何设计界面，起点都是 `UIDocument` 类。以下是在应用中使用 `UIDocument` 的基本步骤：

1.  创建 `UIDocument` 的自定义子类。
2.  设计用于选择、命名和共享文档的界面（可选）。
3.  将应用的数据模型转换为适合持久化存储的数据，以及从这种数据转换回来。
4.  处理文档的异步读取。
5.  将文档移动到云端（可选）。
6.  观察来自共享文档的更改通知并处理冲突（可选）。
7.  实现撤销/重做功能，或至少跟踪文档的更改。

你将重新审视 MyStuff 应用并进行修改，使其将所有酷炫物品、物品描述乃至图片都存储在一个文档中。这次 MyStuff 的界面没有任何变化。用户唯一会注意到的是，当他们重新启动你的应用时，他们的物品仍然存在！

## 我的文档究竟该存到哪里？

那么，你在 iOS 中应该将文档存储在哪里？简短的回答是：将文档存储在你的应用私有 `Documents` 文件夹中，并可选择存储在云端。

详细的回答是，你可以将文档存储在你的应用有权访问的任何位置，但唯一合理的位置是你的应用私有 `Documents` 文件夹。每个 iOS 应用都可以访问一组称为其*沙盒*的私有文件夹。`Documents` 文件夹是其中之一，iOS 将其保留用于存放你的应用的文档。此文件夹的内容由 iTunes 自动备份。如果你还想通过 iTunes 交换文档，你的文档必须存储在 `Documents` 文件夹中。

这与大多数桌面计算机系统上的情况有所不同：在桌面系统中，应用允许你将文档加载和保存到任何位置，并且你的 `Documents` 文件夹可由所有应用自由共享。在 iOS 中，一个应用只能访问其沙盒中的文件，并且其他应用或用户无法访问这些目录——除非你特意将 `Documents` 文件夹暴露给 iTunes。

**注意** 如果你对沙盒中其他文件夹及其用途感兴趣，请阅读 Xcode 的"文档和 API 参考"窗口中的《文件系统编程指南》的"关于 iOS 文件系统"一节。

对于 MyStuff，你将在 `Documents` 文件夹中存储一个单一的文档。但是，你不会为此文档提供任何用户界面。当应用启动时，文档将自动打开，用户所做的任何更改都将自动保存到该文档中。即使你将使用标准的文档类并将数据存储在 `Documents` 文件夹中，整个过程对用户来说也是不可见的。

这并不是说你不能或不应该提供一个界面，让用户查看他们的 `Documents` 文件夹中有哪些文档。一个典型的界面会显示文档名称，可能还有预览图，并允许用户打开、重命名和删除它们。你可以使用表格视图、集合视图，甚至页面视图控制器来实现这一点。iOS 8 引入了一个标准化的界面来实现这一功能。如果你想要一个简单的文档选择器界面（该界面也适用于 iCloud 云盘），可以从 `UIDocumentPickerViewController` 类入手。如果你仍想自行设计界面，iOS 还提供了一个新的 `NSURLThumbnail` 资源——参见 `NSURL.getResourceValue(_:,forKey:,error:)` 以获取 URL 资源——这使得显示文档缩略图变得简单。

但正如我所说，你不需要向 MyStuff 用户暴露文档结构。你需要做的是创建 `UIDocument` 的一个自定义子类，并定义文档的存储位置和方式——这听起来正是我们开始的地方。

## MyStuff 的文档化

从第 7 章末尾的 MyStuff 版本（你在其中为每个物品添加了图片）开始。从文件模板库中拖拽一个新的 Swift 文件到你的项目中（或选择"新建文件"命令）。将新文件命名为 `ThingsDocument`，并使其成为 `UIDocument` 的子类，代码如下：

```
import UIKit

class ThingsDocument: UIDocument {
}
```

首先要添加的是一个常量，用于存储你唯一文档的名称。将此代码添加到 `ThingsDocument` 类定义之外，使其成为一个全局常量。

```
let ThingsDocumentName = "Things I Own.mystuff"
```

现在，添加一个计算属性来定位你的应用的 `Document` 文件夹，并将你的单一文档文件作为 URL 返回。

```
class var documentURL: NSURL {
    let fileManager = NSFileManager.defaultManager()
    if let documentsDirURL = fileManager.URLForDirectory( .DocumentDirectory,
                                                inDomain: .UserDomainMask,
                                       appropriateForURL: nil,
                                                  create: true,
                                                   error: nil) {

return documentsDirURL.URLByAppendingPathComponent(ThingsDocumentName)
    }
    assertionFailure("Unable to determine document storage location")
}
```

你的新 `documentURL` 属性返回一个 `NSURL` 对象，该对象指向 MyStuff 应用所使用的唯一文档在文件系统中的位置，文档名为 `Things I own.mystuff`。

这里重要的方法是 `URLForDirectory(_:,inDomain:,appropriateForURL:,create:,error:)` 函数。这是用于定位关键 iOS 目录（如应用沙盒中的 `Documents` 目录）的少数几个函数之一。`.DocumentDirectory` 常量指明了——在大约六个指定目录中——你感兴趣的是哪一个。要定位应用沙盒中的目录，请指定 `.UserDomainMask`。`create` 标志告知文件管理器，如果目录不存在则创建它。这里使用该标志是多余的，因为 `Documents` 目录在你的应用安装时已创建，且应始终存在，但设置"肯定"并无害处。

**注意** 不要使用像 `"~/Documents/"` 这样的常量来"硬编码"标准 iOS 目录的路径。应使用 `URLsForDirectory(_:,inDomain:)` 等函数来确定已知目录的路径。标准目录的位置会不时更改，你不应对它们的名称或路径做出假设。



有了你的 `Documents` 文件夹的 URL 后，你的代码会接着追加文档的名称，从而构建出文档当前（或将要）存储位置的完整路径。

现在，编写一个函数来打开你的文档。`MyStuff` 并不会提供文档界面。当它启动时，要么创建一个空文档，要么重新打开已有文档。请将这一逻辑整合到紧跟在 `documentURL` 属性之后的一个单独函数中。

```
class func document(atURL url: NSURL = ThingsDocument.documentURL) -> ThingsDocument {
    let fileManager = NSFileManager.defaultManager()
    if let document = ThingsDocument(fileURL: ThingsDocument.documentURL) {
        if fileManager.fileExistsAtPath(url.path!) {
            document.openWithCompletionHandler(nil)
            }
        } else {
            document.saveToURL(url, forSaveOperation: .ForCreating, completionHandler: nil)
        }
        return document
    }
    assertionFailure("Unable to create ThingsDocument for \(url)")
}
```

这个函数会在给定的（文件）URL 处创建一个 `ThingsDocument` 对象的新实例。如果你没有指定文件 URL，它会默认使用你刚刚定义的 `documentURL` 属性。它通过文件管理器来判断该位置是否已存在文档（`fileExistsAtPath(_:)`）。如果存在，则调用文档的 `openWithCompletionHandler(_:)` 函数来打开文档并读取其包含的数据。如果不存在，则调用 `saveToURL(_:,forSaveOperation:,completionHandler:)` 函数来保存文档。由于文档对象刚被创建，它是空的，因此保存操作会创建一个新的（空）文档。最后，这个打开的文档对象会被返回给调用者。

**提示**   `MyStuff` 文档的名称无关紧要，因为除了其开发者之外，没有人会看到它。不过，如果你确实希望用户能够访问你应用 `Documents` 文件夹中的文档，你只需在应用的 `info.plist` 中添加 `UIFileSharingEnabled` 键（值为 `YES`）即可。这个标志会告诉 iTunes 将存储在 `Documents` 文件夹中的文档暴露给用户。通过 iTunes，用户可以浏览、下载、上传和删除该文件夹中的文档。详见《iOS 应用编程指南》中的“应用相关资源”章节。也可以查阅《技术问答 #1699》（`QA1699`）。它描述了如何有选择性地通过 iTunes 共享部分文档，同时隐藏其他文档。

## 提供文档的数据

在你 `UIDocument` 的子类中，你需要重写两个函数：`contentsForType(_:,error:)` 和 `loadFromContents(_:,ofType:,error:)`。这两个方法会将你的应用数据模型对象转换成 `UIDocument` 可以保存的形式，然后再将保存的数据转换回你的应用所需的数据模型对象。

这也是 `UIDocument` 实现变得有趣的地方。关键在于理解 `UIDocument` 为你做了什么，以及 `UIDocument` 期望从 `contentsForType(_:,error:)` 和 `loadFromContents(_:,ofType:,error:)` 得到什么。这两者有着严格的责任分工。

*   `UIDocument` 负责实际存储和检索你的文档数据。
*   `contentsForType(_:,error:)` 和 `loadFromContents(_:,ofType:,error:)` 负责在你的数据模型对象与该信息的序列化版本之间进行转换。

`UIDocument` 可能将你的文档存储在文件系统上，也可能存储在云中，或者通过 USB 连接进行传输。将来它甚至可能存储在你随身携带的钥匙扣无线电子钱包里。这些你无需了解，也不应关心。让 `UIDocument` 去操心文档数据的存储位置和方式吧。

当 `UIDocument` 想要保存你的文档时，它会调用你的 `contentsForType(_:,error:)` 函数。你的实现应该将你的数据模型对象转换成适合存储的数据。`UIDocument` 获取返回的数据，并将其存储在文件系统、云或其他任何地方。

当需要读取文档时，`UIDocument` 会逆转这个过程。它首先重新获取数据（无论数据保存在哪里），然后将数据传递给 `loadFromContents(_:,ofType:,error:)`，该函数的任务是将数据重新转换为你应用的数据模型对象。

关键问题在于：“如何将你的数据模型对象转换成 `UIDocument` 可以存储的字节？” 这是个极好的问题，答案可能简单得令人震惊，也可能复杂得令人棘手。总的来说，你有四种选择。

*   将所有内容序列化为一个单一的 `NSData` 对象
*   使用文件包装器对象描述多部分文档
*   使用 Core Data 支撑你的文档
*   实现你自己的存储方案

第一种解决方案最为简单，适用于许多文档类型。使用字符串编码、属性列表序列化或对象归档（你很快就会学到），将你的数据模型对象转换为单一的字节数组。然后，你的 `contentsForType(_:,error:)` 函数将这些字节作为 `NSData` 对象返回，供 `UIDocument` 存储。之后，`UIDocument` 检索该数据，并将一个 `NSData` 对象传递给 `loadFromContents(_:,ofType:,error:)` 函数，该函数将其解归档/反序列化/解码回原始对象。如果这符合你的应用需求，那么恭喜你——你的 `UIDocument` 实现的这一部分基本完成了！

你的 `MyStuff` 应用则稍显复杂。将应用的所有数据——描述和图片——转换成一个单一的字节流会非常繁琐。图片体积大，编码耗时。这不仅会花费很长时间来保存文档，而且在用户使用应用之前，整个文档还需要加载到内存并转换回图片对象。没人愿意等待十秒钟，更别说整整一分钟来打开你的应用！

`MyStuff` 将采用的解决方案是：将物品的描述（很像第一种方案）归档成一个单一的 `NSData` 对象，但将图片存储在包内的各个独立文件中。*包* 是一个包含多个文件的目录，但向用户呈现并表现为一个单一文件。例如，所有 iOS 和 OS X 应用都是包。

## 封装你的数据

你可能已经察觉到一丝棘手之处。或者，你可能并没有意识到。如果没注意到也不必担心，因为这是一个非常微妙的问题。`contentsForType(_:,error:)` 背后的概念是：它返回代表你的文档的原始数据——仅仅是数据。`contentsForType(_:,error:)` 中的代码无法知道这些数据将如何被存储，它也不负责存储。设计一个“图片将存储在独立文件中”的方案是行不通的，因为 `contentsForType(_:,error:)` 并不处理文件。返回的数据最终可能被存储在一个甚至不像文件的东西里。

那么，`contentsForType(_:,error:)` 如何返回一个对象，该对象描述的不是一个，而是一组独立的数据块，其中一块包含归档后的对象，其他块包含独立的图片数据呢？好吧，恰好 iOS 为此提供了一个工具。它被称为文件包装器，这把我们引向了提供文档数据的第二种方法。



