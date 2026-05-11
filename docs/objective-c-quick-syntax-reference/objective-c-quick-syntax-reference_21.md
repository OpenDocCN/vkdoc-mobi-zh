# 定义类

我在演示用于处理字符串、数字、数组和字典的 Objective-C 对象时，已经介绍过对象。对象是一种基本的面向对象编程模式。虽然你通常会直接使用已经为你设置好的 Foundation 对象，但通常你需要为你的应用自定义你自己的对象类型。

## 类

你可以在演示用于处理字符串、数字、数组和字典的 Objective-C 对象时已经介绍过对象。**（注：此处英文原文有重复段落，译文按原文结构保留重复）** 类是用于创建对象的代码定义。编写类定义的主要目的是表达一个具有属性和行为的实体。

在编写类时，属性称为 properties，行为称为 methods。Properties 用于描述一个对象，而 methods 用于让对象执行一个动作。

定义类时需要完成两项重要任务：编写接口和编写实现。



### 类接口

使用类接口可以指定类名以及构成该类的属性和方法。以下是创建一个名为 `Project` 的类的方法：

```
#import <Foundation/Foundation.h>

@interface Project : NSObject

@end
```

以 `#import` 开头的代码行导入了 Foundation 框架。每当您需要使用 `NSObject` 或 `NSString` 这类 Objective-C 类时（几乎总是如此），都需要包含此框架。

具体来说，您需要引用 `NSObject`，因为它将成为您的基类。基类是您派生类的起点，`NSObject` 提供了使对象按预期工作所需的对象创建方法（例如 `alloc` 和 `init`）。

在以 `@interface` 关键字开头的行中，您可以使用类名 `Project` 以及冒号后面的基类 `NSObject`。

`@interface` 关键字必须与 `@end` 关键字配对使用。

#### 属性前置声明

属性需要在类接口中编写前置声明。这些声明位于 `@interface` 行和 `@end` 行之间的空间内。

```
#import <Foundation/Foundation.h>

@interface Project : NSObject

@property(strong) NSString *name;

@end
```

属性前置声明以 `@property` 关键字开头，后跟括号内的属性描述符。有关可使用的参数描述符列表，请参见表 16-1。

**表 16-1.** 参数描述符

| 属性 | 描述 |
| --- | --- |
| `Readwrite` | 该属性需要同时包含 getter 和 setter 方法（默认值）。 |
| `Readonly` | 该属性只需要 getter 方法（对象无法设置此属性）。 |
| `strong` | 该属性将具有强引用关系。 |
| `weak` | 当目标对象被释放时，该属性将被设为 nil。 |
| `assign` | 该属性仅使用赋值操作（用于基础数据类型）。 |
| `Copy` | 该属性返回一个副本，并且必须实现 `NSCopying` 协议。 |
| `retain` | setter 方法中会发送 retain 消息。 |
| `nonatomic` | 指定该属性是非原子性的（在访问时不会加锁）。 |

属性前置声明的后两部分是数据类型和属性名。您可以将此前置声明理解为：定义了一个名为 `name` 的属性，其类型为 `NSString`，并且 `Project` 将与其建立强引用关系。

**注意**  
`strong`、`retain` 和 `weak` 等术语与属性值的内存管理方式有关。`strong` 和 `retain` 都意味着您的类对象将始终持有对属性值的引用，从而保证在您需要期间该对象不会超出作用域。而属性描述符 `weak` 和 `assign` 则不提供此保证。

#### 方法前置声明

方法同样需要前置声明。属性用于描述对象，而方法则代表对象可以执行的操作。以下是向 `Project` 类添加前置声明的方法：

```
#import <Foundation/Foundation.h>

@interface Project : NSObject

@property(strong) NSString *name;

-(void)generateReport;

@end
```

方法前置声明以减号开头，后跟括号内的返回类型。此方法具有 `void` 返回类型，但您可以使用任何数据类型或类作为方法的返回类型。

之后是方法签名。当您看到一个包含参数的方法时，我会进一步讨论签名。以下是一个包含参数的方法示例：

```
#import <Foundation/Foundation.h>

@interface Project : NSObject

@property(strong) NSString *name;

-(void)generateReport;

-(void)generateReportAndAddThisString:(NSString *)string
                     andThenAddThisDate:(NSDate *)date;

@end
```

在此参数示例中，您可以看到方法签名被分为两部分。每部分都有一个参数和一个由冒号分隔的参数描述符。在 Objective-C 中，方法签名是参数描述符和参数的集合。当您没有参数时（如第一个方法），只需使用一个参数描述符来描述该方法即可。

### 实现类

定义类接口是定义类过程的第一步。下一步称为实现，因为您要在此处提供使类对象正常工作的代码实现。

要开始实现一个类，请使用 `@implementation` 关键字加上类名。

```
#import "Project.h"

@implementation Project

@end
```

第一行代码将 Project 的前置声明导入到此文件中，以便该类了解需要实现的内容。

**注意**  
虽然并非强制要求，但通常类接口和实现会分别编写在不同文件中。接口文件以 `.h` 扩展名结尾，有时称为头文件。实现文件以 `.m` 扩展名结尾，有时称为代码文件。

实现以 `@implementation` 关键字开头，后跟类名 `Project`。实现以 `@end` 关键字结束。现在，您需要实现属性和方法。

属性会自动为您实现，无需采取任何额外操作即可使属性生效。

#### 实现方法

当您实现一个方法时，需要重复接口中的方法前置声明。但您需要添加一个代码块以及使方法执行特定操作的代码。

```
#import "Project.h"

@implementation Project

-(void)generateReport{
    NSLog(@"This is a report!");
}

@end
```

当您实现包含参数的方法时，可以在代码中引用这些参数值。

```
#import "Project.h"

@implementation Project

-(void)generateReport{
    NSLog(@"This is a report!");
}

-(void)generateReportAndAddThisString:(NSString *)string
                     andThenAddThisDate:(NSDate *)date{
    [self generateReport];
    NSLog(@"%@", string);
    NSLog(@"Date: %@", date);
}

@end
```


好的，作为一名高级文档工程师和翻译员，我将遵循您提供的注意事项和示例，将给定的英文文本翻译成高质量的中文。

---


##### 私有属性和方法

上述过程定义的都是公共属性和方法。这意味着其他对象可以引用这些属性并使用这些方法。从该类派生出的类也可以使用并覆写这些属性和方法。如果你希望阻止这种情况发生，使属性和方法成为私有，可以使用类扩展。

###### 类扩展

类扩展提供了一种在实现文件中扩展类接口的方式。由于其他类会导入以 `.h` 扩展名结尾的接口文件（头文件），它们将无法访问在类扩展中使用前向声明定义的任何内容。

你可以像这样在实现文件中放置类扩展：

```
#import "Project.h"

@interface Project()

@property(strong) NSArray *listOfTasks;

-(void)generateAnotherReport;

@end

@implementation Project

-(void)generateReport{
    NSLog(@"This is a report!");
}

-(void)generateReportAndAddThisString:(NSString *)string
                   andThenAddThisDate:(NSDate *)date{
    [self generateReport];
    NSLog(@"%@", string);
    NSLog(@"Date: %@", date);
}

-(void)generateAnotherReport{
    NSLog(@"Another report!");
}

@end
```

类扩展看起来像接口，但类名后面有空的括号。上述类扩展中的前向声明遵循与其他属性和方法前向声明相同的规则。类扩展必须以 `@end` 关键字结尾（每个 `@interface` 都需要一个匹配的 `@end`）。

###### 局部实例变量

有时你需要一些存储变量，这些变量不值得声明为属性。例如，有时你可能需要一个“计数器”或“进度”变量，或者一个维护日志的变量。这些变量是必需的，但它们并不能真正描述对象，因此不值得像属性一样对待。

在这种情况下，你可以使用实例变量。实例变量可以直接包含在类扩展中，就像这样：

```
#import "Project.h"

@interface Project() {
    int counter;
    NSString *log;
}

@property(strong) NSArray *listOfTasks;

-(void)generateAnotherReport;

@end

@implementation Project

-(void)generateReport{
    NSLog(@"This is a report!");
}

-(void)generateReportAndAddThisString:(NSString *)string
                   andThenAddThisDate:(NSDate *)date{
    [self generateReport];
    NSLog(@"%@", string);
    NSLog(@"Date: %@", date);
}

-(void)generateAnotherReport{
    NSLog(@"Another report!");
}

@end
```

