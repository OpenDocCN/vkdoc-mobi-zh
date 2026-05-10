# 文件和目录操作

## 唯一显著的区别在于，`user.dir` 属性只是一个字符串，直到文件操作需要解析相对路径时才会被求值。`changeCurrentDirectoryPath:` 方法会对路径进行鉴定，如果路径不可用，则会拒绝更改。代码清单 11-2 修改了当前工作目录，使其指向一个 “temp” 子目录，但仅在该子目录存在时才进行修改。

### 代码清单 11-2. 更改当前工作目录

**Java**

```java
String workingPath = System.getProperty("user.dir");
File testFile = new File(workingPath, "temp");

if (testFile.exists()) {
    System.setProperty("user.dir", testFile.getAbsolutePath());
}
```

**Objective-C**

```objc
NSString *current = [[NSFileManager defaultManager] currentDirectoryPath];
NSString *temp = [current stringByAppendingPathComponent:@"temp"];

[[NSFileManager defaultManager] changeCurrentDirectoryPath:temp];
```

Mac OS X 进程的初始当前目录继承自启动该进程的环境。对于命令行工具，它将是启动该工具的 Shell 的工作目录。对于应用程序捆绑包，操作系统通常会在启动进程前，将工作目录设置为包含该应用程序捆绑包的目录。

##### 文件 URL

在几个主要版本之前，Cocoa 框架开始采用通用资源定位符 (URL) 作为数据源的首选标识符对象。例如，原有的 `-[NSDocumentController openDocumentWithContentsOfFile:display:]` 方法已被弃用，取而代之的是 `-openDocumentWithContentsOfURL:display:error:`。这提高了许多类的灵活性，因为 URL 可以引用来自各种来源的数据——不仅仅是文件系统中的数据文件。但这同时也意味着，你可能需要将文件系统路径转换为 URL，或者反过来转换。

Java 的 `java.net.URL` 对象可以引用文件，但通常不结合 `java.io.*` 类使用。`java.io.File` 类是对 URL，或者更准确地说，是对 URI（通用资源标识符）的仅有让步。它支持从 URI 构造 `File` 对象，并提供了 `File.toURI()` 和 `File.toURL()` 方法用于获取 `File` 的 URL。表 11-3 列出了在 Cocoa 中路径与文件 URL 之间进行转换的基本方法。

**表 11-3.** 与文件相关的 `NSURL` 方法

| 方法                                 | 描述                             |
| ------------------------------------ | -------------------------------- |
| `+(NSURL*)fileURLWithPath:(NSString*)path` | 为给定文件创建一个文件 URL。     |
| `-(BOOL)isFileURL`                   | 如果接收者是文件方案 URL，则返回 `YES`。 |
| `-(NSString*)path`                   | 如果接收者是文件方案 URL，返回该项的文件系统路径。 |

## 创建和删除目录

方法 `-[NSFileManager createDirectoryAtPath:withIntermediateDirectories:attributes:error:]` 用于创建新目录。如果 `withIntermediateDirectories:` 参数为 `YES`，那么任何缺失的中间目录也会一并创建。例如，如果路径 `/Users/daphne/Music` 已存在，向 `-createDirectoryAtPath:withIntermediateDirectories:attributes:error:` 发送路径 `@"/Users/daphne/Music/Albums/Cocteau Twins"` 并将 `withIntermediateDirectories:` 设置为 `YES`，将会在 `Music` 目录内创建 `Albums` 目录，然后在 `Albums` 目录内创建 `Cocteau Twins` 目录。在此模式下，该消息等同于 `java.io.File.mkdirs()` 方法。如果 `withIntermediateDirectories:` 为 `NO`，则会返回一个错误，因为期望其父目录已经存在。这相当于调用 `java.io.File.mkdir()`。`attributes:` 参数是一个要设置的属性字典。有关属性字典的信息，请参阅本章后面的“文件属性”部分。

删除目录与删除任何文件一样，都使用 `-[NSFileManager removeItemAtPath:error:]`。

##### 定位特殊目录

通常认为，将知名目录的路径硬编码为字符串常量是一种不好的做法。这些路径常常会随着时间推移而改变，从而破坏你的应用程序。Mac OS X 操作系统定义了许多具有特定用途的目录。此外，它还定义了一组域，这些域形成了一个层级结构，用以定义资源的作用范围。例如，安装字体的方法是把字体文件放入 `Fonts` 文件夹。每个用户都有自己的 `Fonts` 文件夹，所有用户共享一个 `Fonts` 文件夹，系统有一个 `Fonts` 文件夹，网络上也有一个 `Fonts` 文件夹。字体被复制到哪个 `Fonts` 文件夹决定了其作用范围。

通过调用 `NSSearchPathForDirectoriesInDomains(…)` 并传入三个参数来定位特殊目录：一个描述知名目录的常量、一个可接受域的位掩码，以及一个用于自动展开返回路径中 “~” 简写标记的标志位。代码清单 11-3 展示了如何使用 `NSSearchPathForDirectoriesInDomains(…)` 来获取用户桌面的路径。

### 代码清单 11-3. 定位用户的桌面目录

```objc
NSArray *paths = NSSearchPathForDirectoriesInDomains(NSDesktopDirectory, NSUserDomainMask, YES);
if ([paths count] > 0) {
    NSString *desktopPath = [paths objectAtIndex:0];
    …
}
```

`NSSearchPathForDirectoriesInDomains(…)` 返回一个数组，其中包含在所请求的每个域中该目录的路径，顺序按照搜索顺序排列，从最具体到最通用。表 11-4 列出了常用的目录常量。域参数可以是表 11-5 中任何域的任意组合（使用 OR 运算），也可以是常量 `NSAllDomainsMask`。

**表 11-4.** 常用目录常量

| 目录常量                              | 描述（示例路径）                  |
| ------------------------------------ | --------------------------------- |
| `NSApplicationDirectory`             | 应用程序 (`/Applications`)        |
| `NSLibraryDirectory`                 | 用户资源，如字体、偏好设置和日志文件 (`~/Library`) |
| `NSUserDirectory`                    | 包含所有用户主文件夹的文件夹 (`/Users`) |
| `NSDocumentDirectory`                | 文档 (`~/Documents`)              |
| `NSDesktopDirectory`                 | 桌面 (`~/Desktop`)                |
| `NSCachesDirectory`                  | 缓存文件 (`~/Library/Caches`)     |
| `NSApplicationSupportDirectory`      | 应用程序使用的辅助文件 (`/Library/Application Support`) |
| `NSDownloadsDirectory`               | 下载文件夹 (`~/Downloads`)        |

**表 11-5.** 目录域

| 域                      | 描述               |
| ----------------------- | ------------------ |
| `NSUserDomainMask`      | 当前用户           |
| `NSLocalDomainMask`     | 此系统的所有用户   |
| `NSSystemDomainMask`    | 整个系统           |
| `NSNetworkDomainMask`   | 本地网络上的所有系统 |

还有两个专门用途的函数可以返回可变路径。`NSHomeDirectory()` 返回当前用户主目录的路径。`NSTemporaryDirectory()` 返回专门为创建临时文件而保留的目录路径。

### 向用户请求文件

在 Java 中，提示用户以交互方式选择文件的方式非常相似。Java 使用 `JFileChooser` 类来选择现有文件或允许用户输入新文件的名称。Cocoa 提供了 `NSSavePanel` 类用于输入新文件名，以及它的子类 `NSOpenPanel` 用于选择现有文件。代码清单 11-4 展示了用于提示用户从文件系统中选择单个数据文件或目录的等效代码。

### 代码清单 11-4. 选择单个文件

**Java**

```java
JFileChooser chooser = new JFileChooser();
chooser.setFileSelectionMode(JFileChooser.FILES_AND_DIRECTORIES);
chooser.setMultiSelectionEnabled(false);

int result = chooser.showOpenDialog(null);
if (result == JFileChooser.APPROVE_OPTION)
    return chooser.getSelectedFile().getAbsolutePath();
return null;
```

**Objective-C**

```objc
NSOpenPanel *panel = [NSOpenPanel openPanel];
[panel setCanChooseFiles:YES];
[panel setCanChooseDirectories:YES];
[panel setAllowsMultipleSelection:NO];

NSInteger result = [panel runModal];
if (result == NSFileHandlingPanelOKButton)
    return [panel filename];
return nil;
```

每个类都有许多属性，用于确定可选择项的类型。



