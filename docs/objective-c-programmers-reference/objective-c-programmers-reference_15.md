# 9. 键值编码

## 摘要

在前面的章节中，你看到了 Objective-C 程序员使用面向对象编程概念处理代码的多种方式。例如，开发者可以利用语言的动态性，通过消息传递实现多态功能，甚至可以使用协议的概念向现有类添加新方法。

然而，面向对象编程的另一方面涉及对对象中包含的数据的访问。虽然明智的做法是避免直接访问对象管理的数据，但 Objective-C 提供了一种使用属性机制获取和设置数据的明确定义的过程。属性使数据对其他对象可用，同时保留了控制这些数据如何共享的能力。

然而，属性并不是跨对象共享数据的唯一方式。在本章中，我将讨论键值编码（KVC），这是一种基于 `NSObject` 层次结构功能的通用数据发布访问机制。键值编码被设计为允许通过使用编码为字符串的键在对象之间交换数据。因此，在 KVC 中，无需为每个数据元素调用不同的方法；相反，可以使用一个简单的接口来获取和设置广泛的数据。

键值编程之所以是一个重要主题的另一个原因是它在许多 Objective-C 库中被使用。例如，用于从数据库及其他存储库中存储和检索数据的 CoreData 框架，采用基于 KVC 的模型来保存和检索数据对象。借助 KVC，数据可以通过一个简单的接口进行访问，从而避免了在对象之间的唯一区别是它们包含的值集不同时创建多个子类的需要。



## 键值编码的概念

键值编码是一组接口的规范，允许对象仅使用字符串作为标识符来访问属性。这个字符串被称为*键*，用于访问属性的存储值。

该规范的客户端非常简单，它使用两个方法来读取和设置特定属性。`valueForKey:` 方法负责访问特定键中存储的值。键对象是一个 `NSString`，因此你可以使用相同的接口访问不同类型的对象。第二个方法是 `setValue:forKey:`，用于为特定键设置值对象。

KVC 最出色的特点之一是，你几乎不需要做任何额外工作就能支持对实例变量的访问——前提是你愿意提供对数据的完全访问权限。默认情况下，KVC 通过其在 `NSObject` 上的实现启用，因此你只需在任何 `NSObject` 派生对象中调用 `valueForKey:` 即可访问数据，示例如下：

```
// 文件：KVSample.h
// 包含三个变量的示例接口

@interface KVSample : NSObject
{
        int anInt;
        double aDouble;
        NSString *aString;
}
- (id) init;
+ (void) accessData;
@end
```

这是一个简单的类接口，包含三个实例变量：一个整数、一个双精度浮点数和一个字符串变量。此外，还有一个 `init` 方法和一个名为 `accessData` 的类方法，用于测试目的。以下是使用 KVC 访问这些实例变量的方式：

```
// 文件：KVSample.m
#import "KVSample.h"

@implementation KVSample

- (id) init
{
        KVSample *obj = [super init];
        if (obj)
        {
               obj->anInt = 10;
               obj->aDouble = 11.0;
               obj->aString = @"test";
        }
        return obj;
}

+ (void) accessData
{
        KVSample *sample = [[KVSample alloc] init];
        NSNumber *a = [sample valueForKey:@"anInt"];
        NSLog(@"整数值: %@", a);
        NSNumber *b = [sample valueForKey:@"aDouble"];
        NSLog(@"双精度浮点值: %@", b);
        NSNumber *c = [sample valueForKey:@"aString"];
        NSLog(@"双精度浮点值: %@", c);
        [sample release];
}

@end
```

此处使用的 `init` 方法很直接，与 Objective-C 中使用的其他 `init` 方法类似。在类的初始化部分，你为 `KVSample` 中的每个实例变量赋测试值。它们将包含值 `10`、`11.0` 和 `"test"`。

`accessData` 方法只是一个示例，展示了任何其他客户端如何使用 KVC 来读取存储在 Objective-C 对象中的数据。要使此方法生效，你需要将要访问的数据名称作为键提供给 `valueForKey:`。运行时系统知道类中每个实例变量的名称，并将你作为键传递的名称转换为包含数据的变量的正确地址。

### 避免直接访问

从上面的例子可以看出，派生自 `NSObject` 的类的默认行为是允许通过 KVC 机制进行访问。但是，你可能不希望授予这种访问权限。例如，如果你正在定义一个可编写脚本的接口，你可能希望将一些实例变量置于外部代码无法访问的范围。再比如，如果类包含一个保存密码的实例变量，你可能希望限制与应用程序交互的脚本对该变量的访问。

要禁用默认行为，KVC 提供了一个名为 `accessInstanceVariablesDirectly` 的方法。可以在派生类中重写此方法，以表明你不希望授予所有实例变量默认的访问级别。以下是该方法的声明方式：

```
// 文件：KVSample.h
@interface KVSample : NSObject
{
        int anInt;
        double aDouble;
        NSString *aString;
}
- (id) init;
+ (BOOL) accessInstanceVariablesDirectly;
+ (void) accessData;
@end
```

在实现部分，你可以添加该方法，返回 `NO` 以指示变量不应被直接访问，如下所示：

```
// 文件：KVSample.m
#import "KVSample.h"

@implementation KVSample
// 在此处实现其他方法

+ (BOOL) accessInstanceVariablesDirectly
{
        return NO;
}

@end
```

这个修改后的 `KVSample` 版本实现了类方法 `accessInstanceVariablesDirectly`。该类方法返回一个假值，以指示运行时系统不应访问其中包含的实例变量。要检查新实现是否正常工作，你可以在该修改版本中运行 `accessData` 方法。结果，你将看到以下错误信息：

`this class is not key value coding-compliant for the key anInt.`

### 编写属性访问器

KVC 属性可以隐式地通过实例变量定义，也可以显式地通过 getter 和 setter 定义。通用 getter 方法的语法是 `– (<data type>) <key>`，其中 *key* 是你期望作为键接收的字符串。例如，如果你想提供对 `KVSample` 中实例方法的访问，可以编写一个包含 getter 的接口，如下所示：

```
// 文件：KVSample.h
// 包含三个变量的示例接口

@interface KVSample : NSObject
{
        int _anInt;
        double _aDouble;
        NSString *_aString;
}
- (int) anInt;
- (double) aDouble;
- (NSString*)aString;
+ (void) accessData;
// 在此处添加其他方法
@end
```

请注意，这次你更新了实例变量名称，使其以下划线开头，这是一种常见的命名约定，用于将它们与属性名称区分开来。

现在你需要为声明的 KVC 数据提供 getter 实现。

```
// 文件：KVSample.m
#import "KVSample.h"

@implementation KVSample

- (id) init
{
        KVSample *obj = [super init];
        if (obj)
        {
               obj->_anInt = 10;
               obj->_aDouble = 11.0;
               obj->_aString = @"test";
        }
        return obj;
}

- (int) anInt
{
        return _anInt;
}

- (double) aDouble
{
        return _aDouble;
}

- (NSString*)aString
{
        return _aString;
}

// 在此处添加其他方法
@end
```

现在你可以使用以下示例代码在 `KVSample` 上调用 `accessData` 方法：

```
- (void) testKVSample
{
        [KVSample accessData];
}
```

这将顺利运行，因为 `accessData` 使用的所有键都有相应的 getter 实现，这些 getter 返回实例变量的内容。例如，当执行以下代码段时：

```
[sample valueForKey:@"aDouble"]
```

Objective-C 运行时将查找名为 `aDouble` 的 getter 方法，并返回将该消息发送给目标对象 `sample` 后收到的响应。运行时还会将 `double` 类型的返回值存储到一个 `NSNumber` 对象中，以便你可以通过面向对象的接口统一处理返回值。



#### 修改值

KVC 的另一方面涉及通过字符串键来修改传递给目标的值。客户端访问此功能的接口也非常统一，并采用了 `setValue:forKey:` 方法。

以下是类方法 `accessData` 如何被修改以设置导出键 `aDouble`、`anInt` 和 `aString` 的初始值的示例：

```
@interface KVSample : NSObject
{
    int _anInt;
    double _aDouble;
    NSString *_aString;
}
- (int) anInt;
- (double) aDouble;
- (NSString*)aString;
- (void) setAnInt:(int) val;
- (void) setADouble: (double) val;
- (void) setAString: (NSString*) str;
- (id) init;
+ (BOOL) accessInstanceVariablesDirectly;
+ (void) accessData;
@end
```

这个修改后的接口增加了一组 `set<Key>:` 方法，这些方法接收一个输入值并用它来更新对应的实例变量。以下是这些方法的实现方式：

```
- (void) setAnInt:(int) x
{
    _anInt = x;
}
- (void) setADouble: (double) x
{
    _aDouble = x;
}
- (void) setAString: (NSString*) s
{
    _aString = s;
}
```

最后，使用这些 setter，你可以修改 `accessData` 的代码，以便更新 `aString`、`anInt` 和 `aDouble` 键的内容为新值：

```
+ (void) accessData
{
    KVSample *sample = [[KVSample alloc] init];
    [sample setValue:@20 forKey:@"anInt"];
    [sample setValue:@44.0 forKey:@"aDouble"];
    [sample setValue:@"another value" forKey:@"aString"];
    NSNumber *a = [sample valueForKey:@"anInt"];
    NSLog(@"int value: %@", a);
    NSNumber *b = [sample valueForKey:@"aDouble"];
    NSLog(@"double value: %@", b);
    NSNumber *c = [sample valueForKey:@"aString"];
    NSLog(@"double value: %@", c);
}
```

这里，你获取了 `KVSample` 的 `sample` 实例，并向其发送了三条消息，每条消息都为此对象所支持的一个键提供了一个值。

从这段代码可以看出，KVC 支持存储所有常见数据类型值的键，例如 `int`、`double` 和 `NSString`。还有其他支持的类型，例如 `float`、`char` 和 `long`。本质上，KVC 接口支持所有原生数据类型。`BOOL` 类型也有两种支持方式。例如，一个名为 `enabled` 的 `BOOL` 类型键，可以映射到 getter 方法 `-(BOOL)isEnabled` 或 `-(BOOL)enabled`。如果找到这两个 getter 方法中的任何一个，都将用于返回所需的值。另一方面，在这种情况下，setter 方法必须命名为 `setEnabled:`。

## 注意

Foundation 框架定义了一种方法，让类能够验证传递给 `setValue:forKey:` 的值。可以向 KVC 兼容的类添加 `validate<key>:` 方法，以确定此类参数是否有效。但是，KVC 类的使用者有责任在必要时调用 `validate<key>:`。你应该检查你所使用的类是否提供了参数验证，并按照类接口中定义的方式使用它。

### KVC 与属性

在阅读上述关于键和值之间的关系，以及它们如何映射到 getter 和 setter 的描述时，你可能已经注意到这些概念与属性概念之间的相似性。尽管 KVC 和属性是两项独立的技术，但它们被设计为具有互补特性，以便能够很好地相互配合。

属性与 KVC 兼容对象暴露的值之间最明显的关系是，它们都使用 getter 和 setter，而这些 getter 和 setter 与当前类中的底层实例变量紧密映射。这意味着，通过定义属性，你可以进一步简化创建 KVC 兼容类的过程。

利用属性与键值对之间的这种等价关系，你可以使用以下更简单的接口重写 `KVSample` 类：

```
// file: KVSample.h
// 包含三个变量的示例接口
@interface KVSample : NSObject
@property int anInt;
@property double aDouble;
@property (retain) NSString *aString;
- (id) init;
+ (BOOL) accessInstanceVariablesDirectly;
+ (void) accessData;
@end
```

请注意，现在你有三个属性，分别代表类中存储的三个实例变量。你可以从接口中移除实例变量的声明，因为当编译器处理属性定义时，它们会被隐式创建。

你还可以移除那一组充当 getter 和 setter 的方法。这些方法现在是隐式定义的，因为在编译期间它们会与其对应的属性一起被合成。此外，这些更改使内存管理变得更加容易，因为你可以使用诸如 `retain` 或 `copy` 之类的关键字来执行所需的内存管理策略。

以下是 `KVSample` 类的简化实现：

```
// file: KVSample.m
#import "KVSample.h"
@implementation KVSample
- (id) init
{
    self = [super init];
    if (self)
    {
        self.anInt = 10;
        self.aDouble = 11.0;
        self.aString = @"test";
    }
    return self;
}
+ (void) accessData
{
    KVSample *sample = [[KVSample alloc] init];
    NSNumber *a = [sample valueForKey:@"anInt"];
    NSLog(@"int value: %@", a);
    NSNumber *b = [sample valueForKey:@"aDouble"];
    NSLog(@"double value: %@", b);
    NSNumber *c = [sample valueForKey:@"aString"];
    NSLog(@"double value: %@", c);
    [sample setValue:@20 forKey:@"anInt"];
    [sample setValue:@44.0 forKey:@"aDouble"];
    [sample setValue:@"another value" forKey:@"aString"];
    [sample setValue:@YES forKey:@"enabled"];
    a = [sample valueForKey:@"anInt"];
    NSLog(@"int value: %@", a);
    b = [sample valueForKey:@"aDouble"];
    NSLog(@"double value: %@", b);
    c = [sample valueForKey:@"aString"];
    NSLog(@"double value: %@", c);
}
+ (BOOL) accessInstanceVariablesDirectly
{
    return NO;
}
@end
```

从实现中移除所有 setter 和 getter 后，你会得到更简洁、更易于维护的代码。你可能还想更改访问属性的方式，使用更简洁的点语法来引用数据，而不是之前一直使用的以下划线开头的实例变量。



## 使用结构体

在前面的章节中，你已经学习了如何使用 KVC 公开简单的属性，这些属性是通过数字或字符串等标量数据类型定义的。对于数字，其值会自动转换为对象包装器，例如 `NSNumber`。处理结构体也是类似的。不过，你无需担心数据包装的过程，因为运行时库已经知道如何为任何用户自定义的结构体创建必要的对象。

使用 KVC 系统处理结构化数据的秘诀在于，运行时库会自动将 `struct` 转换为 `NSValue` 对象。`NSValue`，顾名思义，是一个能够容纳任何值的对象，比如数字、字符串，甚至是包含用户自定义数据类型的 `struct`。一旦数据存储到 `NSValue` 中，它就变得不可变，并且之后可以转换为所需的目标类型。

下面是一个示例类，演示了如何将结构体与 KVC 配合使用。首先，你需要使用 `typedef` 来命名一个用户自定义类型。

```
// 文件 KVStructSample.h

typedef struct
{
        int anInt;
        double aDouble;
}
SampleStruct;
```

然后，定义一个新类，其中包含一个 `SampleStruct` 类型的数据项。

```
// 文件 KVStructSample.h

@interface KVStructSample : NSObject
{
        SampleStruct _structValue;
}

@property SampleStruct structValue;

@end
```

为了简化实现，添加了一个 `SampleStruct` 类型的属性，但你也可以像之前看到的那样，仅使用实例变量来完成同样的操作。现在，类的实现负责初始化对象。同时，你还可以有一个类方法，用于创建新实例并显示其数据。

```
// 文件 KVStructSample.m

@implementation KVStructSample

- (id) init
{
        KVStructSample *obj = [super init];
        if (obj)
        {
               SampleStruct val = { 1, 2.0 };
               obj.structValue = val;
        }
        return obj;
}

+ (void) accessData
{
        KVStructSample *sample = [[KVStructSample alloc] init];
        NSValue *val = [sample valueForKey:@"structValue"];
        SampleStruct structVal;
        [val getValue:&structVal];
        NSLog(@"该值是 %d 和 %lf", structVal.anInt, structVal.aDouble);
        [sample release];
}

@end
```

第一个方法 `init` 为 `SampleStruct` 类型的属性设置初始值。为此，你需要创建一个给定结构类型的新变量，并将其复制到实例变量 `structValue` 中。

这里展示的 `accessData` 方法只是一个如何使用此类进行操作的示例。它创建了一个 `KVStructSample` 实例，然后通过发送 `valueForKey:` 消息来获取一个 `NSValue` 对象。由 `valueForKey:` 返回的 `NSValue` 对象包含一个 `SampleStruct` 的值。要取回该值，你需要使用 `getValue:` 方法，该方法接收一个指向用户定义结构体的指针作为参数。然后，你可以继续打印该结构体组件 `anInt` 和 `aDouble` 的值，这些值将在日志窗口中显示为 `1` 和 `2.0`。

## 通过键路径访问对象

一旦你能够通过符合 KVC 的键访问对象中包含的标量数据和结构体，就需要下一级别的功能：即，能够对使用对象类型定义的属性执行相同的操作。例如，一个 `Factory` 对象可能包含一个或多个 `Employee` 对象。这些对象应该可以通过 KVC 风格的接口进行访问。

为了通过 KVC 使通用对象变得可访问，运行时系统不仅支持由一个单词组成的简单键的概念，还支持使用完整路径标记法确定的复合键的概念。在路径定义中，Objective-C 使用点号 (`.`) 作为路径各段之间的分隔符，这样你就可以指定基对象和所需属性之间包含层次结构中的所有元素。

使用键路径，运行时系统能够遍历由点号分隔的元素指定的对象列表。然后，就可以根据键路径说明来检索或修改元素。

为了更好地理解这个过程是如何工作的，考虑一下 `BankEmployee` 类，它存储了单个银行员工的信息，包括一个名为 `salary` 的属性。以下是其接口定义：

```
// 文件 BankEmployee.h

@interface BankEmployee : NSObject

@property double salary;

- (id) init ;

// 其他属性和方法在此处

@end
```

这里只展示了 `salary` 属性和 `init` 方法。`init` 的实现与你之前在其他示例中看到的类似。

```
// 文件 BankEmployee.m

#import "BankEmployee.h"

@implementation BankEmployee

- (id) init
{
        self = [super init];
        if (self)
        {
               self.salary = 200000.0;
        }
        return self;
}

@end
```

请注意，出于测试目的，你将 `salary` 属性初始化为一个常量，该常量稍后将被检索。现在，考虑将此类的对象用作 `Bank` 类的一部分，该类具有以下接口：

```
// 文件 Bank.h

@interface Bank : NSObject

@property (retain) BankEmployee *employee;

+ (void) accessBank ;

- (id) init;

@end
```

这个类中有一个属性，就像之前的示例一样，但这次该属性不是原生数据类型或 `struct`，而是一个 `BankEmployee` 类型的对象。你使用 `init` 函数来创建一个该类型的新对象，并设置其默认值。`accessBank` 方法演示了如何使用键路径来访问这个基于对象的属性的内容：

```
// 文件 Bank.m

#import "Bank.h"

@implementation Bank

- (id) init
{
        Bank *obj = [super init];
        if (obj)
        {
               obj.employee = [[BankEmployee alloc] init];
        }
        return obj;
}

+ (void) accessBank
{
        Bank *bank = [[Bank alloc] init];
        id res = [bank valueForKeyPath:@"employee.salary"];
        NSLog(@"薪资是 %@", res);
}

// 你需要释放 employee 对象

- (void) dealloc
{
        self.employee = nil;
        [super dealloc];
}

@end
```

请注意在 `accessBank` 方法内部使用了 `valueForKeyPath:` 方法。你需要传入一个相对于目标对象的点号分隔路径，才能访问所需的属性。这个消息调用返回的值是一个 `NSValue` 类型的对象，类似于你在之前的示例中看到的。`accessBank` 方法的结果将是，默认薪资 `200000.0` 会被打印到日志窗口。



## 访问集合

在使用 KVC 时，另一种常见的情况是一个或多个键对应一个对象集合。当出现这种情况时，你需要使用一些面向集合的方法，这些方法能够与因请求而创建的数组或元素集进行交互。

KVC 规范提供了一种访问内部使用的集合成员的方式。例如，假设你有一个名为 `employees` 的数组。KVC 可以通过实例变量名直接访问、通过属性访问，或使用一对访问器方法来访问该数组的元素。下面我们使用方法访问的方式，以此说明一般情况下的具体操作。在这种情况下，如果你想暴露一个名为 `bankEmployees` 的属性，你需要提供一个名为 `objectInEmployeesAtIndex:` 的属性 getter 方法。为了通过 KVC 支持 `bankEmployees` 属性，需要对 `Bank` 类进行以下修改：

```objc
// file Bank.h

@interface Bank : NSObject

{
NSArray *employees;
}

- (NSUInteger) countOfBankEmployees;
- (id) objectInBankEmployeesAtIndex:(NSUInteger)index;
+ (void) accessBankData;
- (id) init;

@end
```

首先，请注意你将值存储在一个名为 `employees` 的内部 `NSArray` 对象中。KVC 的接口是通过 `countOfBankEmployees` 和 `objectInBankEmployeesAtIndex:` 这两个方法实现的。这些方法在功能上与 `NSArray` 类中的 `count` 和 `objectAtIndex:` 方法类似。以下是该类的实现：

```objc
@implementation Bank

- (id) init
{
self = [super init];
if (self)
{
self->employees = @[[[[BankEmployee alloc] init] autorelease]];
}
return self;
}

- (NSUInteger) countOfBankEmployees
{
return self->employees.count;
}

- (id) objectInBankEmployeesAtIndex:(NSUInteger)index
{
return self->employees[index];
}

- (void) dealloc
{
[self->employees release];
[super dealloc];
}

@end
```

`init` 方法负责设置数组的初始内容。为了演示，我创建了一个包含单个元素的 `NSArray`。接着实现了 `count` 和 `objectIn<key>AtIndex:` 方法。由于你有一个包含待发布内容的内部数组，这些方法的实现很简单。但你不需要局限于使用数组作为底层实现。任何对象集合都可以用来存储此接口的数据。

接下来，你需要使用 `dealloc` 释放该类中的资源，这与你在其他情况下看到的方式类似。最后，我将展示如何通过一个名为 `accessBankData:` 的类方法来使用 `Bank`。

```objc
+ (void) accessBankData
{
Bank *bank = [[Bank alloc] init];
NSArray * res = [bank valueForKeyPath:@"bankEmployees"];
NSLog(@"The first employee is %@", res[0]);
[bank release];
}
```

首先，使用 `alloc` 创建一个新对象。然后你可以使用 `valueForKeyPath:` 来检索 `bankEmployees` 数组。可以看到，即使 `bankEmployees` 仅作为一组提供对该命名属性访问权限的方法而存在，仍然返回了一个数组。最后，你可以使用 `NSArray` 操作的现代数组表示法打印数组的第一个元素。

返回集合是一项有用的功能，但这并非 KVC 在数据集合上能做的全部事情。客户端可以请求对数据集应用某些操作，并将操作结果作为给定键的值返回。这些特殊操作成为键的一部分，可以看作是一种用于集合操作的迷你语言。以下是可以在键路径中应用的一些操作的简要列表。

- `@avg`：返回索引集合中所有元素的平均值。
- `@max`：返回键路径中指定集合内某个属性的最大值。
- `@min`：返回键路径中指定集合内某个属性的最小值。
- `@sum`：返回键路径中指定集合内所有条目的总和。

使用这些属性，你可以基于数组和集合的内容执行快速计算，这有助于简化对对象的某些操作。在所有这些操作中，运行时负责收集数据并执行操作以返回所需的值，而无需目标类进行任何干预。

以下是一个在键路径中使用运算符的简单示例，考虑以下类方法：

```objc
+ (void) accessBankSalaries
{
Bank *bank = [[Bank alloc] init];
id res = [bank valueForKeyPath:@"bankEmployees.@sum.salary"];
NSLog(@"The total salary amount is %@", res);
[bank release];
}
```

类方法 `accessBankSalaries` 负责计算银行支付的总薪资。此计算由 KVC 系统执行，输入为完整的键路径以及 `@sum` 运算符。当此代码运行时，KVC 系统将首先查找集合（作为键路径的第一个元素提供）。然后，运算符 `@sum` 应用于前一路径的 `salary` 属性。最后，计算出的数量作为与键路径关联的值返回。

## 响应未定义键

KVC 建立了一种机制，客户端可以仅使用通用字符串键作为输入，来请求存储在对象上的属性。因此，运行时系统被有效地用于将调用者与实现所请求功能的类解耦。由于你不依赖编译，所有涉及 KVC 的操作都涉及仅在执行期间执行的属性检查。因此，某些属性检查可能不成功并产生意外错误。

在这种情况下，充分的测试是彻底避免运行时问题的最佳方法。然而，为了减轻因错误的 KVC 键访问而导致的一些问题，Objective-C 提供了一个名为 `valueForUndefinedKey:` 的附加方法。通过实现此方法，类可以启用一种有效的机制来处理作为输入提供的未定义键。

`valueForUndefinedKey:` 的特定实现可用于在 KVC 系统运行时未将键识别为有效时仍提供一个值。这样，`valueForUndefinedKey:` 就充当了返回用户请求值的“最后一次机会”。例如，以下方法避免了未定义键的错误，并简单地返回 `0` 作为默认响应：

```objc
- (id)valueForUndefinedKey:(NSString *)key
{
         return [NSNumber numberWithInt:0];
}
```



## KVC 与 KVO

`KVC` 是一项在 Cocoa 框架多个领域使用的技术。其中一个大量运用 `KVC` 的领域是用于数据观察和通知的键值观察（`KVO`）系统，该机制广泛应用于 `CoreData` 及其他场景。`KVO` 是一种机制，通过它，对象能够对被观察属性值的变化做出响应。换言之，`KVO` 是一种通用的通知机制，它依赖 `KVC` 来确定如何访问键及其对应的值。

`KVO` 解决方案包含两个主要元素：观察者和观察目标。要成为观察者，你需要在合适的目标对象上调用 `addObserver：forKeyPath：options：context：` 方法。随后，观察者还需要实现 `observeValueForKeyPath：ofObject：change：context：` 方法。每当被观察对象发生变更时，该方法都会被调用。最后，客户端可以通过调用 `removeObserver：` 方法来停止观察某个对象。

要成为 `KVO` 目标，对象首先需要符合 `KVC` 规范。这意味着 `KVC` 属性需要像前文讨论的那样暴露给客户端。这保证了 `NSObject` 能够管理对象中暴露的属性，同时使这些属性可供观察者使用。每当对象中的某个属性发生变化时，已订阅的观察者将自动收到通知。

让我们看一个实际运作的例子。假设你正在创建一个 `BankPayroll` 类，它使用之前讨论过的 `BankEmployee` 类。你希望获知 `salary` 属性值何时发生变化，以便使薪资计算保持最新。以下是使用 `KVO` 实现此功能的方式：

```
@interface BankPayrool : NSObject
{
    BankEmployee *employee;
    double _currentSalary;
}

- (id) initWithEmployee:(BankEmployee *)anEmployee;
- (void) startObserving;
+ (void) useBankPayroll;
@end
```

你存储了两个实例变量：一个用于 `employee` 对象，另一个用于存储当前薪资信息。以下是实现文件：

```
@implementation BankPayrool

- (id) initWithEmployee:(BankEmployee *)anEmployee
{
    self = [super init];
    if (self)
    {
        employee = anEmployee;
        [employee retain];
    }
    return self;
}

- (void) dealloc
{
    [employee removeObserver:self forKeyPath:@"salary"];
    [employee release];
    [super dealloc];
}

- (void) startObserving
{
    [employee addObserver:self forKeyPath:@"salary"
                  options:NSKeyValueObservingOptionNew
                  context:nil];
}

- (void) observeValueForKeyPath:(NSString *)keyPath
                       ofObject:(id)object
                         change:(NSDictionary *)change
                        context:(void *)context
{
    if (object == employee)
    {
        [self setValue:
            [change objectForKey:NSKeyValueChangeNewKey]
                forKey:@"currentSalary"];
        NSLog(@"current salary is now %lf", _currentSalary);
    }
}

@end
```

最后，你可以通过实现一个示例类方法 `useBankPayroll` 来使用上述代码，该方法会更改 `salary` 属性。结果，你将看到一条日志消息，显示 `BankPayroll` 类接收到的新的薪资值。

```
+ (void) useBankPayroll
{
    BankEmployee *employee = [[BankEmployee alloc] init];
    BankPayrool *payroll = [[BankPayrool alloc] initWithEmployee:employee];
    [payroll startObserving];
    [employee setValue:@300000.0 forKey:@"salary"];
    [payroll release];
    [employee release];
}
```

首先，你创建了 employee 对象，因为它将作为 `BankPayroll` 类的参数。然后你发送了 `startObserving` 消息，该消息将 payroll 对象注册为 `employee` 的观察者。最后，你为 `employee` 的 `salary` 属性设置了一个新值。这会触发向 payroll 发送通知，后者会将一些信息打印到日志中。

## 总结

在本章中，你探索了 Objective-C 中的键值编码系统。`KVC` 是一套约定和方法，允许仅使用字符串键或键路径来进行通用数据访问。要使用 `KVC` 访问数据，你只需使用 `valueForKey：` 消息。你也可以使用 `setValue:forKey:` 方法修改已暴露的数据。这个简单的接口可用于与原生或用户定义的（`struct`）数据类型进行交互。`KVC` 还允许你使用键路径与嵌入式对象进行交互，其中完整的属性名使用点号分隔。

`KVC` 既支持处理集合对象，也支持处理整型和字符串等单值。对于集合，你需要暴露标准的 Objective-C 集合（如 `NSArray` 和 `NSSet`），或者实现与 `NSArray` 中 `count` 和 `objectAtIndex:` 等价的方法。我并未涵盖支持集合时你可能拥有的所有选项，但有关 `KVC` 的在线文档非常易读，并包含了你所需的所有细节。

最后，我向你展示了键值观察（`KVO`）是如何与 `KVC` 交互的。`KVO` 利用 `KVC` 系统，在其之上创建了一个通知机制。这样，对象不仅可以通过键访问暴露的属性，还可以在这些属性发生变化时收到通知。

在下一章中，你将看到迄今为止所讨论的面向对象编程概念的一个重要应用。为此，我将向你展示 Objective-C 如何使用 Foundation 框架支持与文件系统的基本和高级交互。

