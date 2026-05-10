# Objective-C 接口定义

@interface NSString : NSObject <NSCopying, NSMutableCopying, NSCoding>

- (NSUInteger)length;

- (unichar)characterAtIndex:(NSUInteger)index;

@end

即使你有其他编程语言的经验，这段代码也毫无意义。是时候拆解语法了！

首先是 `@interface`：

`@interface` NSString : NSObject <NSCopying, NSMutableCopying, NSCoding>  这行代码告诉 Objective-C，你即将为一个类定义数据和方法。

实际上，这是从该类实例化的每个对象的模板。接口也是类名定义为 `NSString` 的地方。

接口定义一直持续到 `@end`，嗯，结束。

## 一统众类的基类

在定义之后，有一个冒号和一些以 `NS` 开头的其他名称。这暗示你正在处理系统类的定义。其中最重要的是 `NSObject`：

`@interface NSString :` **`NSObject`** `<NSCopying, NSMutableCopying, NSCoding>` 在 Objective-C 中，所有类（因此也是所有对象）都“继承”自另一个类。

*继承* 允许父类的特性传递给子类。子类（通常称为 *子类*）可以修改父类定义的方法和实例变量。（父类通常被称为 *超类*。）

**提示：** 这个继承规则有一个例外，就是 `NSObject`。它是层级结构中的根类，不继承自任何其他类。

就像从父母那里继承一样，继承让你免费获得很多行为（即使你不想要或不需要！）。即使这种继承不涉及金钱，你也会发现它价值连城，因为它节省了你大量的时间和精力。（有关此“免费代码”的详细信息，请参见第 36 页的方框。）

继承的一个例子出现在自定义视图中。在你开发的某个时刻，很可能需要开发一个自定义视图来显示某些特定于应用程序的数据。你将通过子类化一个系统类（如 `UIView`）来开始这项工作。

在实现 `UIView` 的子类时，你会发现很多代码已经为你完成了。像处理多点触控事件、绘图、动画和视图管理这些事情都可以从父类中获得。你只需要为视图实现新的行为。

## 大量的类

既然你已经了解了 Objective-C 中的类层级结构，那么应该清楚冒号后的第一个单词是被定义类的超类（父类）。`NSString` 的超类是 `NSObject`。

### 遵循协议

在超类名称之后，你会看到一些尖括号（`< >`）中的其他类名：

`@interface NSString : NSObject` **`<NSCopying, NSMutableCopying, NSCoding>`** 这些是类所采用的 *协议*。协议定义了不与任何特定类关联的一组方法。

使用协议就像签署合同。如果你通过在尖括号中包含类名称来指定你的类采用某个协议，你就承诺实现该协议中定义的方法。例如，`NSString` 协议承诺履行提供自身副本的契约。这些对象副本可以是只读的（使用 `NSCopying`）和可写的（使用 `NSMutableCopying`）。`NSCoding` 协议告诉你该类也实现了用于编码和解码对象的方法。

**注意：** `NSCoding` 中的编码和解码方法用于将对象归档到磁盘上或通过网络分发它们。这是 Cocoa 序列化对象的机制。

定义新类时，协议不是必需的，但你会发现，在开发应用程序时，它们被广泛使用。协议最常见的用途是与委托一起使用；你将在下一章中看到这种设计模式。

**注意：** 由于 Objective-C 仅支持单继承，因此协议经常被用来实现其他语言中多重继承的相同目标。

## 疯狂背后的方法

仅仅为了理解一行代码就需要阅读这么多内容！值得庆幸的是，接下来的两行内容会稍微容易一些，这两行都以减号（`-`）开头。

```
- (NSUInteger)length;

- (unichar)characterAtIndex:(NSUInteger)index;
```

这就是在类接口中定义方法（第 49 页）的方式。你只需这两个方法就可以表示 Cocoa 中的任何字符串：第一个返回字符串的长度；另一个返回索引位置上的 Unicode 字符。

**提示：** 在在线论坛、邮件列表或博客中撰写方法时，许多开发者使用方法签名的缩写形式：他们省略了类型信息和参数名称。Apple 的开发文档也使用这种格式。

在求助帖中经常能看到类似“我在使用 `–messageWithFirstParameter:andSecondParameter:` 时遇到问题”的表述。这也是让方法签名保持可读性的另一个原因！上面显示的 `NSString` 方法的简短形式是 `–length` 和 `–characterAtIndex:`。本书通篇使用这种格式。

定义中还包含了任何参数和结果的类型。`–length` 方法返回一个无符号整数（`NSUInteger`）。`–characterAtIndex:` 方法接受一个无符号整数索引作为参数，并返回一个 `unichar`。这些方法使用基本类型，但你也可以使用指向类名的指针来指定对象（稍后在 `–uppercaseString` 的定义中你会看到）。

**提示：** 方法定义中使用的类型在编译时进行检查，但在运行时发送消息时则不会。当你看到编译器警告“`NSString`”可能无法响应“`–aNonexistentMethod`”时，请确保你正在将消息发送给正确类型的对象。

## 高级用户课堂：自我介绍

免费代码的概念贯穿了整个对象层级结构。当你在处理一个 `UIView` 子类时，会向对象发送一个 description 消息，你将能够访问来自 `UIView`、`UIResponder`（处理触摸事件的地方）和 `NSObject`（根类）的代码。

这种特性在记录信息时也非常方便。Cocoa 的日志记录函数 `NSLog` 接受一个 `printf` 风格的字符串，并将结果输出到当前控制台设备：

```
NSLog(@″THIS OBJECT IS AWESOME: %@″, chockLockObject);
```

格式化说明符是通过一个 `NSString` 提供的（记住前面提到的 `@` 快捷方式）。还有一个新的格式化说明符——`%@`——它像 `%s` 打印 C 风格字符串一样打印 `NSString`。

真正酷的地方在于，日志记录函数足够智能，知道 `chockLockObject` 不是 `NSString` 的实例，因此它会使用对象的 description 方法来创建要显示的字符串。

要查看层级结构的威力，请查看 `NSObject` 中的 `–description` 方法。由于每个对象都是此根类的子类，因此它们都知道如何描述自己。该方法的默认实现返回对象的类名和一个内存地址。因此，即使你对一个对象一无所知，你也可以这样做：

```
[mysteryObject description]
```

当你调试或处理来自你无法直接控制的外部源的数据时，描述 `mysteryObject` 的结果字符串会很有帮助。

例如，你与 Xcode 一起使用的调试器（gdb）包含一个 `print-object` 命令。当你在调试器中输入以下内容时：

```
(gdb) po myObjectVariable
```

描述 `myObjectVariable` 的结果字符串将被打印到控制台上。



