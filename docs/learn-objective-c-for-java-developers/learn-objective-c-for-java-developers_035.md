# 过滤与动态定制

过滤及其他动态定制功能通过 `NSSavePanel` 的委托（delegate）实现。要为显示内容中的项目添加过滤功能，需创建一个实现了 `-(BOOL)panel:(id)sender shouldShowFilename:(NSString*)filename` 方法的对象，并将其设置为面板对象的 delegate 属性。

有关委托对象可实现非正式协议的完整列表，请参阅 `NSSavePanel` 文档的“委托方法”章节。

虽然清单 11-4 中的 Objective-C 代码与 Java 代码功能等价，但它并非呈现文件对话框的首选方式。清单 11-5 中的代码会挂起整个应用程序，直至用户选择文件。如果文件选择操作与某个窗口关联，更推荐使用表单（sheet）——即附加到特定窗口的模态子窗口——以避免阻塞高级应用程序界面。清单 11-5 使用与清单 11-4 相同的面板启动了一个模态表单。`beginSheet…` 消息会立即返回，使主应用程序的运行循环保持连续运行。当用户在表单中做出选择后，`didEndSelector:` 参数指定的消息会发送给 `modalDelegate:` 对象。只要方法参数与非正式协议定义的内容匹配，这些对象和消息可由你自由选择。

[www.it-ebooks.info](http://www.it-ebooks.info/)

## 第 11 章：文件

### 清单 11-5. 使用文件选择表单

```
NSOpenPanel *panel = [NSOpenPanel openPanel];
[panel setCanChooseFiles:YES];
[panel setCanChooseDirectories:YES];
[panel setAllowsMultipleSelection:NO];
[panel beginSheetForDirectory:nil       // 使用默认目录
                         file:nil       // 不预选任何文件
                modalForWindow:documentWindow  // 表单附加到该窗口
                 modalDelegate:self
                didEndSelector:@selector(myOpenDidEnd:returned:context:)
                   contextInfo:NULL];
...
- (void)myOpenDidEnd:(NSSavePanel*)sheet returned:(int)code context:(void*)ignored
{
    if (code==NSOKButton) {
        NSString *file = [sheet filename];
        …
    }
}
```

### 符号链接、硬链接与别名

Mac OS X 操作系统提供了三种引用其他文件的文件类型。第一种是符号链接。这类特殊文件包含指向文件系统中另一个目录的路径。

通常，符号链接的行为表现如同它所指向的目录。例如，目录 `/Users/hannah/Documents/Updates` 中包含文件 `Monday.doc`，而文件 `/Users/hannah/Public/New` 是一个符号链接，包含路径 `/Users/hannah/Documents/Updates`。在这种情况下，路径 `/Users/hannah/Documents/Updates/Monday.doc` 与 `/Users/hannah/Public/New/Monday.doc` 在功能上等价，并指向同一文件。

`NSFileManager` 提供了创建符号链接及获取其内容的方法。

大多数文件操作会透明地遍历符号链接。以符号链接结尾的路径通常指向链接文件本身，而非其指向的目录。例如，语句 `[[NSFileManager defaultManager] removeItemAtPath:@"/Users/hannah/Public/New" error:NULL]` 会删除符号链接文件，而非 `Updates` 文件夹。一个值得注意的例外是 `-[NSFileManager fileAttributesAtPath:traverseLink:]` 方法。其第二个 `BOOL` 参数决定了它是返回符号链接文件的属性，还是返回符号链接所指向文件的属性。表 11-6 列出了用于创建和读取符号链接内容的方法。

[www.it-ebooks.info](http://www.it-ebooks.info/)

## 第 11 章：文件

### 表 11-6. NSFileManager 符号链接方法

| 方法 | 描述 |
|--------|-------------|
| `-(BOOL)createSymbolicLinkAtPath:(NSString*)path withDestinationPath:(NSString*)destPath error:(NSError**)error` | 在 path 处创建一个指向 destPath 文件的符号链接文件。 |
| `-(NSString*)destinationOfSymbolicLinkAtPath:(NSString*)path error:(NSError**)error` | 提取符号链接文件所指向的路径。 |



`-stringByStandardizingPath`的主要目的之一（如表 11-1 所述）是替换路径中的符号链接，使其指向原始文件。沿用前述示例，将

`@"/Users/hannah/Public/New/Monday.doc"`传递给`-stringByStandardizingPath`会返回

`@"/Users/hannah/Documents/Updates/Monday.doc"`。

第二种替身文件是别名文件，其概念和数据结构继承自经典 Macintosh 操作系统。别名文件包含序列化的`AliasRecord`结构。要使用它们，必须读取文件内容，并通过 Core Services 函数解析别名。别名的优势在于其智能性：例如，为文档文件创建别名后，即使原文件在同一卷中被重命名或移动到其他目录，别名仍能定位到它。此外，如果原文件位于外部卷或网络卷上，别名可以自动重新挂载该卷，恢复对原始文件的访问。

从 Cocoa 和 BSD 框架的角度看，别名文件仅是普通数据文件，不会自动被识别或解析。唯一的例外是少数高级文件类（如刚刚讨论过的`NSOpenPanel`）。`NSOpenPanel`具有`resolvesAliases`属性，若该属性被设置，系统会自动解析用户选择的任何别名，返回别名所指向的项目，而非别名文件本身。本章后续部分将介绍一些用于处理别名记录的基础函数。

最后，某些卷格式支持硬链接。硬链接是指文件系统中两个或多个文件共享相同的内容，修改其中一个的数据会同时改变另一个。与别名和符号链接不同，硬链接文件是平级关系，每个文件都与普通文件无异，不存在一个文件“指向”另一个文件的情况；两者在卷上封装的是相同的物理数据。实际上，硬链接文件并不常用。`NSFileManager`创建硬链接的方法为：

`-(BOOL)linkItemAtPath:(NSString*)srcPath toPath:(NSString*)dstPath error:(NSError**)error`。

若要“断开”硬链接，只需删除其中一个文件。删除硬链接文件不会丢弃任何数据，因为数据仍保留在另一个文件中。

### 处理目录内容

通过读取目录内容来发现文件是一项常见的编程任务。通常是为了对目录中的每个文件执行某些操作，可能还会递归处理目录中子目录包含的文件。前者是对目录的浅层检查，后者则是深层检查。

获取目录内容的三种基本方法如表 11-7 所示。

`-contentsOfDirectoryAtPath:…`仅返回目录的直接内容（作为文件名数组，而非路径）。`-subpathsOfDirectoryAtPath:…`返回一个数组，包含目录中每个文件的完整路径，以及所有子目录、子子目录等内容。

**表 11-7.** `NSFileManager`目录内容方法

| 方法 | 返回值 |
|------|--------|
| `-(NSArray*)contentsOfDirectoryAtPath:(NSString*)path error:(NSError**)error` | 目录中文件的浅层列表 |
| `-(NSArray*)subpathsOfDirectoryAtPath:(NSString*)path error:(NSError**)error` | 包含目录下所有文件路径的深层列表 |
| `-(NSDirectoryEnumerator*)enumeratorAtPath:(NSString*)path` | 目录项枚举器 |

`-enumeratorAtPath:`方法返回一个有状态的`NSDirectoryEnumerator`对象，用于迭代目录中的项目。枚举可以是浅层、深层或任意混合形式，具体通过在枚举对象上战略性地发送`-skipDescendents`消息实现。清单 11-6 展示了遍历目录层级、处理所有`txt`文件但忽略具有`private`扩展名的任何目录内容的代码。



### 递归处理目录内容 (Listing 11-6)

**Java**

`scanDirectory("/Users/ann/Documents");`

…

```java
void scanDirectory( File directory )
{
    File[] files = directory.listFiles();
    for ( File file: files ) {
        if (file.isFile()) {
            if (file.getName().endsWith(".txt"))
                scanTextFile(file);
        }
        else if (file.isDirectory()) {
            if (!file.getName().endsWith(".private"))
                scanDirectory(file);
        }
    }
}

void scanTextFile( File textFile )
{
    …
}
```

**Objective-C**

`NSString *path = @"/Users/ann/Documents";`

`NSDirectoryEnumerator *e = [[NSFileManager defaultManager] enumeratorAtPath:path];`

`while ( (path=[e nextObject])!=nil ) {`

`NSString *type = [[e fileAttributes] objectForKey:NSFileType];`

```objectivec
if ([type isEqualToString:NSFileTypeRegular]) {
    if ([[path pathExtension] isEqualToString:@"txt"])
        [self scanTextFile:path];
}
else if ([type isEqualToString:NSFileTypeDirectory]) {
    if ([[path pathExtension] isEqualToString:@"private"])
        [e skipDescendents];
}
```

…

```objectivec
- (void)scanTextFile:(NSString*)textFilePath
{
    …
}
```

Java 与 Objective-C 代码（如 Listing 11-6 所示）最显著的区别在于，Java 方案使用了递归方法，而线性循环的 Objective-C 则将目录递归委托给了 `NSDirectoryEnumerator` 对象。

### 文件属性

文件的属性或元数据是关于文件的信息。Java 的 `java.io.File` 对象是获取文件属性的主要接口。它实现了多种特定的方法（即 `boolean isFile()`、`long lastModified()`、`long length()`），用于描述文件的各种属性。

Objective-C 有一个单一的 `-[NSFileManager fileAttributesAtPath:traverseLink:]` 方法，该方法接受一个文件路径，并返回一个包含文件所有显著属性的不可变字典。`traverseLink:` 参数决定如果路径指定了一个符号链接文件应如何处理。如果为 `YES`，则返回的属性将是符号链接所指向文件的属性；如果为 `NO`，则属性描述的是符号链接文件本身。

要检查某个特定属性，可以从字典集合中检索其值。Listing 11-6 中已经演示了一个使用文件属性的示例。这些键是字符串常量，其值为 `NSNumber`、`NSDate` 或 `NSString` 对象。表 11-8 列出了 `java.io.File` 中的属性方法及其 Objective-C 等价物。其中一些是方法，而另一些是属性字典的键。

#### *表 11-8. 文件属性键*

| `java.io.File` 方法 | Objective-C 方法或键 | 描述                                     |
| :----------------- | :------------------------- | :--------------------------------------- |
| `exists()`         | `-[NSFileManager fileExistsAtPath:]` | 文件是否存在                             |
| `canRead()`        | `-[NSFileManager isReadableFileAtPath:]` | 文件是否可读                           |
| `canWrite()`       | `-[NSFileManager isWritableFileAtPath:]` | 文件是否可写                           |
|                    | `NSFilePosixPermissions`   | 包含用户、组和全局读/写/执行权限位的整数 |
| `isFile()`         | `NSFileType`               | 如果类型为 `NSFileTypeRegular` 则为真    |
| `isDirectory()`    | `NSFileType`               | 如果类型为 `NSFileTypeDirectory` 则为真  |
| `isHidden()`       | `LSCopyItemInfoForURL(…)`  | Launch Services API 提供显示信息        |
| `lastModified()`   | `NSFileModificationDate`   | 文件最后修改的 `NSDate`                 |
| `length()`         | `NSFileSize`               | 文件的逻辑长度                         |
|                    | `NSFileCreationDate`       | 文件创建的 `NSDate`                     |
|                    | `NSFileOwnerAccountID`     | 文件所有者的账户编号                   |
|                    | `NSFileOwnerAccountName`   | 文件所有者的账户名                     |
|                    | `NSFileGroupOwnerAccountID` | 文件组的组编号                       |
|                    | `NSFileGroupOwnerAccountName` | 文件组的组名                         |

文件的类型是通过检查其 `NSFileType` 属性来确定的。该属性值将是以下之一：`NSFileTypeDirectory`、`NSFileTypeRegular`、`NSFileTypeSymbolicLink`、`NSFileTypeSocket`、`NSFileTypeCharacterSpecial`、`NSFileTypeBlockSpecial` 或 `NSFileTypeUnknown`。关于项目显示名称的详细信息、文件的扩展名是否应对用户隐藏，或者整个文件是否应被隐藏，都可以通过各种 Launch Services 函数获得。有关文件属性键的完整列表，请参阅 `NSFileManager` 文档。

[www.it-ebooks.info](http://www.it-ebooks.info/)



大多数文件属性可以通过`-[NSFileManager setAttributes:ofItemAtPath:error:]`方法进行修改。你需要提供一个包含要修改属性的字典以及文件路径。

某些属性（如`NSFileType`和`NSFileSize`）无法通过`-setAttributes:…`修改。安全策略可能会条件性地禁止修改其他属性，例如`NSFileOwnerAccountID`。

[www.it-ebooks.info](http://www.it-ebooks.info/)

## 第 11 章 ■ 文件

如果所有属性更改均成功生效，该方法返回`YES`。如果返回`NO`，则表示更改结果不确定。

### 高级文件操作

Objective-C 应用程序倾向于以整体方式处理文件，而不是采用更传统的做法：打开文件、读取部分或全部数据、然后再次关闭。应用类（如文档管理类）更倾向于将整个文件读取到单个数据对象中。文档文件的典型生命周期是：完整读取到单个`NSData`对象中、编辑、然后写回覆盖原始文件。

在现代化文件系统中，即使是执行像复制文件这样的简单操作，也很难“正确”完成。扩展元数据、访问控制列表、文件所有权、多个数据分支、显示属性以及远程文件复制协议，仅仅是复制一个文件时需要考虑的看似无穷无尽的细节中的一小部分。

为了减轻你的负担，Cocoa 框架提供了许多高级方法，这些方法能够正确地对整个文件执行各种原子操作。常用的方法列于表 11-9。

**表 11-9. 常见的高级文件操作**

| 方法 | 描述 |
| :--- | :--- |
| `-[NSFileManager contentsAtPath:]` | 返回整个文件的内容作为`NSData`对象 |
| `+[NSData dataWithContentsOfFile:]` | 使用文件内容创建新的`NSData`对象 |
| `+[NSData dataWithContentsOfMappedFile:]` | 创建映射到文件数据的虚拟内存区域 |
| `-[NSData writeToFile:atomically:]` | 将数据对象的内容写入文件 |
| `+[NSString stringWithContentsOfFile:encoding:error:]` | 使用文件内容创建新的`NSString`对象 |
| `-[NSString writeToFile:atomically:encoding:error:]` | 将字符串内容写入文件 |
| `-[NSFileManager copyItemAtPath:toPath:error:]` | 复制文件 |
| `-[NSFileManager moveItemAtPath:toPath:error:]` | 移动或重命名文件 |
| `-[NSFileManager removeItemAtPath:error:]` | 删除文件 |
| `-[NSFileManager contentsEqualsAtPath:andPath:]` | 比较两个文件或两个目录的内容 |
| `-[NSWorkspace performFileOperation:source:destination:files:tag:]` | 移动、复制、链接或丢弃一组文件 |

[www.it-ebooks.info](http://www.it-ebooks.info/)

## 第 11 章 ■ 文件

有很多方法可以将整个文件内容读取到`NSData`或`NSString`对象中，此处并未全部列出。如果表 11-9 中的方法不能满足需求，请查阅`NSData`和`NSString`文档中与这些方法类似但不完全相同的变体。接受`atomically:`参数的写入方法可以选择执行所谓的“安全保存”：它们将数据写入一个临时文件，将临时文件与目标文件交换，然后删除原始文件。如果在保存过程中发生任何意外，原始文件不会丢失。

`NSFileManager`中用于复制、移动、删除文件或比较两个文件的方法，同样适用于数据文件和目录。

`NSWorkspace`的`-performFileOperation:source:destination:files:tag:`方法也可用于移动、复制或链接文件，具体取决于传递给`operation`参数的常量。这些操作等同于`NSFileManger`提供的方法。其一个特殊功能是`NSWorkspaceRecycleOperation`操作，该操作会将文件移动到废纸篓。将文件移动到废纸篓实际上是一个复杂的过程，最好由操作系统来处理。

`NSWorkspace`



`NSWorkspace`类提供了许多高级文件和用户相关函数，这些函数仅在图形化应用程序的上下文中有效。例如，方法`-[NSWorkspace iconForFile:]`会返回包含文件图标的`NSImage`对象，而`-[NSWorkspace launchApplication:]`则会启动另一个 GUI 应用程序。这些信息对于不在图形界面上下文中运行的守护进程或进程是不可用的。因此，你无法在守护进程中使用`NSWorkspace`，但可以使用`NSFileManager`。其他文件显示细节可通过本章末尾将介绍的 Launch Services API 获得。这类信息正是你需要使用`javax.swing.filechooser.FileSystemView`来获取的。

### 随机文件访问

如果你需要更传统的打开-读取-写入-关闭文件接口，Cocoa 提供了`NSFileHandle`类，其功能大致相当于`java.io.RandomAccessFile`。`NSFileHandle`比`RandomAccessFile`更通用，因为它本质上是一个 BSD 文件描述符的对象包装器。POSIX 文件系统中的“文件”可以是数据文件、串行通信端口或管道。因此，`NSFileHandle`对象可用于与所有这些构造进行交互。基本的`NSFileHandle`方法列于表 11-10 中。

***表 11-10.** 文件方法*

| 随机文件访问 | `NSFileHandle` | 描述 | `RandomAccessFile` |
| :--- | :--- | :--- | :--- |
| `new` | `+fileHandleForReadingAtPath:` | 打开文件进行读取 | `RandomAccessFile(…,"r")` |
| `new` | `+fileHandleForWritingAtPath:` | 打开文件进行写入 | `RandomAccessFile(…,"w")` |
| `new` | `+fileHandleForUpdatingAtPath:` | 打开文件进行读写 | `RandomAccessFile(…,"rw")` |
| `close()` | `-closeFile` | 关闭文件 | `close()` |
| `getFD()` | `-fileDescriptor` | 返回底层文件描述符 | `getFD()` |
| `length()` | 使用`-seekToEndOfFile`或获取属性 | 获取文件长度 | `length()` |
| `readFully(byte[])` | `-readDataOfLength:` | 读取指定数量的字节 | `readFully(byte[])` |
| `read(byte[])` | `-availableData` | 读取尽可能多的可用数据 | `read(byte[])` |
| -- | `-readInBackgroundAndNotify` | 异步读取可用数据 | -- |
| -- | `-readToEndOfFileInBackgroundAndNotify` | 异步读取所有数据 | -- |
| -- | `-waitForDataInBackgroundAndNotify` | 等待数据变为可用 | -- |
| `getFilePointer()` | `-offsetInFile` | 当前文件位置 | `getFilePointer()` |
| `seek(long)` | `-seekToFileOffset:` | 设置文件位置 | `seek(long)` |
| -- | `-seekToEndOfFile` | 将文件位置设置为文件长度 | `seek(long)` |
| `setLength()` | `-truncateFileAtOffset:` | 设置文件的逻辑长度 | `setLength()` |
| `skipBytes()` | 使用`-seekToFileOffset:` | 跳过字节 | `skipBytes()` |
| `write(byte[])` | `-writeData:` | 写入字节 | `write(byte[])` |

异步方法在派生出最终执行操作的新线程后会立即返回。操作完成后，你的应用程序会收到一个通知。对于读取操作，通知中包含获取的数据。通知将在第 18 章中介绍。此外，还有专门用于`NSFileHandle`包装通信套接字文件时的异步通知。有关更多细节和发送的特定通知，请参阅`NSFileHandle`文档。

若需要更精细的控制，你可以转向使用 BSD 文件函数。你可以使用 BSD 函数执行所有文件操作，或者在需要执行特定操作时从`NSFileHandle`对象获取文件描述符。

### NSFileManager 委托

与许多 Objective-C 对象一样，`NSFileManager`支持委托模式。你的委托对象可以预先拦截特定操作，或在发生某些故障时尝试恢复。表 11-11 列出了文件管理器会发送给委托对象的消息（如果实现了这些方法）。

`NSFileManager`同时使用持久委托和临时委托。设置为单例`NSFileManager`对象委托的对象会接收关于一般文件管理器操作的相关消息。少数`NSFileManager`方法接受一个临时委托对象，即其处理程序。这些方法是`-copyPath:toPath:handler:`、`-movePath:toPath:handler:`、`-removeFileAtPath:handler:`和`-linkPath:toPath:handler:`。在这些方法中，委托消息会发送给处理程序对象，而非全局委托。

***表 11-11.** NSFileManager 委托方法*

| 方法 | 接收者 | 发送时机 |
| :--- | :--- | :--- |
| `-fileManager:willProcessPath:` | 处理程序 | 在尝试复制、移动、删除或创建文件的硬链接之前 |
| `-fileManager:shouldCopyItemAtPath:toPath:` | 委托 | 在复制文件之前 |
| `-fileManager:shouldMoveItemAtPath:toPath:` | 委托 | 在移动文件之前 |
| `-fileManager:shouldRemoveItemAtPath:` | 委托 | 在删除文件之前 |
| `-fileManager:shouldLinkItemAtPath:toPath:` | 委托 | 在创建硬链接之前 |
| `-fileManager:shouldProceedAfterError:copyingItemAtPath:toPath:` | 处理程序 | 在复制、移动、删除或链接文件出错后 |
| `-fileManager:shouldProceedAfterError:copyingItemAtPath:toPath:` | 委托 | 在复制文件出错后 |
| `-fileManager:shouldProceedAfterError:movingItemAtPath:toPath:` | 委托 | 在移动文件出错后 |
| `-fileManager:shouldProceedAfterError:removingItemAtPath:` | 委托 | 在删除文件出错后 |
| `-fileManager:shouldProceedAfterError:linkingItemAtPath:toPath:` | 委托 | 在创建硬链接出错后 |

`should…AtPath:`方法在操作开始前发送，允许委托有机会禁止该操作发生。你可以用此方法来监控文件管理器的进度或筛选其处理的项目。`shouldProceedAfterError:…`方法在操作处理某个项目时遇到错误时发送，允许处理程序记录错误、从故障中恢复或忽略它。

### 其他 API

本章主要集中于与文件系统交互的 Objective-C 类和方法。这些对于大多数用途来说已经足够，但许多重要功能只能通过 Core Services 和 BSD API 获得。

各个框架的功能之间存在大量重叠。例如，Core Services 框架提供了`FSCopyObjectSync`函数，其功能大致相当于`-[NSFileManager copyItemAtPath:toPath:error:]`。然而，它还包括`FSCopyObjectAsync`，该函数会在其自身线程中执行复制操作。因此，你选择使用哪个 API 在很大程度上将取决于你需要的特殊功能或能力。

Core Services 框架实际上是一个框架的集合，其中包含基本的文件 I/O 函数。其中值得关注的子框架是 Carbon Core 和 Launch Services 框架。Carbon 函数除了提供与经典 Macintosh OS 编写应用程序的向后兼容性外，还提供了基本的文件函数。Launch Services 提供了当用户与文件系统交互时至关重要的函数。这包括文件的可见性信息、显示名称、图标、用户打开文档时将启动哪个应用程序、哪些应用程序能够打开给定文档等等。一些常见的 Core Services 函数列于表 11-12 中。通常，Carbon 框架中的文件系统函数都以“FS”开头，而 Launch Services 的函数则以“LS”开头。



