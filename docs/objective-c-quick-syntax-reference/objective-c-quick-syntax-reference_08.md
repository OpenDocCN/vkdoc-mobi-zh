# 3. 变量

**摘要**  
Objective-C 将信息存储在变量中。这些变量分为两种类型：基本类型和复合类型。基本变量存储单条信息，例如一个数字或一个字符。复合变量存储一组信息，例如三个相关的数字和一个字符。

## 变量定义

Objective-C 将信息存储在变量中。这些变量分为两种类型：基本类型和复合类型。基本变量存储单条信息，例如一个数字或一个字符。复合变量存储一组信息，例如三个相关的数字和一个字符。

### 数据类型

表 3-1 显示了你在 Objective-C 中会看到的最常见的基本数据类型。

**表 3-1.** Objective-C 数据类型

| 数据类型 | 格式说明符 | 描述 |
| --- | --- | --- |
| `NSInteger` | `%li` | 有符号整数 |
| `NSUInteger` | `%lu` | 无符号整数 |
| `BOOL` | `%i` | 布尔值 (YES/NO) |
| `CGFloat` | `%f` | 浮点数 |

> **注意**  
> Objective-C 程序除了可以使用表 3-1 中列出的 Objective-C 数据类型外，还可以使用 C 语言的数据类型，如 `int`、`long`、`float`、`double` 和 `char`。这是因为 Objective-C 基于 C 编程语言，因此除了我们在这里讨论的 Objective-C 语法外，它还继承了 C 语言的所有功能。

### 声明变量

在 Objective-C 中声明变量时，需要先指定数据类型，然后是变量名。使用变量之前必须先声明。变量名应该有意义，但你可以随意命名变量。

以下是在 Objective-C 中声明一个整数的方法：

```
NSUInteger numberOfPeople;
```

### 赋值

你可以使用赋值运算符（`=`）给变量赋值，如下所示：

```
numberOfPeople = 100;
```

一旦赋值完成，你就可以通过引用变量名来检索和使用该值。

```
NSLog(@"The number of people is %lu", numberOfPeople);
```

> **注意**  
> 你可能已经注意到 `NSLog` 语句中需要 `%lu` 符号。这个符号被称为格式说明符，`NSLog` 会将其用作占位符，在字符串后面的逗号分隔列表中插入值。关于你必须用于 Objective-C 数据类型的格式说明符列表，请参见表 3-1。

如果你愿意，也可以在同一行声明变量并赋值。

```
NSUInteger numberOfGroups = 20;
```

### 整数类型

整数是整数数字，因此任何不需要小数点的数字都是整数。在 Objective-C 中，整数使用数据类型 `NSInteger` 和 `NSUInteger` 来表示。

`NSUInteger` 是无符号整数，这意味着它们只能是正数。`NSUInteger` 可以取的最大值取决于编译 Objective-C 代码的系统。如果你为 64 位 Mac 编译，最大值为 18,446,744,073,709,551,615。

对于 iPhone 5 及以下的 32 位平台，最大值是 4,294,967,295。你可以使用 `NSUIntegerMax` 常量自行检查这些数字。

```
NSLog(@"NSUIntegerMax is %lu", NSUIntegerMax);
```

`NSInteger` 是有符号整数，这意味着它们可以是正数或负数。`NSInteger` 的最大值是 `NSUInteger` 值的一半，因为 `NSInteger` 必须同时支持正数和负数。

因此，如果你需要处理巨大的数字，可能需要坚持使用 `NSUInteger`，但如果你需要同时处理正数和负数，则需要使用 `NSInteger`。你可以使用 `NSIntegerMin` 和 `NSIntegerMax` 常量检查你系统上 `NSInteger` 的最小值和最大值。

```
NSLog(@"NSIntegerMin is %li", NSIntegerMin);
NSLog(@"NSIntegerMax is %li", NSIntegerMax);
```

### 布尔类型

当值可以为真或假时，会使用布尔数据类型。在 Objective-C 中，此数据类型声明为 `BOOL` 类型。`BOOL` 类型的值要么是 `YES`，要么是 `NO`。

```
BOOL success = YES;
```

由于 Objective-C 将 `BOOL` 值存储为 `1`（代表 `YES`）和 `0`（代表 `NO`），因此你必须使用 `%i` 格式说明符来打印 `BOOL` 值。`%i` 是另一个用于整数的格式说明符。

```
NSLog(@"success is %i", success);
```

上面的 `NSLog` 语句对于 `YES` 会打印出 `1`，对于 `NO` 会打印出 `0`，但有些人更喜欢在日志中看到 `YES` 或 `NO` 字符串。你可以使用以下替代语句来实现这一点：

```
NSLog(@"success: %@", success ? @"YES" : @"NO");
```

这里变量 `success` 被替换为一个需要求值的语句。该语句将根据变量 `success` 的值返回字符串 `YES` 或字符串 `NO`。如果 `success` 为零，则返回语句中最后位置的内容；如果 `success` 是任何其他值，则返回第一个位置的内容。三元运算符（`?`）告诉编译器对语句进行求值。



### 浮点类型

在 Objective-C 中，浮点类型使用 `CGFloat` 数据类型来表示。当需要在数字中包含小数位时，就会用到 `CGFloat`。例如，如果要表示一个百分比，可以这样写：

`CGFloat percent = 33.34;`

在 32 位系统中，`CGFloat` 类型的最大值可通过 `FLT_MAX` 获取。而在 64 位系统中，则必须使用 `DBL_MAX`。

### 作用域

与大多数源自 C 语言的编程语言类似，Objective-C 变量的作用域由花括号 `{ }` 的位置决定。当用 `{ }` 将代码行括起来时，就定义了一个代码块。放置在代码块内部的变量只能在该代码块内部使用，这被称为作用域。

例如，我们来看之前的一个例子：声明了一个名为 `numberOfPeople` 的无符号整数，给该变量赋值，然后将其值打印到日志中。

`NSInteger numberOfPeople;`

`numberOfPeople = 100;`

`NSLog(@"The number of people is %li", numberOfPeople);`

这段代码运行得非常好，因为变量 `numberOfPeople` 在你需要它的整个过程中都保持在其作用域内。但是，如果你用花括号将前两行代码括在它们自己的区域内，那么在赋值时变量可以正常工作，但在尝试将值输出到日志时就会失败。如果你试图在花括号定义的作用域之外将 `numberOfPeople` 输出到日志，程序将无法编译。

```
{
    NSInteger numberOfPeople;
    numberOfPeople = 100;
}
NSLog(@"The number of people is %li", numberOfPeople);
```

作用域用于为函数、循环、方法、if 语句和 switch 语句定义代码块。所有这些内容都将在本书后面讨论。

