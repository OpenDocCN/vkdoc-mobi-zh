# 我的文档都去哪儿了？

那么，在 iOS 中你将文档存储在哪里？简单来说：将文档存储在你的应用的私有 `Documents` 文件夹中，也可以选择存储在云端。

详细来说，你可以将文档存储在你的应用有权访问的任何地方，但唯一真正有意义的地方是你的应用的私有 `Documents` 文件夹。每个 iOS 应用都可以访问一组称为其沙盒的私有文件夹。`Documents` 文件夹是其中之一，并且由 iOS 保留，专门用于你的应用的文档。此文件夹的内容会自动由 iTunes 备份。如果你还希望通过 iTunes 交换文档，那么你的文档必须存储在 `Documents` 文件夹中。

这与你在大多数桌面计算机系统上习惯的方式有些不同，在桌面系统中，应用允许你将文档加载和保存到任何位置，并且你的 `Documents` 文件夹可以被所有应用自由共享。在 iOS 中，一个应用只能访问其沙盒中的文件，并且这些目录对用户来说是不可访问的——除非你刻意将 `Documents` 文件夹暴露给 iTunes——或其他应用。

**注意**

如果你对沙盒中的其他文件夹以及它们的用途感兴趣，请阅读文件系统编程指南中的“关于 iOS 文件系统”一节，你可以在 Xcode 的“文档和 API 参考”窗口中找到该指南。

对于 MyStuff，你将在 `Documents` 文件夹中存储一个单独的文档。但是，你不会为此文档提供任何用户界面。该文档将在应用启动时自动打开，用户所做的任何更改都会自动保存到这里。即使你使用标准的文档类，并将数据存储在 `Documents` 文件夹中，整个过程对用户来说也是不可见的。

这并不是说你不能或不应该提供一个界面让用户查看其 `Documents` 文件夹中有哪些文档。一个典型的界面会显示文档名称，可能还会显示预览，并允许用户打开、重命名和删除它们。你可以通过表格视图、集合视图，甚至使用页面视图控制器来实现。如果文档管理对你的应用有意义，就创建一个能够最好地展示你的文档的界面。

从哪里开始保存你的文档似乎是一个很好的起点。你将首先创建 `UIDocument` 的一个自定义子类，并定义你的文档存储在哪里以及如何存储。



## 文档中的 MyStuff

从第 7 章末尾的 `MyStuff` 版本继续，当时你为每个条目添加了一张图片。在项目导航器中，通过 `File` 菜单或右键/按住 Control 键点击导航器，选择 `New ➤ File ...`。使用 `Objective‑C` 类模板，将新类命名为 `MSThingsDocument`，并将其设为 `UIDocument` 的子类。将其添加到项目中。

在 `MSThingsDocument.h` 接口文件中声明两个类方法（新代码以粗体显示）：

```
@interface MSThingsDocument : UIDocument
+ (NSURL*)documentURL;
+ (MSThingsDocument*)documentAtURL:(NSURL*)url;
@end
```

切换到 `MSThingsDocument.m` 实现文件。在 `#import` 语句之后添加以下常量定义：

```
#define kThingsDocumentType     @"mystuff"
#define kThingsDocumentName     (@"Things I Own." kThingsDocumentType)
```

将第一个方法添加到 `@implementation` 部分：

```
+ (NSURL*)documentURL
{
    static NSURL *docURL = nil;
    if (docURL==nil)
    {
        NSFileManager *fileManager = [NSFileManager defaultManager];
        docURL = [fileManager URLForDirectory:NSDocumentDirectory
                                     inDomain:NSUserDomainMask
                            appropriateForURL:nil
                                       create:YES
                                        error:NULL];
        docURL = [docURL URLByAppendingPathComponent:kThingsDocumentName];
    }
    return docURL;
}
```

`+documentURL` 方法返回一个 `NSURL` 对象，其中包含 `MyStuff` 应用使用的唯一文档的文件系统位置。其中包含一些代码，确保位置只构造一次，因为它永远不会改变。

这里重要的方法是 `-URLForDirectory:inDomain:appropriateForURL:create:error:`。这是用于定位 iOS 关键目录（如应用沙盒中的 `Documents` 目录）的几种方法之一。`NSDocumentDirectory` 常量指定了你感兴趣的目录——在六七个指定目录中。要定位应用沙盒中的目录，需指定 `NSUserDomainMask`。`create` 标志告诉文件管理器，如果目录不存在则创建它。这是多余的，因为 `Documents` 目录在应用安装时创建，且应始终存在。

**警告**

不要使用像 `@"~/Documents/"` 这样的常量对标准 iOS 目录的路径进行“硬编码”。应使用 `-URLsForDirectory:inDomain:` 等方法来确定已知目录的路径。标准目录位置会不时更改；不要对其名称或路径做出假设。

一旦获得 `Documents` 文件夹的 URL，就附加文档的名称，创建指向文档存储位置（或即将存储位置）的完整路径。

现在编写一个方法来打开你的文档。`MyStuff` 不会呈现文档界面。启动时，它要么创建一个空文档，要么重新打开现有文档。将该逻辑整合到一个方法中，紧跟在 `+documentURL` 方法之后：

```
+ (MSThingsDocument*)documentAtURL:(NSURL *)url
{
    MSThingsDocument *document;
    document = [[MSThingsDocument alloc] initWithFileURL:url];
    NSFileManager *fileManager = [NSFileManager defaultManager];
    if ([fileManager fileExistsAtPath:url.path])
    {
        [document openWithCompletionHandler:nil];
    }
    else
    {
        [document saveToURL:url
           forSaveOperation:UIDocumentSaveForCreating
          completionHandler:nil];
    }
    return document;
}
```

此方法在给定的（文件）URL 处创建 `MSThingsDocument` 对象的新实例。然后使用文件管理器判断该位置是否已存在文档（`-fileExistsAtPath:`）。如果存在，则向文档对象发送 `-openWithCompletionHandler:` 消息以打开文档并读取其中包含的数据。如果不存在，则通过发送 `-saveToURL:forSaveOperation:completionHandler:` 消息创建一个新文档。之后将打开的文档对象返回给调用者。

**提示**

`MyStuff` 的文档名称无关紧要，因为除了开发者之外，没有人会看到它。但是，如果你确实希望用户能够访问应用 `Documents` 文件夹中的文档，只需在应用的 `info.plist` 中添加 `UIFileSharingEnabled` 键（值为 `YES`）。此标志会告诉 iTunes 向用户公开存储在 `Documents` 文件夹中的文档。通过 iTunes，用户可以浏览、下载、上传和删除该文件夹中的文档。请参阅 iOS 应用编程指南的“应用相关资源”章节。另请查看技术问答 #1699（`QA1699`）。它描述了如何通过 iTunes 有选择地共享某些文档，同时隐藏其他文档。



