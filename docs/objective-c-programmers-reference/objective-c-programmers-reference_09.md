# 3. 字符串与容器类

## 摘要

在本章中，我将讨论 Foundation 框架中一些最常用的类。具体来说，我会介绍用于操作 Objective-C 中所有字符序列的 `NSString` 类。`NSString` 提供了便捷的接口来创建字符串、获取字符串的一部分、比较字符串值以及将其转换为其他格式。

Foundation 框架中的另一个重要类是 `NSNumber`。数字是计算机编程中最基本的概念之一，通常由 C 语言作为原生数据类型来处理。然而，有时需要将数字传递给其他对象或由其他对象存储。在这种情况下，像 `NSNumber` 这样的包装类是简化 C 语言与 Objective-C 世界之间转换的最佳方式。

我还将讨论 Objective-C 提供的两种主要集合类型。`NSArray` 是一个用于对象序列的简单类接口。借助 `NSArray` 对象，您可以高效地按简单序列存储和检索元素。

`NSDictionary` 是一个提供存储键/值对通用服务的类。每个键在字典中都有一个唯一的值，以便能够高效地检索。`NSDictionary` 为程序员提供了一套丰富的消息，可用于查询、检索和处理存储在这种字典容器中的对象。

## 字符串

字符串是一个字符序列。`NSString` 类提供了创建和修改字符串所需的全部功能，并且被广泛应用于 Cocoa 及相关框架中。字符串不仅用于消息传递，当您需要以二进制形式内部表示的组件（例如整数、浮点数或对象）的文本表示时，也会用到它。

### 创建字符串

要创建字符串，您需要使用作为 Foundation 框架一部分的 `NSString` 类。要创建一个空字符串，只需向 `NSString` 类发送一条 `alloc` 消息，然后发送一条 `init` 消息来正确初始化该类的成员。

```
- (void)createString
{
        NSString *myStr = [[NSString alloc] initWithCString:"my original string"
                                            encoding:NSASCIIStringEncoding];
        NSLog(@"the string is %@", myStr);
}
```

在此函数中，您首先要做的是向 `NSString` 发送 `alloc` 消息，就像您对任何 Objective-C 类所做的那样。然后，向分配好的对象发送 `initWithCString:encoding:` 消息。该方法的第一个参数是一个用单引号括起来的 C 字符串。第二个参数是字符的编码类型。最常见的选项是示例中显示的 ASCII 编码，但还有其他几个选项可用。

当然，仅仅为了创建一个字符串而完成所有这些步骤会显得很繁琐。这就是为什么还有一个更简单的选项——从第一章开始您就一直在使用它：在双引号字符串的开头加上字符 `@`，就可以告诉编译器创建一个采用标准编码的新 `NSString` 对象。编译器负责为生成的对象调用 `alloc` 和相应版本的 `init` 方法。这极大地减少了工作量，同时也体现了编译器在处理 Objective-C 代码时为您完成了大量工作。

### NSString 方法

`NSString` 提供了丰富的文本操作接口。有些方法可用于访问单个字符或一系列字符。有些方法可用于比较不同的字符串，而另一些方法则可返回有关前缀和后缀的信息。

`length` 方法用于返回字符串中的字符数。例如，您可以用以下方式重写上面的函数，以便显示传递给构造函数的字符串的大小：

```
- (void)createString
{
NSString *myStr = [[NSString alloc] initWithCString:"my original string"
encoding:NSASCIIStringEncoding];
NSLog(@"the length of the string is %d", [myStr length]);
// 输出：the length of the string is 18
}
```

方法 `characterAtIndex:` 返回位于给定索引位置的单个字符。请记住，与普通的 C 数组一样，`NSString` 对象使用从零开始的索引。也就是说，索引 1 用于第二个元素，依此类推。以下函数将始终返回 `YES`：

```
- (BOOL) compareString
{
NSString *myStr = [[NSString alloc] initWithCString:"my original string"
encoding:NSASCIIStringEncoding];
return [myStr characterAtIndex:4] == 'r';
}
```

如果两个字符串具有相同的字符，以下函数将返回 `YES`：

```
- (BOOL) stringsEqual:(NSString *) :(NSString *)b
{
NSUInteger n1 = [a length];
NSUInteger n2 = [b length];
if (n1 != n2)
{
return NO;
}
for (int i=0; i<n1; ++i)
{
if ([a characterAtIndex:i] != [b characterAtIndex:i])
{
return NO;
}
}
return YES;
}
```

> **注意：** `NSUInteger` 是 Objective-C 库定义的一种类型，用于表示无符号整数。它通常等同于 `unsigned int`，但被保留为一种单独的类型，以方便将 Objective-C 代码移植到其他平台。由于不能保证 `unsigned int` 在所有平台上都具有相同的大小，因此使用 `NSUInteger` 来实现一致性是一个好主意，尤其是在编写与内存大小（如字符串长度）相关的代码时。

虽然可以在比较两个字符串时使用 `characterAtIndex:`，就像你在前面的示例中所做的那样，但更便捷的方式是使用实例方法 `compare:` 来完成这项任务。使用 `compare:` 时，返回值类型为 `NSComparisonResult`，其可能值有三种。`NSOrderedAscending` 表示原始字符串在字典顺序上位于参数字符串之前。结果为 `NSOrderedSame` 表示两个字符串相同。另一种结果为 `NSOrderedDescending`。以下是一个示例：

```
- (void) compareString:(NSString *)a with:(NSString *)b
{
NSComparisonResult res = [a compare:b];
if (res == NSOrderedAscending)
{
NSLog(@"字符串 %@ 在 %@ 之前", a, b);
}
else if (res == NSOrderedDescending)
{
NSLog(@"字符串 %@ 在 %@ 之前", b, a);
}
else
{
NSLog(@"这两个字符串相同");
}
}
```

最后，如果您只关心简单的相等性测试（也就是说，您不关心两个字符串在字典顺序上是升序还是降序），那么还有一种更快捷的方式。方法 `isEqualToString:` 将另一个字符串作为参数，并根据两个字符串中所有对应字符是否相等来返回 `YES` 或 `NO`。

上述同类测试也可以使用不区分大小写的比较来执行。例如，`NSString` 有一个名为 `caseInsensitiveCompare:` 的方法，其行为与 `compare:` 类似，区别在于它认为诸如 `"a"` 和 `"A"` 这样的字符是相同的。



### 子字符串

对`NSString`对象执行的另一种操作是检索字符序列的子集。这些方法根据字符在字符串中出现的位置提取当前字符串的一部分，并返回一个仅包含所需字符的新`NSString`对象。例如，要查找从字符串开头到指定索引的所有字符组成的子字符串，可以使用`substringToIndex:`方法。以下是示例代码：

```
- (void) initialSubstring
{
    NSString *original = @"Original string";
    NSString *partial = [original substringToIndex:4];
    NSLog(@"The partial string is %@", partial);
}
```

这段代码会将"Orig"（原始字符串的前四个字母）打印到日志屏幕。另一个用途类似的方法是`substringFromIndex:`。它可以应用于字符串，其返回值由从特定位置开始的字符串最后几个字符组成。例如，假设有一个函数用于检测单词是否为复数形式。一种简单的方法是检查给定字符串的后缀是否是"s"。

```
- (BOOL) isPluralWord:(NSString *)word
{
    int length = (int)[word length];
    int initialPosition = length - 1;
    if (initialPosition < 0)
    {
        return NO;
    }
    NSString *suffix = [word substringFromIndex:initialPosition];
    return [suffix isEqualToString:@"s"];
}
```

这段代码首先计算接收到的字符串的长度，然后从开头开始计算最后一个字符的位置。该位置为`length - 1`，因为索引总是从零开始。如果得出的位置小于零，则说明字符串为空，返回`NO`。否则，确定单词的后缀并将其与"s"比较，以此判断该单词是否为复数形式。

使用这两种方法，你可以通过`substringToIndex:`和`substringFromIndex:`的组合选择给定字符串中的任意内部子字符串。不过，还有一个名为`substringWithRange:`的方法，它接受一个由两个位置组成的范围，并返回包含该范围内所有字符的子字符串。例如，假设你接收到一个带引号的字符串（即首尾字符都是引号的字符串）。你可以设计一个函数，使用`substringWithRange:`移除该字符串中的引号，返回`NSString`对象的内部部分。

```
-(NSString *) removeQuotes:(NSString *)str
{
    int len = (int)[str length];
    if (len < 2)
    {
        return @"";
    }
    NSRange range = NSMakeRange(1, len-2);
    return [str substringWithRange:range];
}
```

在此示例中，首先计算作为参数传入的字符串的长度，然后检查字符串是否至少有两个字符（否则直接返回空字符串）。接着，创建一个从位置 1 开始、长度等于原长度减 2 的范围。最后，返回由`substringWithRange:`计算出的子字符串。

## NSNumber 类

在使用对象时，经常需要通过面向对象的接口来存储或获取数值。毕竟，Objective-C 的主要优势在于能够编写灵活的代码，在对象之间传递消息，而不是使用由函数和 C 运算符定义的固定接口。

此外，虽然像整型和双精度浮点型这样的数值使用 C 函数很容易操作，但对象需要支持源自基本`NSObject`类的额外一系列操作。因此，Objective-C 提供了一个专用类`NSNumber`，它可以用来存储任何标准 C 数值类型对应的数值。

`NSNumber`是一个通用接口，可用于包装 C 语言中提供的任意原生数值类型。`NSNumber`能表示的数值类型包括整型（`int`）、长整型（`long int`）、短整型（`short`）以及无符号整型（`unsigned int`）。非常小的数值在 C 中可以用字符表示，因此`NSNumber`也支持`char`类型。浮点型（`float`）和双精度浮点型（`double`）同样可以表示。最后，`NSNumber`还能包装布尔类型（`BOOL`）的值。

要创建`NSNumber`对象，只需调用`alloc/init`组合，其中`init`方法取决于要包装的数值类型。以下是一些示例：

```
- (void) createNumbers
{
    int aInt = 1;
    long aLong = 23456;
    double aDouble = 3.14;
    BOOL aBool = YES;
    char aChar = 'A';
    // 以下是相应的 NSNumber 对象
    NSNumber *objInt = [[NSNumber alloc] initWithInt:aInt];
    NSNumber *objLong = [[NSNumber alloc] initWithLong:aLong];
    NSNumber *objDouble = [[NSNumber alloc] initWithDouble:aDouble];
    NSNumber *objBool = [[NSNumber alloc] initWithBool:aBool];
    NSNumber *objChar = [[NSNumber alloc] initWithChar:aChar];
    NSLog(@"Here are the values: %@, %@, %@, %@, %@ ",
              objInt, objLong, objDouble, objBool, objChar);
}
```

虽然这是创建此类数值对象的标准方式，但还有其他构造对象的方法。例如，可以使用类方法在一步内完成分配和初始化，从而简化操作。如下所示：

```
- (void) createNumbers2
{
    int aInt = 1;
    long aLong = 23456;
    double aDouble = 3.14;
    BOOL aBool = YES;
    char aChar = 'A';
    // 以下是相应的 NSNumber 对象
    NSNumber *objInt = [NSNumber numberWithInt:aInt];
    NSNumber *objLong = [NSNumber numberWithLong:aLong];
    NSNumber *objDouble = [NSNumber numberWithDouble:aDouble];
    NSNumber *objBool = [NSNumber numberWithBool:aBool];
    NSNumber *objChar = [NSNumber numberWithChar:aChar];
    NSLog(@"Here are the values: %@, %@, %@, %@, %@ ",
              objInt, objLong, objDouble, objBool, objChar);
}
```

如果你使用的是某个较新版本的 Xcode，那么你应该能够使用最新的数值字面量语法。新语法通过使用`@`字符作为指示符，表示紧随其后的字面量值需要转换为`NSNumber`对象。这个技巧适用于字面数值（或如上述示例中的变量），因此你可以将其应用于前面的示例进一步简化，如下所示：

```
- (void) createNumbers3
{
    NSNumber *objInt = @1;
    NSNumber *objLong = @23456;
    NSNumber *objDouble = @3.14;
    NSNumber *objBool = @YES;
    NSNumber *objChar = @'A';
    // 将值作为对象打印
    NSLog(@"Here are the values: %@, %@, %@, %@, %@ ",
              objInt, objLong, objDouble, objBool, objChar);
}
```



### 访问 `NSNumber`

`NSNumber` 的另一面是能够访问其存储的值。这可以通过一组方法来实现，这些方法提供了对 `NSNumber` 中可能存储的每种值的访问。例如，对于整数值，有 `integerValue` 方法；对于字符，有 `charValue` 方法。类似地，对于 `NSNumber` 直接支持的每种类型，都存在相应的访问方法。以下示例展示了如何访问 `NSNumber` 中存储的数据：

```
- (void) accessNumberValue

{
        NSNumber *objInt = @1;
        NSNumber *objLong = @23456;
        NSNumber *objDouble = @3.14;
        NSNumber *objBool = @YES;
        NSNumber *objChar = @'A';

        // 以原生数据类型打印值
        NSLog(@"这里是具体数值：%d, %ld, %lf, %d, %c ",
                  (int)[objInt integerValue],
                  [objLong longValue],
                  [objDouble doubleValue],
                  [objBool boolValue],
                  [objChar charValue]);
}
```

> **注意：** 从 `NSNumber` 中检索数据并不能保证使用正确的格式。由于 `NSNumber` 内部存储的数据只是二进制格式的数字，访问器会尝试转换内部数据以适配请求的格式。这可能导致数据丢失。例如，存储一个 `long` 值，然后尝试以 `char` 格式访问它，会导致数据截断。不过，在访问 `NSNumber` 中存储的数据之前，你可以使用 `objCType` 方法来确定其中包含的确切类型。

### 何时使用 `NSNumber`？

接下来要解决的问题是何时使用 `NSNumber`。尽管能够向对象发送消息而不是调用函数很方便，但这并非总是必需的。此外，每次使用对象替代原生类型时，都会带来性能开销。对象需要分配内存和消息传递，与编译器处理整数和布尔值等原生类型的高效方式相比，这些操作代价高昂。

因此，如果情况需要高性能，几乎没有理由使用对象替代原生类型。例如，进行频繁计算的数值算法更适合使用原生类型，这样可以充分利用 C 语言启用的所有优化功能。

另一方面，在某些情况下，你可能希望将数字视为对象。当你希望它们成为能够响应常见消息的某种集合的一部分时，就会发生这种情况。例如，假设你想创建一个对象数组。当数组被销毁时，这些对象都会收到一组特定的消息，例如 `deallocate`。为了使数字能够被添加到这样的数组中，必须能够将数字表示为对象。因此，不需要直接将数字添加到对象数组中，而是为数字创建对象包装器。

对象的集合是使用 `NSNumber` 类的一个主要原因。在接下来的几节中，你将看到容器类如何实现数组和字典的维护。

> **注意：** 在使用 `NSNumber` 对象之前，请考虑性能因素。如果你需要执行快速计算，请优先使用原生类型。然而，当你需要将数字视为对象时（例如，与仅接收对象的方法进行交互，或将数字添加到容器中时），`NSNumber` 对象是必需的。

## 容器

当数据以对象形式存储时，能够将元素集合作为另一个对象来操作是非常有用的。Foundation 框架提供了一套容器类，使实现这一目标更加容易。Foundation 框架提供的容器包括数组和字典，两者都有可变和不可变两种形式。在接下来的几节中，我将解释如何创建、更新、添加以及从这些容器中移除元素。



## 数组

数组是存储在连续内存中的一组对象。正如你在第一章中所见，C 语言为原生数组提供了灵活高效的实现。然而，使用数组可能较为困难，特别是对于那些需要显式内存管理的动态数组。`NSArray`类应运而生，旨在为数组对象提供简单而强大的接口。借助`NSArray`，你可以通过消息传递机制对数组执行多种操作。

首先，考虑如何创建数组并向其中添加元素。创建数组最简单的方法是调用一对`alloc`/`init`方法。一旦数组对象被正确初始化，就可以通过向原始`NSArray`添加新元素来创建新数组。

```
- (void) createSimpleArray
{
NSArray *a0 = [[NSArray alloc] init];
NSArray *a1 = [a0 arrayByAddingObject:@"我的第一个对象"];
NSArray *a2 = [a1 arrayByAddingObject:@"我的第二个对象"];
NSLog(@"数组大小为 %d", (int)[a2 count]);
}
```

函数`createSimpleArray`创建了一个包含两个字符串元素的数组，并将结果数组（命名为`a2`）的大小输出到系统日志。为此，你需要向`NSArray`实例发送`arrayByAddingObject`消息。此外，要获取数组中的元素数量，你需要向结果数组发送`count`消息。

虽然上述技术可用于向任何数组添加元素，但更简便的做法是在一步内同时添加两个元素。这可以通过使用`NSArray`类的一个特殊版本的`init`方法来实现，如下所示：

```
- (void) createSimpleArray2
{
NSArray *array = [[NSArray alloc] initWithObjects:
@"我的第一个对象",
@"我的第二个对象", nil];
NSLog(@"数组大小为 %d", (int)[array count]);
}
```

虽然两个例子的结果相同，但函数`createSimpleArray2`使用了一个特殊版本的`init`方法。`initWithObjects:`方法能够接收一组对象作为参数。你只需注意一个细节：最后一个元素必须是`nil`，因为这是表示序列结束的哨兵值。然而，`nil`值不会被存储为数组的一部分。

对`initWithObjects:`实例方法的描述揭示了`NSArray`类的一个非常重要的特性：该类只接受非`nil`值作为数组成员。这意味着，在将`nil`元素存入`NSArray`（或任何其他容器）之前，必须将其转换为其他表示形式。为此，有一个名为`NSNull`的类，它将`nil`（或`NULL`）表示为单个对象。

通过`initWithObjects:`消息向数组添加元素是完成这项基本任务的简单方法。然而，在同一行中使用两条消息仍然有些繁琐。为简化这一常见工作流程，`NSArray`还提供了一个功能类似的类方法，即`arrayWithObjects:`方法。该方法的优点在于，你可以直接将元素序列传递给`NSArray`类，而无需先调用`alloc`。这省去了额外的方法调用，从而简化了代码。上述例子可以更简洁地表示为：

```
- (void) createSimpleArray3
{
NSArray *array = [NSArray arrayWithObjects:
@"我的第一个对象",
@"我的第二个对象", nil];
NSLog(@"数组大小为 %d", (int)[array count]);
}
```

`arrayWithObjects:`或`initWithObjects:`方法可用于创建包含任意数量元素的数组。只要以`nil`结束元素列表，条目数量就没有限制。然而，有些情况下你可能只想创建一个只包含一个元素的数组。这可以通过`arrayWithObject:`或`initWithObject:`方法实现。它们与你之前看到的方法类似，区别在于它们只接受单个元素。

```
- (void) createSimpleArray4
{
NSArray *array = [NSArray arrayWithObject:
@"我的单一对象"];
NSLog(@"数组大小为 %d", (int)[array count]);
}
```

在以数组创建为主题时，请注意还有其他版本的`init`和`arrayWith`方法可与`NSArray`一起使用。例如，你可以用另一个先前创建的数组来初始化一个`NSArray`。假设你想要复制上面构建的数组，可以按如下方式操作：

```
- (void) createArrayAndCopy
{
NSArray *array = [NSArray arrayWithObjects:
@"我的第一个对象",
@"我的第二个对象", nil];
NSLog(@"数组大小为 %d", (int)[array count]);
NSArray *copy = [NSArray arrayWithArray:array];
NSLog(@"复制数组的大小也是 %d", (int)[copy count]);
}
```

最后，你可能有兴趣从现有的 C 数组创建数组。`NSArray`也支持这一点，但你需要同时提供数组中的元素数量。这是必需的，因为 C 数组本质上等同于指针，因此它们不存储元素数量。

```
- (void) createArrayWithCArray
{
NSString *orig_array[] = { @"Obj1", @"Obj2" };
NSArray *array = [NSArray arrayWithObjects:orig_array count:2];
NSLog(@"数组大小为 %d", (int)[array count]);
}
```

在此例的第一行，你创建了一个包含两个对象的 C 数组。第一行的声明意味着你正在创建一个由`NSString *`（字符串对象使用的类型）组成的数组（用`[]`表示）。花括号内的记法是 C 函数用于初始化相同元素数组的方式。接下来，你将数组名称（如第一章所述，它等同于指针）传递给`arrayWithObjects:count:`方法。

了解所有这些创建`NSArray`对象的方法很有用，因为数据可能以多种格式存在。如果仅仅为了使用`NSArray`而将所有这些不同的数据格式转换为单一格式，将会非常困难。而且，我们尚未穷尽所有转换可能性。例如，创建`NSArray`的其他方式包括直接从文件读取数据（参见`arrayWithContentsOfFile:`）或从 URL 读取数据（参见`arrayWithContentsOfURL:`）。



### 向 `NSArray` 添加其他对象类型

在目前看到的示例中，添加到 `NSArray` 的所有对象均为 `NSString` 类型。我这样做只是为了简单起见。实际上，`NSArray` 是一个动态容器，这意味着你可以向数组添加任何类型的对象。以下是一个实现示例：

```
- (void) createArrayWithDiffferentObjects

{

        NSNumber *num1 = [NSNumber numberWithInt:42];

        NSArray *array = [NSArray arrayWithObjects:num1, @"str2", nil];

        NSLog(@"数组大小为 %d", (int)[array count]);

}
```

第一个对象是 `NSNumber`，第二个是 `NSString`。它们现在同属一个数组。这种数组带来的唯一问题是如何根据元素类型来区分它们。这个问题的答案取决于你想对该数组成员执行什么操作。如果你只打算使用通用消息（如 `description`），那么混合不同类型的对象是没有问题的。

然而，使用 `NSArray` 集合最安全的方式是根据类型创建集合。例如，如果你正在开发一个处理几何图形的应用程序，有些数组应该只包含图形。这正是继承在处理动态对象时的优势所在：一组几何图形可以拥有一个公共类，用于对不同的形状进行分类。

例如，`Oval` 或 `Rectangle` 这样的形状应该是通用 `Shape` 类的子类。使用这个超类，就可以将 `NSArray` 中的元素限制为派生自 `Shape` 的对象。这样，就可以发送 `Shape` 专用的、且能被所有子类理解的消息。

如果你混合了没有超类关联的不同类型对象，则需要用其他方式来确定某个对象属于哪个特定类。虽然不推荐这样做，但确实有方法可以判断对象是否属于特定类。首先，每个对象都有一个名为 `class` 的实例方法，该方法返回类的表示。原则上，你可以将对象返回的类与特定类表示进行比较，如下所示：

```
- (void) checkPersonClass:(Person *)person

{

        if ([person class] == [Employee class])

        {

                NSLog(@"这是一个 Employee");

        }

        else

        {

                NSLog(@"这不是一个 Employee");

        }

}
```

虽然这种方法在某些情况下可能有效，但它也是一种危险的解决问题的方式。主要问题在于，检查特定类也会排除属于子类的对象。例如，如果你要查找类型为 `Shape` 的对象，就会遗漏类型为 `Rectangle` 的对象。然而，`Rectangle` 也是一种 `Shape`，这类代码在这种情况下将无法正常工作。

为了避免这个问题，你可以使用 `NSObject` 的一个方法 `isSubclassOfClass:`。该方法能够测试给定类是否与指定类相同，或者为其子类。以下示例展示了如何测试某个对象是 `NSNumber` 还是 `NSNumber` 子类的实例：

```
- (void) checkClass2

{

        NSNumber *num1 = [NSNumber numberWithInt:42];

        // 检查该对象是否为 NSNumber 或其子类的实例

        if ([[num1 class] isSubclassOfClass: [NSNumber class]])

        {

                NSLog(@"这是一个 NSNumber");

        }

        else

        {

                NSLog(@"这不是一个 NSNumber");

        }

}
```

调用 `class` 是为了获取对象的类。另一方面，`class` 也发送给 `NSNumber` 以获取该类的类对象。获取相同信息的另一种方法是使用 `isKindOfClass` 方法，该方法衍生自 `NSObject`。

### 使用字面量表示法

如果你使用的是 Xcode 4.5 或更高版本，则应该能够使用 Apple 编译器引入的新字面量表示法。传统的创建数组方式涉及使用 `alloc`/`init`，或前面讨论过的辅助类方法。虽然这些初始化技术运行良好，但与原生类型（如整数或原生数组）的初始化相比，它们仍然显得繁琐。

新的字面量表示法使初始化 `NSArray` 变得像初始化原生类型一样简单。新表示法不是向 `NSArray` 类发送消息，而是使用简单的语法来处理数组的创建过程。示例如下：

```
- (void) createArrayWithNewSyntax

{

        NSArray *array = @[@"obj1", @"obj2" ];

        NSLog(@"数组大小为 %d", (int)[array count]);

}
```

请注意，这个函数产生的结果与你在之前示例中看到的结果类似。然而，通过 `@[` 标记，编译器会自动创建一个 `NSArray`。无需发送消息来分配或初始化结果对象，因为这些操作将由生成的代码自动执行。

数组初始化新表示法的另一个优点是可以减少潜在错误。通过使用更简单的语法，更容易发现简单错误，例如重复对象或 `nil` 元素，而这些错误在使用原始表示法时可能会被引入。

另一方面，新语法并不能完全消除对 `NSArray` 初始化器的需求。一个例子是将现有的 C 数组用作 `NSArray` 的初始内容。在这种情况下，仍然需要使用 `arrayWithArray:` 形式，该形式允许传入一个基于 C 的数组及其大小。



### 访问数组

在大多数程序中，创建数组只是过程的开始。另一方面是访问存储在结果容器中的数据。`NSArray` 通过 `objectAtIndex:` 方法提供了一种简单的数据访问方式。使用该方法，你可以传入要访问对象基于零的索引。考虑以下示例：

```
- (void) accessArrayData
{
        NSArray *array = @[@"obj1", @"obj2" ];
        NSLog(@"数组的第二个元素是 %@", [array objectAtIndex:1]);
}
```

调用 `objectAtIndex:` 的结果是存储在指定位置的对象。由于索引从零开始，请求索引 1 将返回第二个元素，这与 C 语言中本地数组的约定相同。

虽然 `objectAtIndex:` 足以提供对任何元素的访问，但还有其他方法可以实现这一目标。例如，如果你将数组用作简单队列（后进先出），你可能只对最后一个元素感兴趣。

```
- (void) accessLastElement
{
        NSArray *array = @[@"obj1", @"obj2" ];
        NSLog(@"数组的第二个元素是 %@", [array lastObject]);
}
```

因此上面的示例与前一个等效。访问数组元素的另一种方法是先将它们与其他现有对象进行比较。在这种情况下，你可能希望找到先前存储在数组中的对象的索引。使用 `indexOfObject:` 方法可以轻松实现这一点，如下所示：

```
- (void) getIndexOfElement
{
        NSString *secondObj = @"obj2";
        NSArray *array = @[@"obj1", secondObj, @"obj3" ];
        int position = (int)[array indexOfObject:secondObj];
        NSLog(@"第二个元素的索引是 %d", position);
}
```

在函数的第一行，你创建了一个 `NSString` 类型的新对象。第二行创建了一个包含该对象的数组。第三行向数组发送消息，询问已知对象的位置，然后将其打印到日志中。

最后，为 `NSArray` 和其他容器引入的新语法也提供了一种简化的元素访问方式。如果你有一个 `NSArray`，现在可以直接使用与本地数组相同的语法访问元素。

```
- (void) accessWithNewSyntax
{
        NSArray *array = @[@"obj1", @"obj2" ];
        NSLog(@"数组的第二个元素是 %@", array[1]);
}
```

在幕后，编译器生成的代码与你之前看到的类似，会调用 `objectAtIndex:`。然而，这种语法使得你访问元素的方式更加直观，这对于代码维护是一个巨大的优势。

## 可变数组

让初次接触 Objective-C 的程序员感到惊讶的一个问题是，标准的 `NSArray` 对象是不可修改的。一旦使用上一节中描述的某个 `init` 函数创建了对象，你唯一能做的就是访问数据，或者基于原始内容请求一个新数组。换句话说，`NSArray` 是一种不可变的数据结构。

`NSArray` 不可变的原因是此类对象针对快速访问进行了优化。对数组的更新操作，例如添加和删除元素，可能会产生新的内存分配和/或内存移动。鉴于 `NSArray` 的简单实现，这被认为是不理想的。然而，为了允许修改，`NSArray` 被子类化为一种可修改的形式，即 `NSMutableArray` 类。

我之前所说的关于 `NSArray` 的功能对于 `NSMutableArray` 仍然适用。然而，后者还能够添加和删除元素，以及它学到的其他重要技巧。创建 `NSMutableArray` 与你创建 `NSArray` 的方式非常相似。

```
- (void) createMutableArray
{
        NSMutableArray *array = [NSMutableArray arrayWithObjects:
                                                         @"我的第一个对象",
                                                         @"我的第二个对象", nil];
        NSLog(@"数组的第二个元素是 %@", [array objectAtIndex:1]);
}
```

然而，一个 `NSMutableArray` 能够随时添加元素，而不仅仅是在创建时。因此，可以使用以下代码创建相同的可变数组：

```
- (void) addElementsToArray
{
        NSMutableArray *array = [[NSMutableArray alloc] init];
        [array addObject: @"我的第一个对象"];
        [array addObject: @"我的第二个对象"];
        NSLog(@"数组的第二个元素是 %@", [array objectAtIndex:1]);
}
```

在这样一个可变数组中，你可以执行的另一个操作是移除元素。例如，你可以通过提供所需对象的索引来移除任何元素。

```
- (void) addAndRemoveArrayElements
{
        NSMutableArray *array = [[NSMutableArray alloc] init];
        [array addObject: @"我的第一个对象"];
        [array addObject: @"我的第二个对象"];
        NSLog(@"数组的第二个元素是 %@", [array objectAtIndex:1]);
        [array removeObjectAtIndex:1];
        NSLog(@"数组现在有 %d 个元素", (int)[array count]);
}
```

可变数组的另一个用途是作为简单的队列数据结构。队列是一种有用的数据存储机制，它使用先进先出策略。有大量算法受益于使用先进先出数据结构，包括语言解析器和模拟系统等。要使用 `NSMutableArray` 实现队列，你可以使用 `addObject:` 和 `removeLastObject` 方法。以下是在这种数据结构中入队和出队元素的方法：

```
- (void) enqueue:(NSMutableArray *)array forId:(id) object
{
        [array addObject: object];
}

- (id) dequeue:(NSMutableArray *)array
{
        id element = [array lastObject];
        [array removeLastObject];
        return element;
}
```



## 字典

字典是一种用于维护一组键与其对应值之间关系的数据结构。你可以将字典视为一个广义的数组，其中的键不仅可以为整数，还可以是任何类型的对象。因此，可以使用字典来维护一组相关对象，例如国家及其首都城市的列表：`("加拿大","渥太华")`、`("法国", "巴黎")`、`("美国", "华盛顿特区")` 等。

如果你了解 `NSArray` 的工作原理，那么对字典对象（称为 `NSDictionary`）的接口就不会感到陌生。`NSDictionary` 是一个不可变类；因此它只允许创建和数据检索操作。不可变性的原因与 `NSArray` 的情况非常相似：出于性能和可维护性方面的考虑，我们希望避免改变底层数据结构。

有几种初始化方法可以调用来创建新字典。例如，你可以使用 `initWithObjectsAndKeys:`，其中值和键以列表形式输入，并以 `nil` 结尾。请注意，提供错误数量的条目或忘记输入 `nil` 元素将导致错误。

```
- (void) createDictionary
{
        NSDictionary *dict = [[NSDictionary alloc] initWithObjectsAndKeys:
                                                  @"first", @"one", @"second",
                                                  @"two", @"third", @"three", nil];
        NSLog(@"字典中的元素数量为 %d", (int)[dict count]);
}
```

另一种实现相同目的的方法是使用值数组和键数组。在这种情况下，你需要确保两个数组的元素数量相同，否则程序运行时将引发运行时错误。

```
- (void) createDictionaryFromArrays
{
        NSArray *values = [NSArray arrayWithObjects:@"first", @"second", @"third", nil];
        NSArray *keys = [NSArray arrayWithObjects:@"one", @"two", @"three", nil];
        NSDictionary *dict = [[NSDictionary alloc] initWithObjects:values forKeys:keys];
        NSLog(@"字典中的元素数量为 %d", (int)[dict count]);
}
```

虽然这是实现相同结果的更简单方法，但根据数据的可用性，还有其他创建方法可能更方便。例如，可以使用 `dictionaryWithContentsOfFile:` 从文件中存储的数据创建新的 `NSDictionary`，甚至可以使用 `dictionaryWithContentsOfURL:` 方法从 URL 创建。

最后，对于使用最新版本 Apple 编译器的用户，可以使用简单直观的表示法来初始化 `NSDictionary`，这与数组可用的表示法非常相似。示例如下：

```
- (void) createDictionaryWithNewSyntax
{
        NSDictionary *dict = @{ @"one": @"first", @"two" : @"second", @"three" : @"third" };
        NSLog(@"字典中的元素数量为 %d", (int)[dict count]);
}
```

该语法使用 `@{` 序列来引入键/值对。结果类似于使用 `initWithObjectsAndKeys:` 方法，但键位于第一个位置。此外，键和值之间用冒号分隔。最后，序列末尾无需 `nil` 对象，而这在使用创建方法时是需要的。

### 访问字典元素

创建字典后，可以使用 `NSDictionary` 类中的一些专门方法来访问其元素。检索元素的最简单方法是使用 `objectForKey:` 方法。通过向字典发送此消息，将返回与给定键关联的值。如果所请求的键没有存储值，则返回值为 `nil`。

```
- (void) retriveDictionaryElement
{
        NSDictionary *dict = @{ @"one": @"first", @"two" : @"second", @"three" : @"third" };
        NSLog(@"字典中的元素数量为 %d", (int)[dict count]);
        NSLog(@"与 two 关联的元素是 %@", [dict objectForKey:@"two"]);
}
```

另一种检索元素的方法是使用 `objectsForKeys:` 实例方法。该方法的优势在于可以同时返回多个项，这可以加快依赖于字典查找的操作。

```
- (void) retrieveSetElementSet
{
        NSDictionary *dict = @{ @"one": @"first", @"two" : @"second", @"three" : @"third" };
        NSArray *objects = [dict objectsForKeys:@[@"two", @"three"]
                            notFoundMarker:[NSNull null]];
        NSLog(@"找到的对象数量为 %d", (int)[objects count]);
}
```

在调用 `objectsForKeys:` 时，第二个参数是 `notFoundMarker:`。其目的是提供一种方法来标识键数组中与字典中任何元素都不对应的元素。因此，传递的值应该是唯一的，并且在字典中不会被用到。一个可能的解决方案是使用 `NSNull` 类的空对象。

#### 检索键

访问 `NSDictionary` 中数据的另一种方法是检索容器中存储的所有键。当你想检查正在使用的键时，这可能会很有用。`NSDictionary` 通过 `allKeys` 方法使此操作成为可能，该方法的结果是包含所有当前正在使用的键的数组。

```
- (void) retrieveKeys
{
        NSDictionary *dict = @{ @"one": @"first", @"two" : @"second", @"three" : @"third" };
        NSArray *keys = [dict allKeys];
        NSLog(@"字典中的键数量为 %d", (int)[keys count]);
}
```

`allKeys` 方法是检索键的最简单方式，但并非唯一方式。`NSDictionary` 类提供了一系列丰富的方法来访问键列表。例如，一个密切相关的方法是 `allKeysForObject:`，它返回一个数组，其中包含与作为参数传递的对象关联的所有键。当处理同一个对象可能为多个键存储的字典时，此消息非常有用。

一个例子是这样的应用：食品配料（键）可能与食谱（值）相关联。由于一个食谱有多种配料，在这种应用中，一个对象与多个键关联是很常见的。因此，要找出准备蛋糕所用的所有配料，你可能希望使用 `allKeysForObject:` 方法。

```
- (void) findIngredientsForCake:(NSDictionary *)ingredientDictionary
{
        NSArray *ingredients = [ingredientDictionary allKeysForObject:@"cake"];
        NSUInteger size = [ingredients count];
        NSLog(@"蛋糕配料的数目为 %d", (int)size);
        for (int i=0; i<size; ++i)
        {
                NSLog(@"蛋糕配料: %@", [ingredients objectAtIndex:i]);
        }
}
```

类似地，你可能希望使用数组访问字典中的所有值。这可以通过 `allValues` 方法实现，其用法类似于 `allKeys`。显然，无需 `allValuesForObject`，因为每个键只有一个值。取而代之，你会找到我之前讨论过的 `objectsForKeys:notFoundMarker:` 方法。



#### 使用字典枚举器

另一种访问对象和键的方法是使用 `NSEnumerator`。通过一个 `NSEnumerator` 类型的对象，可以使用简单的 `for` 循环来检索序列，每次迭代请求一个元素。`NSEnumerator` 类提供了 `nextObject` 方法，该方法返回枚举器中的下一个对象。例如，要获取字典中所有对象的枚举器，可以向 `NSDictionary` 发送 `objectEnumerator` 消息，然后遍历生成的元素。以下是示例代码：

```
- (void) enumerateDictionaryObjects
{
    NSDictionary *dict = @{ @"one": @"first", @"two" : @"second", @"three" : @"third" };
    NSEnumerator *objects = [dict objectEnumerator];
    id obj;
    while ((obj = [objects nextObject]))
    {
        NSLog(@"The dictionary contains object %@", obj);
    }
}
```

这个例子展示了如何获取一个 `NSEnumerator`，用于访问 `NSDictionary` 中包含的每个对象。`while` 循环中的测试有两个作用。首先，它发送 `nextObject` 消息。其次，它检查返回值是否不为 nil，如果不为 nil，则对检索到的对象执行 `while` 循环体。

类似地，你可以使用枚举器来访问字典中的键。负责此功能的方法称为 `keyEnumerator`，其工作方式如下例所示：

```
- (void) enumerateDictionaryKeys
{
    NSDictionary *dict = @{ @"one": @"first", @"two" : @"second", @"three" : @"third" };
    NSEnumerator *keys = [dict keyEnumerator];
    id key;
    while ((key = [keys nextObject]))
    {
        NSLog(@"The dictionary contains key %@", key);
        NSLog(@"The corresponding object is %@", [dict objectForKey:key]);
    }
}
```

请注意，通过键来枚举对象更为有用，因为它允许以一种能同时揭示键和关联值的方式来遍历字典。

另一种枚举字典元素的方法是使用快速枚举协议。快速枚举简化了访问集合中每个元素的过程，尽管它仅适用于苹果操作系统的最新版本。

```
- (void) enumerateDictionaryObjects
{
    NSDictionary *dict = @{ @"one": @"first", @"two" : @"second", @"three" : @"third" };
    for (NSString *key in dict)
    {
        NSLog(@"The dictionary contains key %@ and value %@",
              key, [dict objectForKey:key]);
    }
}
```

### 可变字典

正如 `NSArray` 是一个不可变的对象序列一样，`NSDictionary` 也是不可变的。这意味着，一旦创建，就无法在其中插入新的键。此外，对于给定的键集，也无法更改字典中包含的键/值关联。其原因与 `NSArray` 不可变的原因类似：不可变的数据更高效，且易于算法处理。因此，这使得 `NSDictionary` 拥有较小的开销，在考虑这种频繁使用的数据结构时这一点非常重要。

另一方面，经常需要考虑可变字典，在其中可以根据需要添加键和更改关联。为满足这一需求，Foundation 框架提供了 `NSDictionary` 的可变版本，称为 `NSMutableDictionary`。

`NSMutableDictionary` 类继承自 `NSDictionary`，因此任何 `NSMutableDictionary` 对象也是一个 `NSDictionary` 对象。这意味着我之前关于 `NSDictionary` 的所有描述在此情况下仍然成立。此外，还添加了一些新方法来修改字典的内容。

第一个方法是 `setObject:forKey:`。此方法的第一个参数是要设置的值，第二个参数是关联的键。使用 `setObject:forKey:` 可以向 `NSMutableDictionary` 添加新元素，如下例所示：

```
- (void) changeDictionary
{
    NSDictionary *immutable_dict =
        @{ @"one": @"first", @"two" : @"second", @"three" : @"third" };
    NSMutableDictionary *dict = [NSMutableDictionary
        dictionaryWithDictionary:immutable_dict];
    [dict setObject:@"fourth" forKey:@"four"];
    [dict setObject:@"fifth" forKey:@"five"];
    for (NSString *key in dict)
    {
        NSLog(@"The dictionary contains key %@ and value %@",
              key, [dict objectForKey:key]);
    }
}
```

这段代码创建了一个名为 `immutable_dict` 的初始字典，然后使用该字典初始化一个 `NSMutableDictionary` 实例。这可以通过使用 `dictionaryWithDictionary:` 方法实现。接着，使用 `setObject:forKey:` 方法添加两个键/值对。最后使用一个 `for` 循环遍历键，并通过 `objectForKey:` 检索给定键对应的对象，打印生成的字典。

另一个可以在 `NSMutableDictionary` 上调用的方法是 `removeObjectForKey:`，用于从字典中删除条目。此方法的使用很简单：处理完消息后，与给定键关联的对象将从字典中移除。如果要删除多个键，可以使用 `removeObjectsForKeys:` 方法。`removeObjectsForKeys:` 的参数必须是一个 `NSArray`，包含所有要删除的对象对应的键。例如，你可以修改上面的示例，使用一个键数组删除字典中的两个条目，如下所示：

```
- (void) deleteFromDictionary
{
    NSDictionary *immutable_dict =
        @{ @"one": @"first", @"two" : @"second", @"three" : @"third" };
    NSMutableDictionary *dict = [NSMutableDictionary
        dictionaryWithDictionary:immutable_dict];
    [dict setObject:@"fourth" forKey:@"four"];
    [dict setObject:@"fifth" forKey:@"five"];
    for (NSString *key in dict)
    {
        NSLog(@"The dictionary contains key %@ and value %@",
              key, [dict objectForKey:key]);
    }
    NSArray *keys_to_delete = @[ @"two", @"three" ];
    [dict removeObjectsForKeys:keys_to_delete];
    for (NSString *key in dict)
    {
        NSLog(@"The dictionary still contains key %@ and value %@",
              key, [dict objectForKey:key]);
    }
}
```



## 总结

本章讨论了 Objective-C Foundation 框架中一些最重要的类。首先是 `NSString` 类，用于存储和操作字符串。然后介绍了 `NSNumber` 类，它作为整型、浮点数、字符和布尔值等常见数值的封装器。最后，详细讨论了容器类，包括 `NSArray` 和 `NSDictionary`。

字符串之所以重要，是因为它们是对输入和输出数据进行编码的最常见方式。`NSString` 类提供了多种初始化方法，可根据字符串数据提供给应用程序的不同方式，用于各种场景。常用功能包括子字符串匹配和比较方法。

在 Objective-C 中，数字可以通过两种方式处理。原生类型与 C 语言中使用的类型相同，它们最适合用于高性能算法。然而，许多 Objective-C 类需要引用一个对象。在这些情况下，可以将数字包装在 `NSNumber` 的实例中。该类提供了以所需格式访问数据的方法，包括 `int`、`long`、`float`、`double`、`char` 和 `BOOL`。

集合是用于维护其他对象集的对象。`NSArray` 类定义了按顺序存储的对象的接口。`NSArray` 类的对象可以从零开始的索引存储和检索元素。`NSMutableArray` 是 `NSArray` 的子类，它维护具有可变接口的数组，允许添加和/或删除元素。

`NSDictionary` 类提供了一个与键关联的元素集合。你需要提供一个有效的键来检索元素。`NSMutableDictionary` 允许用户向字典中添加和删除条目。

这些类共同提供了编写更复杂算法所需的大部分基础设施，范围从图形界面到游戏编程逻辑。在下一章中，你将看到如何使类对于需要扩展它们的应用程序更加灵活。通过协议和类别，你将了解到两种可用于向现有类添加高级功能的机制。

