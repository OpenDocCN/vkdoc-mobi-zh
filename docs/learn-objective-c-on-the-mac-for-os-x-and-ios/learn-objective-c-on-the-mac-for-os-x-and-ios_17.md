# 第 17 章

## 文件加载与保存

许多计算机程序最终都会创建某种用户工作的半永久性产物。可能是编辑过的照片，可能是小说的一个章节，也可能是你们乐队翻唱的《Free Bird》。在这些情况下，用户最终都会得到一个保存的文件。

标准 C 库提供了创建、读取和写入文件的函数调用，例如 `open()`、`read()`、`write()`、`fopen()` 和 `fread()`。这些函数在其他地方已有详细文档，因此我们不再赘述。Cocoa 提供了 Core Data，它能在后台为你处理所有这些文件相关操作，我们也不会讨论这个。

那么，我们还能讨论什么呢？Cocoa 提供了两类通用的文件处理方式：**属性列表（Property Lists）**和**对象编码（Object Encoding）**，我们将在这里进行介绍。

### 属性列表

在 Cocoa 中，有一类对象被称为**属性列表（property list）**对象，通常缩写为 **plist**。这些列表包含一组 Cocoa 已知如何操作的小型对象，特别是如何将它们保存到文件以及从文件中加载回来。属性列表的类包括 `NSArray`、`NSDictionary`、`NSString`、`NSNumber`、`NSDate` 和 `NSData`，以及它们的可变版本（如果有的话）。

前四个类你已经见过，但后两个尚未涉及。在开始进行文件操作之前，我们将先聊聊这些类。本章这一部分的全部代码可以在项目 *15.01 PropertyListing* 中找到。

### NSDate

时间和日期处理在程序中非常常见。`iPhoto` 知道你给狗拍照的日期，你的个人记账应用知道对账时的结账日期。`NSDate` 是 Cocoa 日期和时间处理中的基础类。

要获取当前日期和时间，请使用 `[NSDate date]`，它会返回一个自动释放的对象。因此，以下代码：

```
NSDate *date = [NSDate date];
NSLog (@"today is %@", date);
```

将打印出类似这样的内容：

```
today is 2012-01-23 11:32:02 -0400
```

你可以使用一些方法比较两个日期，从而对列表进行排序。还有一些方法可以获取相对于当前时间的日期差值。例如，你可能想获取恰好 24 小时前的日期：

```
NSDate *yesterday = [NSDate dateWithTimeIntervalSinceNow: -(24 * 60 * 60)];
NSLog (@"yesterday is %@", yesterday);
```

上述代码打印出：

```
yesterday is 2012-01-23 11:32:02 -0400
```

`+dateWithTimeIntervalSinceNow:` 方法接受一个 `NSTimeInterval` 类型的参数，它是 `double` 类型的 `typedef`，表示以秒为单位的时间间隔。这允许你通过正的时间间隔指定未来的时间偏移，或像此处这样，通过负的时间间隔指定过去的时间偏移。

**注意**：如果你希望格式化日期的打印方式，Apple 提供了一个名为 `NSDateFormatter` 的类，它符合 Unicode 技术标准 #35。这为你提供了各种为用户格式化日期的方法。

### NSData

在 C 语言中，一个常见的惯用做法是将数据缓冲区传递给函数。为此，你通常需要向函数传递缓冲区的指针和缓冲区的长度。此外，在 C 中可能会出现内存管理问题。例如，如果缓冲区是动态分配的，那么当它不再有用时，谁负责清理它？

Cocoa 为我们提供了 `NSData` 类，它封装了一段字节数据。你可以获取数据的长度以及指向字节起始位置的指针。由于 `NSData` 是一个对象，常规的内存管理行为同样适用。因此，如果你需要将一段数据传递给某个函数或方法，你可以传递一个自动释放的 `NSData` 对象，而无需担心清理问题。下面是一个包含普通 C 字符串（本质上就是一个字节序列）的 `NSData` 对象，并打印出其数据：

```
const char *string = "Hi there, this is a C string!";
NSData *data = [NSData dataWithBytes: length: strlen(string) + 1];
NSLog (@"data is %@", data);
```

结果如下：

```
data is <48692074 68657265 2c207468 69732069 73206120 43207374 72696e67 2100>
```

嗯，这看起来有点特别。但是，如果你手边有一张 ASCII 表（你可以通过终端输入命令 `man ascii` 来查找），你会发现这串十六进制数实际上就是我们的字符串。`0x48` 是“H”，`0x69` 是“i”，依此类推。`-length` 方法返回字节数，而 `-bytes` 方法返回指向字符串开头的指针。注意到在 `+dataWithBytes:` 调用中使用了 `+ 1` 吗？这是为了包含 C 字符串所需的结尾零字节。另外，请注意 `NSLog` 输出结果末尾的 `00`。通过包含这个零字节，我们可以使用 `%s` 格式说明符来打印字符串：

```
NSLog (@"%d byte string is '%s'", [data length], [data bytes]);
```

这将产生以下输出：

```
30 byte string is 'Hi there, this is a C string!'
```

`NSData` 对象是不可变的。一旦创建，就确定下来。你可以使用它，但无法更改它。不过，`NSMutableData` 允许你向数据内容中添加或移除字节。

### 写入和读取属性列表

现在你已经了解了所有的属性列表类，我们可以用它们做什么呢？集合属性列表类（`NSArray` 和 `NSDictionary`）有一个名为 `-writeToFile: atomically:` 的方法，用于将属性列表写入文件。`NSString` 和 `NSData` 也有一个 `writeToFile:atomically:` 方法，但它只是写出字符串或数据块。

因此，我们可以将一个包含字符串的数组加载起来，然后保存它：

```
NSArray *phrase;
phrase = [NSArray arrayWithObjects: @"I", @"seem", @"to", @"be", @"a", @"verb", nil];
[phrase writeToFile: @"/tmp/verbiage.txt" atomically: YES];
```

现在，查看文件 `/tmp/verbiage.txt`，你应该会看到类似这样的内容：

```
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN"
"http://www.apple.com/DTDs/PropertyList-1.0.dtd"> <plist version="1.0"> <array>
<string>I</string>
<string>seem</string>
<string>to</string>
<string>be</string>
<string>a</string>
<string>verb</string> </array> </plist>
```



这段代码虽然略显冗长，但正是我们想要存储的内容：一个字符串数组。这些属性列表文件可以极其复杂，其中包含字典数组，而字典里又可能嵌套着字符串、数字和日期等数组。Xcode 也提供了一个属性列表编辑器，因此你可以浏览并修改 plist 文件。如果你查看操作系统内部，你会发现大量属性列表文件，例如你个人目录下 `Library/Preferences` 中的所有偏好设置文件，以及 `/System/Library/LaunchDaemons` 等系统配置文件。

**注意：** 某些属性列表文件（尤其是偏好设置文件）会以压缩的二进制格式存储。你可以使用 `plutil` 命令将这些文件转换为人类可读的格式：
`plutil -convert xml1 filename.plist`

现在，我们的 `verbiage.txt` 文件已经存储在磁盘上，我们可以使用 `+arrayWithContentsOfFile:` 方法将其读入，代码如下：

```
NSArray *phrase2 = [NSArray arrayWithContentsOfFile: @"/tmp/verbiage.txt"];
NSLog (@"%@", phrase2);
```

而我们的输出结果与之前保存的内容完全一致：

```
(
I,
seem,
to,
be,
a,
verb
)
```

**注意：** 你是否注意到 `writeToFile:` 方法中的 `atomically`（原子性）参数？这个调用是否具有放射性？并没有。`atomically:` 参数接受一个 `BOOL` 值，它告诉 Cocoa 是否先将文件内容保存到一个临时文件中，然后在保存成功后再用这个临时文件替换原文件。这个参数是一种安全机制——如果保存过程中出了问题，你不会破坏原始文件。但这种安全机制也有代价：在保存期间，由于原始文件依然存在，你会消耗双倍的磁盘空间。除非你要保存可能填满用户硬盘的巨大文件，否则建议使用原子性方式保存文件。

如果你能将数据简化为属性列表类型，就可以使用这些非常便捷的方法将数据保存到磁盘，并在以后读取回来。当你在尝试新想法或启动新项目时，可以利用这些便捷功能快速启动和运行程序。即使你只是想将一块数据保存到磁盘而不使用任何对象，也可以借助 `NSData` 来简化工作。只需将数据包装在 `NSData` 对象中，然后对该 `NSData` 对象调用 `writeToFile:atomically:` 方法即可。

这些函数的一个缺点是它们不返回任何错误信息。如果无法加载文件，你只会从方法中收到一个 `nil` 指针，而完全不知道问题出在哪里。

## 修改对象

请注意，当你使用集合类型从文件中读取数据时，无法修改这些数据。一种修改方法是使用蛮力，遍历 plist 并创建一个可变的并行结构。但你无需如此极端：还有另一种方法。事实上，如果在 Cocoa 中做某件事显得过于复杂，苹果通常已经慷慨地提供了更简单的解决方案。

有一个名为 `NSPropertyListSerialization` 的类（这个名字读起来有点拗口）。顾名思义，它可以根据你想要的选项来保存和加载属性列表。

我们主要关注其 `propertyListFromData:mutabilityOption:format:errorDescription:` 方法。该方法会返回你的 plist 数据，并且在出现问题时，你还会得到一个错误信息。

以下是用于以二进制格式写入 plist 数据的代码：

```
NSString *error = nil;
NSData *encodedArray = [NSPropertyListSerialization dataFromPropertyList:capitols
format:NSPropertyListBinaryFormat_v1_0 errorDescription:&error];
[encodedArray writeToFile:@"/tmp/capitols.txt" atomically:YES];
```

如你所见，我们将数组数据转换成了 `NSData` 并写入文件。

将数据读回内存时，需要多执行一步，即指定文件格式。我们创建一个指针，这样如果格式与我们指定的不同，就可以利用指针使用原始格式，或将其转换为新格式。

```
NSPropertyListFormat propertyListFormat = NSPropertyListXMLFormat_v1_0;
NSString *error = nil;
```


```objc
NSMutableArray *capitols = [NSPropertyListSerialization propertyListFromData:data
                                                 mutabilityOption:NSPropertyListMutableContainersAndLeaves
                                                 format:&propertyListFormat
                                                 errorDescription:&error];
```

其中一个选项决定了我们如何读取数据：我们是否希望能够修改 plist？此外，我们是仅想修改列表的结构，还是连数据本身也要修改？

## 编码对象

遗憾的是，你并不总能将对象的信息表示为属性列表类。如果我们能将所有内容都表达为数组的字典，那就无需自定义类了。幸运的是，Cocoa 提供了机制，让对象能将自身转换为可保存到磁盘的格式。对象可以将其实例变量和其他数据编码为一块数据，然后保存到磁盘。之后，这块数据可以被读回内存，并基于保存的数据创建新的对象。这个过程称为**编码与解码**，或者**序列化与反序列化**。

还记得上一章我们初次尝试 Interface Builder 时，从库中拖拽对象到窗口，然后这些内容就被保存到了 nib 文件中。换句话说，`NSWindow` 和 `NSTextField` 对象被序列化并保存到了磁盘。当程序运行时，nib 文件被加载到内存，对象被反序列化，新的 `NSWindow` 和 `NSTextField` 对象被创建并连接起来。

你大概能猜到，通过采用 `NSCoding` 协议，你也可以对自己的对象做同样的事情。该协议定义如下：

```objc
@protocol NSCoding

- (void) encodeWithCoder: (NSCoder *) encoder;

- (id) initWithCoder: (NSCoder *) decoder;

@end
```

采用此协议后，你需要实现这两个方法。当要求对象保存自身时，`-encodeWithCoder:` 会被调用；当要求对象加载自身时，`-initWithCoder:` 会被调用。

那么这个编码器是什么呢？`NSCoder` 是一个抽象类，定义了一系列有用的方法，用于将你的对象转换为 `NSData` 以及从 `NSData` 转换回来。你永远不会直接创建 `NSCoder` 实例，因为它本身并不做实质性的工作。但 `NSCoder` 有几个具体的子类，你可以用它们来编码和解码对象。我们将用到其中两个：`NSKeyedArchiver` 和 `NSKeyedUnarchiver`。

理解它们最简便的方法也许就是通过一个示例。你可以在项目 *15.02 SimpleEncoding* 中找到所有代码。

我们先从一个包含若干实例变量的简单类开始：

```objc
@interface Thingie : NSObject <NSCoding>
{
    NSString *name;
    int magicNumber;
    float shoeSize;
    NSMutableArray *subThingies;
}

@property (copy) NSString *name;
@property int magicNumber;
@property float shoeSize;
@property (retain) NSMutableArray *subThingies;

- (id)initWithName: (NSString *) n magicNumber: (int) mn shoeSize: (float) ss;

@end // Thingie
```

这应该相当熟悉了。我们有四个实例变量，包含对象类型和标量类型，其中还有一个集合。属性默认是 readwrite 的，因此我们在属性定义中未作特别说明。每个变量都有公开的属性，以及一个便捷的一站式初始化方法，用于从头创建一个新的 `Thingie`。

注意 `Thingie` 采用了 `NSCoding` 协议。这意味着我们需要为 `encodeWithCoder:` 和 `initWithCoder:` 方法提供实现。目前，我们先让这两个方法为空。

```objc
@implementation Thingie

@synthesize name;
@synthesize magicNumber;
@synthesize shoeSize;
@synthesize subThingies;

- (id)initWithName: (NSString *) n magicNumber: (int) mn shoeSize: (float) ss
{
    if (self = [super init])
    {
        self.name = n;
        self.magicNumber = mn;
        self.shoeSize = ss;
        self.subThingies = [NSMutableArray array];
    }
    return (self);
}

- (void) dealloc
{
    [name release];
    [subThingies release];
    [super dealloc];
} // dealloc

- (void) encodeWithCoder: (NSCoder *) coder
{
    // 空方法
} // encodeWithCoder

- (id) initWithCoder: (NSCoder *) decoder
{
    return (nil);
} // initWithCoder

- (NSString *) description
{
    NSString *description =
        [NSString stringWithFormat: @"%@: %d/%.1f %@", name,
```

（注意：原文在此处突然结束。末尾的代码块不完整。）


```objc
magicNumber, shoeSize, subThingies];

return (description);

} // description

@end // Thingie
```

这段代码将初始化一个新对象，清理我们造成的任何混乱，创建存根方法使编译器满意我们采用`NSCoding`，并返回一个描述信息。

请注意，在`init`方法中，我们在赋值语句的左侧使用了`self.attribute`。记住，这实际上意味着我们在调用这些属性的访问器方法，而这些方法是由`@synthesize`创建的。我们并没有直接进行实例变量赋值。这种对象创建技术将使我们能够正确管理传入的`NSString`和我们创建的`NSMutableArray`的内存，因此我们不必显式地提供内存管理。

所以，在`main()`内部，创建一个`Thingie`对象并打印它：

```objc
Thingie *thing1;

thing1 = [[Thingie alloc] initWithName: @"thing1" magicNumber: 42 shoeSize: 10.5];

NSLog (@"some thing: %@", thing1);
```

前面的代码将打印出如下内容：

```
some thing: thing1: 42/10.5 ( )
```

这很有趣。现在，让我们对这个对象进行归档。像这样实现`Thingie`的`encodeWithCoder:`方法：

```objc
- (void) encodeWithCoder: (NSCoder *) coder

{

[coder encodeObject: name forKey: @"name"];

[coder encodeInt: magicNumber forKey: @"magicNumber"];

[coder encodeFloat: shoeSize forKey: @"shoeSize"];

[coder encodeObject: subThingies forKey: @"subThingies"];

} // encodeWithCoder
```

我们将使用`NSKeyedArchiver`来完成将对象归档到`NSData`中的所有工作。键控归档器，顾名思义，使用键/值对来保存对象的信息。`Thingie`的`-encodeWithCoder`方法将每个实例变量编码到与实例变量名称匹配的键下。你不必这样做。你可以将名称编码到键`flarblewhazzit`下，没有人会在意。保持键名与实例变量名相似，可以很容易地知道什么映射到什么。

你可以随意使用像这样的裸字符串作为编码键，或者你可以定义一个常量来防止拼写错误。你可以像这样`#define kSubthingiesKey @"subThingies"`，或者在文件中设置一个局部变量，例如`static NSString *kSubthingiesKey = @"subThingies";`。

请注意，每种类型都有不同的`encodeSomething:forKey:`方法。你需要确保使用正确的方法来编码你的类型。对于任何 Objective-C 对象类型，你使用`encodeObject:forKey:`。

当你恢复一个对象时，你将使用`decodeSomethingForKey:`方法：

```objc
- (id) initWithCoder: (NSCoder *) decoder

{

if (self = [super init]) {

self.name = [decoder decodeObjectForKey: @"name"];

self.magicNumber = [decoder decodeIntForKey: @"magicNumber"];

self.shoeSize = [decoder decodeFloatForKey: @"shoeSize"];

self.subThingies = [decoder decodeObjectForKey: @"subThingies"];

}

return (self);

} // initWithCoder
```

`initWithCoder:` 就像其他任何`init`方法一样。你需要让父类先初始化，然后才能处理你自己的部分。有两种方法可以做到这一点，具体取决于你的父类是什么。如果你的父类采用了`NSCoding`，你应该调用`[super initWithCoder: decoder]`。如果你的父类没有采用`NSCoding`，你只需调用`[super init]`。`NSObject`没有采用`NSCoding`，所以我们使用简单的`init`。

当你使用`decodeIntForKey:`时，你从解码器中取出一个`int`值。当你使用`decodeObjectForKey:`时，你从解码器中取出一个对象，并递归地对任何嵌入的对象使用`initWithCoder:`。内存管理的工作方式如你所预期：你从一个不是`alloc`、`copy`或`new`的方法中获取对象，因此你可以假定这些对象是自动释放的。我们的属性声明确保了所有内存管理都得到正确处理。

你会注意到，我们的编码和解码顺序与实例变量的顺序相同。你不必这样做；这只是确保你编码和解码了所有内容、没有遗漏什么的便捷习惯。这也是在调用中使用键的原因之一——你可以按任意顺序放入和取出它们。



现在，让我们实际使用这些内容。我们之前创建了 `thing1`，现在把它归档：

```
NSData *freezeDried;

freezeDried = [NSKeyedArchiver archivedDataWithRootObject: thing1];
```

`+archivedDataWithRootObject:` 这个类方法会对该对象进行编码。首先，它在底层创建一个 `NSKeyedArchiver` 实例；然后，将这个实例传递给对象 `thing1` 的 `-encodeWithCoder:` 方法。当 `thing1` 编码其属性时，它会导致其他对象也被编码，比如字符串、数组，以及我们可能放入该数组的任何内容。一旦整堆对象完成了键和值的编码，键控归档器就会将所有内容扁平化为一个 `NSData` 并返回。

如果需要，我们可以使用 `-writeToFile:atomically:` 方法将这个 `NSData` 保存到磁盘。这里，我们只是要释放 `thing1`，从冻结干燥的表示形式中重新创建它，并打印出来：

```
[thing1 release];

thing1 = [NSKeyedUnarchiver unarchiveObjectWithData: freezeDried];

NSLog (@"reconstituted thing: %@", thing1);
```

它打印出和之前完全一样的内容：

```
reconstituted thing: thing1: 42/10.5 ( )
```

在契诃夫戏剧的第一幕中看到墙上挂着一把枪，会让你心生好奇；同样，你可能也在好奇那个叫做 `subThingies` 的可变数组。我们可以把对象放入数组，当数组被编码时，这些对象也会自动编码。`NSArray` 对 `encodeWithCoder:` 的实现会调用所有对象的 `encodeWithCoder:`，最终导致所有内容都被编码。让我们给 `thing1` 添加一些 `subThingies`：

```
Thingie *anotherThing;

anotherThing = [[[Thingie alloc]
initWithName: @"thing2"
magicNumber: 23
shoeSize: 13.0] autorelease];

[thing1.subThingies addObject: anotherThing];

anotherThing = [[[Thingie alloc]
initWithName: @"thing3"
magicNumber: 17
shoeSize: 9.0] autorelease];

[thing1.subThingies addObject: anotherThing];

NSLog (@"thing with things: %@", thing1);
```

这会打印出 `thing1` 及其子对象：

```
thing with things: thing1: 42/10.5 (
thing2: 23/13.0 (
),
thing3: 17/9.0 (
)
)
```

编码和解码的工作方式完全相同：

```
freezeDried = [NSKeyedArchiver archivedDataWithRootObject: thing1];

thing1 = [NSKeyedUnarchiver unarchiveObjectWithData: freezeDried];

NSLog (@"reconstituted multithing: %@", thing1);
```

并打印出之前看到的相同日志。

如果被编码的数据中存在循环引用，会发生什么？例如，如果 `thing1` 位于它自己的 `subThingies` 数组中呢？`thing1` 是否会编码数组，而数组又编码 `thing1`，然后 `thing1` 再次编码数组，如此循环往复？幸运的是，Cocoa 在归档器和解归档器的实现上非常巧妙，使得对象循环可以被保存和恢复。

要测试这一点，将 `thing1` 放入它自己的 `subThingies` 数组：`[thing1.subThingies addObject: thing1];`

不过，不要尝试对 `thing1` 使用 `NSLog`。`NSLog` 不够智能，无法检测到对象循环，所以它会陷入无限递归，试图构建日志字符串，最终让你掉进调试器，伴随着成千上万次的 `-description` 调用。

但是，如果我们现在尝试对 `thing1` 进行编码和解码，它会完美地工作，不会陷入混乱：

```
freezeDried = [NSKeyedArchiver archivedDataWithRootObject: thing1];

thing1 = [NSKeyedUnarchiver unarchiveObjectWithData: freezeDried];
```

## 总结

正如你在本章所看到的，Cocoa 提供了两种加载和保存文件的技术：属性列表（plist）和对象编码。属性列表数据类型是一组知道如何加载和保存自己的类的集合。如果你有一组全部是属性列表类型的对象，可以使用方便的便捷函数将它们保存到磁盘并读回。

如果像大多数 Cocoa 程序员一样，你拥有自己的非属性列表类型的对象，你可以采用 `NSCoding` 协议并实现编码和解码对象的方法：你可以将自己的一堆对象转换为 `NSData`，然后保存到磁盘，稍后再读回来。从这个 `NSData` 中，你可以重建这些对象。



