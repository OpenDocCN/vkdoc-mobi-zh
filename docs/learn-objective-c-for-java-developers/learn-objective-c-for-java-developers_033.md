# 文件系统基础

每个 Objective-C 进程都有一个 `NSFileManager` 类的单例实例，通过发送 `[NSFileManager defaultManager]` 获得。`NSFileManager` 实现了大多数全局文件系统方法。本章尽可能提供常见文件系统任务的 Objective-C 解决方案，但某些有用的功能只能通过 Cocoa、Core Services 或 BSD 框架中的 C 函数获得。

Java 虚拟机可以托管在各种计算平台上，这些平台具有不同的底层文件系统。因此，Java 文件 API 往往是通用的，并尽可能少做假设。相比之下，Objective-C 和 BSD API 明确假设存在 POSIX 兼容的文件系统。在 Objective-C 中，你不会找到与 `java.io.File.pathSeparatorChar` 等价的变量。Objective-C 中的所有文件和目录路径都是 POSIX 路径，路径组件分隔符始终是单个 `'/'` 字符。Core Services 框架主要假定 HFS+ 文件系统，尽管这主要是为了与遗留代码兼容，因为大多数 HFS+ 文件说明符和数据结构都会被直接转换为 BSD 调用。

虽然编程接口明确假设存在 POSIX 文件系统，但底层文件服务框架在如何将文件系统 API 调用转化为实际操作方面提供了很大的自由度。最终，所有文件系统调用都会对卷（物理存储设备的逻辑分区）的内容进行某些更改。该卷的格式可能差异很大。HFS+ 卷格式是 Mac OS X 操作系统的原生格式，但 BSD 文件系统框架可以很好地处理 UFS 卷、Samba 卷、AppleShare 卷、Windows 卷等。你调用的文件系统 API 会通过名为虚拟文件系统（VFS）的插件架构自动转换为适合卷格式的适当操作。你唯一需要意识到这一点的时候是处理卷格式所不支持的晦涩功能时。例如，UFS 卷可能不支持访问控制列表，而 HFS+ 卷上的文件名可能是区分大小写的，也可能不区分。你通常可以忽略底层卷格式的实现细节；只需注意某些功能可能不会被操作系统支持的所有卷格式实现。

## 识别文件系统中的项目

本节说明文件系统中项目命名的基本规则，以及如何构建指向这些项目的路径。它还详细介绍了如何获取和设置工作目录或默认目录。除了字符串路径外，还可以使用 URL 对象和别名来标识文件。本节说明如何使用文件 URL 和别名，以及如何将它们转换为字符串路径。最后，Mac OS X 定义了许多标准目录，可以通过符号方式请求其位置，从而避免硬编码那些可能在未来发生变化或本质上是可变路径的系统资源。本节描述了用于获取这些目录路径的函数。

### 文件和路径名称

欢迎学习 POSIX 文件名称基础 101。让我们快速回顾一下基础知识，以免后面产生混淆。要处理卷上的任何实体，必须通过名称来标识它。文件名可以是几乎任何字符串，但有以下限制：不能包含 `'/'` 路径分隔符字符、不能是保留名称（`"."` 或 `".."`）、不能是空字符串、也不能太长（不超过 255 个 Unicode 字符）。

字符串 `@"notes.txt"` 是一个有效的文件名。

路径是一个或多个由 `'/'` 路径分隔符字符分隔的串联名称。字符串 `@"james/Documents/Publications"` 是一个路径。路径的最后一个元素命名一个文件。最后一个元素之前的所有名称必须是目录或其他类型的文件系统容器（例如挂载点）的名称。



■ 注意：Objective-C 文件类构建于 POSIX 文件概念之上，并继承了原始 UNIX 操作系统的许多概念。其中最重要的概念是“一切皆文件”。在 UNIX 中，文件系统并非磁盘驱动器上数据文件的具象集合，而是一个用于标识任何串行数据来源的抽象命名系统。UNIX 中的“文件”可以是数据文件、目录、网络套接字、串行端口、信号量、随机数生成器、内存块、逻辑控制台设备等等。可以将术语“文件”视为文件系统层次结构中任何命名实体的基类。目录、数据文件和 FIFO 管道都是“文件”的特化子类。这一术语贯穿于各类 API 中。例如，`-[NSFileManager removeFileAtPath:handler:]` 方法删除的远不止数据文件。如果传入一个目录路径，它会删除该目录及其包含的所有项目。它也会删除一个命名套接字或信号量。在本书中，我遵循 UNIX 对“文件”的命名惯例，意指文件系统中几乎任何命名实体——否则会变得过于混乱。当指代某个卷上的实际文档文件时，我会使用术语“数据文件”。

路径可以是绝对路径或相对路径。以 `/` 开头的路径是绝对路径，指定从文件系统根目录开始的一系列目录。否则，路径是相对路径，指定从当前工作目录开始的一系列目录。某些文件系统函数会识别以波浪号开头的路径，如 `~/Documents/Publications`，表示从当前用户的主目录开始的绝对路径。

无论好坏，UNIX 的扩展名命名惯例都跟随我们进入了二十一世纪。文件扩展名暗示文件包含的信息类型。它们通常是简短标识符——三到四个字母字符——附加在文件名末尾的句点（`.`）之后。名称 `@"Outline.doc"` 的扩展名是 `@"doc"`，名称 `@"Letter to Jake 2.0.pages"` 的扩展名是 `@"pages"`。

Java 的 `java.io.File` 对象大体上是用于标识文件系统中文件的基本对象。可以为不存在的文件创建 `File` 对象。路径操作通过提取 `File` 的属性或基于现有 `File` 对象创建新的 `File` 对象来完成。例如，可以通过创建一个指向该路径的 `File` 对象，然后调用 `getParent()` 来确定路径的父目录。

Objective-C 没有能够表示抽象或不存在的文件的对象。字符串用于表示文件名和路径。如何组装路径或文件名完全由你决定。你可以使用任何字符串操作方法，但 Cocoa 框架提供了几个专门用于处理路径的方法。这些方法通过 `NSPathUtilities` 分类附加到 `NSString` 类上。常用的方法列于表 11-1 中。清单 11-1 展示了 Java 与 Objective-C 路径操作之间的对比。例如，清单 11-1 中的 Java 和 Objective-C 方法都生成了一个指向不存在的文件的路径。

**表 11-1.** `NSString` 的路径和文件名方法

| 方法 | 描述 |
| --- | --- |
| `+(NSString*)pathWithComponents:(NSArray*)components` | 从文件名数组构建一个路径。要构建绝对路径，请将数组的第一个元素设置为字符串 `@"/"`。 |
| `-(NSArray*)stringsByAppendingPaths:(NSArray*)paths` | 将数组中的文件名追加到接收器的路径上。 |
| `-(NSString*)pathComponents` | 将路径分解为单独的文件名，并以数组形式返回。如果路径是绝对的，数组的第一个元素是 `@"/"`。 |
| `-(BOOL)isAbsolutePath` | 如果路径是绝对的（以 `@"/"` 开头），则返回 `YES`。 |
| `-(NSString*)lastPathComponent` | 从路径中提取最后一个文件名。 |
| `-(NSString*)stringByDeletingLastPathComponent` | 移除接收器路径中的最后一个文件名。 |
| `-(NSString*)stringByAppendingPathComponent:(NSString*)str` | 将附加名称追加到路径的末尾。 |
| `-(NSString*)pathExtension` | 从路径中的最后一个名称提取扩展名。 |
| `-(NSString*)stringByDeletingPathExtension` | 从路径中的最后一个名称移除扩展名（如果有）。 |
| `-(NSString*)stringByAppendingPathExtension:(NSString*)str` | 将新的扩展名追加到路径中的最后一个名称。 |
| `-(NSString*)stringByAbbreviatingWithTildeInPath` | 如果接收器的路径以当前用户的主目录路径开头，则将其替换为简写 `@"~"`。 |
| `-(NSString*)stringByExpandingTildeInPath` | 将接收器路径开头的 `@"~"` 替换为当前用户主目录的路径，或将 `@"~account"` 替换为用户“account”的主目录路径。 |
| `-(NSString*)stringByStandardizingPath` | 简化并规范化接收器的路径。 |

这些方法大多不言自明。它们都不会修改接收器。正如 `-stringBy…` 方法名所暗示的，每个方法都返回一个新的字符串对象，其中包含转换的结果。

前几个方法将名称数组转换为完整路径，反之亦然。大部分方法用于提取、删除或向现有路径追加单个名称或文件扩展名。`-stringByStandardizingPath` 方法通过将 `@"~"` 或 `@"~user"` 简写替换为显式路径，将所有相对路径引用和符号链接替换为其物理（非符号）等价物，并移除任何无关的路径元素来“规范化”路径字符串。简而言之，这是一个“清理”方法，将路径简化为其最简单、最明确的形式。

**清单 11-1.** 生成指向不存在的文件的路径

**Java**

```java
public String autoName( String parentDir, String baseName, String ext )
{
    int suffix = 0;
    File parentFile = new File(parentDir);
    File testFile = new File(parentFile,baseName+"."+ext);
    while (testFile.exists()) {
        suffix += 1;
        testFile = new File(parentFile,baseName+"-"+suffix+"."+ext);
    }
    return testFile.getAbsolutePath();
}
```

**Objective-C**

```objc
- (NSString*)autoNameInDirectory:(NSString*)parentDir
                            name:(NSString*)name
                       extension:(NSString*)ext
{
    int suffix = 0;
    NSString *testPath = [parentDir stringByAppendingPathComponent:name];
    testPath = [testPath stringByAppendingPathExtension:ext];
    while ([[NSFileManager defaultManager] fileExistsAtPath:testPath]) {
        NSString *newName = [NSString stringWithFormat:@"%@-%d",name,++suffix];
        testPath = [parentDir stringByAppendingPathComponent:newName];
        testPath = [testPath stringByAppendingPathExtension:ext];
    }
    return testPath;
}
```

##### 工作目录

每个进程都维护一个默认的目录路径，称为当前目录或工作目录。这是用于解析相对路径的目录。所有相对路径都与当前工作目录路径拼接，以获得文件的显式路径。表 11-2 显示了在 Objective-C 中用于获取和更改当前工作目录路径的两个 `NSFileManager` 方法。

**表 11-2.** 工作目录消息

| 方法 | 描述 |
| --- | --- |
| `-(NSString*)currentDirectoryPath` | 返回当前工作目录路径 |
| `-(BOOL)changeCurrentDirectoryPath:(NSString*)path` | 设置工作目录路径 |

如果路径指定了文件系统中一个可访问的目录，该方法会将当前工作目录设置为该路径标识的目录并返回 `YES`。否则，它不做任何操作并返回 `NO`。

Java 中的等效功能通过设置 `"user.dir"` 系统属性来实现。


好的，作为一名高级文档工程师和翻译员，我将严格按照您提供的注意事项和示例，将给定的英文文本翻译成中文。


