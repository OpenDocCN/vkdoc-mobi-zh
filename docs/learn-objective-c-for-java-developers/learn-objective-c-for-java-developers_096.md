# Objective-C 内存管理与 C/Objective-C 混合编程

### 自动释放池

所有 Objective-C 对象都假定它们在一个活跃的自动释放池（autorelease pool）范围内运作。如果不存在自动释放池，向对象发送`-autorelease`消息将在控制台打印一条警告消息，提示您的应用程序正在泄漏对象。

运行循环（run loop）和 AppKit 框架会为您创建和清理自动释放池。您必须自行创建自动释放池的两种情况是：在命令行工具的开头，或者创建新线程时。自动释放池属于线程，因此任何新线程都应立即创建一个自动释放池，如代码清单 24-19 所示。

**代码清单 24-19.** 创建线程的自动释放池

```
@implementation Threader

- (void)myThread:(id)ignored

{

NSAutoreleasePool *pool = [NSAutoreleasePool new];

…

[pool drain];

}

@end
```

代码清单 24-19 中的`-myThread:`方法在其自身的线程中启动。它的首要任务是创建一个顶层自动释放池，该池将被所有对象使用，直到线程结束，此时必须清理该池。

**注意：** `-[NSAutoreleasePool drain]`消息是相当新的。旧代码发送`-release`消息代替。在受管内存环境中，两者是相同的。当将受管内存与垃圾回收混合使用时（参见下一节），`-drain`会向垃圾回收器发送一个提示，让其开始回收。两者都是正确的。

如果代码清单 24-19 中的线程在循环中执行某些操作，它应创建嵌套的自动释放池，以定期释放临时对象。这也适用于任何在返回前产生大量临时对象的方法。代码清单 24-20 展示了一个循环，它在工作时定期创建和释放自动释放池。

**代码清单 24-20.** 创建嵌套的自动释放池

```
NSAutoreleasePool *wadingPool = nil;

int poolCount = 0;

NSUInteger i;

for (i=0; i<1000000; i++) {

if (wadingPool==nil)

    wadingPool = [NSAutoreleasePool new];

…

if (++poolCount>2000) {

    poolCount = 0;

    [wadingPool drain];

    wadingPool = nil;

}

}
```

代码清单 24-20 中的循环定期创建一个嵌套的自动释放池，该池被清理以释放循环体中创建的临时对象。这当然打破了“自动释放的对象直到当前方法返回才会被释放”的规则，但这是因为您自己在清理池——所以您应该知道这一点。

新的自动释放池本质上是嵌套在先前活跃的自动释放池内部的。

清理一个池会隐式地清理在其内部创建的所有自动释放池。代码清单 24-19 中的代码不需要确保`wadingPool`在退出循环前被清理。包含`wadingPool`的自动释放池会在其被清理时清理它。例如，异常处理器不需要清理任何嵌套的自动释放池。唯一重要的是，顶层自动释放池在线程终止前被清理。

### 混合受管内存与垃圾回收

您可能会发现自己需要编写既能在受管内存环境又能在垃圾回收环境中运行的 Objective-C 代码。这是可行的，因为受管内存方法和垃圾回收技术重叠很少。在垃圾回收环境中，管理引用计数的消息（`-retain`、`-release`、`-autorelease`）被忽略。在受管内存环境中，编译器对垃圾回收的支持（`__strong`和`__weak`指针类型）被忽略。最终结果是代码在这两种环境中都能正确运行。

当编译将在混合内存环境中运行的代码时，应包含编译器对垃圾回收的支持。在 Xcode 中，将“Objective-C Garbage Collection”构建设置为“Supported”。这会使编译器包含对垃圾回收的支持，但同时也包含支持受管内存所需的代码。当“Objective-C Garbage Collection”构建设置为“Required”（仅垃圾回收）时，编译器会主动剥离仅对受管内存有用的代码。这使代码更高效，但牺牲了向后兼容性。

编写能在两种环境中运行的代码相当直接。通常，正确实现受管内存的代码也能在垃圾回收环境中工作。例如，本章中的所有代码示例在启用垃圾回收时都能正确运行。表 24-2 列出了每种环境对代码的影响。

**表 24-2.** 受管内存与垃圾回收的效果对比

| 代码 | 受管内存 | 垃圾回收 |
|------|----------|----------|
| `-retain` | 保留一个对象 | 忽略 |
| `-release` | 释放一个对象 | 忽略 |
| `-autorelease` | 将对象添加到自动释放池 | 忽略 |
| `-retainCount` | 返回当前保留计数 | 无意义 |
| `-dealloc` | 发送给对象以销毁它 | 运行时阻止此消息发送 |
| `-finalize` | 从不发送 | 当对象即将被回收时发送 |
| `__strong` | 忽略 | 表示强引用 |
| `__weak` | 忽略 | 表示弱引用 |
| `-drain` | 清理自动释放池 | 触发垃圾回收 |
| `@property (assign)` | 未保留的属性 | 强引用 |
| `@property (retain)` | 保留的属性 | 强引用 |

`NSAutoreleasePool`的`-drain`方法大约是唯一在两种环境中都有功能的方法。当垃圾回收开启时，它提示垃圾回收器可能想要开始一个垃圾回收周期。

### 总结

受管内存需要一些额外的努力，但并不繁重。只需记住这些基本规则：

- 将对象引用存储在持久变量中时，保留它。
- 在丢弃任何保留的引用之前，释放或自动释放它。
- 编写一个`-dealloc`方法来释放所有保留的对象。
- 始终返回保留或自动释放的对象。
- 自动释放的对象在当前方法返回之前不会被释放。

---

## 第 25 章：混合 C 和 Objective-C

Objective-C 的一大优势是它允许与 C 函数和变量无缝集成。这让您可以直接访问大量现有的 C API。本章讨论了在同一应用程序中混合 C 和 Objective-C 代码的基本方法，并介绍了 Core Foundation，这是一个 C 库，包含可与 Objective-C 对象互换的数据结构。混合语言会带来一些有趣的内存管理问题，这些问题将在本章末尾进行解释。

### 在 Objective-C 中使用 C

“在 Objective-C 中使用 C”这个说法有些误导，因为 Objective-C 是 C 语言的严格超集。您实际上不能*使用* C 在 Objective-C 中，因为 Objective-C *就是* C。这个术语通常用于当您编写直接使用 C 结构体和调用 C 函数的代码时，而不是使用 Objective-C 对象和消息。

#### 从 Objective-C 调用 C 函数

本书中已经呈现了许多代码示例，它们实现了 C 函数并在 Objective-C 类中使用它们。这些是从 C 或 Objective-C 代码中调用的标准 C 函数。具有静态类型的 C 函数将其作用域限制在正在编译的模块中的代码。非静态函数是全局的，可以从任何模块调用。（请记住，在此上下文中，“static”意味着私有，而不是 Java 意义上的静态。）一个静态 C 函数的示例如代码清单 25-1 所示。



### **列表 25-1.** C 函数与 Objective-C 方法

```objc
static NSInteger compareZombieBrains( id l, id r, void *ignored )
{
    float lSize = [l brainSize];
    float rSize = [r brainSize];
    if (lSize < rSize)
        return NSOrderedAscending;
    else if (lSize > rSize)
        return NSOrderedDescending;
    return NSOrderedSame;
}
```

[www.it-ebooks.info](http://www.it-ebooks.info/)

## 第 25 章 ■ 混合使用 C 与 Objective-C

```objc
- (NSArray*)zombiesSortedByIntelligence
{
    return [zombieArray sortedArrayUsingFunction:compareZombieBrains context:NULL];
}
```

C 函数可以定义在任何 Objective-C 指令之外的位置，或者任何可以定义 Objective-C 方法的位置。这意味着你可以在 `@implementation` 指令中混合使用 C 函数和 Objective-C 方法，但有些程序员会将他们的 C 函数统一放置在 `@implementation` 外部。这属于编码风格的问题。

你的 Objective-C 应用程序可以调用操作系统提供的 C API，可以链接标准 C 库，也可以在项目中包含 C 源代码模块。这使得与广泛的 POSIX 函数、C 框架、共享库以及开源代码进行交互变得非常容易。

#### 在 C 中使用 Objective-C 对象

Objective-C 模块（`.m`）中的 C 函数会被编译为 Objective-C 代码，而不仅仅是 C 代码。列表 25-1 中的 C 函数向对象发送 Objective-C 消息，就像它是一个 Objective-C 方法一样。唯一的区别是它没有对象上下文（`self`）。就此而言，C 函数相当于 Java 中的静态类方法。

当你在 Xcode 项目中添加一个 C 源文件（`.c`）时，它会被编译为纯 C 代码。该源文件不能包含任何 Objective-C 语法。如果你需要在 C 模块中使用 Objective-C 对象，有两种选择：

-   将那些需要使用 Objective-C 对象的函数隔离出来，并将它们移动到一个 Objective-C（`.m`）源文件中。该文件可以完全由 C 函数组成，但它会被编译为 Objective-C 代码，从而能够使用 Objective-C 语法来引用对象和发送消息。
-   直接使用一个 Objective-C 运行时函数（例如 `objc_msgSend(id, SEL, …)`）来发送 Objective-C 消息。

后一种解决方案比较笨拙，但从技术上讲，你可以在 C 中完成任何在 Objective-C 中能完成的事情。毕竟，整个 Objective-C 运行时都是用 C 编写的。具体细节请参阅 *Objective-C 2.0 运行时参考* ¹。

### Core Foundation

Core Foundation 是一个庞大的 C 函数库，它围绕“对象”这一概念进行设计，并将这些对象称为*不透明类型*或简称*类型*。这些不透明类型具有与 Objective-C 对象兼容的内部结构，并且相当多的 Core Foundation 类型可以与 Objective-C 类互换。这种互惠性被称为*免费桥接*。Core Foundation 的目标是无缝地为 C 和 Objective-C 程序员提供大量操作系统和框架服务。C 程序员使用 `CFTypeRef` 值（指向不透明数据结构的指针）并将其传递给 Core Foundation 函数。Objective-C 程序员则存储对象指针并向其发送消息。在这两种情况下，执行的代码是相同的。

大多数 Core Foundation 类型没有与 Objective-C 的免费桥接。如果你需要它们的功能，则需要直接使用 Core Foundation 数据类型和函数。Core Foundation 采用了许多与 Objective-C 相同的概念，只是用其自己的类型、函数和术语来实现它们。常见的 Core Foundation 类型和函数及其 Objective-C 对应项的对比列于表 25-1 中。

¹ Apple Inc., *Objective-C 2.0 运行时参考*, [`developer.apple.com/documentation/Cocoa/Reference/ObjCRuntimeRef/`](http://developer.apple.com/documentation/Cocoa/Reference/ObjCRuntimeRef/), 2008.

[www.it-ebooks.info](http://www.it-ebooks.info/)

### **表 25-1.** Core Foundation 术语

| Objective-C | Core Foundation |
|-------------|----------------|
| `Class` | `CFTypeID` |
| `id` 或 `NSObject*` | `CFTypeRef` |
| `NSString*` | `CFStringRef` |
| `[t class]` | `CFGetTypeID(t)` |
| `[t isEqual:t2]` | `CFEqual(t1,t2)` |
| `[t description]` | `CFCopyDescription(t)` |
| `[t retain]` | `CFRetain(t)` |



`CFRetain(t)`

`[t release]`

`CFRelease(t)`

一个 Core Foundation *类型* 是一个标识符，它在概念上等同于一个 Objective-C 类。一个 Core Foundation *引用*（简称 `ref`）是一个类型的实例，在功能上等同于一个标识符（`id`）或对象指针。

类型像类一样按继承层次结构组织。`CFStringRef` 是 `CFTypeRef` 的子类型，就像 `NSString` 是 `NSObject` 的子类一样。`CFStringRef` 可以传递给任何接受 `CFStringRef` 或 `CFTypeRef` 参数的函数，正如 `NSString` 对象会响应为 `NSString` 或 `NSObject` 定义的任何消息一样。

### 无缝桥接

Core Foundation 和 Objective-C 类框架被设计成使特定的 Core Foundation 类型与等价的 Objective-C 类可互换。形成无缝桥接的类型和类别于表 25-2 中。

**表 25-2.** Core Foundation 无缝桥接

| Cocoa 类 | Core Foundation 类型 |
| :--- | :--- |
| `NSArray` | `CFArrayRef` |
| `NSAttributedString` | `CFAttributedStringRef` |
| `NSCalendar` | `CFCalendarRef` |
| `NSCharacterSet` | `CFCharacterSetRef` |
| `NSData` | `CFDataRef` |
| `NSDate` | `CFDateRef` |
| `NSDictionary` | `CFDictionaryRef` |
| `NSError` | `CFErrorRef` |
| `NSInputStream` | `CFReadStreamRef` |
| `NSLocale` | `CFLocaleRef` |
| `NSMutableArray` | `CFMutableArrayRef` |
| `NSMutableAttributedString` | `CFMutableAttributedStringRef` |
| `NSMutableCharacterSet` | `CFMutableCharacterSetRef` |
| `NSMutableData` | `CFMutableDataRef` |
| `NSMutableDictionary` | `CFMutableDictionaryRef` |
| `NSMutableSet` | `CFMutableSetRef` |
| `NSMutableString` | `CFMutableStringRef` |
| `NSNumber` | `CFNumberRef` |
| `NSOutputStream` | `CFWriteStreamRef` |
| `NSSet` | `CFSetRef` |
| `NSString` | `CFStringRef` |
| `NSTimer` | `CFRunLoopTimerRef` |
| `NSTimeZone` | `CFTimeZoneRef` |
| `NSURL` | `CFURLRef` |

简而言之，指向表 25-2 中某个类的对象指针，在功能上等同于其匹配的 Core Foundation 类型引用。这个单一值可以被透明地视为任何一种类型。

清单 25-2 中的代码通过一个结合了 Core Foundation UUID 类型的 Objective-C 类演示了这一点。

**清单 25-2.** 在 Objective-C 类中使用 Core Foundation 类型

```
#include <CoreFoundation/CoreFoundation.h>

@interface Unique : NSObject <NSCoding> {
@private
    __strong CFUUIDRef uuid;
}

@property (readonly) NSString *uuid;

@end

@implementation Unique

- (id)init
{
    self = [super init];
    if (self != nil) {
        CFUUIDRef newUUID = CFUUIDCreate(kCFAllocatorDefault);
        uuid = CFMakeCollectable(newUUID);
    }
    return self;
}

- (id)initWithCoder:(NSCoder*)decoder
{
    self = [super init];
    if (self != nil) {
        NSString *uuidString = [decoder decodeObjectForKey:@"UUID"];
        CFUUIDRef savedUUID = CFUUIDCreateFromString(kCFAllocatorDefault, (CFStringRef)uuidString);
        uuid = CFMakeCollectable(savedUUID);
    }
    return self;
}

- (void)encodeWithCoder:(NSCoder*)encoder
{
    [encoder encodeObject:self.uuid forKey:@"UUID"];
}

- (NSString*)uuid
{
    CFStringRef cfString = CFUUIDCreateString(kCFAllocatorDefault,uuid);
    NSString *objcString = (NSString*)CFMakeCollectable(cfString);
    return objcString;
}

@end
```

标准的 Objective-C 类框架不包含封装通用唯一标识符（UUID）的类。然而，Core Foundation 框架拥有非常实用的 `CFUUIDRef` 类型以及一组用于创建和编码 UUID 的函数。清单 25-2 中的 `Unique` 类存储了一个 `CFUUIDRef` 值（等同于一个对象指针）作为一个 Core Foundation 类型，就像对待任何其他 C 指针一样。`-init` 方法创建一个通用唯一 ID 类型并存储一个指向它的引用。

Core Foundation 函数 `CFUUIDCreateString(...)` 根据一个 `CFUUIDRef` 创建 UUID 的文本表示，并将其作为一个新的 `CFStringRef` 返回。查看表 25-2，`CFStringRef` 和 `NSString` 是无缝桥接的。因此，对 Core Foundation 字符串类型的引用可以被当作一个 `NSString` 对象指针来处理，并返回给调用者。

类似地，`-initWithCoder:` 方法首先从序列化数据流中解码一个 `NSString` 对象。它将 `NSString` 对象指针传递给 `CFUUIDCreateFromString(...)`，就好像它是一个 `CFStringRef` 一样。`CFUUIDCreateFromString(...)` 函数使用这个 `CFStringRef` 来重建 UUID 不透明类型。

无缝桥接意味着你可以无缝地将许多常见的 Core Foundation 类型当作 Objective-C 对象来使用，反之亦然。Core Foundation 类型与其对应的 Objective-C 类之间唯一显著的区别在于内存管理。这将在下一节讨论。

**提示** 还有其他一些你可能感兴趣的 Objective-C 桥接。例如，存在从 Objective-C 到 Ruby 和 Python 编程语言的桥接。Apple 曾开发过一个 Objective-C 到 Java 的桥接，但后来已被弃用。

表 25-2 中列出的 Objective-C 类是唯一与 Core Foundation 类型有无缝桥接的。例如，`NSRunLoop` 与 `CFRunLoopRef` *不* 可互换。要在 `NSRunLoop` 上使用 Core Foundation 函数，请向该对象发送 `-getCFRunLoop` 消息，它将返回其底层的 `CFRunLoopRef`。

### C 内存管理

第 24 章描述了 C 内存管理，或者说是其缺乏。Core Foundation 的内存管理要么与 Objective-C 使用的内存管理协同工作，要么在其上分层。当使用 Core Foundation 类型时，你必须采用 Core Foundation 内存管理模式，或者将引用过渡到 Objective-C 所使用的内存管理方案。

Core Foundation 类型使用简单的引用计数。如果你跳过了第 24 章，你可能想回顾一下，以便理解基本原理。以下是关于 Core Foundation 内存管理你需要了解的关键概念：

*   Core Foundation 使用引用计数。
*   每个 Core Foundation 类型都是使用一个*分配器*分配的。相当于 `+alloc` 的分配器是 `kCFAllocatorDefault` 或 `NULL`。
*   Core Foundation 函数 `CFRetain(o)` 和 `CFRelease(o)` 分别等同于 `[o retain]` 和 `[o release]`（在托管内存环境中）。
*   Core Foundation 不使用自动释放池。函数返回的 refs 要么是新的（刚刚创建，隐式由调用者拥有），要么是拥有的（至少被其他所有者 retain 一次）。

#### 使用 Core Foundation 内存管理模式

除非你需要保留或返回一个对 Core Foundation 类型的引用，否则最简单的方法是遵循 Core Foundation 内存管理模式。Core Foundation 不使用自动释放池，因此所有返回的 refs 要么是新的，要么是拥有的。要使用 Core Foundation 内存管理，你的代码应该执行以下操作：

*   像在托管内存环境中对待一个新对象一样使用一个新 ref。
*   在需要的时间内使用该 ref。
*   使用完毕后调用 `CFRelease(ref)`。

将拥有的引用视为类似托管内存环境中的自动释放对象。

*   拥有的 refs 通常可以在当前方法或函数返回前自由使用。
*   或者，调用 `CFRetain(ref)` 来 retain 该类型，在需要的时间内使用它，然后调用 `CFRelease(ref)` 来 release 它。

关于 Core Foundation 内存管理约定和规则的完整描述可以在 *Memory Management Programming Guide for Core Foundation* 中找到。

#### 使用垃圾回收的 Core Foundation



