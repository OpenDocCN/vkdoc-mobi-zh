# 格式化字符与数字转换

要将`char`格式化为无符号数值，请使用说明符`%hhu`。要将 64 位`long long`整数格式化为十六进制，请使用说明符`%llx`或`%qx`。如果不确定参数类型是否与说明符匹配，请使用类型转换强制转换，如`[NSString stringWithFormat:@"%hi",(short int)i]`。

在 32 位 CPU 架构中，`long int`类型是 32 位的，因此`int`和`long int`含义相同。尽管如此，在传递`long int`参数时，匹配正确的长度修饰符（`%ld`）是良好的编程实践，即使更简单的形式（`%d`）也兼容。

Objective-C 字符串格式化不提供布尔值、日期或时间的格式说明符。`-[NSDate description]`方法使用类似 ISO 的国际格式返回日期和时间。如果这足够，可以使用`%@`格式化日期对象。对于更复杂的日期格式化，请使用`NSDateFormatter`，其结果可以通过`%@`插入到格式字符串中。格式化布尔值时，我通常使用三元条件表达式，如`[NSString stringWithFormat:@"%@",(t?@"YES":@"NO")]`。

这绝不是对格式化字符串的全面探讨。Cocoa 框架实现了 IEEE `printf`规范，并附带一些额外的 Objective-C 特定扩展。该 IEEE 标准广泛且包含许多晦涩的选项、功能和说明符。“Format Specifiers”部分的“String Programming Guide for Cocoa”[¹]，由 Apple 发布，以及“Standard 1003.1”[²]，由 IEEE 和 The Open Group 发布，包含完整描述。

#### NSFormatter

`NSFormatter`类是用于将值对象转换为字符串及反向转换的对象的抽象基类。不要将其与`java.util.Formatter`类混淆。Java 的`Formatter`是上一节描述的格式化逻辑的实现位置。Cocoa 的`NSFormatter`类定义了创建格式化器对象的抽象接口。`NSFormatter`对象执行用户所见所输入的内容与（可能的）复杂值（如货币、日历日期和时钟时间）之间的转换。

`NSFormatter`对象旨在封装诸如规范化、本地化和用户显示偏好等功能——这些转换对于简单的格式说明符来说过于复杂。它们可以单独使用，也可以附加到某些视图对象上，在视图中自动调用以在数据模型和视图之间进行转换。

Cocoa 框架提供了两个具体实现：`NSNumberFormatter`和`NSDateFormatter`。如果有需要，您可以子类化`NSFormatter`并创建自己的格式化器。

多年来，某些`NSFormatter`类发生了重大变化。为了向后兼容，除非另行配置，这些类可能表现出旧版行为。请根据您的编程环境检查`NSFormatter`行为。为获得最佳体验，请在可用时使用现代行为。如果这不是默认行为，清单 8-7 显示了如何在开始使用它们之前设置现代行为。

**清单 8-7. 设置现代格式化行为**

```objc
// 在创建任何日期或数字格式化器对象之前发送这两条消息。
// 在应用程序初始化中执行此操作是一个好地方。
// 这将为所有新的日期和数字格式化器对象设置行为模式。

[NSDateFormatter setDefaultFormatterBehavior:NSDateFormatterBehavior10_4];
[NSNumberFormatter setDefaultFormatterBehavior:NSNumberFormatterBehavior10_4];

// 或者，您可以在创建每个日期和数字格式化器后单独设置其行为。

NSDateFormatter *dateFormatter = [NSDateFormatter new];
[dateFormatter setFormatterBehavior:NSDateFormatterBehavior10_4];
NSNumberFormatter *numberFormatter = [NSNumberFormatter new];
[numberFormatter setFormatterBehavior:NSNumberFormatterBehavior10_4];
```

格式化器使用所需的格式化选项进行配置，然后重复用于转换值。`-(NSString*)stringForObjectValue:(id)value`方法使用当前配置的格式将对象值转换为字符串。`-(BOOL)getObjectValue:(id*)object forString:(NSString*)string errorDescription:(NSString**)error`执行反向转换，为给定字符串返回一个值。如果转换成功，它返回`YES`。如果不成功，它返回`NO`并创建一个描述问题的错误对象。

[¹]: Apple, Inc., “String Programming Guide for Cocoa,” http://developer.apple.com/documentation/Cocoa/Conceptual/Strings/index.html, 2008.
[²]: The IEEE and The Open Group, “The Open Group Base Specification Issue 6, IEEE Std 1003.1,” http://www.opengroup.org/onlinepubs/009695399/functions/printf.html, 2004.

##### NSNumberFormatter

`NSNumberFormatter`格式化十进制数字。它实现了两个便捷方法：`-stringFromNumber:`和`-numberFromString:`。配置格式化器的最简单方法是使用`-setNumberStyle:`的预定义格式化样式之一。样式会考虑用户的语言、用户区域设置的文化惯例以及他们的全局显示偏好。表 8-9 列出了部分预定义的数字格式样式。

**表 8-9. 预定义数字格式样式**

| 样式 | 用于 |
| --- | --- |
| `NSNumberFormatterDecimalStyle` | 十进制数 |
| `NSNumberFormatterCurrencyStyle` | 本地货币 |
| `NSNumberFormatterPercentStyle` | 百分比 |
| `NSNumberFormatterScientificStyle` | 科学计数法 |
| `NSNumberFormatterSpellOutStyle` | 自然语言（例如“二十二”） |

如果预定义样式之一不够用，您可以使用数字格式模式配置格式化器。数字格式字符串使用 Unicode 数字格式模式标准（Unicode 技术标准#35[³]），不应与`-stringWithFormat:`及类似方法使用的格式说明符混淆。数字格式模式形成一个模板，描述显示多少有效数字、使用什么标点符号，并且可能包含零值和负值的备用模板。表 8-10 显示了一些示例数字格式模式以及为美国英语用户格式化数字时的结果字符串。表 8-11 显示了西班牙用户的相同结果。

**表 8-10. 英语（美国）中的示例数字格式模式**

| 数字格式 | 123 | 4.5 | 6 | -0.98765 |
| --- | --- | --- | --- | --- |
| `#,##0.5` | 7.0 | | | |
| `1,234.6` | | | | |
| `0.0` | -1.0 | | | |
| `#,##0.###;zero;#,##0.###-` | 7 | | | |
| `1,234.56` | | | | |
| `zero` | | | | |
| `0.988-` | | | | |
| `000000.0000` | `000007.0000` | | | |
| `001234.5601` | | | | |
| `000000.0000` | | | | |
| `-000000.9876` | | | | |
| `00.0%` | `700.0%` | | | |
| `123456.0%` | | | | |
| `00.0%` | | | | |
| `-98.8%` | | | | |

**表 8-11. 西班牙语（西班牙）中的示例数字格式模式**

| 数字格式 | 123 | 4.5 | 6 | -0.98765 |
| --- | --- | --- | --- | --- |
| `#,##0.5` | 7,0 | | | |
| `1.234,6` | | | | |
| `0,0` | -1,0 | | | |
| `#,##0.###;zero;#,##0.###-` | 7 | | | |
| `1.234,56` | | | | |
| `zero` | | | | |
| `0,988-` | | | | |
| `000000.0000` | `000007,0000` | | | |
| `001234,5601` | | | | |
| `000000,0000` | | | | |
| `-000000,9876` | | | | |
| `00.0%` | `700,0%` | | | |
| `123456,0%` | | | | |
| `00,0%` | | | | |
| `-98,8%` | | | | |

最后，您可以通过直接单独设置`NSNumberFormatter`的近四十个属性中的任意一个来对格式化进行极其精细的控制。

数字格式样式会自动本地化结果。格式字符串会为您本地化某些元素，例如小数点分隔符，但并非全部。单独设置各种`NSNumberFormatter`属性可赋予您最终控制权，但会忽略用户设置的本地化和显示偏好。

##### NSDateFormatter

[³]: Mark Davis, “Unicode Technical Standard #35, Locale Data Markup Language,” http://unicode.org/reports/tr35/tr35-6.html, 2006.



与`NSNumberFormatter`类似，`NSDateFormatter`是一个用于翻译日历日期和时钟时间的专用格式化器。它也可以通过预定义样式、日期格式模式或通过独立属性进行配置。预定义样式列于表 8-12 中，并附有英国英语用户的使用结果。

**表 8-12. 英文（英国）预定义日期格式化样式**

| 日期样式 | 结果 |
|---|---|
| `NSDateFormatterShortStyle` | 15/02/2005 |
| `NSDateFormatterMediumStyle` | 15 Feb 2005 |
| `NSDateFormatterLongStyle` | 15 February 2005 |
| `NSDateFormatterFullStyle` | Tuesday, 15 February 2005 |

`Unicode Technical Standard #35` 还定义了日期格式模式，它们与 `java.util.Formatter` 支持的日期和时间格式说明符非常相似。Objective-C 的字符串格式化并不尝试支持哪怕最简单的日期和时间格式化，而是将这一任务委托给功能更强大的 `NSDateFormatter` 类。表 8-13 展示了一些简单的日期格式模式，结果基于美国英语用户。

**表 8-13. 英文（美国）日期格式模式示例**

| 日期格式 | 结果 |
|---|---|
| `yyyy-MM-dd 'at' HH:mm` | 2005-02-15 at 13:40 |
| `EEEE MMMM d, yyyy` | Tuesday February 15, 2005 |
| `'millisecond' A 'of Julian day' g` | millisecond 49259000 of Julian day 2453417 |
| `QQQQ` | 1st quarter |

### 总结

Objective-C 中的字符串和包装类在概念上与 Java 中的并无太大差异。包装类更加通用，可以包装的不仅仅是内置的值类型。与 Java 不同的是，Objective-C 的字符串可以被继承。这意味着它们可能是可变的，但通常不是，并且通常可以像在 Java 中一样使用。请记住在格式字符串中使用 `%@` 而不是 `%s`，并利用 `NSFormatter` 类进行复杂的值转换。

---

## 第 9 章：垃圾回收

垃圾对象的回收最显著的特点就是程序员无需执行的操作。在理想情况下，你只需忽略垃圾回收的机制，让运行时系统处理细节即可。但在某些情况下，你确实需要关注垃圾回收。弱引用和终结方法需要你对导致对象成为垃圾的原因以及它们如何被回收有一定的理解。

Objective-C 2.0 的垃圾回收在总体上与你体验过的 Java 垃圾回收类似。大多数时候，你只需忘记它，它就能“正常工作”。Objective-C 的垃圾收集器与现代 Java 实现处于同一水平。它是一个保守的、多代的垃圾收集器，在后台的独立线程上运行。它能从容处理所有典型的编程问题，如循环引用。它高效且极少妨碍你的应用程序。

在本章后面，你将学习如何编写 `-finalize` 方法以及如何使用弱引用。如果你只编写“纯”Objective-C 代码（仅处理对象和对象指针），这可能就是你所需了解的全部内容，学完后你可以直接跳到下一章。只有当你在 Objective-C 的范畴之外，接触到 C 指针的“荒野”时，才需要继续阅读。

正如常言道，细节决定成败。由于 C 语言对内存访问的宽松（有人称之为无政府状态）权限，Objective-C 的垃圾回收变得复杂。由于缺少 Java 所强加的程序员与物理内存之间的严格隔离，因此无法绝对确定哪些对象正在被引用，哪些没有。Objective-C 采用了一种尽力而为的方法，在效率和便利性之间取得平衡。然而，这种方法中存在一些微妙的漏洞，可能导致对象泄漏或过早地被回收。学习如何识别并避免这些情况是本章后半部分的重点。

## 垃圾回收的理论

现代垃圾回收系统通过确定可达对象图来运作。这是指你的应用程序拥有引用，或能够通过其他引用获得引用的对象集合。

该图从根对象集开始。这包括全局（静态）变量中的对象指针、每个线程栈中的对象指针以及 CPU 寄存器中的任何对象指针。然后，通过添加所有被根对象引用的对象，以及被这些对象引用的所有对象，以此类推，直到集合中不再有任何未包含的对象引用，从而扩展该集合。这就形成了完整的可达对象集。不在该集合中的任何对象都有资格被回收和销毁。

导致一个对象被回收，只需清除（有时称为“遗忘”）所有指向该对象的可达引用。该对象便会落入可达对象集之外，从而被回收。

### 选择使用垃圾回收

Objective-C 的垃圾回收并非普遍可用。并非所有的开发和运行时环境都支持它，即便支持，也通常不是默认的内存管理服务。在撰写本文时，Objective-C 垃圾回收是一项功能，而非标准。请检查你的部署环境（平台和操作系统版本）的能力，以确定是否支持垃圾回收。如果支持，请确保在你的项目中启用它——参见第 4 章的示例。如果垃圾回收不可用，你将不得不采用传统的 Objective-C 内存管理。第 24 章将对此进行更详细的说明。

在大多数运行时环境（如 Cocoa 应用程序）中，垃圾收集器会自动为你启动。如果你正在构建一个使用 Objective-C 的命令行工具，则必须通过在你的工具启动时尽早调用 C 函数 `objc_startCollectorThread()` 来启动垃圾收集器。

## 使用垃圾回收编写代码

选择是否使用垃圾回收将决定你的编码风格。可以编写在 GC（垃圾回收）和非 GC（手动管理内存）运行时环境中都能运行的 Objective-C 代码，但这很复杂，而且很少有必要这样做。一个例子是由其他应用程序动态加载的插件框架，其中一些使用 GC，另一些不使用。对于直接的应用程序开发，请在假定使用垃圾回收或手动管理内存的前提下编写所有代码。本章为在垃圾回收环境中编写 Objective-C 提供了指导。第 24 章解释了传统的手动内存管理。

在 Objective-C 中创建新对象的常见方法如下：

`[SomeClass new]`

`[[SomeClass alloc] init]`

`[[SomeClass alloc] initWith:…]`

`[SomeClass someConvenienceConstructor]`

这些与非 GC 环境中创建对象的方式完全相同。在 GC 环境中，你基本上就完成了。一旦对象创建完成，你无需再做其他任何事情；当你不引用它时，垃圾收集器会处理它。

**注意：** 如果你正在阅读为非 GC 环境编写的代码，你会看到对象被发送了 `-retain`、`-release` 和 `-autorelease` 消息。在垃圾收集环境中，这些消息会被忽略。如果你要将代码从非 GC 环境移植到 GC 环境，可以安全地删除所有 `-retain`、`-release` 和 `-autorelease` 消息。此外，`-retainCount` 和 `-dealloc` 消息也会被拦截。`-retainCount` 返回的值没有意义。对象的 `-dealloc` 方法将永远不会被执行，即使你自己尝试向它发送 `-dealloc` 消息——无论如何你都不应该这样做。


在 GC 环境中，属性和手动编写的 setter 方法应使用简单赋值。清单 9-1 展示了一个属性和一个假设使用垃圾回收机制而手动实现的 setter 方法。作为对比，还包含了一个适用于非 GC 环境的实现。两种实现具有等效的行为且都是线程安全的。

**清单 9-1：GC 与非 GC 环境下的属性实现**

**GC 环境**

```objc
@interface Doll : NSObject {
    NSColor *hairColor;
    NSColor *eyeColor;
}
@property (assign) NSColor *hairColor;
@property (assign) NSColor *eyeColor;
@end

@implementation Doll
@synthesize hairColor;

- (NSColor*)eyeColor {
    return eyeColor;
}

- (void)setEyeColor:(NSColor*)color {
    eyeColor = color;
}
@end
```

**非 GC 环境**

```objc
@interface Doll : NSObject {
    NSColor *hairColor;
    NSColor *eyeColor;
}
@property (retain) NSColor *hairColor;
@property (retain) NSColor *eyeColor;
@end

@implementation Doll
@synthesize hairColor;

- (NSColor*)eyeColor {
    @synchronized(self) {
        return [[eyeColor retain] autorelease];
    }
}

- (void)setEyeColor:(NSColor*)color {
    @synchronized(self) {
        if (eyeColor != color) {
            [eyeColor release];
            eyeColor = [color retain];
        }
    }
}
@end
```

**编写 `-finalize` 方法**

当一个对象的所有引用都消失后，该对象就具备了被回收和销毁的资格。在 Objective-C 中，与 Java 一样，垃圾回收器会在每个可回收对象被销毁前向其发送一条 `-finalize` 消息。行为良好的 `-finalize` 方法的规则与 Java 中的非常相似，因此这里仅作总结：

- 不要尝试执行耗时的清理或资源回收操作。这些操作应在对象被遗忘之前完成。
- 不要试图断开对象图或通过将实例变量设置为 `nil` 来帮助垃圾回收器（这是多余的）。
- 不要尝试将对象从集合或视图层次结构中移除（这是多余的）。
- 在对象接收到 `-finalize` 消息之前，所有对它的弱引用都已被断开。
- 向其他对象发送消息通常是安全的，但应将其保持在最低限度。
- 对象可能会以任意顺序被终止，因此你的对象应准备好（在接收 `-finalize` 之前或之后）接收来自其他可回收对象的消息。
- 接收到 `-finalize` 消息的对象不应尝试复活可回收对象，也不应通过创建对 `self` 的强引用来尝试复活自身。
- 每个对象只会收到一条 `-finalize` 消息。
- 对象的 `-finalize` 方法必须是线程安全的。

**创建弱引用**

在某些情况下，你的应用程序希望保持对一个对象的引用，但又不希望阻止垃圾回收器回收它。典型的情况是一个缓存或对象池（假设它们是图形图像），被许多其他对象使用。单一的资源对象缓存或池使得各个对象能够方便地获取对这些公共资源对象的引用。当应用程序中的所有对象都处理完某个资源后，它们都会“忘记”这个对象。

理想情况下，该资源对象现在应该被回收，但来自池的对该对象的唯一引用阻止了它被收集。

这可以通过使用弱引用来解决。弱引用是指向一个对象的指针，垃圾回收器在构建可达对象集时不会对其进行遍历。从垃圾回收器的角度来看，它不是一个引用，也不会阻止该对象被回收。

在 Java 中，弱引用是通过 `java.lang.ref.WeakReference` 对象建立的。要创建一个弱引用，需创建一个 `WeakReference` 对象来持有对弱引用对象的引用，如清单 9-2 所示。在 Objective-C 中，在任何对象指针后附加 `__weak` 修饰符即可创建弱引用。


### 创建弱引用

**Java**

```java
String name = "Clarence";
WeakReference weakName = new WeakReference(name);
name = null;
...
name = (String)weakName.get();
if (name != null) {
    // ... name 尚未被回收
}
```

**Objective-C**

```objectivec
__weak NSString *name = @"Clarence";
...
if (name != nil) {
    // ... name 尚未被回收
}
```

当垃圾回收器确定某个对象没有其他强引用，且该对象符合回收条件时，`__weak` 对象指针会被设置为 `nil`。垃圾回收器保证在对象被终结并销毁之前，所有指向该对象的 `__weak` 引用都会被设置为 `nil`。

为清晰起见，所有非弱引用均视为强引用。Objective-C 不支持软引用或虚引用，也没有引用队列，因此当垃圾回收器决定回收某个对象时，你的对象不会收到任何通知。

为了便于通过弱引用管理对象组，Java 和 Objective-C 都提供了专门的集合，这些集合持有对象组的弱引用，并在对象被回收时优雅地将其移除。相关集合见表 9-1。

**表 9-1.** 弱引用集合类

| 弱引用 Java 集合 | 弱引用 Objective-C 集合 |
|----------------------|-----------------------------|
| `WeakHashMap` | `NSHashTable` |
| | `NSMapTable` |
| | `NSPointerArray` |

`NSHashTable` 实现了一个通用集合。它很大程度上与传统的 `NSMutableSet` 类一致，但可以通过不同的“特性”进行配置，以定义它如何处理集合中的每个元素。其中一个最实用的选项是使用构造函数 `[NSHashTable hashTableWithWeakObjects]` 将集合配置为对其所有对象使用弱引用。这将创建一个可变的对象集合，如果集合中的对象缺少任何强引用，它们就可以被回收。对象被回收时，它会从集合中直接消失。

类似地，`NSMapTable` 是一个键/值对的可变字典（映射）。与 `java.util.WeakHashMap` 不同，`NSMapTable` 可以创建为其键使用强引用或弱引用、为其值使用强引用或弱引用的实例，如表 9-2 所示。集合中的键/值对在任一对象被回收时都会被移除。

**表 9-2.** `NSMapTable` 构造函数

| 构造函数 | 键指针 | 值指针 |
|-------------|--------------|----------------|
| `[NSMapTable mapTableWithStrongToStrongObjects]` | 强 | 强 |
| `[NSMapTable mapTableWithWeakToStrongObjects]` | 弱 | 强 |
| `[NSMapTable mapTableWithStrongToWeakObjects]` | 强 | 弱 |
| `[NSMapTable mapTableWithWeakToWeakObjects]` | 弱 | 弱 |

最后，`NSPointerArray` 集合与 `NSMutableArray` 集合类似，区别在于它允许集合中的项目为 `nil`（或 `NULL`）。它旨在与通用指针一起使用（通常超出垃圾回收的范围），但可以通过构造函数 `[NSPointerArray pointerArrayWithWeakObjects]` 配置为使用弱对象指针。当对象被回收时，其在集合中的指针条目会被设置为 `nil`。这不会改变集合中的项目数量，只会改变其内容。

有关集合类的更多信息，请参阅第 16 章。

### 创建强引用

你可以通过编程方式，动态地将对象指针提升到对象的根集合中，从而创建强引用，即使你的应用程序并未持有对该对象的强引用。例如，如果你创建了一个自主控制器，它自行执行操作（可能响应通知或 C 回调，而这些回调本身就是弱引用），而你不需要持有对该控制器的引用，那么这种方法会很有用。清单 9-3 中的代码使用 `-disableCollectorForPointer:` 方法阻止对象被垃圾回收。若要再次使该对象可被回收，请为每条 `-disableCollectorForPointer:` 消息配以一条 `-enableCollectorForPointer:` 消息。

**清单 9-3.** 阻止对象被回收



`EventDaemon *eventDaemon = [EventDaemon new];`

`[[NSGarbageCollector defaultCollector] disableCollectorForPointer:eventDaemon]; eventDaemon = nil;` // EventDaemon 将不会被回收

`__strong` 类型修饰符与 `__weak` 相对应。在 Objective-C 中，所有对象指针和标识符（`id`）默认都是 `__strong`。将其显式声明为 `__strong` 是多余的，但也是允许的。`__strong` 修饰符主要用于处理既非 `__strong` 也非 `__weak` 的其他类型指针，使你可以利用 Objective-C 的垃圾回收器来管理非对象内存的分配。

请参阅本章的“分配可回收内存”一节，以及第 24 章中关于使用垃圾回收管理 Core Foundation 结构的章节。

## 促进垃圾回收

通常情况下，你不需要直接与垃圾回收系统交互。如果需要，`NSGarbageCollection` 类提供了该服务的面向对象接口。与 Java 中类似，有一些方法可以促使垃圾回收器开始回收对象。这些方法并不会强制垃圾回收器执行操作——例如，它可能刚刚完成对象回收，因此再次回收将是浪费时间。它只是向 GC 建议，现在可能是执行回收的合适时机。

此外，你可以通过发送 `-disable` 消息来临时禁用 Objective-C 中的垃圾回收。在性能要求极高的代码段中，你可能会这样做，但我不建议长时间禁用。发送 `-enable` 消息可恢复回收。控制垃圾回收器的常用方法列于表 9-3 中。

***表 9-3.** 垃圾回收器控制方法*

| 方法 | 描述 |
| --- | --- |
| `+[NSGarbageCollector defaultCollector]` | 返回当前进程的 `NSGarbageCollector` 单例实例。 |
| `-[NSGarbageCollector disable]` | 关闭垃圾回收。 |
| `-[NSGarbageCollector enable]` | 在发送 `-disable` 后重新启动垃圾回收。每个 `-disable` 必须与一个 `-enable` 成对出现。 |
| `-[NSGarbageCollector collectIfNeeded]` | 如果内存消耗阈值已超过，则建议垃圾回收器开始一个回收周期。 |
| `-[NSGarbageCollector collectExhaustively]` | 建议垃圾回收器开始一个完整彻底的回收周期。 |

[www.it-ebooks.info](http://www.it-ebooks.info/)

### GC 与非 GC 指针

Java 中的垃圾回收既是确定性的，也是同质的；所有引用都指向对象。而在 Objective-C 中，指针几乎可以指向任何东西。它们可以指向对象、块、栈上的结构、其他结构内部的结构、数组元素，甚至可能填充随机的无意义值。因此，显然无法绝对确定地判断所有可达对象的集合。

Objective-C 的解决方案是忽略大多数指针，只关注那些（应该）引用对象的指针。这导致了内存管理技术的混合使用。Objective-C 对象使用垃圾回收器，而传统的 C 内存分配则沿用其传统设计模式进行管理。

本节将解释哪些指针由垃圾回收服务自动管理，哪些不是，以及你对这些指针有何控制权。同时，还将强调非托管指针可能引发问题的场景以及如何处理这些问题。

#### 写入屏障

Objective-C 使用一种称为写入屏障的技术来跟踪其管理的指针引用。编译器会将所有对 `__strong` 和 `__weak` 指针的赋值替换为一个快速函数调用，该函数执行赋值操作并将指针注册到垃圾回收系统。请记住，所有 Objective-C 对象指针默认都是 `__strong`的。每个对象指针的赋值都会被注册到垃圾回收器中，回收器利用这些信息来确定可达对象的集合。

#### 分配可回收内存



您也可以让垃圾回收器通过显式地将指针类型指定为`__strong`或`__weak`，然后将指针设置为一个托管的内存块，从而管理指向分配内存块的任何指针。垃圾回收器会像对待其他对象引用一样对待该指针，并在该内存不再可达时将其回收。此技术有两个前提条件：

- 所有指向内存分配的指针都必须声明为`__strong`（或`__weak`，视情况而定）。
- 使用`NSAllocateCollectable(int size, int options)`来分配内存。
- 如果可回收内存块的内容包含`__strong`或`__weak`指针，则`NSAllocatableCollectable`的`options`参数应为`NSScannedOption`。否则，传递`0`。清单 9-4 演示了如何分配一个由垃圾回收器管理的任意内存块。请注意，这是对上一章中使用`NSMutableData`对象存放数组的代码片段的改写。

**清单 9-4. 分配垃圾回收的内存块**

```
- (__strong NSPoint*)pointsForObjects:(NSArray*)objects

{

    __strong NSPoint *points;

    unsigned int count = [objects count];

    unsigned int i;

    points = (NSPoint*)NSAllocateCollectable(count*sizeof(NSPoint),0);

    for (i=0; i<[objects count]; i++) {

        points[i] = …

    }

    return points;

}
```

## 垃圾回收的陷阱

这里列举了一些最常见的垃圾回收相关风险及其解决方法。

##### 内部指针

指向可回收对象内部结构的指针不能阻止该对象被回收。如果对象被过早回收，指针将变得无效。清单 9-5 中的示例演示了此问题。

**清单 9-5. 内部指针**

```
NSData *data = [NSData dataWithData:originalData];

const char *bytes = [data bytes];
```

假设清单 9-5 中的`data`变量之后再也没有被引用。紧接着这两行代码之后，`NSData`对象可能被回收，导致`bytes`变量指向无效内存。原因在于`[data bytes]`语句是该方法中对`NSData`对象的最后一个可达引用。Objective-C 编译器在回收自动变量方面可能非常激进。编译器很可能将`data`和`bytes`分配到同一个 CPU 寄存器，因为它们的生命周期不重叠。因此，当`bytes`赋值完成时，`NSData`对象指针已消失，该对象变得可回收。

解决方案是在最后一次使用任何内部指针之后，至少再引用一次`data`对象。引用方式无关紧要，包括向它发送无关的`-retain`和`-release`消息。我个人偏好这种技术，因为这些消息用于经典的内存管理，并且它们明确地表示“我正在这里进行内存管理”，事实也确实如此。

另一种替代方案是使用可回收的内存块，这在前面“分配可回收内存”一节中已有描述。

##### 不透明指针

分配给既非`__strong`也非`__weak`（即不受写屏障保护）的指针的对象，不会被垃圾回收器考虑。当通过`void*`或其他不透明指针类型传递对象时，这可能会成为一个问题。清单 9-6 演示了在传递一个字典对象作为稍后发送的消息的上下文时出现的问题。指向该对象的`void*`无法阻止它在消息发送之前被回收。

**清单 9-6. 将对象赋值给不透明指针**

```
NSDictionary *info = …;

[NSApp beginSheet:sheetWindow

        modalForWindow:window

         modalDelegate:delegate

        didEndSelector:@selector(sheetDidEnd:returnCode:contextInfo:);

           contextInfo:(void*)info];

…

- (void)sheetDidEnd:(NSWindow*)sheet

        returnCode:(int)returnCode

       contextInfo:(void *)contextInfo

{

    NSDictionary *info = (id)contextInfo;

    id value = [contextObject objectForKey:@"Value"];

    …

}
```

在清单 9-6 的第一部分中，创建了一个字典，用于向`didEndSelector:`参数中指定的模态面板完成方法传递一个或多个值。然而，由于`contextInfo:`参数不是一个`__strong`指针，`NSDictionary`对象在`beginSheet:…`消息发送之后立即变得不可达——从垃圾回收器的角度看。

这个问题有两个简单的解决方案。第一种是使用 Core Foundation 函数`CFRetain(id)`和`CFRelease(id)`来给对象一个非零的保留计数。如前所述，Objective-C 的垃圾回收器与传统的 C 内存管理共存。`CFRetain`会增加对象的保留计数——如同该对象也被 C 代码使用一样——从而防止它被回收。一个更面向对象的解决方案是在模态面板完成方法中使用`+[NSGarbageCollector disableCollectorForPointer:]`来防止对象被回收，并使用`+[NSGarbageCollector enableCollectorForPointer:]`使其再次可回收。

##### 枚举弱集合

枚举弱对象指针集合时必须格外小心。随着对象被回收，集合的内容和元素计数可能会自发地改变。这可能导致枚举器对象和`for(;;)`循环行为异常。

为避免这种情况，要么使用 Objective-C 的快速枚举，要么创建一个使用强引用的临时不可变集合，直到枚举完成。快速枚举在第 16 章中介绍。

##### 未初始化的栈引用

由于 Objective-C 垃圾回收器检测栈上自动变量中强引用的方式，它可能会误认为一个未初始化的自动变量包含强引用。你可以严格初始化所有自动变量，或者简单地偶尔调用 C 函数`objc_clear_stack(0)`。该函数应在尽可能低的合理栈帧中调用——理想情况下从线程的顶层循环中调用。调用该函数时，栈上的所有方法应尽可能少地包含未初始化的自动变量。如果线程正在运行一个运行循环，则无需执行此操作。每个运行循环会自动为你调用`objc_clear_stack(0)`。

##### 其他陷阱

还有其他一些逐渐变得更晦涩的情况，可能导致对象泄漏或被过早回收。如果你认为在使用垃圾回收时遇到问题，请参考苹果公司发布的《垃圾回收编程指南》[¹]。

### 应避免的设计模式

以下是在编写使用垃圾回收的程序时应避免的一些编程模式：

- 你不能重写、拦截或发送`-release`、`-dealloc`或`-retainCount`方法。
- 在使用托管内存时，委托、父对象和观察者引用通常被描述为“弱”引用，因为它们不会被保留——以避免循环保留。这些实际上并非弱引用，因此不应声明为`__weak`。垃圾回收器在处理循环引用方面没有问题。
- 不要使用对象的生命周期来管理昂贵的资源。通常，一个对象被创建出来仅仅是为了管理一个大的缓冲区或计算出的对象池。一旦对该对象的所有引用都被释放，它就会释放底层资源。使用垃圾回收时，请实现你自己的引用计数，或者创建一个字典（映射），其中包含每个客户端对象作为键，以及对资源的引用作为值。每个客户端对象在完成资源使用后应从集合中移除自己，从而允许在最后一个客户端被移除时回收该资源。
- 不要为 Objective-C 对象使用分配区。垃圾回收器要求所有托管对象都在默认区域中分配。

### 调试

[¹]: 译者注：原文中`1`为注释标记，但在提供文本中未找到对应注释内容。



此外，还有一些调试辅助工具。如果将环境变量 `OBJC_PRINT_GC` 设置为 `YES`，垃圾回收框架便会向控制台输出诊断信息。

如果你怀疑某个指针是否被写入屏障保护，可以使用编译器选项 `-Wassign-intercept` 来帮助发现代码中哪些位置插入了写入屏障。

1Apple, Inc., *垃圾回收编程指南*，http://developer.apple.com/documentation/Cocoa/ Conceptual/GarbageCollection/，2008 年。

[www.it-ebooks.info](http://www.it-ebooks.info/)

## 第 9 章 ■ 垃圾回收

### 小结

从表面上看，Objective-C 的垃圾回收与 Java 不相上下——你只需将其开启即可。强引用和弱引用是 Java 程序员熟悉的概念，在 Objective-C 中行为也几乎完全相同。当处理其他指针类型时需要格外小心，因为非对象指针通常不会自动为你管理，同时还要警惕与不受垃圾回收器管理的指针打交道时可能出现的陷阱。

[www.it-ebooks.info](http://www.it-ebooks.info/)

## 第 10 章 ■ 内省

内省（反射）是指探索对象相关信息的行为，这些信息通常被称为元数据：对象的类、实现的方法、声明的属性、遵循的协议（接口）等等。常见的问题（例如“这个对象属于哪个类？”“我能将这个对象视为特定类吗？”“这个对象实现了某个特定方法吗？”）都很容易回答。本章将首先介绍如何回答这些常见问题，然后深入探讨对对象和类的更深入探索。

本章还将探讨键值编码，这是一种与内省密切相关的技术。

键值编码（简称 KVC）允许你以符号方式访问对象的属性。举一个简单的例子：有一个 `Person` 类，包含 `NSString *name`、`Person *mother` 和 `Person *father` 这些属性。

KVC 允许你通过路径 `@"name"` 获取一个人的名字，并通过路径 `@"mother.name"` 获取其母亲的名字。KVC 并非内省；KVC 不会告诉你对象实现了哪些属性。但它与内省关系密切，了解它能为你做什么非常有用。你可能会更倾向于使用 KVC 而不是自行执行内省。同时，你还需要了解如何设计你的类，使其能够与 KVC 配合使用。

出于实用和理念两方面的原因，Objective-C 的内省技术更侧重于发现单个特征，而不是先判断对象属于哪个类，再据此推断其拥有的方法和属性。如果你想了解某个对象能否打开 URL，应该判断该对象是否实现了 `-openURL:` 方法。这比将其类与你认为实现了 `-openURL:` 方法的类进行对比更好。你对对象类的假设越少，你的代码就越健壮、越灵活。以下是关于对象最常见的几个问题：

• 这个对象实现了某个特定方法吗？

• 这个对象是某个特定类的实例吗？

• 这个对象是某个特定类或其任何子类的实例吗？

在 Objective-C 中，这三个问题最容易回答。它们是按效率和速度排序的。判断对象是否实现了特定方法，比判断它是否属于某个类成员有两个优势：它更直接地指向对象的功能性问题，而且效率高得多。

这并不是说你不应该测试对象的类。这样做有很多合理的理由。但是，如果你测试对象的类是为了推断它实现了某些方法，那么请考虑更直接地测试断言本身。

### 测试方法

Java 没有一种简单、简洁的方法来判断对象是否实现了特定方法。



## 第 10 章：自省

相反，Java 程序倾向于创建实现方法功能组的类和接口。然后程序员会测试对象是否属于这些类或接口的成员。由于 Java 的强类型检查和严格的继承模型，这种方法在 Java 中效果良好。

**Objective-C** 的类结构更加宽松和动态，因此在 Java 中可以做出的假设在这里并不那么适用。判断一个对象是否实现了某个特定方法的首选方法是直接测试该方法，如代码清单 10-1 所示。

**代码清单 10-1.** 测试特定方法的实现

**Java**

```
Method method = null;
try {
    Class objectClass = object.getClass();
    Class[] paramTypes = { };
    method = objectClass.getMethod("intValue", paramTypes);
} catch (Exception e) {
}
if (method != null)
    … // object can convert itself into an int
```

**Objective-C**

```
if ([object respondsToSelector:@selector(intValue)])
    … // object can convert itself into an int
```

`-respondsToSelector:` 消息接受一个选择器常量，如果接收者响应该方法（即实现了该方法），则返回 `YES`。参数可以是使用 `@selector()` 指令生成的选择器常量，也可以是 `SEL` 类型的值。代码清单 10-2 演示了如何使用 `-respondsToSelector:` 有选择地向一组监听器发送消息。该方法会向所有实现了特定方法的监听器发送通知。这使得每个监听器只需选择实现这些方法，就能接收到特定的消息。当发送者广播监听器未实现的消息时，该监听器会被忽略。

**代码清单 10-2.** 仅向实现该方法的对象发送消息

```
- (void)updateListenersUsingSelector:(SEL)sel
{
    for ( id listener in listeners ) {
        if ([listener respondsToSelector:sel])
            [listener performSelector:sel withObject:self];
    }
}
```

发送消息 `-updateListenersUsingSelector:@selector(startingChat:)` 会向每个实现了 `-startingChat:` 方法的监听器发送通知。这种基于对象实现的方法来识别对象角色的做法被称为非正式协议，在第 5 章中有更详细的描述。

## 测试类的成员关系

Java 的 `instanceof` 运算符用于测试一个对象是否是某个类的实例、该类的子类、实现了某个接口或该接口的子接口。秉持其极简主义的传统，**Objective-C** 语言本身没有成员运算符。相反，类框架在根类 `NSObject` 中实现了表 10-1 列出的方法。

**表 10-1.** 类和协议成员关系测试

| 消息 | 测试内容 |
| --- | --- |
| `-isKindOfClass:(Class)class` | 对象是该类的实例，或是该类的子类的实例。 |
| `-isMemberOfClass:(Class)class` | 对象是某个特定类的实例。 |
| `-conformsToProtocol:(Protocol*)protocol` | 对象遵循该协议，或继承自遵循该协议的类。 |

**Objective-C** 在内部机制和语法上，对类成员关系和协议（接口）采纳的处理方式是不同的。因此，没有与 `instanceof` 运算符完全等价的单一方法。测试类成员关系时使用 `-isKindOfClass:`，测试协议采纳时使用 `-conformsToProtocol:`。代码清单 10-3 说明了每种情况应使用的不同方法和语法。特定类的 `Class` 对象通常通过向该类本身或该类的任何实例发送 `-class` 消息来获得。使用 `@protocol()` 指令生成协议标识符常量。

**代码清单 10-3.** 测试并强制转换类成员关系和协议采纳

**Java**

```
Object object = …
if (object instanceof MyClass) {
    MyClass myObject = (MyClass)object;
    …
}
if (object instanceof MyInterface) {
    MyInterface myObject = (MyInterface)object;
    …
}
```

**Objective-C**

```
id object = …
if ([object isKindOfClass:[MyClass class]]) {
    MyClass *myObject = object;
    …
}
if ([object conformsToProtocol:@protocol(MyProtocol)]) {
    id<MyProtocol> myObject = object;
    …
}
```

### 键值编码

正如本章开头所提到的，键值编码本身并非一种自省技术；它无法描述对象支持哪些属性。然而，KVC 与自省紧密相连，以至于很难将其归入其他主题。

KVC 是一个强大的工具，是许多其他技术（如键值观察、绑定和脚本）的基础。在实现你自己的属性自省解决方案之前，首先要考虑 KVC 是否已经解决了你的问题。将你的属性设计为与 KVC 兼容，能使你的对象与其他技术具有最大的互操作性。本节将解释键值编码的基础知识，以及让你的类兼容 KVC 的技术。

简单来说，键值编码允许你使用标识属性名称的字符串（键）来访问（获取和设置）对象的属性。例如，对象的 `name` 属性可以通过 `[object setValue:@"Earnest" forKey:@"name"]` 来设置，这等效于 `[object setName:@"Earnest"]`。如果 KVC 仅能如此，其用途将十分有限。当属性名称被组合成路径并应用于集合时，KVC 才真正变得有趣起来。

最好的解释是举个例子。代码清单 10-4 展示了可能构成一个学校管理程序的对象。它定义了 `Person`、`Student`、`Parent`、`Teacher` 和 `Period` 类。`Student`、`Parent` 和 `Teacher` 对象都是 `Person` 的子类。

Rachael 是其中一位老师，她正在计划一次家庭实地考察，将包括她班级里的所有学生，以及他们的兄弟姐妹和继兄弟姐妹。她需要她班级里学生及其兄弟姐妹的名字。

**代码清单 10-4.** 使用 KVC 访问对象属性

**Java**

```
package com.apress.java.school;

public class Person {
    public String name;
}

public class Parent extends Person {
    public ArrayList<Student> children;
}

public class Teacher extends Person {
    public ArrayList periods;
    public Period homeroom;
}

public class Student extends Person {
    public ArrayList<Parent> parents;
    public ArrayList classes;
    public Period homeroom;
}

public class Period {
    public ArrayList<Student> students;
    public Teacher teacher;
}

// 从 Rachael 班级的学生中收集兄弟姐妹的名字
Class Teacher teacher = School.teacherWithName("Rachael");
Period homeroom = teacher.homeroom;
HashSet siblingNames = new HashSet();
for ( Student student : homeroom.students ) {
    for ( Parent parent : student.parents ) {
        for ( Student child : parent.children ) {
            if (!siblingNames.contains(child.name)) {
                siblingNames.add(child.name);
            }
        }
    }
}
return siblingNames;
```

**Objective-C**

```
@class Period;

@interface Person : NSObject {
    NSString *name;
}
@end

@interface Parent : Person {
    NSMutableArray *children;
}
@end

@interface Teacher : Person {
    NSMutableArray *periods;
    Period *homeroom;
}
@end

@interface Student : Person {
    NSMutableArray *parents;
    NSMutableArray *classes;
    Period *homeroom;
}
@end

@interface Period : NSObject {
    NSMutableArray *students;
    Teacher *teacher;
}
@end

// 从 Rachael 班级的学生中收集兄弟姐妹的集合
Teacher *rachael = [School teacherWithName:@"Rachael"];
return [rachael valueForKeyPath:
```




`@"homeroom.students.@distinctUnionOfArrays.parents.@unionOfArrays.children.name"`；Java 实现采用传统的过程式解决方案，遍历 Rachael 班级中的学生、他们的父母以及子女，并汇总出一组唯一的姓名。而 Objective-C 解决方案则利用了 KVC 的强大能力。路径中的每个标识符都指向接收对象或前一对象的某个属性。如果属性是对象集合，则会将路径的剩余部分应用于集合中的每个元素，并将结果汇总到一个新集合中。因此，路径 `children.name` 会返回一个集合，其中包含了 `children` 属性中每个 `Student` 对象的 `name` 属性。

特殊路径运算符（例如 `@distinctUnionOfArrays`）会对结果执行转换或计算。在此示例中，返回值被缩减为单个唯一对象数组，从而消除了任何重复的名称和集合的集合。

更重要的是，屏幕上的列表视图对象或脚本属性可以绑定到同一条路径，无需编写一行代码，列表即可显示（或脚本属性即可返回）搜索结果。

#### 使用键值编码

使用键值编码非常简单。根类 `NSObject` 定义了 `-valueForKey:(NSString*)key` 和 `-valueForKeyPath:(NSString*)path` 方法。发送其中任一方法即可检索对象的属性值。通过发送 `-setValue:(id)value forKey:(NSString*)key` 或 `-setValue:(id)value forKeyPath:(NSString*)path` 即可设置可变值。

键是单个属性名，而键路径可以是简单的属性名，也可以是包含多个属性名和操作的复杂路径。原始属性值会自动转换为 `NSNumber` 或 `NSValue` 对象，反之亦然。

表 10-2 列出了 Cocoa 框架实现的一些 KVC 操作。运算符会转换其后面路径的结果。表 10-2 中的所有运算符都转换一个值的集合（集或数组）。

**表 10-2.** 键值编码路径运算符

| 运算符 | 结果 |
| --- | --- |
| `@count` | 对象数量 |
| `@avg` | 数值平均值 |
| `@max` | 集合中的最大值 |
| `@min` | 集合中的最小值 |
| `@sum` | 集合中数值的总和 |
| `@distinctUnionOfArrays` | 从数组集合中得到的唯一对象数组 |
| `@distinctUnionOfObjects` | 唯一对象数组 |
| `@distinctUnionOfSets` | 从集合集合中得到的唯一对象集合 |
| `@unionOfArrays` | 从数组集合中得到的对象数组 |
| `@unionOfObjects` | 对象数组 |
| `@unionOfSets` | 从集合集合中得到的对象集合 |

在使用键值观察、绑定和脚本化等技术时，你很可能会间接地使用 KVC。这些技术都使用 KVC 路径来标识被观察、绑定或脚本化的属性。为了使你的对象能够与这些技术协同工作，你需要确保它们符合 KVC 规范。

## 设计 KVC 兼容的类

你定义的大多数属性无需额外工作即可与 KVC 兼容。至少，仅声明一个实例变量（例如代码清单 10-4 中的 `NSString *name`）就定义了一个 KVC 兼容的属性。KVC 将使用实例变量内省来查找并使用该属性。

如果你遵循使用 `@property` 声明的实践，这些属性也将与 KVC 兼容。只需避免使用非标准的 getter 和 setter 名称即可。KVC 优先使用存取器方法，而不是同名的实例变量。如果你实现自己的存取器方法，它们必须遵循标准的 getter 和 setter 模式。表 10-3 列出了与 KVC 配合使用的可接受的存取器方法名称。将每个方法中的斜体 *Property* 替换为你属性的实际名称（注意大小写）。对于不可变属性，只需实现一个 getter；对于可变属性，则需要实现一个 getter 和一个 setter。

**表 10-3.** KVC 兼容的单值存取器名称

| 方法名称 | 角色 |
| --- | --- |
| `-property` | Getter |
| `-getProperty` | Getter |
| `-isProperty` | 布尔型 getter |
| `-setProperty:` | Setter |

作为数组或集合的属性（例如 `@property NSArray *teachers` 或 `@property NSSet *teachers`）会自动与键值编码兼容。若要实现一个非标准数组或集合类型的 KVC 兼容集合属性，你需要实现一些特殊方法。要实现自定义数组属性，请实现以下方法：

- `-(unsigned int)countOfProperty`

至少实现以下方法之一：
- `-(Class*)objectInPropertyAtIndex:(NSUInteger)index`
- `-(NSArray*)propertyAtIndexes:(NSIndexSet*)indexes`

为了提升 getter 性能，可选择性地实现：
- `-(void)getProperty:(id*)array range:(NSRange)range`

可变属性必须至少实现以下方法之一：
- `-(void)insertObject:(Class*)object inPropertyAtIndex:(NSUInteger)index`
- `-(void)insertProperty:(NSArray*)objects atIndexes:(NSIndexSet*)indexes`（性能更优）

可变属性必须至少实现以下方法之一：
- `-(void)removeObjectFromPropertyAtIndex:(NSUInteger)index`
- `-(void)removePropertyAtIndexes:(NSIndexSet*)indexes`（性能更优）

为了提升性能，可变属性可选择性地实现以下任一方法：
- `-(void)replaceObjectInPropertyAtIndex:(NSUInteger)index withObject:(Class*)object`
- `-(void)replacePropertyAtIndexes:(NSIndexSet*)indexes withProperty:(NSArray*)objects`

在这些方法中，斜体 *Property* 名称应替换为正在定义的属性名称，例如 `-(NSUInteger)countOfTeachers`。请注意，如果你选择了单数形式的属性名称，在构造 KVC 兼容的方法名称时，请始终使用单数形式（而非复数形式）。将 *Class* 替换为属性对象的类，例如 `-(Teacher*)objectInTeachersAtIndex:(NSUInteger)index`。

要实现自定义集合属性，请实现以下方法：

- `-(NSUInteger)countOfProperty`
- `-(NSEnumerator*)enumeratorOfProperty`
- `-(Class*)memberOfProperty:(Class*)object`

可变属性必须至少实现以下方法之一：
- `-(void)addPropertyObject:(Class*)object`
- `-(void)addProperty:(NSSet*)objects`

可变属性必须至少实现以下方法之一：
- `-(void)removePropertyObject:(Class*)object`
- `-(void)removeProperty:(NSSet*)objects`

为了提升性能，可变属性可选择性地实现：
- `-(void)intersectProperty:(NSSet*)set`

#### 自定义键值

此外，可以通过重写 `-(id)valueForUndefinedKey:(NSString*)key` 和 `-(void)setValue:(id)value forUndefinedKey:(NSString*)key` 以编程方式实现命名属性。每当尝试获取或设置 KVC 无法识别的属性时，都会向对象发送这些消息。你的代码可以转换名称或合成一个值。

KVC 还定义了验证属性的标准。更多细节请参阅 Apple 发布的《键值编码编程指南》。

### 检查类

本节描述了获取 Class 信息的主要函数。接下来的几节将描述如何执行最常见和最有用的内省操作。鼓励更高级的开发者阅读 Apple 发布的《Objective-C 2.0 运行时参考》。

如同在 Java 中一样，高级内省始于 Class 对象。Java 中的类内省被清晰且逻辑地组织成类层次结构，从 `java.lang.Class` 类开始，向下延伸至 `java.lang.reflect.Method`、`java.lang.reflect.Field` 等。




在 Objective-C 中，情况变得有些晦涩。到目前为止，我讨论的是定义每个类的单个 Objective-C 类对象，并通过每个对象的 `isa` 变量来引用它。这有些误导。严格来说，实际上并不存在一个 `Class` 类。`Class` 类型是一个指向不透明结构体 `objc_class` 的 C 结构体指针。眼尖的读者会注意到，对 `Class` 的引用（例如表 10-1 中的那些）总是 `Class` 而不是 `Class*`，如果它是一个真正的 Objective-C 对象指针的话，情况本应如此。

我可以这样欺骗过去，是因为指向 `objc_class` 结构体的指针像任何 Objective-C 对象一样响应消息。这就是为什么你可以使用 Objective-C 方法调用语法向其发送消息，例如 `[MyClass new]`。

因此，`Class` 类型存在于真实类和 C 结构体之间的一个模糊地带，通常表现得像一个对象，但没有任何正式的类定义。对类和对象进行更深层次的内省涉及使用 C 函数，这些函数接收一个 `objc_class` 结构体指针并返回关于它的信息。这些信息通常以其他 C 结构体或 C 字符串的形式出现。这并不特别困难，但请注意你正在越过面向对象编程的边界，进入 Objective-C 运行时系统的内部。

1. Apple, Inc., *键值编码编程指南*, [`developer.apple.com/documentation/Cocoa/Conceptual/KeyValueCoding/`](http://developer.apple.com/documentation/Cocoa/Conceptual/KeyValueCoding/), 2009.
2. Apple, Inc., *Objective-C 2.0 运行时参考*, [`developer.apple.com/documentation/Cocoa/Reference/ObjCRuntimeRef/`](http://developer.apple.com/documentation/Cocoa/Reference/ObjCRuntimeRef/), 2008.

[www.it-ebooks.info](http://www.it-ebooks.info/)

## 第十章 内省

> **注意：** Objective-C 的运行时系统被设计为完全透明的。与 Java 的 `java.lang.ClassLoader`（其内部机制被保密）不同，Objective-C 类的创建、注册、修改、实例化和消息发送所涉及的函数都可以直接通过 Objective-C 运行时 API 获得。希望实现脚本语言或动态定义和增强运行时类的进阶程序员，可以不受限制地访问与 Cocoa 框架所使用的相同 API。

**表 10-4** 列出了一些用于探索类信息的函数。表中的前四个函数将名称转换为 `Class` 或 `Protocol` 指针，反之亦然。这就是你如何仅凭一个名称就能获得 `Class` 引用的方法。后四个函数只是执行相同功能的包装器，它们接收并返回 Objective-C 字符串对象。包装函数简单地调用前四个函数之一，并为你转换字符串。最后，`class_getSuperclass(Class)` 函数返回一个类的超类引用，如果该类是根类则返回 `Nil`。它相当于向一个对象发送 `-superclass` 消息。

***表 10-4.** 用于内省类的函数*

| 函数 | 返回值 |
| --- | --- |
| `objc_getClass(const char*)` | 具有该名称的 `Class` |
| `class_getName(Class)` | `Class` 的名称 |
| `objc_getProtocol(const char*)` | 具有该名称的 `Protocol` |
| `protocol_getName(Protocol)` | `Protocol` 的名称 |
| `NSClassFromString(NSString *name)` | 具有该名称的 `Class` |
| `NSStringFromClass(Class)` | `Class` 的名称 |
| `NSProtocolFromString(NSString *name)` | 具有该名称的 `Protocol` |
| `NSStringFromProtocol(Protocol*)` | `Protocol` 的名称 |
| `class_getSuperclass(Class)` | `Class` 的超类 |

**清单 10-5** 展示了如何遍历一个对象继承的类。

[www.it-ebooks.info](http://www.it-ebooks.info/)

## 第十章 内省

**清单 10-5.** 向上遍历超类列表

**Java**

```java
Class objClass = object.getClass();
while (objClass!=null) {
  …
  objClass = objClass.getSuperclass();
}
```

**Objective-C**

```objectivec
Class class = [object class];
while (class!=Nil) {
  …
  class = class_getSuperclass(class);
}
```

> **注意：** Objective-C 定义了用于 `Class` 指针的常量 `Nil`。使用 `Class` 指针时，使用 `Nil` 的方式与使用 `nil` 处理对象指针的方式完全相同。

探索协议




