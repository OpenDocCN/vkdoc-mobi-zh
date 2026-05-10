# Objective-C 序列化与归档

## 类编码与对象替换

第一种方式是重写 `-classForCoder` 方法。在编码过程中，此消息会被发送给对象。归档中对象的类由返回值决定。基类实现返回 `[self class]`，这会使对象以其实际类进行记录。如果对象的 `-classForCoder` 方法返回不同的类，则在解码时将创建该返回类对应的对象。请注意，在 `-encodeWithCoder:` 方法中编码的数据必须与 `-classForCoder:` 返回的类兼容。

`-classForCoder` 影响所有归档类型。如果只想将类替换限制在特定归档类型，可以重写 `-classForArchiver`、`-classForKeyedArchiver` 或 `-classForPortCoder`。如果未重写，这些方法将返回 `-classForCoder` 的值。

第二种技术允许对象替换为完全不同的对象进行编码。这是通过重写 `-replacementObjectForCoder` 实现的，该方法通常返回 `self`（从而编码原始对象）。如果返回不同的对象，则编码该代理对象。`-replacementObjectForCoder:` 会对所有归档编码执行此替换，但可以重写替代方法 `-replacementObjectForKeyedArchiver:`、`-replacementObjectForArchiver:` 或 `-replacementObjectForPortCoder:`，以仅对特定归档类型执行替换。默认情况下，前两个方法会调用 `-replacementObjectForCoder:`。`-replacementObjectForPortCoder:` 方法是分布式对象中的关键机制，其默认实现会将原始对象替换为远程代理对象。有关重写此方法的原因，请参见第 13 章。

如果这还不够灵活，编码器的委托对象也可以通过实现 `-archiver:willEncodeObject:` 方法来执行对象替换。委托可以返回替换对象，临时执行替换，无需修改被编码的类。

### Objective-C 序列化

在 Objective-C 术语中，序列化将一组数据对象转换为可传输的字节流，通常采用人类可读的格式。Objective-C 中有两种标准的序列化形式：属性列表（Property Lists）和 XML。属性列表简单但非常方便，构成了用户默认设置（偏好设置）服务的基础。Cocoa 的 XML 支持包括常见的 DOM 和基于事件的 XML 解析与编码。Objective-C 序列化不像归档那样可以编码任意对象，属性列表仅限于属性列表对象，XML DOM 编码则仅限于 XML 文档对象模型类。

### 属性列表

属性列表是一个或多个属性列表对象中值的文本或二进制表示。属性列表对象是那些可以编码到属性列表中的对象。表 12-3 列出了所有属性列表对象。

**表 12-3.** 属性列表对象

| 对象类 | 描述 |
|--------|------|
| `NSDictionary` | 属性列表对象的键/值映射 |
| `NSArray` | 属性列表对象的顺序列表 |
| `NSString` | 字符串 |
| `NSNumber` | 整数、浮点数或布尔值 |
| `NSDate` | 日期和时间 |
| `NSData` | 任意字节数组 |

属性列表用于多种目的，如存储用户默认设置（详见第 26 章）。它们也非常适合编码简单值的集合，以便持久化或供其他应用程序解析。通常，属性列表是一个包含属性列表对象的键/值对字典，这些对象可以包含其他数组和字典。这使得属性列表对象能够形成任意复杂的树结构，叶节点由字符串、数字、日期或不透明数据组成。实际上，任何对象都可以通过先归档对象，然后将生成的 `NSData` 对象存储在属性列表树中，来存储到属性列表中。清单 12-13 创建了一个属性列表对象树，并将其序列化为属性列表。

**清单 12-13.** 生成属性列表

```
NSArray *attendees = [NSArray arrayWithObjects:
    @"Randy",
    @"Joy",
    @"Douglas",
    @"Heather",
    @"Jon",
    nil];

NSDictionary *invitation = [NSDictionary dictionaryWithObjectsAndKeys:
    @"X-Prize Launch Strategy", @"Description",
    [NSDate dateWithString:@"2009-04-02 10:00:00 -0700"], @"StartTime",
    [NSNumber numberWithInt:55], @"Duration",
    attendees, @"Attendees",
    @"Room 312", @"Location",
    nil];

NSData *data = [NSPropertyListSerialization dataFromPropertyList:invitation
    format:NSPropertyListXMLFormat_v1_0
    errorDescription:NULL];
```

`data` 现在包含以下内容：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN"
    "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
    <key>Attendees</key>
    <array>
        <string>Randy</string>
        <string>Joy</string>
        <string>Douglas</string>
        <string>Heather</string>
        <string>Jon</string>
    </array>
    <key>Description</key>
    <string>X-Prize Launch Stratagy</string>
    <key>Duration</key>
    <integer>55</integer>
    <key>Location</key>
    <string>Room 312</string>
    <key>StartTime</key>
    <date>2009-04-02T17:00:00Z</date>
</dict>
</plist>
```

用于编码和解码属性列表的 Objective-C 接口是 `NSPropertyListSerialization` 类。它提供了三个方法：

- `+dataFromPropertyList:format:errorDescription:`：将对象编码为属性列表。
- `+propertyListFromData:mutabilityOption:format:errorDescription:`：解码属性列表。
- `+propertyList:isValidForFormat:`：确定数据是否包含有效的属性列表。

对于所有转换，必须指定属性列表的格式。可能的格式如表 12-4 所示。

**表 12-4.** 属性列表格式

| 格式 | 描述 |
|------|------|
| `NSPropertyListXMLFormat_v1_0` | 属性值的 XML 表示 |
| `NSPropertyListBinaryFormat_v1_0` | 属性值的紧凑二进制表示 |
| `NSPropertyListOpenStepFormat` | 已弃用的 ASCII 格式；可用于读取旧版 `.plist` 文件，但不能用于编码新的属性列表 |

`NSDictionary` 和 `NSArray` 都接受 `-writeToFile:atomically:` 或 `-writeToURL:atomically:` 消息。这会将它们的内容序列化，并将生成的 XML 属性列表写入文件或 URL。这些消息仅在集合包含属性列表对象时才能成功；任何非属性列表对象都会导致操作失败并返回 `NO`。不要将这些方法与 `NSData` 和 `NSString` 实现的 `-writeToFile:atomically:` 和 `-writeToURL:atomically:` 混淆，后者将对象的原始内容写入文件，而非属性列表。

与 `-writeTo…:atomically:` 消息互补的是 `+[NSArray arrayWithContentsOfFile:]`、`+[NSArray arrayWithContentsOfURL:]`、`+[NSDictionary dictionaryWithContentsOfFile:]` 和 `+[NSDictionary dictionaryWithContentsOfURL:]` 便捷构造器。这些方法通过解释属性列表的内容来创建新的集合。

默认情况下，通过解码属性列表创建的属性列表集合对象是不可变的，无论最初用于创建属性列表的对象的可变性如何。要创建可变的属性列表对象，请使用 `+[NSPropertyListSerialization propertyListFromData:mutabilityOption:format:errorDescription:]`。



`propertyListFromData:mutabilityOption:format:errorDescription:` 并在`mutabilityOption:`参数中传入`NSPropertyListMutableContainers`或`NSPropertyListMutableContainersAndLeaves`。前者会返回包含不可变叶子值的可变集合对象，后者将导致树中所有对象在可能的情况下变为可变。此选项不影响`NSNumber`或`NSDate`对象，它们本质上是不可变的。你也可以通过显式创建可变集合来创建顶层可变集合，例如`[NSMutableDictionary dictionaryWithContentsOfFile:propertyFilePath]`。

### XML

与 Java 类似，Cocoa 框架提供了一组用于创建、操作、编码和解码 XML 文件的类。虽然 XML 格式的属性列表是将非常简单值编码为 XML 的一种便捷方式，但`NSXML`类可以解释任何 XML 或 HTML 格式的数据。

与 Java 一样，Objective-C 可以解析整个 XML 文档，生成文档对象模型（DOM）。或者它可以使用事件驱动解析器增量式地解释 XML 流。尽管许多细节有所不同，但 Java 和 Objective-C 为 XML 处理提供的总体接口几乎相同。

清单 12-14 展示了用于从 XML 文件创建文档对象模型，然后将该 DOM 编码回 XML 文件的代码。

**清单 12-14 XML 使用文档对象模型**

**Java**

```java
String filePath = ...
Document document = null;
DocumentBuilderFactory factory = DocumentBuilderFactory.newInstance();
factory.setValidating(false);
factory.setNamespaceAware(true);
try {
    DocumentBuilder builder = factory.newDocumentBuilder();
    document = builder.parse(new File(filePath));
}
catch (Exception e) {
    e.printStackTrace();
}
...
try {
    Source source = new DOMSource(document);
    Result result = new StreamResult(new File(filePath));
    Transformer transformer = TransformerFactory.newInstance().newTransformer();
    transformer.transform(source, result);
}
catch (Exception e) {
    e.printStackTrace();
}
```

**Objective-C**

```objectivec
NSString *filePath = ...
NSXMLDocument *document;
NSURL *furl = [NSURL fileURLWithPath:filePath];
document = [[NSXMLDocument alloc] initWithContentsOfURL:furl options:NSXMLNodePreserveAll error:NULL];
...
NSData *xmlData = [document XMLData];
[xmlData writeToFile:filePath atomically:YES];
```

Objective-C 编码更为简单，因为`NSXMLDocument`类自动提供了 DOM 转换器。要将 XML 或 HTML 文件转换为文档对象模型，只需使用 XML 源的内容初始化一个新的`NSXMLDocument`对象。类似地，将现有 DOM 转换为其 XML 表示形式，只需向`NSXMLDocument`请求其`NSData`表示形式。另一种方法`-[NSXMLDocument XMLDataWithOptions:]`接受一组影响 XML 编码方式的标志。

Objective-C 的事件驱动 XML 解析与其 Java 同类 SAX（Simple API for XML）相似。在 Java 中，你创建一个实现`org.xml.sax.ContentHandler`接口的自定义对象。该接口定义了许多回调方法（`startDocument()`、`startElement(String, String, String, Attributes)`、`characters(char[], int, int)`等），这些方法在解析每个 XML 元素时被调用。你对这些方法的实现通常会使用解析后的内容来创建自定义数据模型对象，或将信息提供给另一个对象。

在 Objective-C 中，过程几乎相同，只是由委托方法（由非正式协议定义）接收解析事件。要使用 Objective-C 解析 XML 文件，需在你的类中实现相应的委托方法（`-parserDidStartDocument:`、`-parser:didStartElement:namespaceURI:qualifiedName:attributes:`、`-parser:foundCharacters:`等）。使用`-initWithData:`或`-initWithContentsOfURL:`创建`NSXMLParser`对象的实例，以指定 XML 源。将你的自定义对象设置为解析器的委托（`[xmlParser setDelegate:myParser]`），然后向其发送`parse`消息以开始解码。

### 复制对象

归档和序列化实际上都是复制对象。如果你只需要高效地复制对象，Objective-C 提供了`-copy`消息，它可以几乎以与 Java 的`Object.clone()`方法完全相同的方式复制对象。此外，Objective-C 定义了一个用于获取不可变对象可变副本的协议。

复制对象可能产生浅拷贝或深拷贝，具体取决于对象的性质。浅拷贝是 Java 和 Objective-C 中的默认方式。浅拷贝创建一个新对象，其实例变量包含与原始对象相同的值。之所以称为浅拷贝，是因为复制后的对象将引用原始对象所引用的所有相同对象。对于不可变对象的引用，这是首选结果，因为它避免了不必要的对象重复。然而，对于可变对象，更改某个属性值也会影响副本的值。要真正独立于原始对象，复制的对象必须递归地复制其引用的任何可变对象，这称为深拷贝。

在 Java 中，一个可克隆的对象必须实现`java.lang.Cloneable`。如果你没有做其他操作，调用`Object.clone()`将对该对象执行浅拷贝。如果对象需要执行深拷贝，则必须重写`Object.clone()`并执行所需的任何额外复制操作。

Objective-C 非常类似。要使一个对象可复制，它必须遵循`NSCopying`协议并实现`-copyWithZone:`方法。要执行浅拷贝，`-copyWithZone:`可以调用`NSCopyObject(...)`来生成并返回对象的副本。如果需要进行深拷贝，应在返回之前执行额外的复制操作或其他内存管理。

清单 12-15 展示了一个对自身执行深拷贝的简单对象。

**清单 12-15 复制对象**

**Java**

```java
public class StormTrooper implements Cloneable {
    ArrayList evilOrders;
    public Object clone() throws CloneNotSupportedException {
        try {
            StormTrooper clone = (StormTrooper)super.clone();
            clone.evilOrders = (ArrayList)this.evilOrders.clone();
            return (clone);
        }
        catch (CloneNotSupportedException e) {
            throw new InternalError(e.toString());
        }
    }
}
```

**Objective-C**

```objectivec
@interface StormTrooper : NSObject <NSCopying> {
    NSMutableArray *evilOrders;
}
@end

@implementation StormTrooper
- (id)copyWithZone:(NSZone*)zone {
    StormTrooper *clone = NSCopyObject(self, 0, zone);
    if (clone != nil)
        clone->evilOrders = [evilOrders copy];
    return (clone);
}
@end
```

要复制一个遵循`NSCopying`协议的对象，请向其发送`-copy`消息。该对象将向自身发送`-copyWithZone:`消息，如果该对象不遵循`NSCopying`协议，则会引发异常。请勿通过重写`-copy`来自定义对象复制。

`NSCopyObject`创建一个新对象。如果一个对象继承自已实现`-copyWithZone:`的类，则应通过向父类发送`-copyWithZone:`来创建副本，例如`MyClass *clone = [super copyWithZone:zone]`，然后继续执行任何子类特定的复制操作。

`NSCopyObject`很方便，但你并非必须使用它。你可以选择使用任何可用方法创建并初始化一个等效对象。例如，你可以使用`[[[self class] alloc] init]`之类的方法创建一个新对象，然后初始化该新对象使其与原始对象相等。你也可以从类似对象的池中取出一个已创建的对象，并设置其属性使其匹配。一个不可变对象可以选择返回`self`，而不是实际进行复制。

Objective-C 还定义了一个用于获取不可变对象可变副本的接口。如果你实现了一个可复制的不可变类，并且该类有一个可变子类，你应遵循以下设计：



• 不可变的超类也应遵循 `NSMutableCopying` 协议并实现 `-mutableCopyWithZone:` 方法。
• 在不可变的超类中，`-mutableCopyWithZone:` 方法应创建一个可变子类的实例，复制相关数据，并返回新实例。
• 在可变子类中，`-mutableCopyWithZone:` 方法应模仿 `-copyWithZone:` 的行为，返回自身的一个可变副本。

现在，你的对象将智能地响应 `-mutableCopy` 消息。

[www.it-ebooks.info](http://www.it-ebooks.info/)

## 第 12 章：序列化

### 摘要

Objective-C 的归档功能扮演了 Java 序列化的角色。创建一个可归档的类需要预先多做一些工作，但其基本模式与 Java 中的序列化相同。为归档数据提供向后（甚至可能是向前）兼容性并不困难。Objective-C 还提供了额外的好处：可以将编码对象的图限制为仅与根对象相关的那些对象，并在编码和解码过程中拥有灵活的对象替换与简化框架。

正如你现在所发现的，Objective-C 的序列化与 Java 序列化并不等同，但它确实提供了一种使用 XML 对数据进行编码的简单便捷方法。你还学习了创建对象内存副本的基本知识。

在牢固理解消息分发和对象归档的基础上，你现在可以体会到下一章将要讨论的分布式对象的简洁性了，它巧妙地将这两项特性结合在了一起。

[www.it-ebooks.info](http://www.it-ebooks.info/)

## 第 13 章：近程与远程通信

通信是一个非常宽泛的术语。在抽象意义上，向一个 Objective-C 对象发送消息就是在与该对象“通信”。在另一个极端，将文件刻录到光盘上，然后由另一个程序读取，则是向该应用程序“通信”数据。本章重点介绍通过独立代理或服务在对象之间直接交换数据的通信技术。

在这个范围内，通信可以大致分为三个领域：同一进程内对象之间的消息交换、不同进程内对象之间的消息交换，以及通过网络进行的数据交换。本章将概述用于这三种情况的常用 Objective-C 技术，但其中一些技术的细节会在其他章节中介绍。这些领域之间有一定重叠。有些技术几乎专门用于在同一进程内运行的对象之间交换消息，但也可以用于向其他进程发送消息，反之亦然。因此，在确定解决方案之前，请花点时间熟悉所有这些技术。

### 单进程内通信

有几种技术可用于与位于你进程内存地址空间中的对象交换消息。基础的 Objective-C 消息分发是其中之一，但本节将回顾以下几种代表其他对象向目标对象发送消息的技术：

- 延迟消息
- 通知
- 键值观察
- 分布式对象

延迟消息已在第 6 章讨论过。延迟消息使用 `-performSelector:…` 方法族发送。这是最简单的对象间通信形式。它将一个 Objective-C 消息排入队列，该消息将在稍后的某个时间发送给一个对象。该消息通常在同一线程中发送，但某些变体会在不同线程中将其发送给对象。

通知将 `NSNotification` 对象发送给感兴趣接收它们的对象。一个 `NSNotification` 是一个具名的消息容器，可以包含你想提供给接收者的任意信息。通知是 Objective-C 对提供者/订阅者模式的实现，并在第 18 章中描述。通知与其他通信技术最显著的区别在于，通知是一种一对多的通信路径，并且提供者和订阅者对象之间不需要有任何直接的相互了解。通知通过 `NSNotificationCenter` 对象进行分发。发送者描述其想要分发的通知的性质，接收者描述它希望接收的通知类型。通知中心将发送者与接收者进行匹配，并传递所请求的通知。

[www.it-ebooks.info](http://www.it-ebooks.info/)

键值观察（KVO）是一种专门的通知服务，用于传达对象属性的变更。一个观察者对象可以关联到另一个对象的特定属性。一旦关联，该属性的任何更改都会立即以 `-observeValueForKeyPath:ofObject:change:context:` 消息的形式发送给观察者。键值观察特别有吸引力，因为它对被观察对象没有预先的设计要求——除了它必须实现一个符合 KVO 规范的属性。因此，你的对象可以请求获知几乎任何对象的任何属性的更改。键值观察在第 19 章中描述。

分布式对象（DO）通常用于在不同进程中的对象之间，或通过网络向其他系统发送消息。然而，它也可以用于在同一进程的不同线程之间发送消息。这种分布式对象的使用方式是为你的设计添加异步消息处理的一种简便方法。线程间 DO 将在下一节中描述和演示。

### 与其他进程通信

与其他进程通信受到一个严重限制：本地进程内存地址空间中的一个地址，对于任何其他进程来说都是毫无意义的。要与另一个进程交换信息，所有数据必须处于或转换为一种在进程外部有意义的可传输形式。这通常表现为由接收进程串行解释的字节数组。幸运的是，在上一章中，你刚刚学习了两种能对对象执行此转换的关键技术——归档和序列化。

端口、管道和套接字是用于在进程间交换字节块的低级工具。端口指的是 Mach 内核端口，这是消息发送到内核以及进而发送到其他进程的基本机制。从技术上讲，所有进程外的通信都是通过 Mach 端口执行的，因为这是进程与外部世界通信的唯一方式。在端口之上，是 POSIX 概念中的管道和套接字。从概念上讲，管道是一个单向的串行通信管道，两端各连接一个进程。一个进程注入管道中的字节会立即作为可读数据出现在另一端。套接字是两个进程之间的单向或双向通信管道。虽然管道仅限于在同一系统上运行的两个进程，但套接字可以通过数据网络或其他传输介质连接到在完全不同的计算机系统（可能相当遥远）上运行的进程。套接字更面向数据包，发送和接收离散的信息块，而非单个字节。



