# 字符串与原始值

## 注意

现代 Objective-C 开发工具在构建应用时会合并相同的字符串字面量。无论字符串字面量 `@"Welcome"` 在应用中出现了多少次，每个出现位置在运行时都将引用同一个实例。

然而，`String` 和 `NSString` 之间有几个关键差异你需要了解：

- `NSString` 存在子类。
- 字符串对象可能是可变的。

## 字符串类型与特性

- 存在两种字符串：Objective-C 字符串和 C 字符串。
- 加法运算符（`+`）不会拼接两个字符串。
- Objective-C 字符串字面量遵循 C 语言的编译时字符串拼接语法。字面量表达式 `@"Hello" @", World!"` 和 `@"Hello, World!"` 是等价的。

`NSString` 遵循 Cocoa 类框架中的一致模式：当一个类同时具有不可变和可变的变体时，可变类是不可变类的子类。这有三个重要后果。

**第一**，可变字符串对象的类是 `NSMutableString` 子类。在需要使用 `java.lang.StringBuilder` 的地方，请使用 `NSMutableString`。

**第二**，任何 `NSString` 指针或参数都可能包含对可变子类的引用。在 Java 中，`java.lang.String` 类是 `final` 的，无法被继承，从而保证所有 `String` 对象都是不可变的。要动态创建字符串，需要先构造 `StringBuffer` 对象，然后通过 `toString()` 将其转换为 `String` 对象。在 Objective-C 中，你可以使用 `NSMutableString` 组装字符串，然后直接返回它，或将其作为普通的 `NSString` 对象使用。如果之后不修改它，它就与不可变字符串无法区分。在必须保证字符串对象不可变的情况下，请使用 `[NSString stringWithString:possiblyMutableString]` 构造可疑字符串对象的副本。关于对象复制的信息，请参见第 12 章。

**第三**个后果更类似于一个优势。由于 `NSMutableString` 是 `NSString` 的子类，它继承了 `NSString` 的所有方法，包括通过分类（categories）添加到 `NSString` 的方法。

#### 将对象转换为字符串

正如每个 Java 对象都继承了一个 `toString()` 方法一样，每个 Objective-C 对象都继承了 `-(NSString *)description` 方法。其用途和使用方式与 Java 中的 `toString()` 几乎相同。

值得注意的几点：

- 基类实现 `-[NSObject description]` 返回一个包含对象类名和地址的字符串。除非被重写，否则这是对象的默认描述。
- 字符串格式化函数和调试器会在需要字符串表示时向对象发送 `-description` 消息。
- `-[NSString description]` 返回自身。
- `-[NSNumber description]` 返回原始值的字符串表示。
- 许多类重写了 `-description` 以提供更丰富的字符串表示。例如，集合类会返回描述集合中所有对象的字符串——递归地向每个对象发送 `-description` 消息。

## C 字符串

最后，还有一个额外的混淆点：存在两种字符串类型——Objective-C 字符串对象和传统的 C 字符串。C 字符串只是字符数组的地址，与其说是一个定义明确的实体，不如说是一种编程约定。C 语言对字符串唯一的支持是声明字面量字符串的语法（`"hi!"`），等价于分配一个以空字符结尾的字符数组（`char string[] = { 'h', 'i', '!', '\0' }`）。

> **提示**：作为 Java 程序员，你在编译 Objective-C 程序时无疑会遇到这样的消息：`"warning: initialization from incompatible pointer type"` 或 `"warning: passing argument from incompatible pointer type"`。这通常是因为你在 Objective-C 字符串字面量前省略了 `'@'` 符号。你没有声明字面量字符串对象（`@"Hello"`，类型为 `NSString *`），而是声明了一个 C 字符串字面量（`"Hello"`，类型为 `const char *`）。不必自责，即使经过多年的 Objective-C 编程，我仍然大约每周犯一次这个错误。

### 将 C 字符串转换为 NSString 对象

虽然你可以在 Objective-C 中长时间编程而不接触 C 字符串，但不可避免地会有需要与期望或返回 C 字符串的 C 函数交互的时候。从 C 字符串构造新 `NSString` 对象的主要方法列在表 8-5 中。它们与 Java 中从字节或字符数组创建 `String` 对象的构造函数非常相似。

**表 8-5.** 从字符数组构造 NSString 对象

| Java | Objective-C |
|------|-------------|
| `new String(byte[],String)` | `+stringWithCString:(const char *)cString encoding:(NSStringEncoding)enc` |
| `new String(char[],int,int)` | `+stringWithUTF8String:(const char*)utf8String` |
| `new String(String)` | `+stringWithCharacters:(const unichar*)chars length:(int)length` |

传统的 C 字符串使用 8 位的 `char` 值，通常采用 ASCII 编码，并以单个空字符（`'\0'`）结尾。这是迄今为止最常见的 C 字符串表示形式。要从传统的 C 字符串创建 `NSString`，请使用 `[NSString stringWithCString:cString encoding:NSASCIIStringEncoding]`。由于空终止字符的存在，字符串的长度是隐含的。如果字符串使用了其他编码，请指定相应的编码。`NSStringEncoding` 的文档列出了 Cocoa 框架支持的编码。

另一种流行的 8 位编码是 UTF-8，它由翻译成 8 位字节流的 Unicode 字符组成。大多数 UTF-8 字符串也以空字符结尾。`+stringWithUTF8String:` 消息会从以空字符结尾的 UTF-8 字符数组创建 `NSString` 对象，等同于 `[NSString stringWithCString:utf8String encoding:NSUTF8StringEncoding]`。

最后，你偶尔会遇到 Unicode 字符数组——相当于 Java 中 `char` 的真正对应物。使用 `+[NSString stringWithCharacters:(const unichar*)chars length:(int)length]` 方法从数组构造 `NSString` 对象。你需要向构造函数提供数组中的字符数量。

对于更特殊的情况，还有其他 `NSString` 构造方法。详情请查阅 `NSString` 文档。

### 将 NSString 对象转换为 C 字符串

将 `NSString` 中的字符提取到 `char` 或 `unichar` 数组的方法与表 8-5 中的方法相对应。表 8-6 列出了提取 `NSString` 中字符的常见方法以及一些便捷转换方法。

**表 8-6.** 从 NSString 对象提取字符

| Java | Objective-C |
|------|-------------|
| `getBytes(String)` | `-getCString:(char*)buff maxLength:(int)len encoding:(NSStringEncoding)enc` |
| `new String(String)` | `-cStringUsingEncoding:(NSStringEncoding)enc` |
| `new String(String)` | `-UTF8String` |
| `new String(String)` | `-fileSystemRepresentation` |
| `new String(String)` | `-dataUsingEncoding:(NSStringEncoding)enc` |
| `getChars(int,int,char[],int)` | `-getCharacters:(unichar*)buffer range:(NSRange)range` |
| `toCharArray()` | `-getCharacters:(unichar*)buffer` |

将 `NSString` 转换为 C 字符串时，你可以选择自行分配 `char` 数组，然后使用 `-getCString:maxLength:encoding:` 将字符提取到其中；或者让字符串对象使用 `-cStringUsingEncoding:`、`-UTF8String` 或 `-fileSystemRepresentation` 为你分配字符数组。这三种方法会分配一个临时字符数组缓冲区，将字符提取到其中，以空字符终止，并返回第一个字符的地址。`-dataUsingEncoding:` 方法返回一个包含编码后字符串的 `NSData` 对象。只有 `-dataUsingEncoding:` 是 Java 的 `getBytes(String)` 方法的真正替代品，因为它是唯一一个返回对象的方法。



