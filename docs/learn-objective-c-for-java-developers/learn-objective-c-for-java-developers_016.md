# Objective-C 与 C 语言概念

即便从未自行创建结构体、定义 `typedefs`，或使用指向对象之外变量的指针，你也能成功编写 Objective-C 程序。不过，你仍需要对这些概念有所了解，因为你会其他 Objective-C 代码中遇到它们。我猜想，你最终会想要更深入地探索 C 语言类型，哪怕只是为了使用那些带 C 语言接口的库。

### 数组

C 语言数组同样属于你需要了解、但可能暂时还用不到的知识。C 语言数组与 Java 数组不同。C 语言数组定义了一组占据连续内存地址的值。它不是一个对象，不提供边界检查，并且通常通过指针来操作。

清单 2-11 中的语句声明了一个包含 10 个整型变量的数组，然后获取了数组中第四个整数的值。与 Java 数组类似，可以使用数组下标语法（数组访问表达式）来寻址单个元素。

**清单 2-11.** C 语言整型数组

```
int numbers[10];
int j = numbers[3];
```

C 语言数组和指针紧密交织。在清单 2-12 中，符号 `numbers`（单独使用）会求值为数组第一个元素的地址，等价于表达式 `&numbers[0]`。

**清单 2-12.** 数组与指针的可互换性

```
int *iptr = numbers;
iptr += 3;
if (*iptr==j) …
```

清单 2-12 中的指针 `iptr` 首先被赋值为数组中第一个元素的地址。第二条语句使用指针运算调整了指针。指针运算（在指针值上加或减一个整数值）通过将 `n*sizeof(类型)` 加到指针的地址值上，使指针指向当前地址之后的第 n 个值。在此例中，假设一个整数的大小为 4 字节，那么给一个整型指针加 3，会将其内存地址调整 12 字节。之后，`iptr` 变量指向数组的第四个元素，表达式 `numbers[3]` 和 `*iptr` 是等价的。

> **注意：** `sizeof()` 运算符是一个编译时函数，它会计算所包含类型的大小（以字节为单位）。参数可以是类型或变量。以清单 2-11 为例，表达式 `sizeof(int)`、`sizeof(j)` 和 `sizeof(numbers[3])` 都是等价的，并且都求值为整型常量 4——即一个 32 位整数的字节大小。表达式 `sizeof(numbers)` 求值为 40，即 `10*sizeof(int)`，因为 `numbers` 是一个包含 10 个整数的数组。你经常会看到诸如 `(sizeof(numbers)/sizeof(int))` 的表达式，用于确定数组中的元素个数。

C 语言指针和数组变量本质上是可互换的。数组通常被当作指针来赋值和传递（传递的值将是数组第一个元素的地址）。在指针上使用数组下标语法也是允许的，例如 `iptr[3] = 0`。这等价于 `*(iptr+3) = 0`。

Objective-C 提供了一个对象数组类，并为大多数基本类型提供了对象包装器。它们的行为与你熟悉的 Java 数组和集合非常相似。虽然你需要了解 C 语言数组是如何定义和访问的，但在你熟练掌握 C 语言数组、内存管理和指针运算之前，可以优先使用面向对象的数组类来避开它们。

### `static`

`static` 关键字在 C 语言中有一些额外的含义，并且其使用方式与 Java 不同。Java 使用 `static` 在类声明中声明一个单一的持久类变量。Objective-C 不允许你在类定义中声明变量。相反，`static` 变量被声明为全局 C 变量或代码块内部的 `static` 变量。清单 2-13 声明了两个 `static` 变量。

**清单 2-13.** 全局静态变量

```
int scramingZombieHitCount = 1;          // 所有模块均可访问
static int screamingZombieBounceCount;   // 仅此模块可访问
```

在任何方法、结构体或类定义之外声明的变量会创建一个全局变量，大致等价于 Java 的 `static` 变量。全局变量在启动时会被预初始化为零，除非显式初始化为其他值。在此上下文中，`static` 关键字仅决定符号的作用域（所有全局变量在 Java 意义下都是“静态的”）。省略 `static` 关键字会使该符号全局可访问。程序中的任何模块都可以通过 `extern` 语句按名称访问它。包含 `static` 关键字则会将变量的作用域限制在包含该声明的文件内。因此，`screamingZombieHitCount` 可以被程序中任何模块直接使用，而 `screamingZombieBounceCount` 仅能由此文件中的代码访问。

在代码块内部，声明为 `static` 的变量会创建一个持久的静态变量。该变量的作用域被限制在其声明的代码块内。清单 2-14 展示了一个每次执行代码块时都会递增的 `static` 整数。

**清单 2-14.** 代码块中的静态变量

```
{
    static int hitCount = 0; // 全局命中次数，启动时初始化为 0
    hitCount++;              // 增加全局命中次数变量
    return (hitCount);       // 返回更新后的命中次数
}
```

### 函数

C 语言是一种过程式语言，没有对象的概念。在 C 语言中，可调用的代码块被称为函数。一个 C 函数等价于 Java 中的 `static` 类方法；它没有对象上下文。C 函数名没有作用域限制（不在类或包内），它们不能被继承，也不能被重写。清单 2-15 展示了一个比较两个 Objective-C 日期对象并返回较早日期的 C 函数。

**清单 2-15.** C 函数

```
NSDate* earlierDate( NSDate* a, NSDate* b )
{
    if (a==nil)
        return (b); // 如果 a 和 b 都为 nil，则返回 nil
    if (b==nil || [a compare:b]==NSOrderedAscending)
        return (a);
    return (b);
}
```

函数调用语法与 Java 相同：`NSDate *first = earlierDate(sometime, whenever)`。`earlierDate()` 函数对所有模块全局可访问。函数定义前的 `static` 关键字会将其作用域限制在正在编译的模块内，这与 `static` 变量的情况相同。

### `extern`

一个模块使用 `extern` 语句来访问定义在另一个模块中的全局变量或 C 函数。`extern` 语句通常放在定义它的模块的头文件中（参见清单 2-16）。这仅对定义在其他模块中的全局变量和函数是必需的。你不需要使用 `extern` 语句来声明或引用 Objective-C 类。

**清单 2-16.** 外部变量声明

```
extern int screamingZombieHitCount;
extern void killAZombie( Zombie *victim );
```

清单 2-16 中的语句声明了其他某个模块已经定义了一个名为 `screamingZombieHitCount` 的全局整型变量（来自清单 2-13）和一个名为 `killAZombie` 的 C 函数。编译器在编译此模块时假定该变量和函数存在于某个（或 20 个）其他模块中。链接器负责在构建应用程序时，将此模块的引用连接到实际的变量位置和函数地址。

### 预处理器

C 语言预处理器是一种文本宏语言，（概念上）它在源文件文本被输入到 Objective-C 编译器之前应用于该文本。当编译源文件时，预处理器首先扫描文件文本，执行所有预处理指令并执行任何文本替换。Java 没有类似的功能。

你应该了解的重要预处理语句包括 `#include`、`#import`、`#define` 和 `#if`。



### 预处理器指令（`#if`等）

所有预处理器指令都以换行符分隔。Java 和 C 通常忽略换行符，将它们视为与任何其他空白字符无异。C 预处理器一次处理一行，因此预处理器指令不能与 Objective-C 源代码混在同一行上。

### `#include` 和 `#import`

`#include` 和 `#import` 语句将另一个源文件的内容插入到正在编译的文件中，如代码清单 2-17 所示。`#include` 和 `#import` 语句可以嵌套。也就是说，一个文件可以 `#include` 另一个文件，而该文件又 `#include` 第三个文件，依此类推。

**代码清单 2-17. `#include` 和 `#import` 指令**

```
#include <Cocoa/Cocoa.h>
#import "MyClass.h"
```

C 编译器对其他文件中定义的类或符号一无所知。要使用任何类或符号，正在编译的源代码必须首先定义它。这与 Java 编译器不同，Java 编译器会自动查找并解释其他 `.java` 文件，以获取其他类的定义。

显然，手动输入计划使用的每个类或符号的定义是不切实际的。C 语言社区早就通过一种简单的文件组织方式解决了这个问题。每个模块被分为两个文件：一个源文件和一个头文件。Objective-C 源文件（有时称为实现文件）包含方法的代码，并以 `.m` 文件扩展名保存。头文件（有时称为接口文件）仅包含程序员希望公开供其他模块使用的类定义、变量和常量。

源文件不可避免地以一系列 `#include` 和 `#import` 语句开头，以获取模块所需的定义，如代码清单 2-17 所示。随后是实现该模块中方法的代码。实际上，这类似于 Java 的 `import` 语句。但与简单地声明引入作用域的包名（并将查找这些定义的工作留给 Java）不同，`#import` 指令会插入模块头文件的内容，以声明所需的类和常量。

Java 要求一个完整的类必须在一个文件中定义。虽然可以在一个文件中包含多个 Java 类，但不可能将一个类拆分到多个文件中。Objective-C 没有此类限制，但强烈建议将整个类定义在单个源文件中。Objective-C 的类别（categories）——将在后续章节中介绍——是一种用于将单个类细分为多个部分的正式编程模式。但在开始编写类别之前，请将一个类的所有定义放在以该类命名的头文件中（例如 `MyClass.h`），并将实现该类方法的所有代码放在同名的源文件（例如 `MyClass.m`）中。`MyClass.m` 文件应以导入其 `MyClass.h` 文件开头。然后，为实现需要了解的其它类添加 `#import` 指令。代码清单 2-18 展示了 `StringPool` 类的一对典型源文件的起始部分。

**代码清单 2-18. `StringPool` 类源文件的第一行**

```
// StringPool.h
#include <libkern/OSAtomic.h>
#import <Cocoa/Cocoa.h>

@interface StringPool : NSObject {
@private
    NSHashTable *strings;
    OSSpinLock spinLock;
}
…

// StringPool.m
#import "StringPool.h"
#import "InverseHashTable.h"
#import "StringUtilities.h"

@implementation StringPool
…
```

`#import` 与 `#include` 略有不同。`#include` 无条件地将另一个文件的内容插入进来，而 `#import` 仅在该文件尚未被插入时才插入它。所有类定义头文件都应使用 `#import`，因为它避免了重复包含头文件的可能性，而重复包含会导致重复定义错误。

`#include` 和 `#import` 都接受一个文件名，该文件名由双引号或尖括号分隔。



在包含系统头文件时使用尖括号，在包含自己项目中的文件时使用双引号。

`#define`

`#define`指令创建一个文本宏，如清单 2-19 所示。文本宏会用宏的内容替换源代码中的标记。宏可以包含参数，这些参数看起来类似于函数定义，并执行复杂的替换。更常见的是，预处理器宏简单地用于定义常量。

清单 2-19\. 定义字面常量

**Java**

```
final int MAX_ZOMBIES = 999;
final int MANY_ZOMBIES = MAX_ZOMBIES-100;
```

**C**

```
#define MAX_ZOMBIES 999
#define MANY_ZOMBIES (MAX_ZOMBIES-100)
```

一旦定义了`MAX_ZOMBIES`文本宏，之后任何出现的标记`MAX_ZOMBIES`都将被替换为文本“999”。预处理器会重写语句`if (zombieCount>=MAX_ZOMBIES)`，即替换该标记。Objective-C 编译器最终将编译语句`if (zombieCount>=999)`。Objective-C 编译器从未看到`MAX_ZOMBIES`符号，因为预处理器标记替换是在 C 语言编译器阶段之前执行的。

标记替换仅在单词边界上进行，不会发生在字面字符串或注释内部。

标记替换是递归的。代码`if (zombieCount>MANY_ZOMBIES)`会被替换为`if (zombieCount>(MAX_ZOMBIES-100))`，然后进一步被替换为`if (zombieCount>(999-100))`。为了避免意外的求值顺序，请将包含运算符的任何预处理器宏用括号括起来。

另外请注意，预处理器宏不是 C 语言语句。它们不使用等号进行赋值，也不以分号结尾。如果这样做，这些符号会被包含在替换后的文本中。指令`#define MAX_ZOMBIES = 999;`会将 C 语句`if (zombieCount>=MAX_ZOMBIES)`变成`if (zombieCount>== 999;)`，最终导致编译器错误。

`#if`

`#if`指令在 Java 中没有对应的功能。`#if`指令根据表达式的值包含或忽略一个文本块，如清单 2-20 所示。

清单 2-20\. `#if`预处理器指令

```
#if LOG_OUTPUT > 1
NSLog(@"game now contains %d zombies",zombieCount);
#endif
```

清单 2-20 中`#if`指令和`#endif`指令之间的文本仅在表达式求值为非零值时才会被编译。该表达式只能使用编译时已知的常量。在此示例中，仅当`LOG_OUTPUT`预处理器宏求值大于 1 时，`NSLog`语句才会被编译。否则，该文本不会被编译，就像该`NSLog`语句被注释掉一样。

你可以在`#if`和`#endif`指令之间放置一个`#else`指令。`#if`和`#else`之间的文本在条件表达式为真时被包含。`#else`和`#endif`之间的文本在条件为假时被包含。

`#ifdef`和`#ifndef`是`#if`指令的两种便捷变体。它们的参数都是一个单独的预处理器宏名称。如果该宏已被定义（无论其值是什么），`#ifdef`会包含直到`#endif`的文本。`#ifndef`则相反，仅当该宏名称从未被定义时才包含文本。这些指令常用于根据不同环境修改代码，如清单 2-21 所示。

清单 2-21\. `#ifdef`预处理器指令

```
#ifdef DEVELOPMENT_VERSION
NSAssert(poolSize<256,@"pool overflow"); // alert developer
#else
if (poolSize>=256)
    return; // return immediately if pool overflows
#endif
```

在源文件中或通过构建设置定义预处理器宏`DEVELOPMENT_VERSION`，将会编译代码以在`poolSize`变量大于或等于 256 时抛出断言（程序异常）。如果`DEVELOPMENT_VERSION`宏未被定义，则会编译`if/return`语句。



`#if`指令可以嵌套。被`#if`指令省略的文本同样会被预处理器忽略，这使得我们可以有条件地包含文件或有条件地定义其他预处理宏。

通常也会看到使用`#if 0 … #endif`来注释掉大块不需要或实验性的代码。

### 初始化自动变量

Java 确保所有变量在使用前都被初始化为一个可预测的值。这是 Java 的“明确赋值”规则。类似地，Objective-C 在创建对象时将所有实例变量初始化为零。C 语言将所有静态变量初始化为零，除非显式初始化为其他值。

然而，C 语言不会初始化自动（局部）变量，也不要求它们在使用前被初始化（参见清单 2-22）。

**清单 2-22. 未初始化的自动变量**
```
{
int i;
while (i==0)
…
}
```
清单 2-22 中的整型变量`i`是一个分配在方法栈帧上的自动（局部）变量。C 语言不要求将其初始化为任何值。如果没有初始化，其值是不可预测的。它将是从前占据该栈或 CPU 寄存器中那个字位置的任何值。请确保在使用自动变量之前对其进行初始化。

### 标签：break、continue 和 goto

C 语言的`break`和`continue`语句执行的功能与其 Java 对应项相同。

Java 标签标识一个代码块，通常是`for`或`while`循环。Java 的`break`或`continue`语句可以选择性地指定一个外层控制块的标签，从而允许退出该命名块或跳转到其末尾。C 语言的`break`和`continue`语句不接受标签。

在 C 语言中，执行控制更为宽松。除了将异常流程控制限制在`break`和`continue`语句之外，C 语言还提供了通用的`goto`语句。C 语言标签标识代码块中的一个语句。`goto`语句立即将执行转移到由标签标识的代码。标签和`goto`语句可以出现在函数或方法内的任何位置，如清单 2-23 所示。

**清单 2-23. break、continue 和 goto**

**Java**
```
int segment[][] = new int[100][100];
...
int optimalLength = 200000;
int lineLength = 0;
int bestFit = 0;
lineLoop:
for (int i=0; i<100; i++) {
    lineLength = 0;
    for (int j=0; j<100; j++) {
        int s = segment[i][j]; // get segment length
        if (s==0)              // stop at zero length
            break;
        lineLength += segment[i][j]; // accumulated line length
        if (lineLength>optimalLength) // line too long
            continue lineLoop;
        if (lineLength==optimalLength) // line perfect fit
            break lineLoop;
        if (lineLength>bestFit)       // remember best fit
            bestFit = lineLength;
    }
}
return (lineLength);
```

**C**
```
int segment[100][100];
...
int optimalLength = 200000;
int lineLength;
int bestFit = 0;
for (int i=0; i<100; i++) {
    lineLength = 0;
    for (int j=0; j<100; j++) {
        int s = segment[i][j];
        if (s==0)
            break; // Same as Java
        lineLength += segment[i][j];
        if (lineLength>optimalLength)
            goto nextLine; // Jump to end of outer loop
        if (lineLength==optimalLength)
            goto stop; // Continue after outer loop
        if (lineLength>bestFit)
            bestFit = lineLength;
    }
nextLine:
    ;
}
stop:
return (lineLength);
```

清单 2-23 中的代码片段演示了`break`、`continue`和`goto`语句的使用。`goto nextLine`语句继续在外层循环控制块的末尾执行，这等同于 Java 中的`continue lineLoop`语句。`goto stop`语句跳转到紧随外层循环之后的语句，等同于`break lineLoop`语句。

`nextLine`标签说明了 C 语言标签的一个特性：它们必须位于一个可执行语句之前。在其后跟一个空语句（`;`）即可满足此要求。



