# 排版后的内容

阅读第 26 章（“isa Swizzling”部分），以更好地理解对象的方法如何以及为何会被动态改变。

每个方法生成的 C 函数都期望接收一个接收对象指针（`self`）和一个选择器（`_cmd`）作为前两个参数。其余参数与方法定义的参数保持一致。清单 6-9 演示了如何通过方法的实现地址调用方法。

**清单 6-9.** 将方法作为 C 函数调用

```
@interface BezierPathMapper : NSObject {
    NSBezierPath *path;
}
@property (assign) NSBezierPath *path;
- (BOOL)containsAnyPoint:(NSPoint*)pointArray pointCount:(unsigned int)count;
@end

@implementation BezierPathMapper
@synthesize path;
- (BOOL)containsAnyPoint:(NSPoint*)pointArray pointCount:(unsigned int)count
{
    if (path==nil)
        return NO;
    // 获取 -[NSBezierPath containsPoint:] 的函数地址
    typedef BOOL (*containsPointPtr)(id self, SEL _cmd, NSPoint point);
    containsPointPtr containsPointImpl =
        (containsPointPtr)[path methodForSelector:@selector(containsPoint:)];
    // 通过向路径发送 -containsPoint: 消息来测试数组中的每个点
    unsigned int i;
    for (i=0; i<count; i++)
        if (containsPointImpl(path,@selector(containsPoint:),pointArray[i])!=NO)
            return YES;
    return NO;
}
@end
```

`BezierPathMapper` 的 `-containsAnyPoint:pointCount:` 方法测试一系列坐标，以确定是否有任何坐标位于其 `path` 属性内部。该方法首先获取路径 `-containsPoint:` 方法的实现地址。你在此处看到的奇怪语法是 C 语言中声明函数指针的方式。该类型由描述函数参数和返回类型的完整函数原型组成（`BOOL foo(id self, SEL _cmd, NSPoint point)`），其中函数名被替换为指针声明（`(*containsPointPtr)` 替换 `foo`）。

结果是一个指向具有该原型的函数的变量。在 `typedef` 语句中，它产生了一个新的指针类型。在清单 6-9 中，使用了 `typedef`，这样在转换 `-methodForSelector:` 返回的值时，就不必重复整个函数原型了。C 语言允许将函数指针变量用作函数名，就像存在一个名为 `containsPointImpl` 的函数一样。

调用中必须包含接收器和选择器参数。虽然选择器通常被忽略，但作为良好的编程实践，始终应传递被调用方法的选择器常量。

由于这种技术绕过了分发器的 nil 接收者测试，因此该代码必须首先确保对于 nil 的 `path` 对象绝不会调用该函数——这很可能会导致程序崩溃。

清单 6-9 中的代码主要用于演示该技术。即使测试数千个点，也不太可能带来显著的性能提升——主要是因为 `-[NSBezierPath containsPoint:]` 执行的计算可能远远超过发送消息的微小开销。然而，如果方法执行的代码非常快，并且被调用了数百万次，那么这种技术可以节省宝贵的 CPU 时间。

还应注意，直接调用方法可能比尝试使用 `NSInvocation` 更简单、更快，尤其是在被调用方法的参数已知且明确的情况下。

### 可变参数

Java 没有的一个技巧是可变参数。诚然，Java 1.5 添加了可变参数语法，但这只是 Java 处理可变参数一贯方式的语法糖。程序员（Java 1.5 之前）或编译器（Java 1.5 及以后版本）首先创建一个数组来包含可变参数，用参数填充它（将基本类型值包装在对象中），然后将单个数组对象传递给方法。

Objective-C 中的可变参数更加直接和原始：发送者可以在消息中提供任意所需的额外参数。额外的参数不会被修改，并且像任何其他参数一样被压入栈中。方法在执行时通过编程方式解析参数列表。这些参数的类型、顺序和含义由约定决定。Objective-C 不对它们施加任何限制，也不提供任何关于它们的类型信息。由调用者提供方法所期望的参数类型。

> **注意** Objective-C 从 C 语言“借用”了可变参数。本节中的所有内容同样适用于普通的旧式 C 函数。

要声明可变参数列表，请在最后一个参数变量名后面加上逗号和三个句点（省略号，但不要使用 Unicode 省略号字符）。调用时，命名参数后面可以跟附加的逗号分隔参数。对附加参数的数量或类型没有限制。发送给方法的参数形成了一种数据流。使用特殊的库函数依次提取每个参数的值。这个过程大致类似于使用 `java.io.ObjectInputStream` 读取一系列序列化值。

清单 6-10 演示了两个常用的接受可变参数的 Cocoa 方法。`+[NSArray arrayWithObjects:...]` 方法创建一个新的 `NSArray` 对象，该对象预先填充了作为参数传递的对象。`+[NSString stringWithFormat:...]` 方法使用格式字符串创建字符串。格式字符串包括占位符，这些占位符会被附加参数的值替换。清单 6-10 中显示的示例代码返回字符串“Dark toast will take 45 seconds”。

**清单 6-10.** Cocoa 中的可变参数方法

```
@interface NSArray
+ (id)arrayWithObjects:(id)firstObj, ...
@end

@interface NSString
+ (id)stringWithFormat:(NSString*)format, ...
@end

...
int toastTimes[] = { 8, 22, 35, 45, 60, 90 };
NSArray *toastDescriptions = [NSArray arrayWithObjects:@"Warm",
                                                         @"Light",
                                                         @"Medium",
                                                         @"Dark",
                                                         @"Very dark",
                                                         @"Burnt",
                                                         nil];
int level = 3;
int secs = toastTimes[level];
NSString *desc = [toastDescriptions objectAtIndex:level];
return [NSString stringWithFormat:@"%@ toast will take %d seconds.",desc,secs];
```

一个带有可变参数的方法必须假设列表中参数的数量和类型。参数列表不包含任何类型信息或列表结束指示符；它只是参数值被压入栈时形成的原始字节序列。因此，该方法要么必须假设参数类型，要么使用命名参数中提供的信息来推断它们。

清单 6-10 中展示的两个 Cocoa 方法演示了解决此问题的两种最常见方案。`+arrayWithObjects:` 方法假设所有附加参数都将是对象指针，并且列表以最后的 `nil` 参数结束。

`+stringWithFormat:` 方法使用格式参数来推断后续参数值的序列。它会扫描格式字符串以查找格式说明符。第一个说明符（`%@`）是一个对象格式化程序。这告诉 `+stringWithFormat:` 第一个附加参数是一个指向对象的指针。`%@` 会被替换为对象的 `-description`（`toString()`）值。下一个遇到的说明符是 `%d`，表示下一个参数是一个有符号整数（`d` 代表十进制）。它从参数流中提取一个 `int` 并继续，直到字符串中的所有格式说明符都被替换为止。

在这两种情况下，方法和调用者之间都存在编程约定。`+arrayWithObjects:` 假设只传递对象指针，并以 `nil` 指针结束。



`+stringWithFormat:` 假定附加参数与格式字符串中占位符所隐含的值类型一致。

■ 注意 编译器没有关于可变参数的类型信息，因此它假定所有值都是正确/预期的类型。参数`1`将传递一个整数，`1.0`将传递一个浮点数。要消除任何疑问，请像在`[NSString stringWithFormat:@"float %f, int %d, char %c", (float)34, (int)34, (char)34]`中那样强制转换参数。这将强制编译器在将参数压入堆栈之前将其转换为预期类型。

编写自己的可变参数方法并不困难，但确实需要一些规划。

你必须设计该方法，以便已知或可以推断出附加参数的类型和数量。清单 6-11 重写了`containsAnyPoints:pointCount:`方法，以使用可变参数列表，而不是指针数组。

**清单 6-11\. 可变参数实现**

```
@interface BezierPathMapper : NSObject {
    NSBezierPath *path;
}
@property (assign) NSBezierPath *path;
- (BOOL)containsAnyOfCountPoints:(unsigned int)count, ...;
@end

@implementation BezierPathMapper
@synthesize path;

- (BOOL)containsAnyOfCountPoints:(unsigned int)count, ...
{
    va_list varList;
    BOOL hit = NO;
    NSPoint point;
    va_start(varList, count);
    while (count != 0) {
        point = va_arg(varList, NSPoint); // 复制下一个点
        if ([path containsPoint:point]) {
            hit = YES;
            break;
        }
        count--;
    }
    va_end(varList);
    return hit;
}
@end
```

...
```
if (![mapper containsAnyOfCountPoints:5,
    NSMakePoint(0.0, 1.0),
    NSMakePoint(1.0, 0.0),
    NSMakePoint(1.0, 0.0),
    NSMakePoint(0.0, 0.0),
    NSMakePoint(0.5, 0.5)]) {
    NSLog(@"no points in path");
}
```

新方法直接传递一个`NSPoint`结构列表作为参数。初始的`count`参数告诉该方法期望多少个`NSPoint`参数。

要在你的方法中实现可变参数，请遵循以下步骤：

1. 在方法声明中最后一个参数变量名后跟上“`, ...`”。
2. 定义一个`va_list`自动变量。
3. 使用`va_list`变量和最后一个具名参数调用`va_start()`。该参数仅用于告诉可变参数函数附加参数从哪里开始。
4. 使用`va_list`变量和下一个参数的类型调用`va_arg()`。返回的值是参数的副本。
5. 完成后调用`va_end()`。

在调用`va_end()`之前不必检索所有参数。为超出实际传递数量的参数值调用`va_arg()`是不可预测的，应避免。

### 未实现的方法

或许比消息如何发送更有趣的是，当一个对象被发送一条它未实现的消息时会发生什么。如果你不采取任何特殊措施，结果类似于 Java；运行时将抛出一个“无法识别的选择器”异常。

但在抛出该异常之前，对象有机会以其他方式处理该消息。当消息分发函数发现某个对象未实现该方法时，它会将该消息转换为一个`NSInvocation`对象。然后，它将这个`NSInvocation`传递给该对象的`-forwardInvocation:`方法。

根类`NSObject`的`-forwardInvocation:`只是向自身发送一条`-doesNotRecognizeSelector:`消息，该消息（除非被重写）会抛出无法识别的选择器异常。一个类可以重写`-forwardInvocation:`并拦截未实现的消息。

顾名思义，`-forwardInvocation:`被设计用于将消息重定向或转发给另一个对象。清单 6-12 中的`StandIn`类展示了如何实现这一点。该对象对其实现或继承的所有方法都正常响应。当收到一条它未实现的消息时，它会收到一条`-forwardInvocation:`消息。`StandIn`将消息及调用的所有参数传递给它的`actor`对象。如果`actor`未实现该消息，它将要么抛出异常，要么转发该调用。

**清单 6-12\. 转发一条未实现的消息**

```
@interface StandIn : NSObject {
    id actor;
}
@property (assign) id actor;
@end

@implementation StandIn
@synthesize actor;

- (void)forwardInvocation:(NSInvocation*)invocation
{
    if (actor == nil)
        [self doesNotRecognizeSelector:[invocation selector]]; // 不返回
    [invocation invokeWithTarget:actor];
}

- (NSMethodSignature*)methodSignatureForSelector:(SEL)sel
{
    NSMethodSignature *signature = [super methodSignatureForSelector:sel];
    if (signature == nil)
        signature = [actor methodSignatureForSelector:sel];
    return signature;
}
@end
```

同样需要重写`-methodSignatureForSelector:`。消息分发器首先向对象发送`-methodSignatureForSelector:`，并使用返回的对象创建传递给`-forwardInvocation:`的调用参数。

通过一点小小的手段，由`-invokeWithTarget:`调用的方法返回的任何值都将被返回给最初发送消息的代码。为了使其正常工作，重要的是在发送`-invoke`消息后立即返回。返回值仍然在寄存器或堆栈中，因此调用者会得到它们，就像你的处理程序显式返回了它们一样。

你可以使用`-forwardInvocation:`做很多有趣的事情：

* 将一个对象包装在一个日志记录器对象中，该对象拦截并记录有趣消息的调用。
* 实现由类中其他方法处理的“合成”消息。想象创建一个通用的数据库记录对象，它捕获收到的任何属性消息（例如，`-saleDate`、`-setSaleDate:`）并自动将其转换为记录查询。无需编写`date = [record getDateFieldWithKey:@"SaleDate"]`，你可以直接写`date = [record saleDate]`，而无需编写`-saleDate`方法。`NSManagedObject`和`CALayer`是实现合成属性的类示例。
* 创建一个将消息转发到其他对象层次结构（如响应者链）的对象。第 20 章讨论响应者链。代理对象会搜索一个其他对象的集合，寻找实现该消息的对象。

`-forwardInvocation:`不适用于可变参数方法，因为`NSInvocation`对象不会包含额外参数的副本。

### 总结

虽然概念上类似于 Java 方法，但 Objective-C 消息存在微妙的差异。真正开始以发送消息（而非调用方法）的方式思考。Objective-C 轻量级的方法分发使得以编程方式发送方法变得容易。类不仅可以动态发送消息，还可以动态响应消息。消息易于操作且高效，这就是为什么动态消息在众多 Objective-C 解决方案中扮演如此关键的角色。

**第 7 章**

■ ■ ■

**与 nil 交朋友**

处理 nil（null）引用是编程中不可避免的一部分。本章将解释在 Objective-C 中如何处理 nil 和 NULL 指针，以及一些令人惊讶的后果。学会将 nil 对象指针用于自己的优势，可以极大地简化——而非复杂化——你的设计。



`nil (null)`和`NULL`引用在 Objective-C 中有时比在 Java 中受到更严厉的处理，但在其他时候却是允许的，甚至是受欢迎的。Java 对`null`的处理非常一致；使用`null`引用通常被视为编程错误，并在运行时抛出`java.lang.NullPointerException`对象。运行时异常通常会导致 Java 应用程序终止，但可以被捕获，从而可能从错误中恢复。

在 Objective-C 中，使用`nil`或`NULL`引用的后果是混合的。访问地址 0 附近的内存（即尝试使用值为`nil`的指针时会发生的情况）会导致内存地址违规。地址违规由硬件检测到，并向进程发送`SIGBUS`信号，导致其立即终止。系统`CrashReporter`守护进程通常会捕获`SIGBUS`信号，并准备一份崩溃报告文档，详细说明进程终止前的状态。虽然应用程序在技术上可以拦截某些信号，但从`SIGBUS`信号中恢复的能力极其有限。

■ 注意 Objective-C 常量`nil`和`NULL`在技术上是可互换的。按照惯例，`nil`（由 Objective-C 定义）用于对象指针，而`NULL`（传统的 C 常量）用于所有其他指针类型。因此，你会写成`MyClass *object = nil`和`int *iPtr = NULL`。

就像在 Java 中一样，你应该避免在未确保指针变量包含有效地址的情况下，使用对象指针直接访问成员变量（`int i = object->iVar`）或通过指针访问值（`int i = *intPtr`）。清单 7-1 中的代码演示了避免`NULL`指针引用的一些典型编码策略。

**清单 7-1. 避免 NULL 指针引用**

```objective-c
- (void)expandSize:(NSSize*)size
{
    if (size != NULL) {
        area.height += size->height;
        area.width += size->width;
    }
}

- (BOOL)deleteAllReferencesToKey:(id)key error:(NSError**)outError
{
    ...
    if (!successful && outError != NULL)
        *outError = [NSError errorWithDomain:NSPOSIXErrorDomain
                                        code:errno
                                    userInfo:nil];
    return successful;
}
```

### 向`nil`发送消息是安全的

形成鲜明对比的是，向`nil`对象指针发送消息不仅被允许，而且是被鼓励的！在第 6 章中，我忽略了消息调度函数中一个非常重要的步骤：4. 如果接收者值为`nil`，则调度函数立即返回。

这个条件使得向`nil`对象指针发送任何消息都非常安全。当消息接收者为`nil`时，调度函数不执行任何操作并立即返回。更重要的是，对于期望返回值的发送者，它明确返回一个值为零、`nil`或`NO`的值。

与典型的 Java 代码不同，在发送消息之前没有必要测试对象指针是否为`nil`。实际上，这是多余的，因为每次消息发送时都会执行此测试。

清单 7-2 中的代码片段来自我们虚构的《金星攻击》冒险游戏，展示了`nil`接收者如何简化代码。`TacticalDisplay`对象在地图上绘制显示内容，但仅在存在地图对象时进行。它还会叠加一个可选的位置高亮，以青色或自定义颜色绘制。`paint()`方法中的 Java 代码使用了传统的条件语句来处理以下情况：没有地图对象、地图对象未提供位置高亮形状，或者位置高亮的颜色未设置。

**清单 7-2. 使用 nil 对象简化代码**

**Java**

```java
package com.apress.java;

import java.awt.*;
import javax.swing.*;

public class TerrainMap
{
    private Color customLocationColor;
    private Shape locationHighlightShape;

    public void paint(Graphics graphics)
    {
        // ... draw the map
    }

    public Color getCustomLocationColor() { ... }
    public Shape getLocationHighlightShape() { ... }
}

public class TacticalDisplay extends JComponent
{
```


```java
private TerrainMap terrainMap;

public TerrainMap getTerrainMap() { … }

public void paint(Graphics graphics) {
    super.paint(graphics);
    Graphics2D g2 = (Graphics2D)graphics;
    …
    graphics.setColor(Color.CYAN); // 默认位置颜色
    **TerrainMap map = getTerrainMap();**
    **if (map != null) {**
        **map.paint(graphics);**
        ****Shape locationShape = map.getLocationHighlightShape();**
        **if (locationShape != null) {**
            **Color customColor = map.getCustomLocationColor();**
            **if (customColor != null)**
                **graphics.setColor(customColor);**
            ****g2.draw(locationShape);**
        **}**
    **}**
}
```

**Objective-C**

```objectivec
#import <Cocoa/Cocoa.h>

@interface TerrainMap : NSObject {
    NSBezierPath *locationHighlightPath;
    NSColor *customLocationColor;
}

@property (assign) NSBezierPath *locationHighlightPath;
@property (assign) NSColor *customLocationColor;
- (void)drawRect:(NSRect)dirtyRect;

[www.it-ebooks.info](http://www.it-ebooks.info/)

第 7 章 ■ 与 nil 交朋友

@end

@implementation TerrainMap

@synthesize locationHighlightPath, customLocationColor;

- (void)drawRect:(NSRect)dirtyRect {
    // ... 绘制地图
}

@end

@interface TacticalDisplay : NSView {
    TerrainMap *terrainMap;
    …
}

@property (copy) TerrainMap *terrainMap;
- (void)drawRect:(NSRect)dirtyRect;

@end

…

@implementation TacticalDisplay

@synthesize terrainMap;

- (void)drawRect:(NSRect)dirtyRect {
    …
    [[NSColor cyanColor] setStroke]; // 设置默认位置颜色
    **TerrainMap *map = [self terrainMap];**
    **[map drawRect:dirtyRect];**
    **[[map customLocationColor] setStroke];**
    **[[map locationHighlightPath] stroke];**
}

@end
```

Objective-C 版本充分利用了 `nil` 对象指针的能力。向 `nil` 指针发送消息不会执行任何操作。由于发送给 `nil` 的任何消息都返回 `nil`，因此对前一条消息返回的对象发送消息的嵌套调用也同样不会执行任何操作。如果清单 7-2 中的 `map` 对象指针为 `nil`，则后续的所有消息都不会被发送。如果 `map` 确实存在，但 `-customLocationColor` 消息返回了 `nil`，那么 `-setStroke` 消息会被忽略，并且不会设置自定义颜色。如果 `-locationHighlightPath` 消息返回了 `nil`，那么 `-stroke` 消息会被忽略，并且不会绘制位置图形。

[www.it-ebooks.info](http://www.it-ebooks.info/)

第 7 章 ■ 与 nil 交朋友

> **注意** 虽然向 `nil` 发送消息不会执行任何操作，但这并不排除参数可能带来的副作用。消息的参数会在消息分发之前被组装并压入栈中。参数表达式中发送的消息或其他副作用（例如对某个值进行后置自增）仍然会发生。将接收者显式地与 `nil` 进行比较并在相等时跳过消息发送的一个原因，就是为了避免组装参数时产生不必要的副作用。

**nil 返回零**

只要调用者期望消息返回的是表 7-1 中的一种变量类型，`nil` 接收者总是向调用者返回 `nil` 或 `0`。兼容的变量类型本质上包括任何指针或标量。

***表 7-1.** nil 消息返回值*

| 返回类型 | 返回值 |
|-------------|----------------|
| `id` | `nil` |
| 指向任何类型的指针 | `NULL` |
| `BOOL` | `NO` |
| `(unsigned) char` | `'\0'` |
| `(unsigned) int` / `(unsigned) long int` | `0L` |
| `(unsigned) long long int` | `0LL` |
| `float` | `0.0f` |
| `double` | `0.0` |
| `long double` | `0.0` |

期望消息返回结构体的调用者与 `nil` 接收者不兼容。在这些情况下，你必须像在 Java 中一样避免向 `nil` 发送消息。清单 7-3 展示了一个简单示例。结构体通过使用特殊的“复制板”寄存器进行交换，这些寄存器不属于返回值的一部分。这条规则的一个晦涩例外是当整个结构体通过寄存器返回时。其机制在“Mac OS X ABI 函数调用指南”（可从 [`developer.apple.com/`](http://developer.apple.com/) 获取）中有描述，并且与体系结构相关。

[www.it-ebooks.info](http://www.it-ebooks.info/)

第 7 章 ■ 与 nil 交朋友

**清单 7-3.** 避免向返回结构体的 nil 消息发送消息

```objectivec
NSView *headsUpDisplay = [self headsUpDisplay];
NSRect hudRect = NSMakeRect(0.0,0.0,0.0,0.0);
if (headsUpDisplay != nil)
    hudRect = [headsUpDisplay visibleRect];
```


`if (hudRect.size.width > 0.0 && hudRect.size.height > 0.0) {`

`    …`

`}`

## 与 `nil` 设计

拥抱 Objective-C 中的 `nil` 接收者将改变你编写代码和设计类的方式。本节将展示如何利用 `nil` 接收者来简化代码，并介绍三种利用 `nil` 的设计模式。抽象地说，这是采纳了“让数据做决定”的编程原则，该原则最早由计算机科学先驱、FORTH 语言发明者 Charles Moore 提出。该原则避免编写用于处理异常情况的条件分支语句，转而倾向于使用包含这些情况的数据和表达式。

对比清单 7-4 中的两段代码片段。该代码是一个食谱管理程序的一部分。`makeLists` 方法负责汇总本周计划的食谱列表，以及一份必须在当前预算内的食材购物清单。如果所有食材的总成本超出预算，则省略本周后期餐食所需的食材。

该程序使用了多个类，其中许多类拥有必须考虑的*可选*属性。可能没有 `Budget` 对象，此时对购物清单没有预算限制。规划器中可能没有餐食，此时生成的列表将为空。

某餐可能没有 `recipe` 属性（有些餐食可能是外卖），此时它会进入餐食列表，但不会向购物清单添加任何食材。

**清单 7-4.** 让 `nil` 接收者做决定

**Java**

```java
package com.apress.java;

import java.util.*;

public class Ingredient
{
    public double getCost( ) { ... }
}

public class Recipe
{
    protected ArrayList ingredients;
    public synchronized List<Ingredient> getIngredients() { ... }
}

public class Meal
{
    protected Recipe recipe;
    public synchronized Recipe getRecipe() { ... }
    public synchronized void setRecipe( Recipe recipe ) { ... }
}

public class Budget
{
    public void planExpendature( double amount ) { ... }
    public boolean isOverBudget( ) { ... }
}

public class RecipeBox
{
    protected ArrayList meals;
    public synchronized Budget getMealBudget() { ... }
    public synchronized List<Meal> getMeals() { ... }
    public synchronized void setMeals( List<Meal> meals ) { ... }

    public void makesLists( )
    {
        ArrayList mealList = new ArrayList();
        ArrayList shoppingList = new ArrayList();

        Budget budget = getMealBudget();
        List meals = getMeals();

        if (meals != null) {
            boolean isOverBudget = false;
            for ( Meal meal: getMeals() ) {
                Recipe recipe = meal.getRecipe();

                if (recipe != null) {
                    List<Ingredient> ingredients = recipe.getIngredients();
                    for (Ingredient ingredient: ingredients) {
                        if (budget != null) {
                            budget.planExpendature(ingredient.getCost());
                            if (budget.isOverBudget())
                                isOverBudget = true;
                        }
                    }
                }
                mealList.add(meal);

                if (!isOverBudget)
                    shoppingList.addAll(recipe.getIngredients());
            }
        }
    }
}
```

**Objective-C**

```objc
#import <Cocoa/Cocoa.h>

@interface Ingredient : NSObject
@property (readonly) double cost;
@end

@interface Recipe : NSObject {
    NSMutableArray *ingredients;
}
@property (assign) NSMutableArray *ingredients;
@end

@interface Meal : NSObject {
    Recipe *recipe;
}
@property (assign) Recipe* recipe;
@end

@interface Budget : NSObject
- (void)planExpenditure:(double)amount;
- (BOOL)isOverBudget;
@end

@interface RecipeBoxController : NSObject {
    NSMutableArray *meals;
}
@property (assign) NSMutableArray *meals;
@property (readonly,copy) Budget *mealBudget;
- (void)makeLists;
@end

@implementation RecipeBoxController
…

- (void)makeLists
{
    NSMutableArray *mealList = [NSMutableArray new];
    NSMutableArray *shoppingList = [NSMutableArray new];

    NSMutableArray *affordableGroceryList = shoppingList;
    Budget *budget = [self mealBudget];
```



