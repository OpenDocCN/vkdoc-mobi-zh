# 6. 块

## 摘要

Objective-C 的总体目标之一是通过使用面向对象概念来减少编写复杂应用程序所需的工作量，这有助于简化和阐明编程实体之间的关系。在本章中，你将学习如何创建和使用`blocks`，这是一种使语言更易于使用且更具表现力的语法特性。

`block`是一段可以在应用程序其他部分传递的代码，无需创建单独的函数、方法或类。此外，`block`不仅保留了包含在其中的代码信息，还保留了创建`block`时作用域内的变量信息。这样，`block`就类似于闭包的概念，这在其他编程语言（如`Python`和`Ruby`）中是可用的。在`Java`中，类似的功能将由匿名类提供，而在`C++`中则由`lambda`函数提供。

通过定义`blocks`，你可以使某些算法的操作更具扩展性，因为客户端现在可以通过`block`参数提供实现的关键部分。由于这些特性，`blocks`在`Objective-C`程序中有广泛的适用性。例如，你也可以使用`blocks`实现能在并行线程中无缝运行的多处理代码，正如目前一些`Objective-C`库所实现的那样。

在本章中，你首先将学习`blocks`的基本概念。然后，你将了解更多关于从`block`内部访问变量的信息，包括局部变量和实例变量。接着，我将讨论影响`blocks`内存分配的问题，以及在创建它们的上下文之外访问它们的问题。最后，你将看到一些在你的应用程序中以及`Foundation`框架的类中使用`blocks`的示例。

## 介绍 Blocks

`block`是一组可以由程序操作指令。例如，`block`可以保存在变量中，并在需要时执行。`block`表面上可以比作函数指针，因为两者都提供了存储和执行代码的机制。然而，这两个概念之间有一些重要的区别。

`blocks`和函数指针之间的第一个区别是，`block`字面量可以在任何可执行语句出现的位置声明。因此，例如，`block`可以在函数中间声明，或者作为方法调用的一部分。这对于函数来说是不可能的，因为它们不能嵌套：根据`C`语言的规则，函数只能在实现文件的顶层声明。

`blocks`和函数指针之间的第二个区别也是使用`blocks`的最大优势之一。`block`可以捕获定义它的上下文中活动的任何变量的值和地址。例如，`block`可以使用在方法中声明的局部变量，即使在原始方法本身的执行完成之后也是如此。这个属性也是闭包的定义属性之一，闭包是一种在函数式语言中效果显著的编程结构。

`blocks`的这些特性允许创建一些有趣的算法，否则很难用同样的简洁性来复制。例如，`blocks`可以用来模拟对复杂数据类型（如`for`循环）的控制结构的使用。它们也可以用来为排序过程提供替代代码，你将在本章后面看到。

## 声明 Blocks

`blocks`可以存储在变量中。要声明一个`block`变量，你需要使用类似于声明函数指针的表示法。但是，你使用`^`字符而不是星号来标记它的定义。这是一个将在下一段中解释的简单示例：

```
- (void) simpleBlock
{
    int (^b)(int); // 声明一个 block 变量
    b = ^ int (int a) // 创建一个 block 并将其存储在 b 中
    {
        return a + 1;
    };
}
```

要使用`block`作为变量或方法声明的一部分，首先需要声明你感兴趣使用的`block`类型。如前所述，`blocks`与函数指针相关，因为它们都提供了引用可执行代码段的方法。因此，它们使用类似的声明风格是很自然的。然而，`blocks`有一些语法上的细微差别，你将在下面看到。

要声明一个变量持有`block`，你需要使用插入符表示法。在这种表示法中，变量的名称出现在`^`字符之后并在括号内，位于通常函数名称出现的位置。例如，要声明一个接收两个整数并返回`double`的`block`，你将使用以下声明：

```
- (void) divisionBlock
{
    double (^division)(int, int); // 声明一个 block
    // 在此处使用 block...
}
```

在定义了`block`变量之后，下一步逻辑是设置其值以持有一个新的`block`。你可以使用`block`字面量来完成，语法如下所示：

```
- (void) divisionBlock
{
    double (^division)(int, double); // 声明一个 block
    division = ^ double (int a, double b) // 将 block 保存到变量中
    {
        return a / b;
    };
}
```

在这里，你将`division`变量的值设置为持有插入符后面的`block`。注意，`block`的定义方式也与函数的实现方式非常相似，不同之处在于其定义可以直接出现在将要使用它的位置，就像其他字面值一样。

虽然上述表示法与函数定义差别不大，但`block`语法提供了一些简化，可以大大减少你在这些情况下需要编写的代码量。例如，如果某些定义元素从上下文中变得清晰，则可以省略它们。第一个可以省略的是返回类型，因为编译器可以从返回值本身获取该信息。

```
- (void) divisionBlock
{
    double (^division)(int, double);
    division = ^ (int a, double b)
    {
        return a / b;
    };
}
```

其次，如果参数列表为空，则可以省略参数列表。也就是说，如果没有参数传递给`block`，则不需要额外的括号。这是一个示例：

```
- (void) getPI
{
    double (^f)(); // 声明一个新的 block
    f = ^ {
        return 3.14;
    };
}
```

这段代码定义了一个新的`block`变量`f`，然后向其分配一个返回代表数学常数 Pi 的字面数字的`block`。

要调用由`block`定义的代码，可以使用与函数相同的方式来使用`block`变量的名称。

```
- (void) divisionBlock
{
    double (^division)(int, double); // 声明一个 block
    division = ^ (int a, double b) // 将 block 保存到变量中
    {
        return a / b;
    };
    double result = division(1, 2);
    NSLog(@"the result is %lf", result); // 将会打印 0.5
}
```



### 理解复杂块声明

块不仅可以存储在局部变量中，还可以用作函数或方法的参数，甚至可以作为返回值。在这种情况下，块的声明会变得越来越复杂。例如，以下是一个与你之前见过的类似块作为参数的用法：

```
- (void) useBlock:(double (^)(int, int))aBlock
{
        aBlock(1, 2);
}
```

你可以通过将 `(^)` 字符视为定义块，而周围的类型则是返回值和参数类型来解读此声明。与 Objective-C 方法中的任何其他参数一样，它们被包裹在一对圆括号内，后跟参数的正式名称，在此例中名为 `aBlock`。

最后，是一个如何声明返回块的方法示例：

```
- (double (^)(int, int)) returnABlock
{
        // 在此处返回块
}
```

解读此类声明的过程与我在之前示例中解释的类似。然而，你会注意到，随着参数和返回值数量的增加，这可能会变得相当复杂。简化此过程的一种方法是使用 C 语言提供的 `typedef` 机制。

`typedef` 只是较长类型声明的一种快捷方式。然而，尽管引入了一个新名称，但使用 `typedef` 声明的类型在实际应用中与原始名称完全相同。`typedef` 的一个简单用法如下：

```
typedef int MyNewIntType;
```

在这种情况下，`MyNewIntType` 现在可以像预期使用 `int` 类型一样，在任何地方用作类型名称。然而，`typedef` 的一个更好用途是简化复杂的块（以及函数指针）变量声明。以下是改进前一个示例的方法：

```
typedef double (^MathBlock)(int, double);

- (void) divisionBlock
{
        MathBlock division; // 声明一个块
        division = ^ (int a, double b)
        {
                return a / b;
        };
        double result = division(1, 2);
        NSLog(@"the result is %lf", result);
        // 这将打印结果为 0.500
}
```

首先，这里有 `typedef` 语句，它将 `MathBlock` 声明为块类型的新名称。通常，在 `typedef` 表达式中，类型的新名称出现在变量名通常出现的位置。声明 `MathBlock` 后，你可以在任何需要该类型名称的地方使用它。使用 `typedef` 的主要优点是无需重新键入像这样复杂的块定义。此外，它还使得在声明同一定义行上初始化块变得更加容易。例如，上述示例也可以写成：

```
- (void) divisionBlock
{
        MathBlock division = ^ (int a, double b)
        {
                return a / b;
        };
        double result = division(1, 2);
        NSLog(@"the result is %lf", result);
        // 这将打印结果为 0.500
}
```

这允许你在此行声明时同时初始化该块。同一个 `typedef` 也可以用于方法参数的声明。因此，前一个示例可以重写为以下方式：

```
typedef double (^DoubleIntIntBlock)(int, int);

- (void) useBlock:(DoubleIntIntBlock)aBlock
{
        aBlock(1, 2);
}
```

并且返回值也类似：

```
- (DoubleIntIntBlock) returnABlock
{
  // 在此处返回块
}
```

## 将块作为参数传递

如你所见，创建接收块作为参数的函数方法很容易。同样，将块作为参数传递给函数或方法也相对直接。例如，考虑之前定义的方法：

```
- (void) useBlock:(DoubleIntIntBlock)aBlock;
```

要使用这样的方法，你可以先定义一个变量并将其作为参数传递，如下所示：

```
- (void) callBlockMethod
{
        DoubleIntIntBlock myBlock;
        myBlock = ^ double (int a,int b)
        {
                NSLog(@"传入的值是 %d 和 %d", a, b);
                return 1.0 + a + b;
        };
        [self useBlock:myBlock];
}
```

另一种方法是直接将块用作消息调用的一部分。

```
- (void) callBlockMethod
{
        [self useBlock:^ double (int a,int b)
        {
                NSLog(@"传入的值是 %d 和 %d", a, b);
                return 1.0 + a + b;
        }];
}
```

在这里，你定义了一个未命名的块，并立即将其用作方法调用的唯一参数。该块作为参数传递给 `useBlock:` 方法，该方法可以直接调用它或保存起来以供后续使用。



## 访问外部变量

块（block）区别于标准函数指针的特性之一，就是块可以访问其所在代码封闭区域中的变量。这极大简化了块的使用，因为无需通过函数参数或额外类来复制变量值。块默认对局部变量或实例变量具有只读访问权限（这些变量需在定义位置可访问）。通过使用特殊关键字`__block`，也可以修改此类变量。最简单的数据访问情形是对局部变量的只读访问。创建块时无需程序员额外操作即可实现。

```
- (void) showReadAcccess

{
        int myVariable = 2;
        int (^getMultiple)();
        getMultiple = ^ {
           return 5 * myVariable ;
        };
        NSLog(@"结果如下: %d", getMultiple());
        // 这将打印值 10
}
```

在本例中，声明了一个名为`myVariable`的整型变量。随后定义了一个名为`getMultiple`的块，它返回`myVariable`中存储值的倍数。调用该块后，日志窗口会显示计算结果。

对于定义函数的类中的实例变量，也可以实现类似的数据访问。例如，考虑以下类声明，其中包含一个整型实例变量：

```
// 文件 Blocks.h
@interface Blocks : NSObject
{
        int _myIntValue;
}
- (void) callBlockMethod;
@end
```

对应的实现如下：

```
- (void) showInstVarReadAcccess
{
        _myIntValue = 3;
        int (^getMultiple)();
        getMultiple = ^ {
           return 6 * _myIntValue ;
        };
        NSLog(@"结果如下: %d", getMultiple());
        // 这将打印值 18
}
```

该方法实现与前例类似，但这次展示了块如何轻松访问类中的实例变量`_myIntVariable`。当然，在类内部调用块时实现这一点并不令人惊讶，但请考虑以下场景：

```
// 文件 UseBlock.h
@interface UseBlock : NSObject
{
        int _myIntValue;
}
- (void) receiveBlock:(int (^)())aBlock;
@end

// 文件 UseBlock.m
@implementation UseBlock
- (void) receiveBlock:(int (^)())aBlock
{
        NSLog(@"结果现在是: %d", aBlock());
}
@end
```

该类设计用于接收块并调用它，而不需要了解块来源的任何信息。现在来看看如何使用这个类。

```
- (void) showVariableReadAcccess
{
        _myIntValue = 3;
        int myVariable = 2;
        UseBlock *useBlock = [[UseBlock alloc] init];
        [useBlock receiveBlock:^
        {
                return 5 * myVariable + 2 * _myIntValue;
        }];
}
```

运行此方法时，会执行一个同时引用局部变量和实例变量的块。即使`UseBlock`类完全不了解这些变量的位置和内容，它仍然能够执行所要求的指令。

块之所以能够访问局部数据（无论调用上下文如何），是因为 Objective-C 通过运行时系统处理块的方式。在运行时，块与其他对象一样是一个对象，编译器会隐式设置数据访问，使其指向仅块对象知晓的对象实例变量。块语法提供了一种简单方式来声明能够访问其他对象中存储数据的复杂对象。

### 局部变量的读写访问

如之前示例所示，块对局部变量提供的是标准的只读访问。块访问的变量仅是创建块时当前方法或函数中存储值的副本。不过，Objective-C 也通过`__block`关键字提供了修改此类值的机制。

使用`__block`关键字会强制编译器不仅创建数据副本，还会创建一个引用，该引用可用于修改与局部变量中存储的相同对象或值。

修改之前的示例可以轻松展示这一区别。

```
- (void) showWriteAcccess
{
        __block int myVariable = 2;
        int (^getMultiple)(int);
        getMultiple = ^ (int base)
        {
                myVariable = base;
                return 5 * myVariable ;
        };
        NSLog(@"结果如下: %d", getMultiple(3));
        // 这将打印值 15
}
```

这是一个类似的示例，其中块内引用了局部变量。但这次块还负责修改局部变量中存储的值。与前例不同，块中存储的代码现在需要对局部变量具有写入权限。这一意图通过 Objective-C 关键字`__block`明确表示，该关键字添加在`myVariable`声明之前。

编译器处理这段代码时，底层机制是：该变量的共享副本变得对块以及任何需要读写权限的语句都可用。无论块在何种上下文中被调用（包括在其他类的方法中），它都可以使用该局部变量的共享引用。

然而，当对实例变量进行修改时，无需直接在变量定义中添加`__block`。当块需要更新实例变量时，编译器会自动存储对该对象的引用。

注意

上述共享局部变量和实例变量的机制，在其他语言中被称为闭包。例如，在 Python、Ruby 或 Lisp 中，可以创建闭包，即引用其声明点活跃变量的代码段。Objective-C 块在实现相同目标的同时，还保留了 C 语言固有的类型检查。


## 块与内存管理

块为程序员提供了极大的灵活性，他们需要额外的工具来组织代码实现。例如，使用块可以更轻松地在类之间共享行为，而无需创建单独的对象。然而，如果使用不当，这种灵活性可能会导致难以调试的细微问题。

一个可能让新手程序员感到困惑的问题是块的作用域。虽然块可以在程序的其他位置使用，但这并不意味着块中的所有内存都会被自动管理。主要问题在于，块的创建和使用也涉及对其关联内存的管理。虽然 Objective-C 可以自动为块内部访问的数据设置必要的引用，但它并不直接管理内存。

块内存管理的问题有时可能令人困惑。主要问题出现在有人试图在创建块的执行作用域之外访问它时。例如，考虑在`if`语句内部创建的块（在`for`、`while`、`do`/`while`中声明的块也同样适用）。

```
- (double) invalidBlock:(BOOL)initBlock :(BOOL)callBlock :(int)x1 :(int)x2
{
    double (^division)(int, double) = nil;

    if (initBlock)
    {
        division = ^ double (int a, double b)
        {
            return a / b;
        };
    }

    if (initBlock && callBlock)
    {
        return division(x1, x2); // 这行不通。
    }

    return 0;
}
```

这个方法的意图很明确：根据传入的参数初始化并创建一个块。如果两个参数都为真，则调用该块并返回结果。

然而，问题在于块变量（`division`）是在一组语句（由`if`语句定义）内部初始化的，而这组语句在调用该块时已经结束。由于块的默认存储位于程序栈上，其关联的内存在`if`语句结束时被释放。这意味着当调用块变量`division`时，它是无效的。

出于性能原因，块的内存是在栈上分配的。但是，也可以创建块的动态分配副本。这可以通过与复制系统中任何其他对象相同的方式来实现，即调用`copy`方法。

```
- (double) callValidBlock:(BOOL)initBlock :(BOOL)callBlock :(int)x1 :(int)x2
{
    double (^division)(int, double) = nil;

    if (initBlock)
    {
        division = [^ double (int a, double b)
        {
            return a / b;
        }
        copy];
    }

    if (initBlock && callBlock)
    {
        return division(x1, x2);
    }

    [division release];
    return 0;
}
```

在这个版本中，现在从当前调用位置执行该块是正确的。这是可行的，因为`copy`方法会存储一个正确分配的块版本，该版本在`if`语句完成后仍然存在。但是，由于你创建了一个副本，所以需要在方法末尾调用`release`，以确保其内存被正确释放（我将在后续章节讨论内存管理的这一规则和其他规则）。

当方法返回一个块时，也需要采用类似的方法。由于这样的块需要在创建它的栈环境之外被访问，所以你需要返回一个有效的副本。

```
- (double (^)(int, double)) returnBlock
{
    return [[^ double (int a, double b)
    {
        return a / b;
    }
    copy] autorelease];
}
```

这个示例类似，但由于你要将块返回给调用方，因此还需要调用`autorelease`方法，它会在对象不再使用时自动处理其关联内存的释放。

## 在 Foundation 框架中使用块

在自己的应用中使用块，可以为你带来很多简化代码、减少实现特定目标所需类数量的机会。然而，在处理 Cocoa 库时，块也同样有用。在本节中，我将展示几个使用块与 Foundation 框架交互的示例。

块的一个良好应用是在一组元素上执行相似的代码。这在 Foundation 框架的集合类中得到了体现。例如，看看`NSArray`类。如果你想确定数组中满足特定条件的元素数量，可以使用`for`循环检查数组的每个元素。或者，`NSArray`类允许你通过`indexesOfObjectsPassingTest`方法传递一个块来测试条件。

```
- (void) countPositivesInArray
{
    NSArray *array = @[ @-1, @-2, @1, @2, @3];

    NSIndexSet *indices =
        [array indexesOfObjectsPassingTest:
            ^ BOOL (id obj, NSUInteger idx, BOOL *stop)
            {
                NSNumber *num = (NSNumber*)obj;
                return [num integerValue] > 0;
            }
        ];

    NSLog(@"There are %d positive elements", (int)[indices count]);
    // 这将显示结果为 3
}
```

注意，使用`indexesOfObjectsPassingTest`，你无需手动设置`for`循环并记录满足正数测试的元素数量，因为这些都由`NSArray`类自动完成。通常，将重复性任务委托给专门的方法是个好主意，而这正是使用块的最大优势：你可以让其他方法处理细节，同时只定义计算任务中的重要方面。

`sortedArrayUsingComparator`方法也演示了类似的方法。在这里，目标是在排序过程中对数组中的两个元素进行比较。基于比较结果，`sortedArrayUsingComparator`可以决定是否应该交换这两个元素的位置。在常规的排序过程中，这通常通过传递函数指针或对现有基类进行子类化来完成。然而，使用块，你可以更轻松地决定如何比较元素，而无需创建额外的样板代码。让我们看一个示例。

```
- (void) sortArray
{
    NSArray *array = @[ @-1, @2, @100, @4, @-3];

    NSArray *sorted = [array sortedArrayUsingComparator:^ (id a, id b)
    {
        if ([a integerValue] > [b integerValue])
        {
            return (NSComparisonResult)NSOrderedDescending;
        }
        else if ([a integerValue] < [b integerValue])
        {
            return (NSComparisonResult)NSOrderedAscending;
        }
        else
        {
            return (NSComparisonResult)NSOrderedSame;
        }
    }];

    for (id item in sorted)
    {
        NSLog(@"value is %@", item);
    }
    // 这将按排序顺序打印元素
}
```

在这个方法中，你首先对第一行可执行代码中定义的`NSArray`进行排序。然后，对原始数组调用`sortedArrayUsingComparator`方法，并向其传递一个比较块。该比较块接收两个对象作为参数，并返回一个`NSComparisonResult`，这是一个仅包含三个值的枚举。根据第一个对象与第二个对象的比较结果，你返回`NSOrderedDescending`、`NSOrderedAscending`或`NSOrderedSame`。`NSArray`的排序方法利用此信息决定两个对象是否按顺序排列。最后，`sortArray`方法打印排序后`array`的所有元素，现在这些元素应该已经按排序顺序排列了。

这两个取自`NSArray`的示例展示了块在 Cocoa 框架中的使用方式。这些框架中的许多其他类也在块能提供最大益处的地方使用它们：简化可以直接提供的小段代码的执行。当你探索 Cocoa 和 Cocoa Touch 库提供的功能时，你还会看到其他使用块的示例。


## 总结

在本章中，你已通过示例了解了如何在 Objective-C 中使用块的概览。借助块，你可以创建可发送给其他方法或保存下来以便在不同于其创建上下文中稍后使用的代码片段。虽然块看起来与函数相似，并且能够模拟其他函数或实例方法，但它们是实现小段代码的一种更为简洁的方式。

更重要的是，块提供了一种简单高效的机制，可以隐式地与程序的其他部分共享数据（包括局部变量和实例变量）。这使得应用程序能够减少协调系统中变量访问所需的辅助代码量。如果使用块来简化数据访问看起来是解决编程问题的自然方案，你可以不用在单独的类中创建显式的方法来获取和设置数据。

正如你所见，块不仅对于组织自己的代码非常重要，还与 Cocoa 中的其他类进行交互。例如，Foundation 框架提供了几种方法，要求程序员使用块来完成特定任务。你已经看到了 `NSArray` 类使用这种方法的几个示例。

Cocoa 和 Cocoa Touch 框架中的新框架提供了更多使用块的机会。这种技术的一些应用领域包括并行编程（其中块可以以这样的方式设置，即它们可以在不同的线程中执行）或图形编程（例如，块可以用于绘制 3D 动画序列中的场景）。

在下一章中，你将深入了解 Objective-C 提供的动态绑定机制。具体来说，我将讨论如何与运行时系统交互以提高应用程序的灵活性。

