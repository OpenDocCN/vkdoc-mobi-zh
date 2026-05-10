# 注意

经典 Macintosh OS 中的每个文件都有两个分支：数据分支和资源分支。虽然资源分支被假设具有特定结构，但基本事实是每个文件都可能是两个文件。当你使用 Carbon 函数打开文件时，不仅要指定要打开的文件，还要指定要打开哪个分支。该 API 后来被泛化以支持任意数量的文件分支。但实际上，只有最初的两个分支得到持续支持。数据分支是文件的未命名分支，也是 BSD 函数和 Cocoa 方法访问的分支。虚拟文件系统可以通过创建额外的、不可见的文件来容纳数据，从而在不原生支持多分支文件的文件系统上模拟多分支文件。BSD 和 Cocoa 函数可以使用形式为 `file.data/rsrc` 的合成路径名来访问文件的资源分支。这种语法本质上是将每个数据文件视为一个目录，其中包含任意数量的命名分支文件。

## 表 11-12. 核心服务

| 功能 | 描述 |
|----------|-------------|
| `FSPathMakeRef` | 从 POSIX 路径创建一个 `FSRef` |
| `FSRefMakePath` | 返回与 `FSRef` 等价的 POSIX 路径 |
| `FSMakeFSRefUnicode` | 从父 `FSRef` 和文件名创建一个新的 `FSRef` |
| `FSCompareFSRefs` | 判断两个 `FSRef` 结构是否指向同一实体 |
| `FSGetCatalogInfo` | 获取文件的目录信息 |
| `FSGetCatalogInfoBulk` | 一次性获取多个文件的目录信息 |
| `FSSetCatalogInfo` | 更改文件的目录信息 |
| `FSGetVolumeInfo` | 获取卷的信息 |
| `FSSetVolumeInfo` | 更改卷的信息 |
| `FSCreateFileUnicode` | 创建一个新的空文件 |
| `FSDeleteObject` | 删除一个文件 |
| `FSExchangeObjects` | 交换两个文件的数据内容 |
| `FSCreateFork` | 创建一个数据分支或资源分支 |
| `FSOpenFork` | 打开一个数据分支或资源分支 |
| `FSReadFork` | 从文件中读取数据 |
| `FSWriteFork` | 向文件中写入数据 |
| `FSGetForkPosition` | 获取当前文件位置 |
| `FSSetForkPosition` | 更改文件位置 |
| `FSGetForkSize` | 获取文件的逻辑长度 |
| `FSSetForkSize` | 更改文件的逻辑长度 |
| `FSFlushFork` | 将所有缓存的更改写入物理介质 |
| `FSCloseFork` | 关闭文件 |
| `FSNewAlias` | 为文件创建别名记录 |
| `FSResolveAlias` | 获取别名记录最佳描述的文件 |
| `FSResolveAliasFile` | 获取别名文件最佳描述的文件 |
| `FSMatchAliasBulk` | 获取别名记录可能描述的所有文件的列表 |
| `LSCopyDisplayNameForRef` | 应向用户显示的文件名 |
| `LSCopyItemInfoForRef` | 获取文件的显示属性（隐藏、捆绑包等） |
| `LSGetExtensionInfo` | 获取文件的扩展名和显示信息 |
| `LSGetApplicationForItem` | 获取用户打开文件时将启动的应用程序 |

许多核心服务函数接受或返回一个 `FSRef`（文件系统引用）数据结构。`FSRef` 是一种不透明且不可移植的结构，用于在文件系统中唯一标识一个文件。你不能解释该结构的内容（不透明部分），也不能在当前进程的内存地址之外使用该结构（不可移植部分）。因此，不要试图将其保存到磁盘或复制到另一个进程。没有用于初始化或销毁 `FSRef` 结构的调用，因此它们可以声明为未初始化的变量，然后设置、使用、按值复制，最终被遗忘。

`FSRefMakePath` 和 `FSPathMakeRef` 是将 C 路径字符串转换为 `FSRef` 结构以及反向转换的主要函数。

清单 11-7 中的示例代码从一个 Objective-C 字符串对象中的 POSIX 路径开始，并将其转换为一个可移植的别名记录。该记录存储在由 `FSNewAlias` 函数分配的结构中。“句柄”只是一个指向指针的指针。该别名稍后被解析并转换回路径字符串，适用于 `NSFileManager` 消息。在此过程中，代码演示了如何在 Objective-C 路径字符串和 `FSRef` 结构之间进行转换。请注意，清单 11-7 中的代码本可以通过使用 `FSNewAliasPath` 函数来节省将路径转换为 `FSRef` 的第一步，但此处包含此步骤是为了说明这种常见做法。

## 清单 11-7. Objective-C 路径到别名

```
NSString *path = @"/Users/james/Desktop";
OSStatus err;

// 将 POSIX 路径字符串转换为别名记录
FSRef pathRef;
AliasHandle aliasHndl;
err = FSPathMakeRef((const UInt8*)[path fileSystemRepresentation],&pathRef,NULL);
if (err!=noErr)
    /* 错误处理 */;
err = FSNewAlias(NULL,&pathRef,&aliasHndl);
if (err!=noErr)
    /* 错误处理 */;
/* 成功 */
...
// 解析 aliasHndl 并获取其 POSIX 路径字符串
FSRef originalRef;
Boolean aliasWasUpdated;
err = FSResolveAlias(NULL,aliasHndl,&originalRef,&aliasWasUpdated);
if (err!=noErr)
    /* 错误处理 */;
NSMutableData *pathBuffer = [NSMutableData dataWithLength:2048];
char *pathBytes = (char*)[pathBuffer bytes];
err = FSRefMakePath(&originalRef,(UInt8*)pathBytes,[pathBuffer length]);
if (err!=noErr)
    /* 错误处理 */;
NSFileManager *fm = [NSFileManager defaultManager];
NSString *originalPath = [fm stringWithFileSystemRepresentation:pathBytes length:strlen(pathBytes)];
/* 成功 */
```

表 11-12 仅列出了核心服务框架中实现的一小部分函数。列出的函数有多种变体，此外还有数百个其他函数。核心服务提供了获取和设置卷信息、挂载和弹出卷、接收文件系统通知、实时跟踪更改、记录光学介质以及对远程卷执行操作的函数——仅举几例。对于这些专用函数，Mac OS X 文档是你最好的资源。

在这些框架之下，是实现了底层文件系统的核心 BSD 函数。这些是自 UNIX 诞生以来就存在的传统 `open(...)`、`read(...)`、`write(...)`、`close(...)` 函数。关于这个 API 已经有很多书籍，它被充分记录，并且网上有大量教程和资源——因此我无需赘述。如果你有一个 `NSFileHandle` 对象，并且需要对底层文件应用 BSD 函数，请向其发送 `-fileDescriptor` 消息。返回的整数是用于标识打开文件的 BSD 文件描述符令牌。如果你已经有了一个 BSD 文件描述符，可以使用 `[[NSFileHandle alloc] initWithFileDescriptor:fd]` 将其包装在 `NSFileHandle` 中。

### 总结

你现在应该对 Objective-C、Cocoa 框架和 Mac OS X 的基本文件 I/O 工具有了良好的基础理解。主要的区别在于使用字符串而不是 `java.io.File` 对象来操作路径，使用属性字典而不是文件对象属性，以及依赖 C 函数来实现高级功能。除此之外，你会发现基本的文件相关任务（读取数据文件、写入数据文件、删除文件）没有显著变化。

---

