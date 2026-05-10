# 提供文档的数据

在 `UIDocument` 的子类中，你需要重写两个方法：`-contentsForType:error:` 和 `-loadFromContents:ofType:error:`。这两个方法负责将应用的数据模型对象转换成 `UIDocument` 可以保存的形式，并在后续将保存后的数据转换回应用所需的数据模型对象。

这也是实现 `UIDocument` 最有趣的地方。关键在于理解 `UIDocument` 为你做了什么，以及 `UIDocument` 对 `-contentsForType:error:` 和 `-loadFromContents:ofType:error:` 的期望。两者之间有着严格的责任划分：

- `UIDocument` 负责文档数据的实际存储和读取
- `-contentsForType:error:` 和 `-loadFromContents:ofType:error:` 负责在数据模型对象与序列化版本之间进行转换

`UIDocument` 可能会将你的文档存储在文件系统中。可能会存储在云端。也可能通过 USB 连接传输你的文档。未来某天它甚至可能将你的文档存储在钥匙扣上的无线电子钱包里。我不知道具体细节，你也不必关心。让 `UIDocument` 去操心文档数据的存储位置和方式吧。

当 `UIDocument` 想要保存你的文档时，它会发送 `-contentsForType:error:` 消息。你的实现应将数据模型对象转换为适合存储的数据。`UIDocument` 接收返回的数据，并将其存储在文件系统、云端或其他任何地方。

当需要读取文档时，`UIDocument` 会执行逆向过程。它首先重新获取数据（无论数据保存在何处），然后将数据传递给 `-loadFromContents:ofType:error:`，该方法的任务是将数据转换回应用的数据模型对象。

这里的关键问题是：“如何将数据模型对象转换成 `UIDocument` 可以存储的字节？” 这是一个极好的问题，答案可能简单得令人惊讶，也可能复杂得令人棘手。大致上，你有四种选择：

- 将所有内容序列化为单个 `NSData` 对象
- 使用文件包装器对象描述多部分文档
- 使用 Core Data 作为文档的后端
- 实现自己的存储方案

第一种方案最简单，适用于大多数文档类型。使用字符串编码、属性列表序列化或对象归档（你很快就会学到），将你的数据模型对象转换为单个字节数组。然后你的 `-contentsForType:error:` 方法将这些字节作为 `NSData` 对象返回，供 `UIDocument` 存储。之后，`UIDocument` 检索这些数据并将其传递给你的 `-loadFromContents:ofType:error:` 方法，该方法再将其解归档/反序列化/解码回原始对象。如果这符合你的应用需求，那么恭喜——你的 `UIDocument` 实现中这部分工作就基本完成了！

你的 `MyStuff` 应用则稍微复杂一些。将应用的所有数据（描述和图片）转换为单个字节流会非常繁琐。图片体积大，编码耗时。不仅保存文档需要很长时间，而且用户在打开应用前，整个文档都必须读入内存并转换回图片对象。没有人愿意等待几十秒，更不用说几分钟，来打开一个应用！

**注意**

这种情况不仅仅是让用户烦恼。如果在保存文档时尝试编码和压缩几十张图片，可能需要很长时间，以至于 iOS 会认为你的应用“死锁”并强制终止它。你的应用将会看似崩溃，而文档也永远无法保存。

`MyStuff` 将采用的解决方案是：将物品的描述（类似于第一种方案）归档到单个 `NSData` 对象中，但将图片存储在一个包内的单独文件中。包是一个包含多个文件的目录，对于用户来说，它看起来和操作起来就像一个单独的文件。例如，所有 iOS 和 OS X 应用都是包。

## 打包你的数据

你可能已经隐约看到了一个难题。或者，你可能没有注意到。如果你没发现，也不用担心，因为这是一个非常微妙的问题。`-contentsForType:error:` 背后的概念是返回代表文档的原始数据——仅仅是数据。`-contentsForType:error:` 中的代码无法知道这些数据如何存储，也不负责存储。设计一个声称“图片将存储在单个文件中”的方案是行不通的，因为 `-contentsForType:error:` 并不处理文件。文档最终可能存储在甚至不像文件的东西里。它可能被放入数据库的记录中，或者成为 XML 文件中的一个标签。

那么，`-contentsForType:error:` 如何返回一个对象，该对象描述的不是一个，而是一组独立的数据块，其中一个包含归档后的对象，其他则包含各个图片数据呢？恰好 iOS 为此提供了一个工具。它叫做文件包装器，这也引出了提供文档数据的第二种方法。

文件包装器（`NSFileWrapper`）对象是对存储在一个或多个文件中的数据的抽象。文件包装器有三种类型：常规文件包装器、目录文件包装器和链接文件包装器。从概念上讲，它们分别等同于单个数据文件、文件系统目录和文件系统符号链接。文件包装器允许你的应用描述一组命名数据块，这些数据块按命名目录的层次结构组织。如果这听起来像文件和文件夹，那就对了。当你的 `UIDocument` 存储在文件 URL 中时，这些文件包装器就会精确地变成那样。但通过保持这种抽象，`UIDocument` 可以同样轻松地通过网络传输这些数据集合，或将包装器转换为数据库的记录。

## 使用包装器

使用文件包装器并非特别困难。一个常规文件包装器表示一个字节数组，就像 `NSData` 一样。一个目录文件包装器（或简称为目录包装器）包含任意数量的其他文件包装器。包装器与文件/文件夹之间的一个显著区别是：包装器有一个首选名称和一个键。它的键是唯一标识该包装器的字符串，就像文件名唯一标识一个文件一样。它的首选名称是它希望被识别为的字符串。如果某个包装器的首选名称是唯一的，那么它的键和首选名称将会相同。然而，如果你向一个目录包装器中添加两个或更多具有相同首选名称的包装器，该目录包装器会为重复的包装器生成唯一的键。换句话说，向同一个目录包装器中添加多个同名的包装器是合法的。一个副作用是，添加一个与现有包装器同名的包装器不会像在文件系统中那样替换或覆盖现有包装器。

你的 `-contentsForType:error:` 方法将创建一个包含所有其他常规文件包装器的单个目录包装器。其中会有一个常规文件包装器包含你的数据模型对象的归档版本。每个包含图片的物品都会将其图片作为另一个文件包装器存储。你将修改 `MyWhatsit`，使其在用户添加图片时将图片存储在文档中，并在需要时从文档中获取图片。



### 增量式文档更新

将文档组织成封装器，可为应用带来一个显著特性：增量式文档加载与更新。如果用户在 MyStuff 应用中添加了 100 个项目，那么文档包（保存到文件系统时）将包含 101 个文件：一个归档文件和 100 个图片文件。如果用户将星盘图片换成一张更好的，那么只会更新单个文件封装器。`UIDocument` 了解这一点。当需要再次保存文档时，`UIDocument` 只会重新写入包中的那一个文件。这使得对大型文档的更新变得极其快速且高效。这些品质对你的应用十分有益。

同样地，文件封装器的数据直到被请求时才会被读取。当你打开一个由文件封装器构建的 `UIDocument` 时，每个单独封装器的数据都会保留在原位，直到你的应用需要它。对于你的图片而言，这意味着你的应用在启动时无需读取所有 100 个图片文件。它可以延迟加载，只检索当前需要的图片。这也意味着你的应用能够快速启动，并仅执行显示界面所需的最小工作量。

### 构建你的封装器

选择 `MSThingsDocument.m` 实现文件。在 `@implementation` 段之前，定义两个常量，并添加一个声明了两个实例变量的私有 `@interface` 段：

```
#define kThingsPreferredName    @"things.data"
#define kImagePreferredName     @"image.png"
```

```
@interface MSThingsDocument ()
{
    NSFileWrapper   *docWrapper;
    NSMutableArray  *things;
}
@end
```

这两个常量定义了已归档的 `MyWhatsit` 对象以及添加到目录封装器中的任何图片的首选封装器名称。`docWrapper` 实例变量是一个单一的目录封装器，它将包含所有其他封装器。从各方面来看，`docWrapper` 就是文档的数据。`things` 变量是构成数据模型的 `MyWhatsit` 对象数组。

**注意**

稍后，你将用新的 `MSThingsDocument` 替换 `MSMasterViewController` 中的 `things` 数组。文档对象将成为视图控制器的数据模型。

现在，将 `-contentsForType:error:` 方法添加到 `@implementation` 段中：

```
- (id)contentsForType:(NSString *)typeName
              error:(NSError *__autoreleasing *)outError
{
    if (docWrapper==nil)
        docWrapper = [[NSFileWrapper alloc] initDirectoryWithFileWrappers:nil];
    if (things==nil)
        things = [NSMutableArray array];
    NSFileWrapper *wrapper = docWrapper.fileWrappers[kThingsPreferredName];
    if (wrapper!=nil)
        [docWrapper removeFileWrapper:wrapper];
    NSData *thingsData = [NSKeyedArchiver archivedDataWithRootObject:things];
    [docWrapper addRegularFileWithContents:thingsData
                         preferredFilename:kThingsPreferredName];
    return docWrapper;
}
```

当 `UIDocument` 想要创建或保存文档时，会收到这条消息。如果文档尚未创建，`docWrapper` 和 `things` 将为 `nil`。在这种情况下，代码会创建一个空的目录封装器并将其存储在 `docWrapper` 中。它还会创建一个新的空数组 `things`，该数组将成为应用的数据模型。

该方法的其余部分将汇编 `UIDocument` 存储文档所需的所有数据。它检查 `docWrapper` 是否已经包含一个名为 `things.data` 的封装器。这个封装器包含了数据模型对象的归档版本。应该只有一个这样的封装器，所以如果它已经存在，则会首先从目录封装器中将其移除。请记住，添加另一个同名封装器并不会替换已有的封装器。

最后一步是将所有 `MyWhatsit` 对象归档（序列化）成一个可移植的 `NSData` 对象。我将在下一节解释其过程。该数据被传递给 `-addRegularFileWithContents:preferredFilename:` 方法。这是一个便利方法，用于创建一个新的常规文件封装器，其中包含 `thingsData` 中的字节，并以首选名称将其添加到目录封装器中。此方法让你无需显式编写这些步骤。

你将包含文档中所有数据的目录封装器返回给 `UIDocument`。现在你可能会问：“但是所有图片数据呢？它们在哪里被创建？” 这是一个非常好的问题。图片数据由同一目录封装器中的其他常规文件封装器表示。当文档首次创建时，没有图片，因此目录封装器只包含 `things.data`。随着用户向数据模型添加图片，每个图片都会向 `docWrapper` 添加一个新的封装器。当文档再次保存时，包含图片的文件封装器已经存在于 `docWrapper` 中了！每个常规文件封装器都知道自己是否被修改或更新过，而 `UIDocument` 足够智能，能够判断哪些文件需要写入，哪些文件已经是最新的。




### 解读你的封装器

打开文档时，会发生与前一过程相反的操作。`UIDocument` 获取保存在文档中的数据，然后发送 `-loadFromContents:ofType:error:` 消息。该方法的作用是将文档数据转换回你的数据模型。请将此方法紧接在 `-contentsForType:error:` 方法之后添加：

```
- (BOOL)loadFromContents:(id)contents
                  ofType:(NSString *)typeName
                   error:(NSError *__autoreleasing *)outError

{
    docWrapper = contents;
    NSFileWrapper *wrapper = docWrapper.fileWrappers[kThingsPreferredName];
    NSData *data = wrapper.regularFileContents;
    if (data!=nil)
        things = [NSKeyedUnarchiver unarchiveObjectWithData:data];
    return (things!=nil);
}
```

`contents` 参数是封装了文档数据的对象。它始终与你从 `-contentsForType:error:` 返回的对象属于同一（类）。如果你采用了第一种方法并返回了一个单一的 `NSData` 对象，那么 `contents` 参数将包含一个 `NSData` 对象——其中包含相同的数据。由于 `MyStuff` 选择了使用文件封装器技术，因此 `contents` 是一个与你之前返回的目录封装器对象等效的对象。

第一步是将 `contents` 保存在 `docWrapper` 中；你需要它来读取图像封装器以及后续保存文档。方法的其余部分会找到包含归档 `MyWhatsit` 对象数组的 `things.data` 封装器。它会立即检索该封装器中存储的数据并对其解档，从而重新创建数据模型对象。

`-loadFromContents:ofType:error:` 方法在成功时必须返回 `YES`，如果在解读文档数据时出现问题则返回 `NO`。如果封装器中包含一个 `things.data` 封装器，并且该封装器中的数据成功转换回 `MyWhatsit` 对象数组，则该方法假定文档有效并返回 `YES`。

至此，保存并随后打开新文档所需的工作基本完成。但有一个明显的漏洞：`MyWhatsit` 对象数组无法被归档！现在让我们来修复这个问题。

### 其他存储方案

可供你使用的最后两种文档存储方案是 Core Data 和 DIY（自行处理）。我很少觉得 DIY 有吸引力。它应该是你的最后选择，因为你会被迫处理所有任务，无论是常规的还是异常的，而这些任务通常都是由 `UIDocument` 为你处理的。我的建议是，尽最大努力让前三种方案中的一种生效。如果都不行，你可以自行处理文档存储。请查阅 `UIDocument` 文档中的"高级覆盖"部分。

最有趣的文档方案之一是 Core Data。iOS 包含一个快速高效的**关系型数据库引擎（SQLite）**，并提供了语言级别的支持。Core Data 远超本书的讨论范围，但如果你的应用数据更适合存储在数据库中而不是文本文件中，那么它是一个极其强大的工具。（很遗憾我没有足够的篇幅，因为 `MyStuff` 本可以成为一个完美的 Core Data 应用。）

使用 Core Data 的一大优势是，文档管理基本已为你完成。除了使用 `UIManagedDocument` 类（`UIDocument` 的子类）之外，你几乎不需要做太多事情。本章中许多需要你编写代码来支持的特性——增量文档更新、延迟文档加载、数据模型对象的归档与解档、后台文档加载与保存、云同步等——都由 `UIManagedDocument` 免费提供。

当然，前提是你的应用必须基于 Core Data。你的数据模型对象必须是 `NSManagedObjects`，你需为数据库设计模式，并且要理解面向对象数据库（OODB）技术的方方面面。但除此之外，这就变得轻而易举了。

### 归档对象

在第 18 章中，你已经了解了序列化的所有知识。序列化将属性列表对象的图转换为字节流（可以是 XML 或二进制格式），这些字节流可以存储在文件中、与其他进程交换、传输到其他计算机系统等。在接收端，这些字节被转换回等效的属性列表对象集，供后续使用。

归档是序列化的升级版。归档将遵循 `NSCoding` 协议的对象图进行序列化（计算机科学术语）。这比属性列表对象集的范围要大得多。² 更重要的是，你可以在自己开发的类中采用 `NSCoding` 协议。这样，你的自定义对象就可以与其他对象一起被归档。这正是你的 `MyWhatsit` 类需要实现的功能。



### 采用 `NSCoding`

归档对象图的第一步是确保每个对象都采用 `NSCoding` 协议。如果有对象不采用，你需要要么将其从对象图中移除，要么修改它使其采用。在 `MyWhatsit.h` 中，修改 `@interface` 声明，使其采用 `NSCoding`（新代码以粗体显示）：

`@interface MyWhatsit : NSObject <NSCoding>`

`NSCoding` 协议要求类实现两个方法：`-initWithCoder:` 和 `-encodeWithCoder:`。第一个“初始化”方法从先前归档的数据中重构对象。第二个方法从现有对象创建归档数据。这两个过程都通过 `NSCoder` 对象工作。`NSCoder` 对象负责序列化（编码）以及后续的反序列化（解码）对象的属性。

编码器使用键来标识对象的每个属性值。现在，通过在 `MyWhatsit.m` 文件中的 `@implementation` 部分之前添加以下声明来定义这些键：

```
#define kNameCoderKey       @"name"
#define kLocationCoderKey   @"location"
```

现在你可以将这两个必需的方法添加到 `@implementation` 部分：

```
- (id)initWithCoder:(NSCoder *)decoder
{
    self = [super init];
    if (self != nil)
    {
        _name =     [decoder decodeObjectForKey:kNameCoderKey];
        _location = [decoder decodeObjectForKey:kLocationCoderKey];
    }
    return self;
}
```

`-initWithCoder:` 方法遵循典型的“初始化”方法模式。但它不是用默认值或参数来初始化新对象的属性，而是从编码器对象中检索先前归档的值。在本例中，两个值都是 (`NSString`) 对象。编码器对象也可以直接编码整型、浮点型、布尔型以及其他 C 基本类型。UIKit 为 `NSCoder` 添加了类别来编码点、矩形、大小、仿射变换以及类似的数据结构。

```
- (void)encodeWithCoder:(NSCoder *)coder
{
    [coder encodeObject:_name       forKey:kNameCoderKey];
    [coder encodeObject:_location   forKey:kLocationCoderKey];
}
```

反向转换由你的 `-encodeWithCoder:` 方法提供。此方法将其持久属性的当前值保留在编码器对象中。你的 `MyWhatsit` 对象现在已准备好参与归档过程。

### 子类化一个 `<NSCoding>` 类

当你子类化一个已经采用 `NSCoding` 的类时，处理方式会略有不同。你的 `-initWithCoder:` 方法将如下所示：

```
- (id)initWithCoder:(NSCoder *)decoder
{
    self = [super initWithCoder:decoder];
    if (self != nil)
    {
        ... 在此执行子类解码 ...
    }
    return self;
}
```

而你的 `-encodeWithCoder:` 方法应如下所示：

```
- (void)encodeWithCoder:(NSCoder *)coder
{
    [super encodeWithCoder:coder];
    ... 在此执行子类编码 ...
}
```

你的父类已经编码和解码了它的属性。你的子类必须允许父类执行此操作，然后再编码和解码子类中定义的任何额外属性。

### 归档与解归档对象

当你想要将对象扁平化为字节时，使用如下代码：

```
NSData *data = [NSKeyedArchiver archivedDataWithRootObject:things];
```

`NSKeyedArchiver` 类是归档引擎。它创建一个 `NSCoder` 对象，然后向根对象（`things`）发送一条 `-encodeWithCoder:` 消息。该对象负责将其内容保留在编码器对象中。很可能，它会针对其引用的对象向编码器对象发送 `-encodeObject:forKey:` 消息。这些对象会收到一条 `-encodeWithCoder:` 消息，这个过程会重复进行，直到所有对象都被编码。唯一的限制是每个对象都必须采用 `NSCoding`。

当你想要将对象恢复时，使用 `NSKeyedUnarchiver` 类，如下所示：

```
things = [NSKeyedUnarchiver unarchiveObjectWithData:data];
```

在编码过程中，编码器记录了每个对象的类。解码器随后利用这些信息创建新对象，并向每个对象发送一条 `-initWithCoder:` 消息。生成的对象与原始编码的对象具有相同的类和相同的属性值。

注意
键归档的前身是顺序归档。你可能会偶尔看到对顺序归档的引用，但它在 iOS 中已不再使用。

### 归档序列化对决

既然你已经掌握了序列化（属性列表）和归档（`NSCoding` 对象）这两种方法，我想花点时间对两者进行比较和对比。表 19-1 总结了它们的主要特性。

**表 19-1.** 序列化 vs. 归档

| 特性 | 序列化 | 归档 |
| --- | --- | --- |
| 对象图 | 仅限属性列表对象 | 采用 `NSCoding` 的对象 |
| XML 支持？ | 是 | 否 |
| 可移植性 | Cocoa 或 Cocoa Touch 应用，或任何可解析 XML 版本的系统 | 仅限包含所有原始类的其他进程 |
| 编辑器支持？ | 是 | 否 |

属性列表在可存储内容方面受到更多限制，但在存储、共享和编辑方式上弥补了这一点。当你的值需要被其他进程理解时，尤其是那些不包含你自定义类的进程时，请使用属性列表。一个例子是你在第 18 章中创建的设置捆绑包。“设置”应用永远不会包含你的任何自定义 Objective‑C 类，但你却能够使用属性列表定义、交换这些设置并将它们整合到你的应用中。属性列表是值的“通用”语言。

相比之下，归档可以编码大量的类，并且你可以通过采用 `NSCoding` 协议将你自己的类添加到这个列表中来。你在 Interface Builder 中创建的所有内容都使用键归档进行编码。当你在应用中加载 Interface Builder 文件时，`NSKeyedUnarchiver` 正忙于将该文件转换回你定义的对象。归档极其灵活，且影响深远，这就是它成为在文档中存储数据模型对象首选技术的原因。

那么，为什么我们不把所有事都用归档来做呢？解归档时，归档中记录的每个类都必须存在。所以，别想着用其他不包含你的 `MyWhatsit` 代码的应用或程序来读取你的 MyStuff 文档——这是行不通的。归档在很大程度上是不透明的。没有像属性列表那样的通用编辑器来处理归档。也没有将归档数据转换为 XML 文档的工具。Interface Builder 是最接近归档编辑器的东西，但它是一个单向过程；你可以编辑你的界面并将其编译成归档，但你无法打开已编译的归档文件并编辑它。



### 序列化与归档的相遇

既然你已经对归档和序列化的优缺点有了直观感受，接下来我要向你展示一个将两者结合起来的非常实用的小技巧。（你可能已经想到了这一点，但至少可以假装很惊讶。）`NSData` 是一个属性列表对象。对一个包含 `NSCoding` 对象的对象图进行归档后，得到的结果是一个 `NSData` 对象。你明白我要说什么了吗？

通过先将你的对象归档成一个 `NSData` 对象，你就可以将非属性列表对象存储在属性列表（比如用户默认设置）中了！你的代码看起来会是这样：

```
NSUserDefaults *userDefaults = [NSUserDefaults standardUserDefaults]
NSData *data = [NSKeyedArchiver archivedDataWithRootObject:dataModel];
[userDefaults setObject:data forKey:@"data_model"];
```

你所做的就是将你的数据模型对象归档成一个 `NSData` 对象，而这个对象可以存储在属性列表中。要再次取回它们，只需逆转这个过程：

```
NSData *modelData = [userDefaults objectForKey:@"data_model"];
dataModel = [NSKeyedUnarchiver unarchiveObjectWithData:modelData];
```

这种技术的缺点与归档本身普遍存在的缺点相同。检索对象的过程必须能够解归档它们。此外，任何检查你属性值的编辑器或其他程序都只会看到一团数据。这与你之前在 Pigeon 中使用的将 `MKAnnotation` 对象转换为字典的技术形成了对比。那些属性列表值（地点的名称、经度和纬度）很容易被解释，甚至可以被其他程序编辑。

#### 注意事项

不要过度使用这项技术。像 `NSUserDefaults` 和 `NSUbiquitousKeyValueStore` 这样的服务是为存储少量信息而设计的。不要滥用它们来存储你使用归档器创建的数兆字节大小的 `NSData` 对象。

关于归档和属性列表，我认为说得已经够多了。是时候回到让 MyStuff 实现文档化的正题上来了。

## 文档化你的数据模型

我们讲到哪了？哦，对了，你创建了一个 `UIDocument` 类，并编写了所有必要的代码，将你的数据模型对象转换为文档数据，再转换回来。下一步是让你的 `MSThingsDocument` 对象成为 `MSMasterViewController` 的数据模型。

#### 注意

在一个“大型”应用中，你可能会创建一个独立于 `UIDocument` 类的自定义数据模型类。用 MVC 术语来说，你会有一个数据模型和一个数据模型控制器（即文档对象）。文档对象和视图控制器都会连接到数据模型对象。对于 MyStuff 这个应用，我让你将数据模型和文档合并到一个类中。这简化了设计，并减少了你需要编写的代码量。这不会损害设计，但你应该清楚你的 MVC 边界在哪里。

你当前的 `MSMasterViewController` 使用一个 `NSArray` 作为其数据模型对象。这个数组对象提供了视图控制器正在使用的多个方法。这些方法包括计算数组中对象的数量，以及在数组中添加、移除和定位对象。`UIDocument` 没有这些方法——因为它不是一个数据模型。通过复制视图控制器所需的功能，将其转变为数据模型。选择 `MSThingsDocument.h`，并将这些方法添加到它的 `@interface` 部分（新代码以粗体显示）：

```
@class MyWhatsit;

@interface MSThingsDocument : UIDocument

+ (NSURL*)documentURL;
+ (MSThingsDocument*)documentAtURL:(NSURL*)url;

@property (readonly) NSUInteger whatsitCount;
- (MyWhatsit*)whatsitAtIndex:(NSUInteger)index;
- (NSUInteger)indexOfWhatsit:(MyWhatsit*)object;
- (void)removeWhatsitAtIndex:(NSUInteger)index;
- (MyWhatsit*)anotherWhatsit;

@end
```

切换到 `MSThingsDocument.m` 实现文件。添加另一个 `#import` 指令以获取 `MyWhatsit` 类的定义：

```
#import "MyWhatsit.h"
```

现在将数据模型方法的代码添加到 `@implementation` 部分：

```
- (NSUInteger)whatsitCount
{
    return things.count;
}

- (MyWhatsit*)whatsitAtIndex:(NSUInteger)index
{
    return things[index];
}

- (NSUInteger)indexOfWhatsit:(MyWhatsit*)object
{
    return [things indexOfObject:object];
}

- (void)removeWhatsitAtIndex:(NSUInteger)index
{
    [things removeObjectAtIndex:index];
}

- (MyWhatsit*)anotherWhatsit
{
    MyWhatsit *newItem = [MyWhatsit new];
    newItem.name = [NSString stringWithFormat:@"My Item %u",self.whatsitCount+1];
    [things addObject:newItem];
    return newItem;
}
```

这些方法的目的应该显而易见。视图控制器现在将向你的文档对象发送这些消息，以计算项目数量、获取特定索引处的项目、查找现有项目的索引、移除项目或创建新项目。下一步是在视图控制器中实施这些更改。选择你的 `MSMasterViewController.h` 文件并添加这个 `#import` 语句：

```
#import "MSThingsDocument.h"
```

在项目导航器中选择你的 `MSMasterViewController.m` 文件。找到私有的 `@interface` 部分，并用你的文档对象替换旧的 `things` 数组（新代码以粗体显示）：

```
@interface MSMasterViewController () {
    MSThingsDocument *document;
}
```

你的文档对象现在就是你的数据模型了。现在你需要遍历你的视图控制器代码，并将对所有旧 `things` 数组的引用替换为对文档对象的等效代码。

#### 提示

现在你的文件里充满了编译器错误。是不是很棒？我一直使用这个技巧。当我需要重新定义或重新调整属性值的用途时，我会故意更改属性或变量的名称——哪怕只是暂时的。编译器会立即将所有对旧名称的引用标记为错误。这便成了我需要做出修改的路线图。如果我喜欢原来的属性名，一旦所有功能运行正常，我会使用重构工具将其重新改回来。



你还将删除为测试而创建虚假项目的代码，并替换为从文档加载数据模型的代码。我们从 `-awakeFromNib` 方法开始。删除用于用项目填充 `things` 数组的代码。你不再需要这些代码了，因为 `MyStuff` 会根据文档内容填充数据模型。找到 `-viewDidLoad` 方法，并在该方法末尾添加以下语句：

```
document = [MSThingsDocument documentAtURL:[MSThingsDocument documentURL]];
```

你应该还记得本章开头介绍过这些方法。该语句请求创建一个新的 `MSThingsDocument` 对象，其内容来自 `[MSThingsDocument documentURL]` 指向的文档，该文档位于你应用的 `Documents` 文件夹中。就这样。你的文档对象通过一条语句创建并加载完成。

剩余的工作主要是将使用 `things` 的代码替换为使用 `document` 的代码。找到 `-insertNewObject:` 方法，将其修改如下（修改部分以粗体显示）：

```
- (void)insertNewObject:(id)sender
{
    MyWhatsit *newItem = [document anotherWhatsit];
    NSUInteger newIndex = [document indexOfWhatsit:newItem];
    NSIndexPath *indexPath = [NSIndexPath indexPathForRow: newIndex
                                            inSection:0];
    [self.tableView insertRowsAtIndexPaths:@[indexPath]
                          withRowAnimation:UITableViewRowAnimationAutomatic];
}
```

这是最大的改动。`document` 对象现在负责创建新的 `MyWhatsit` 对象——当你处理 `MyWhatsit` 图像的代码时，就会明白原因。代码还做了修改，不再假设新项目插入到数组开头，而是询问 `document` 将新项目插入到了数组的哪个位置。这是一个明智的改动，因为 `-anotherWhatsit` 方法实际上是将新项目插入到数组末尾。即使你将来再次修改此行为，这段代码仍然能正常工作。

其余的改动非常基础，我在下方做了总结。（提示：顺着编译器错误的提示，将涉及 `things` 的语句替换为等价的 `document` 语句。）

*   在 `-tableView:numberOfRowsInSection:` 中
    *   `things.count` 改为 `document.whatsitCount`
*   在 `-tableView:cellForRowAtIndexPath:`、`-tableView:didSelectRowAtIndexPath:` 和 `–prepareForSeque:` 中
    *   `things[indexPath.row]` 改为 `[document whatsitAtIndex:indexPath.row]`
*   在 `-tableView:commitEditingStyle:forRowAtIndexPath:` 中
    *   `[things removeObjectAtIndex:indexPath.row]` 改为 `[document removeWhatsitAtIndex:indexPath.row]`
*   在 `-whatsitDidChangeNotification:` 中
    *   `[things indexOfObject:notification.object]` 改为 `[document indexOfWhatsit:notification.object]`

现在，你的 `MSThingsDocument` 对象就是应用的数据模型了。这是重要的一步。重要的不是你将文档和数据模型合并为单一对象，而是你将所有对数据模型的改动——计数、获取、删除和创建项目——都封装在了自己的方法背后，而不是简单地使用 `NSArray` 方法。你很快就会明白原因。

你可能会觉得已经编写了足够的代码，应用应该能够将它的 `MyWhatsit` 对象（至少是名称和位置信息）存储到文档中，然后再读取回来。但还有一些小的部分尚未完成。

## 跟踪变更

有一项你还没写，那就是保存文档的代码。你已经编写了将数据模型对象转换为可保存形式的代码，但你从未要求 `UIDocument` 对象去使用它。

你也不会这么做。

至少，这不是理想的技术。`UIDocument` 采用自动保存文档模型，即在用户工作时定期将文档保存到持久化存储中，并在应用退出前再次自动保存。这是 iOS 应用首选的文档保存模型。

要实现自动保存，你的代码必须通知文档已发生了更改。然后 `UIDocument` 会在后台调度并执行新数据的保存。有两种方式可以向文档传达变更：发送 `-updateChangeCount:` 消息，或者使用文档的 `NSUndoManager` 对象。当你向 `NSUndoManager` 注册变更时，它会自动通知其文档对象这些变更。

**注意**  
不使用撤销管理器（undo manager）和自动保存的替代方案是，通过向文档发送 `-saveToURL:forSaveOperation:completionHandler:` 消息（或相关方法之一）来显式保存文档。这意味着界面更像传统的桌面应用，由用户有意地保存文档。

对于这个应用，我们不打算使用 `NSUndoManager`——尽管这是一个值得考虑的优秀特性，而且使用起来并不困难。因此，每当发生变更时，你需要向文档对象发送 `-updateChangeCount:` 消息。剩下的工作就交给 `UIDocument` 处理。

那么，数据模型何时发生变化呢？一个显而易见的地方是添加或删除项目时。选择 `MSThingsDocument.m` 实现文件。找到 `-removeWhatistAtIndex:` 和 `-anotherWhatsit` 方法。在 `-removeWhatistAtIndex:` 的末尾，以及 `-anotherWhatsit` 中的 return 语句之前，添加以下语句：

```
[self updateChangeCount:UIDocumentChangeDone];
```

这条消息告诉文档对象其内容已更改。还有其他类型的变更（例如由撤销或重做操作导致的变更），但除非你创建了自己的撤销管理器，否则这是你需要发送的唯一常量。

文档发生变更的另一个地方是用户编辑单个项目时。你早在第 4 章中就解决了这个问题！每当 `MyWhatsit` 对象被编辑时，你的对象会发布一条 `MyWhatsitDidChange` 通知。你的文档只需要观察这条通知即可。

回到 `MSThingsDocument.m` 的顶部，在私有 `@interface MSThingsDocument ()` 部分声明一个新方法：

```
- (void)whatsitDidChange:(NSNotification*)notification;
```

在你的 `+documentAtURL:` 方法中，在 return 语句之前立即添加以下代码来观察此通知：

```
NSNotificationCenter *center = [NSNotificationCenter defaultCenter];
[center addObserver:document
           selector:@selector(whatsitDidChange:)
               name:kWhatsitDidChangeNotification
             object:nil];
```

对于像 `UIDocument` 这样可能比应用退出更早销毁的对象，请记住在对象销毁前将其从通知中心移除。为此，需要为类添加一个 `-dealloc` 方法：

```
- (void)dealloc
{
    [[NSNotificationCenter defaultCenter] removeObserver:self];
}
```

最后，添加新的通知处理方法：

```
- (void)whatsitDidChange:(NSNotification *)notification
{
    if ([self indexOfWhatsit:notification.object]!=NSNotFound)
        [self updateChangeCount:UIDocumentChangeDone];
}
```

它的唯一目的是通知文档，该文档中的 `MyWhatsit` 对象已发生更改——这就是它所做的一切。



