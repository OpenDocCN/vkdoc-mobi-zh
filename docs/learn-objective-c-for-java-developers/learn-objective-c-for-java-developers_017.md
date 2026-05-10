# C 标签

与 Java 不同，C 语言的标签不仅限于跳转到代码块的末尾。`goto` 语句可以在代码流中向前或向后跳转，也可以跳入或跳出任意层级的代码块。仅使用 `label`、`if` 和 `goto` 语句就可以构建出 `for`、`while`、`do` 和 `case` 语句——尽管我不推荐这样做。

请尽量少用 `goto` 语句（如果确实要用的话）。它们可以解决非常复杂的代码流程问题，但只应作为最后的手段。`goto` 语句容易被滥用，常常会妨碍代码优化，并且会使执行顺序难以分析。

### 本章小结

本章旨在为你提供足够的 C 语言知识，使你能够开始用 Objective-C 进行编程。

使用 Objective-C 编程能让你从 C 编程的许多晦涩和繁琐的方面中解放出来。通过使用 Objective-C 类，你可以在很大程度上避免使用 C 字符串、内存管理、数组以及大量其他结构。

无论从哪个角度看，本章都不是一篇全面的 C 语言教程。C 语言非常庞大——甚至比 Java 还要庞大，因为 Java 的目标之一就是创建一种更简单、更简洁的 C 语言版本。

虽然这个简略的介绍能为你提供足够的入门知识，让你可以开始用 Objective-C 编程，但你最终还是会遇到更复杂的 C 代码，或者需要与 C 库交互。那时就需要对这门语言有更深入的理解。我强烈建议你购买一本关于标准 C 语言的书籍，以便进一步阅读和将来参考。

现在你已经了解了 C 和 Java 之间的显著差异，可以开始探索定义 Objective-C 的额外语法了。

[www.it-ebooks.info](http://www.it-ebooks.info/)

## 第 3 章：欢迎使用 Objective-C

本章介绍 Objective-C 的核心语言及其语法与 Java 的不同之处。功能上的差异以及更晦涩的语言特性将在后续章节中介绍。

Objective-C 通过增加一层额外的语法来增强 C 语言。它并没有以任何有意义的方式重新定义 C 语言或限制其能力。Objective-C 的语法很容易识别。如果 Objective-C 有一个标志，那很可能就是“at”符号（`@`）；所有 Objective-C 指令，包括字符串常量，都以 `@` 开头（例如 `@interface`、`@selector()`、`@"string"`）。其他显著特征包括使用方括号（`[...]`）来调用方法以及非常具有描述性的方法名称。

但如果你忽略这些特性，就会发现两者之间并没有显著的不协调之处。两者都是面向对象的语言，让你可以定义类、声明实例变量和方法、实例化这些类的实例、从子类继承、重写方法、调用方法、传递参数以及返回值。优秀的 Objective-C 编程遵循的设计模式和实践与你所熟悉的 Java 相同。

### 定义一个 Objective-C 类

Objective-C 类在 `@interface` 指令中定义，其实现则在 `@implementation` 指令中定义。这与 Java 的单一类定义不同，Java 的类定义同时包含了类的接口和实现，如代码清单 3-1 所示。

**代码清单 3-1. Objective-C 类定义**

**Java**

```
import com.apress.java.SuperClass;

public class NewClass extends SuperClass
{
    int instanceVariable;

    Object method( )
    {
        return (null);
    }

    Object method(Object param )
    {
        return (null);
    }
}
```

[www.it-ebooks.info](http://www.it-ebooks.info/)

**Objective-C**

```
#import "SuperClass.h"

@interface NewClass : SuperClass {
    int instanceVariable;
}

-method;
-methodWithParameter:param;

@end

@implementation NewClass

-method
{
    return (nil);
}

-methodWithParameter:param
{
    return (nil);
}

@end
```

代码清单 3-1 显示了在 Java 中定义 `NewClass` 类以及在 Objective-C 中定义等效类的过程。类定义的 `@interface` 部分通常放在头文件（`.h`）中，供其他模块引用，而 `@implementation` 部分则放在源文件（`.m`）中。如果你需要复习 C 语言源文件组织方式，请参阅上一章的“`#include` 和 `#import`”部分。

类声明的 `@interface` 部分包含两个部分。第一部分包含用花括号括起来的实例变量声明。这部分类似于 C 语言的结构体声明。变量声明之后是类的方法原型。连字符前缀表示该方法是实例方法。加号前缀表示类方法，类似于 Java 中的 `static` 方法。

`@implementation` 指令包含在 `@interface` 部分中描述的方法的实际代码。声明一个方法却不实现它会导致错误，但相反的情况是允许的。Java 的一个更优雅的设计特性是，一个类文件同时定义了类的接口和实现。在 Objective-C 中，程序员有责任保持接口和实现的一致性。

`@interface` 和 `@implementation` 部分都以 `@end` 指令结束。

类继承的工作方式与 Java 相同。`NewClass` 类继承了 `SuperClass` 类的所有实例变量和方法。在 `NewClass` 中声明一个与从 `SuperClass` 继承的方法同名的方法，会覆盖继承的方法。

[www.it-ebooks.info](http://www.it-ebooks.info/)

> **注意** 始终在 `@interface` 声明中声明父类，即使父类是 `NSObject`。

`NSObject` 是 Cocoa 框架中的逻辑根类，功能上等同于 Java 的 `Object` 类。但它并不是 Objective-C 的根类。Objective-C 的实际根类 `Object` 过于原始，其直接子类基本上没什么用。你不太可能想要创建一个 `Object` 的直接子类。

### 对象指针

包含指向 Objective-C 对象的指针的变量称为对象指针（引用）或对象标识符。两者都等同于 Java 的对象引用。

代码清单 3-2 显示了声明对象指针的两种方式。对象指针可以声明为指向特定类的指针，也可以声明为通用对象标识符（`id`）。两者都包含指向对象内存地址的指针。区别在于编译器如何处理它们。

**代码清单 3-2. 对象指针声明**

```
SpecificClass *specificObject;
id anyObject;
```

> **注意** 与 Java 一样，Objective-C 不允许变量包含对象的内容，只允许包含指向对象的指针（引用）。声明 `SpecificClass object` 是无效的。这是 Objective-C 对象与 C 语言结构体不同的一个例子；变量可以包含整个 C 结构体或指向 C 结构体的指针，但只能声明指向 Objective-C 对象的指针。

指向特定类的指针在大多数情况下类似于 Java 的对象引用。编译器假定该指针指向该类或其子类的对象。如果你尝试调用该类未定义或未继承的方法，编译器会发出警告。它只允许你访问该类定义或继承的实例变量。该指针隐式地与指向其任何父类的指针兼容，但与指向子类的指针不兼容。

特殊的对象标识符类型（`id`）是一种非特定的对象指针（引用）。它不等同于指向基类对象的指针，例如 `NSObject *obj`（`Object obj`）。编译器假定 `id` 类型的变量可以是任何类。编译器允许你调用任何已定义类中的任何方法。对象标识符可以赋值给任何类型的对象指针，也可以从任何类型的对象指针赋值，而不会产生编译器警告。你不能使用对象标识符直接访问对象的实例变量。



### 如果你把所有对象指针都声明为 `id` 类型

如果你把所有对象指针都声明为 `id` 类型，编程风格将类似于某些脚本语言。编译器不做任何假设，允许你调用任何方法，并将指针赋值给任何其他对象指针变量，而不会报错。这些方法和赋值的恰当性将在运行时进行检验。

在另一个极端，如果你将每个引用都声明为指向特定类的指针，编程体验则类似于 Java。编译器只允许调用指针类中定义的方法，并会标记不当的赋值行为。唯一显著的区别是，在 Java 中，从一种类型强制转换为另一种类型会在运行时检查；而在 Objective-C 中，强制转换仅是简单地抑制编译器警告。

实际应用中的 Objective-C 是这两种极端的结合。当对象的类未知、可变或不确定时，会使用 `id` 类型。而在其他所有情况下，则使用强类型、特定类的指针。例如，`NSError` 对象包含一个 `recoveryAttempter` 对象引用。这个对象负责从错误中恢复。由于 `NSError` 不对其类做任何假设，这个变量的类型是 `id`。`id` 类型允许在不进行强制转换的情况下设置、赋值或调用该属性值上的任何方法。另一方面，`NSError` 的 `domain` 和 `userInfo` 变量被声明为指向 `NSString`（字符串）和 `NSDictionary`（字典）对象的指针。这些变量存储何种类的对象以及它们实现了哪些方法，都不存在歧义。

在运行时，所有指针类型在功能上都是相同的。编译后的代码不保留任何指针类型信息。在代码执行时，清单 3-2 中声明的两个指针的行为将完全相同。

### 发送消息

方法调用语法是 Java 和 Objective-C 之间最显著的区别。一个方法调用由一个对象指针（引用）和要执行的方法名称组成，并用方括号括起来。清单 3-3 中展示了一些示例。

**清单 3-3\. Objective-C 消息语法**

- **Java**

```java
object.method();
object.method(param);
```

- **Objective-C**

```objectivec
[object method];
[object methodWithParam:param];
```

与 Java 不同，Objective-C 不调用对象方法。它向对象发送消息。这不仅仅是语义上的差异。

当你调用方法时，Java 的工作方式与 C 或 C++ 非常相似。语句中的方法名称标识了要执行的方法。调用的参数连同返回地址一起被压入栈中。然后执行流转移到该方法。

Objective-C 调用方法时，首先将方法的参数压入栈中。然后，它调用一个中央分发函数，并将对象指针和一个称为选择器的紧凑型方法标识符传递给它。这个分发函数使用这两者来定位对象的方法，并将执行转移到该方法的代码。方法执行并返回，就像任何 C 或 Java 方法一样。

这种差异可能看起来微不足道，甚至毫无意义，但它是 Objective-C 动态特性的关键，并且对于几个重要且独特的语言特性至关重要。当你阅读本书的各个章节时，这一点会变得更加明显。

为了强调这一概念，Objective-C 使用不同的语言来描述调用方法的过程。在 Objective-C 中，你向对象发送一条消息。调用对象是发送者，其方法被调用的对象是接收者。术语“接收者”在 Objective-C 中被广泛使用，尤其是在文档中（例如短语“……返回接收者中 Unicode 字符的数量”）。“方法”和“消息”这两个术语通常可以互换使用。

### 命名方法

Objective-C 方法名称很冗长，由一个或多个自然描述每个参数的关键词组成。清单 3-4 展示了四个假设的 Objective-C 方法，分别带有零个、一个、两个和三个参数。

**清单 3-4\. 命名参数**

```objectivec
-method;
-methodWithParameter:parameter;
-methodWithContext:context object:object;
-methodWithDialog:dialog message:message behindWindow:window flags:mode;
```

清单 3-4 中的第一个 Objective-C 声明定义了一个不带参数的方法。这等同于 Java 的 `Object method()`。第二个声明定义了一个带一个参数的方法，等同于 `Object method( Object parameter )`。

冒号后面的标识符是参数变量的名称；这是你将在方法代码中用于引用该变量的变量名（例如，`dialog`、`message`、`window`、`mode`）。

参数变量的名称和构成方法名称的关键词位于不同的命名空间中。它们可以相同，也可以不同。在方法实现的主体中，只有参数变量名在作用域内。

> **注意：** 与 Java 一样，参数名与对象的实例变量在同一个作用域内。一个实例变量（例如，`cell`）和一个同名的参数变量会造成一些歧义。与 Java 一样，单独的符号名称始终引用参数或局部变量。`cell` 实例变量则必须使用 `self->cell` 间接寻址。与 Java 不同，Objective-C 编译器会发出警告，提示“'cell' 的局部声明隐藏了实例变量”。为避免警告和任何可能的歧义，大多数 Objective-C 程序员会选择不与任何实例变量冲突的参数名称，例如使用 `aCell` 或 `newCell` 这样的名称，而不是仅仅使用 `cell`。

在 Objective-C 中命名方法是一门艺术。Objective-C 方法名称通常使用清单 3-5 中所示的形式之一构建。该清单显示了一些抽象的方法名称，后面跟着几个真实世界的例子。

**清单 3-5\. 方法命名示例**

- **简单操作：**
  ```objectivec
  -action;
  -draw;
  -play;
  -becomeKeyWindow;
  ```

- **带参数的操作方法：**
  ```objectivec
  -actionParameter:param;
  -drawCell:cell;
  -sendEvent:event;
  ```

- **带多个参数的操作：**
  ```objectivec
  -actionParameter:firstParam secondParameter:secondParam;
  -replaceSubview:oldView with:newView;
  -postNotificationName:name object:sender;
  ```

- **返回值的方法：**
  ```objectivec
  -value;
  -key;
  -window;
  -stringByExpandingTildeInPath;
  ```

- **带参数并返回值的方法：**
  ```objectivec
  -valuePrepositionParameter:parameter;
  -objectForKey:key;
  -stringByAppendingString:string;
  -numberFromString:string;
  ```

如果你牢记以下几点，阅读和发明你自己的方法名称就不会太困难。方法名称的通用形式以对返回值的描述（如果有）开头，然后是方法执行的操作，最后是对第一个参数（如果有）的描述。一个名为 `-menuForEvent:` 的方法可以合理地推测为接受一个 `NSEvent` 对象作为参数，并返回一个 `NSMenu` 对象。后续的关键词描述该参数，并且通常包含一个介词，例如“in”、“to”或“with”。参数的描述有时是隐含的，例如在 `-replaceSubview:oldView with:newView` 中。避免包含多余的动词，如“do”或“does”。

Objective-C 不像 Java 那样支持仅参数类型不同而方法名称相同的方法重载。但这实际上并无必要。如果两个 Objective-C 方法仅参数不同，它们将具有不同的名称：`-drawRect:rect`、`-drawCell:cell`、`-drawPage:page`，而不是 Java 中等价的重载函数 `draw( Rect rect )`、`draw( Cell cell )`、`draw( Page page )`。

没有一成不变的规则来命名方法。主要目标是可读性。



