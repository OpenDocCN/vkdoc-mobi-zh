# 从 Objective-C 包装器中获取原始基本类型值

在 Objective-C 中从包装器获取原始基本类型值，或获取不同类型对应的基本类型值，其方式与 Java 中非常相似。`NSNumber` 类提供了多种转换方法，用于返回原始值的最佳表示形式。这些方法列于表 8-2 中。

**表 8-2.** *基本类型转换*

| Java 类型 | Java | Objective-C |
|-----------|------|-------------|
| boolean | `value.booleanValue()` | `[value boolValue]` |
| byte | `value.byteValue()` | `[value charValue]` |
| char | `value.charValue()` | `[value unsignedShortValue]` |
| short | `value.shortValue()` | `[value shortValue]` |
| int | `value.intValue()` | `[value integerValue]` |
| long | `value.longValue()` | `[value longLongValue]` |
| float | `value.floatValue()` | `[value floatValue]` |
| double | `value.doubleValue()` | `[value doubleValue]` |

由于单一的 `NSNumber` 类负责所有原始标量类型的转换，因此在 Objective-C 中类型转换是正交的；任何 `NSNumber` 对象都能将它的值作为 `BOOL`、`char`、`integer` 或浮点值返回。这种对称性在 Java 中被打破，例如 `Boolean` 类不包含 `intValue()` 方法，`Character` 类不包含 `boolValue()` 方法，等等。

转换通常遵循 C 语言中原生类型转换的规则。这意味着，向 `NSNumber` 请求其 `intValue` 与将原始值转换为 `int`（如 `(int)original`）并无区别。如果请求的类型无法表示原始值，不会抛出任何异常。

#### 将字符串转换为标量

在 Java 中，每个包装器类都有多个方法用于解析字符串并解释其标量值，可返回原始类型或对象。例如，`java.lang.Integer` 类包含一个字符串构造函数（`Integer(String)`）、一个接收字符串并返回整数原始值的静态方法（`parseInt(String)`），以及一个接收字符串并返回 `Integer` 对象的静态方法（`valueOf(String)`）。`Boolean`、`Character`、`Short`、`Float` 和 `Double` 类也包含类似的方法。

在 Objective-C 中，`NSString` 类实现了表 8-2 中列出的相同标量转换方法。向字符串对象发送 `-intValue` 消息可将其解释为整数。向其发送 `-floatValue` 可将其解释为浮点数。要将字符串转换为 `NSNumber` 对象，可从转换后的值创建一个新的 `NSNumber`，如 `[NSNumber numberWithInt:[string intValue]]`。

十六进制字符串可以使用 `NSScanner` 对象进行转换。`NSScanner` 是一个用于解析字符串的通用工具类。要将包含十六进制数字的字符串转换为整数，可以为该字符串创建一个扫描器，然后使用 `-[NSScanner scanHexInt:]` 方法进行解析，如代码清单 8-1 所示。

**代码清单 8-1.** 将十六进制字符串转换为整数

```
unsigned int i;
NSScanner *scanner = [NSScanner scannerWithString:@"cafe1234"];
[scanner scanHexInt:&i];
```

`NSFormatter` 提供了另一种且更为复杂的方法来将字符串转换为数字。虽然通常用于将数字转换为字符串，但 `NSFormatters` 是双向的，也能将字符串转换回标量值。本章后续的“格式化”部分将进一步讨论 `NSFormatters`。

Cocoa 框架类中不包含像 Java 的 `java.lang.Integer.parseInt(String s, int radix)` 那样用于任意基数转换的通用数字转换方法。格式化转换仅支持八进制、十进制和十六进制。可以使用 C 库函数 `strtol(…)` 来完成任意基数的转换。这需要将 Objective-C 字符串对象转换为 C 字符串（本章后面会介绍），然后将其传递给 `strtol(…)` 函数或其相关函数。

### 包装数组

在 Java 中，数组本身已是对象，因此语言不需要为它们包装器类。Objective-C 中的原生数组并非对象，但可以使用 `NSData` 类对它们进行包装。`NSData` 为任何无定形的内存块提供了通用包装器。`NSData` 特别方便，因为它能让你摆脱底层 C 语言内存分配函数的束缚，让你像操作对象一样创建和管理大型结构体及原生 C 数组。

`NSData` 封装了一个不可变的字节数组。`NSMutableData` 子类用于管理可变的字节数组。`NSData` 提供了获取字节数组大小和起始地址的方法。`NSMutableData` 扩展了 `NSData`，增加了调整数组大小以及追加、替换和删除字节的方法。表 8-3 列出了创建 `NSData` 对象的常用方式。

**表 8-3.** *常用 `NSData` 构造函数*

| 方法 | 用途 |
|--------|---------|
| `+[NSData dataWithBytes:length:]` | 创建一个新的 `NSData` 对象，其中包含给定地址和长度的内存的副本。 |
| `+[NSData dataWithBytesNoCopy:length:]` | 此构造函数将一个 `NSData` 对象包装在使用 C 语言 `malloc(…)` 函数（底层 C 内存管理）分配的内存块周围。该对象承担这块内存的管理责任，并在对象被销毁时释放它。这是将 C 分配的内存块转换为 Objective-C 对象最有效的方式。 |
| `+[NSData dataWithBytesNoCopy:length:freeWhenDone:]` | 如果 `freeWithDone:` 参数为 `YES`，则此方法等同于 `+[NSData dataWithBytesNoCopy: length:]`。如果参数为 `NO`，则使用 `NSData` 对象包装一个现有的内存块。新对象仅引用在构造时给定的原始地址和长度。程序员有责任确保其引用的内存在 `NSData` 对象的生命周期内有效。此构造函数在避免数据复制的性能开销方面尤为有用。 |
| `+[NSData dataWithContentsOf…:]` | 创建一个 `NSData` 对象，其中包含目标对象内原始数据的副本。此便捷构造函数系列包括 `+dataWithContentsOfFile:`、`+dataWithContentsOfURL:` 等。 |
| `+[NSData dataWithData:]` | 创建一个新的 `NSData` 对象，它是现有 `NSData` 对象的副本。 |
| `+[NSMutableData data]` | 创建一个长度为零的 `NSMutableData` 对象。 |
| `+[NSMutableData dataWithLength:]` | 创建一个给定长度的可变字节数组包装器，并用零填充。 |

由于 `NSMutableData` 是 `NSData` 的子类，所有 `NSData` 的构造函数也同样适用于 `NSMutableData`，例如使用 `[NSMutableData dataWithData:data]` 来创建现有 `NSData` 对象的可变副本。事实上，`+data` 构造函数实际上是由 `NSData` 定义的，只是在其上用途有限。

从原则上讲，`NSData` 对象的内容是不可变的。但实际上，除了硬件限制外，并无任何因素禁止修改其内容。`-[NSData bytes]` 方法返回数组中第一个字节的地址。一旦获得该地址，没有任何东西能阻止你修改该地址上的任何值。我倾向于将 `NSData` 对象用于我不打算修改的内存，而将 `NSMutableData` 对象用于可修改的内存，但在实践中并无此强制规定。

`NSData` 对象将其内容视为一个连续的字节数组。你通常需要将其视为其他类型——例如某种其他类型的数组或一个结构体。代码清单 8-2 演示了如何使用 `NSMutableArray` 来存储一个 `NSPoint` 结构体数组。

**代码清单 8-2.** 使用 `NSMutableData` 包装结构体数组

```
NSArray *objects = ... // 包含坐标的对象数组
unsigned int count = [objects count];
NSMutableData *data = [NSMutableData dataWithLength:count*sizeof(NSPoint)];
NSPoint *points = (NSPoint*)[data bytes];
unsigned int i;
[data retain];
```



```c
for (i=0; i<[objects count]; i++) {

points[i] = [[objects objectAtIndex:i] coordinate];

}

[data release];
```

清单 8-2 中的代码分配了一个空的字节数组，其大小足以容纳 `count` 个 `NSPoint` 结构体。分配完成后，数组的地址被转换为指向 `NSPoint` 结构体的指针。C 语言中，指针与数组变量可以互换，因此该指针可用于寻址数组中的各个元素。此技术适用于单个结构体或任意类型的数组。

**注意：** 在清单 8-2 中，你会注意到两个奇怪的语句：`[data retain]` 和 `[data release]`。由于 Objective-C 2.0 垃圾回收器的一个特性，指向 `NSData` 对象内部的变量（`points`）不足以在 `[data bytes]` 语句之后立即阻止该对象被垃圾回收器回收。这些额外的语句通过保持其对象引用在作用域内来防止 `NSData` 对象被回收。这仅对于临时的 `NSData` 对象是一个问题。如果 `NSData` 对象是从此函数返回的、存储在全局可访问的变量中，或在 `for` 循环之后以任何其他方式被引用，就不会有问题。`-retain` 和 `-release` 方法用于传统内存管理，在垃圾回收环境下运行时不会执行任何操作。有关解释及另一种解决方案，请参阅第 9 章“GC 与非 GC 指针”部分。

### 包装任意值

C 语言允许你使用 `struct` 和 `typedef` 声明来定义自己的内存结构和变量类型。因此，除了语言本身提供的整数和浮点数类型外，还有更多类型可以被包装。这就是 `NSValue` 的职责。

`NSValue` 可以包装任何 C 数据类型，包括由语言或程序员定义的所有非对象类型。创建 `NSValue` 对象的主要方法是 `+[NSValue valueWithBytes:objCType:]`。该方法接收的是值的地址（而非值本身）和一个 Objective-C 类型值。该类型值通过 Objective-C 的 `@encode()` 指令生成。这会生成一个常量 C 字符串，用于正式标识值的类型。`NSValue` 根据这些信息确定值的大小，并对其进行复制。清单 8-3 展示了如何将任意 C 结构体包装到 `NSValue` 对象中，然后将其添加到集合中。

**清单 8-3.** 将任意结构体包装到 `NSValue` 中

```c
typedef struct {
    int population;
    long long hectares;
    BOOL landLocked;
} CountryStats;

...
CountryStats stats;
stats.population = 195450000;
stats.hectares = 859250000;
stats.landLocked = NO;

NSValue *value = [NSValue valueWithBytes:&stats objCType:@encode(CountryStats)];
NSMutableArray *array = [NSMutableArray array];
[array addObject:value];

...
CountryStats lastStats;
value = [array lastObject];
[value getValue:&lastStats];
return (lastStats.population);
```

清单 8-3 的关键点如下：

-   通过将值的地址（而非值本身）传递给 `NSValue` 来构造它。
-   值的大小由 `@encode()` 指令生成的 Objective-C 类型隐含确定。
-   封装后，`NSValue` 可以像任何其他对象一样被对待。
-   要检查存储在 `NSValue` 中的值，请向其发送 `-getValue:` 消息——同样需要传递变量的地址，该变量将被对象中存储的值的副本覆写。

`NSValue` 为常用结构体提供了几个便捷构造函数，例如 `+valueWithPoint:(NSPoint)point`、`+valueWithRange:(NSRange)range`、`+valueWithSize:(NSSize)size` 等。请注意，这些方法都接受原始值的副本，而非地址。

`NSNumber` 实际上是 `NSValue` 的一个子类。从某种意义上说，`NSNumber` 中的所有方法都可以视为处理原生数据类型的便捷方法。在内部，`NSNumber` 使用 `NSValue` 来存储其值。你可以向 `NSNumber` 或 `NSValue` 对象发送 `-objCType` 消息，以确定其原始值的类型。

### 包装 nil

Objective-C 提供了 `NSNull` 类作为 `nil` 值的对象占位符。这提供了一个可以存储在集合中、归档（序列化）或以其他方式用于表示“无内容”（而 `nil` 在此情况下不可用）的对象。

方法 `+[NSNull nil]` 返回由 Objective-C 运行时创建的 `NSNull` 单例实例。这个唯一的 `NSNull` 对象是不可变且不可销毁的。

清单 8-4 演示了一种编写方法的简单技术，该方法可以接受一个对象、一个 `NSNull` 实例或 `nil`——并对后两者同等处理。

**清单 8-4.** 接受对象、`nil` 或 `NSNull` 的方法

```objectivec
- (void)doSomethingWithObject:(id)object {
    if (object==[NSNull null])
        object = nil;
    ...
}
```

### 字符串

在 Java 和 Objective-C 中，字符串都显得有些特别。它们在编程中如此基础，以至于两种语言都包含了声明字符串字面量的特殊语法。然而，除了声明字符串字面量之外，这些语言对字符串的直接支持很少，期望程序员将它们作为对象进行操作。唯一的例外是 Java 的字符串连接运算符（`+`），Objective-C 并不包含该运算符。本节将涵盖字符串字面量、字符串比较、字符串操作、字符串与标量值的相互转换以及复杂格式化。

在许多层面上，Objective-C 中的字符串遵循与 Java 中类似的熟悉模式。所有 Objective-C 字符串都是 `NSString` 类的对象。`NSString` 字符在内部使用 Unicode 表示。`NSString` 对象是不可变的。Objective-C 字符串字面量对象使用 `@"string"` 指令编写。表 8-4 列出了常见的字符串操作及其 Objective-C 对应项。

**表 8-4.** 常见字符串类和方法

| Java                                 | Objective-C                                                  |
|--------------------------------------|--------------------------------------------------------------|
| `"string"`                           | `@"string"`                                                 |
| `java.lang.String`                   | `NSString`                                                   |
| `java.lang.StringBuffer`             | `NSMutableString`（虽然非线程安全）                 |
| `java.lang.StringBuilder`            | `NSMutableString`                                            |
| `Object.toString()`                  | `-[NSObject description]`                                   |
| `new String(byte[],String)`          | `+[NSString stringWithCString:(const char*)cString encoding:(NSStringEncoding)enc]` |
| `String.length()`                    | `-[NSString length]`                                         |
| `String.charAt(int)`                 | `-[NSString characterAtIndex:(int)index]`                   |
| `String.equals(String)`              | `-[NSString isEqualToString:(NSString*)string]`             |
| `String.compareTo(String)`           | `-[NSString compare:(NSString*)string]`                     |
| `String.compareToIgnoreCase(String)` | `-[NSString caseInsensitiveCompare:(NSString*)string]`      |
| `String.concat(String)`              | `-[NSString stringByAppendingString:(NSString*)string]`     |
| `String.substring(int)`              | `-[NSString substringFromIndex:(int)index]`                |
| `String.substring(int,int)`          | `-[NSString substringWithRange:(NSRange)range]`             |
| `String.toLowerCase()`               | `-[NSString lowercaseString]`                                |
| `String.toUpperCase()`               | `-[NSString uppercaseString]`                                |
| `String.trim()`                      | `-[NSString stringByTrimmingCharactersInSet:(NSCharacterSet*)set]` |
| `String.format(String,Object…)`      | `+[NSString stringWithFormat:(NSString*)format, …]`          |

你会在 `String` 和 `NSString` 中找到许多其他类似的方法。`NSString` 倾向于使用更通用的方法处理各种情况，而 Java 类则实现了许多简单方法。一个很好的例子是 `java.lang.String.trim()`。Objective-C 中与之等价的方法是 `-stringByTrimmingCharactersInSet:`，它接受一个 `NSCharacterSet` 对象作为参数。要精确实现 `String.trim()` 的功能，请使用 `[string stringByTrimmingCharactersInSet:[NSCharacterSet characterSetWithRange:NSMakeRange(0,0x20+1)]]`。虽然更冗长，但其优势在于能够从字符串中修剪任意字符集。如果你经常这样做，可以使用 Category 为 `NSString` 添加你自己的 `-trim` 方法，如第 5 章所述。



