# 5. 继承

## 摘要

在前面的章节中，你探索了 Objective-C 中面向对象编程的一些基本概念。正如你所见，OOP 是促使该语言创建的主要软件开发概念。OOP 的一个主要特性是能够创建从其他现有类继承方法、变量和属性的新类。因此，要精通这门语言，你需要对基于类的继承概念有良好的工作知识。

然而，正确使用继承，无论是在设计阶段还是在基类和派生类的实现阶段，都需要投入一定的注意力。在本章中，你将探索与 Objective-C 中继承相关的主题，涵盖设计高效类层次结构所需的一些必要背景知识。

你将更深入地了解继承是如何工作的，以及同一层次结构中类之间的方法重写机制。此外，你还会看到如何解决因 Objective-C 中继承实现方式而引发的一些问题。例如，你将学习如何模拟多继承的使用，或者如何避免过深的类层次结构。



## 继承的概念

类描述了对象的通用行为。在面向对象语言中，类充当了将由系统创建和维护的对象的蓝图。通过定义一个新类，你可以勾勒出该类对象能够响应的一系列行为，以及每个对象可用的数据存储空间。因此，类引入了一个独立的代码单元，应用程序的其他部分可以与之交互和调用。

仅仅能够创建特定类型的对象就已经能够提供强大的功能，并且本身就能带来令人印象深刻的整体软件质量。然而，创建和使用新对象并不足以简化生成具有更复杂行为的类的过程。让我们来看一个典型示例，假设你想为`Employee`（员工）这个概念定义一个类。

```
// 文件 Employee.h

@interface Employee : NSObject

{
NSString *name;
}

- (void) setName:(NSString*) aName;
- (double) calculateSalary;

@end
```

以下是相应的实现文件：

```
// 文件 Employee.m

@implementation Employee

- (void) setName:(NSString*) aName

{

self->name = aName;

}

- (double) calculateSalary

{

double salary = 0;

// 执行薪资计算

return salary;

}

@end
```

有了这些定义，创建和使用具有`Employee`类所指定行为的对象就变得非常容易。正如你在前几章中所见，只需向`Employee`类发送一对`alloc/init`消息，即可创建该特定类型的新对象。

然而，假设你实际上想使用一个经过略微修改的`Employee`版本，一个原始类的创建者未曾预想到的变体。例如，你可能想表示一个按小时计酬的员工，其薪资取决于工作的小时数，而不是每年确定的固定值。如果没有继承，你将被迫为这个概念创建一个独立的新类。

```
// HourlyEmployee.h

@interface HourlyEmployee: NSObject

{
NSString *name;
int hoursPerWeek;
}

- (void) setName:(NSString*) aName;
- (double) calculateSalary;
- (int) getHoursPerWeek;

@end
```

```
// HourlyEmployee.m

@implementation HourlyEmployee

- (void) setName:(NSString*) aName

{

self->name = aName;

}

- (double) calculateSalary

{

double salary = 0;

// 为按小时计酬的员工执行薪资计算

return salary;

}

- (int) getHoursPerWeek

{

// 返回工作小时数

}

@end
```

这个类的定义和内容将与`Employee`类中使用的非常相似，只需进行少量修改以体现两个概念之间的差异。在一个依赖于某些方面是基本概念变体的对象定义的应用程序中，这将导致大量的代码重复——致使程序既难创建又难维护。在这种情况下，重用现有代码会非常困难，因为此类的大部分接口都需要在系统中重复编写。

现在，看看使用继承概念的同一个类：

```
// 文件 HourlyEmployee.h

@interface HourlyEmployee : Employee

- (int) getHoursPerWeek;

@end
```

```
// 文件 HourlyEmployee.m

@implementation HourlyEmployee

- (double) calculateSalary

{

double salary = 0;

// 执行薪资计算，后续会讨论

return salary;

}

- (int) getHoursPerWeek

{

// 返回工作小时数

}

@end
```

现在，`HourlyEmployee`不再是系统中一个独立的类；相反，它直接依赖于`Employee`提供的定义。这种组织方式的主要好处是，你无需重复原始类中包含的每个方法和实例变量。基类中的接口以及方法的实现都会被继承，这样程序员就可以专注于`HourlyEmployee`与原始类不同的方面——而不是重新编写已有的功能。

另外请注意，作为 Objective-C 类型系统的结果，在任何需要`Employee`对象的地方都可以使用`HourlyEmployee`。这意味着你还可以利用任何以某种方式为`Employee`对象提供服务的已有方法。另一方面，正如你将在后续章节中看到的，这种替换并非没有代价：它要求派生自给定子类的类也必须遵守原始类中定义的同一契约。

### 继承与重写

重写是用一个更具体的版本来替换超类中某个方法的行为。重写方法是自定义参与继承层次的类行为的主要方式。

在 Objective-C 中，任何方法都可以被重写。无论该方法是你应用程序中的类的一部分，还是来自第三方框架，都没有区别。唯一的限制是重写的方法需要使用与原始方法相同的关键字名称和参数。

Objective-C 中关于重写方法的一个特殊之处在于，你无需在子类的接口中提及它们。再次考虑`Employee`。假设你想引入另一个名为`Contractor`（承包商）的子类。在这种情况下，你希望调整实现，以反映承包商的职责差异。为了说明，我们假设原始类将增加`provideJobEvaluation`方法，如下所示：

```
// 文件 Employee.h

@interface Employee : NSObject

{
       NSString *name;
}

- (void) setName:(NSString*) aName;
- (double) calculateSalary;
- (void) provideJobEvaluation;

@end
```

该方法用于为每项已完成的工作创建一个常规的内部评估。现在来看如何为`Contractor`类声明接口：

```
// 文件 Contractor.h

@interface Contractor : Employee

{
       NSString *thirdPartyCompany;
}

@end
```

从这里可以清楚地看出，`Contractor` 有额外的存储需求。然而，这里并没有任何指示表明 `Contractor` 类实现了 `Employee` 中任何方法的新版本。不过，这对该类的使用者来说并没有影响。在实现部分，你可以自由地重写父类中的任何方法。

```
// 文件 Employee.m

@implementation Contractor

- (void) provideJobEvaluation

{
       // 此处执行合同工作的评估步骤
}

@end
```

通过这个定义，每当创建`Contractor`类的新对象时，`provideJobEvaluation`方法将为合同工作执行正确的计算，即使该重写在类接口中并未显式声明。这一点对于分类（categories）也同样适用。例如，你可以为`Contractor`创建一个新的分类，该分类将再次调整类的行为，如下所示：

```
// Contractor+NameHanding.h

@interface Contractor (NameHanding)

  // 此处未声明新方法

@end
```

它的实现如下：

```
// 文件 Contractor+NameHanding.m

@implementation Contractor (NameHanding)

- (void) setName:(NSString*) aName

{
       self->name = [NSString stringWithFormat:@"contractor %@", aName];
}

@end
```

由于重写了`setName:`方法，这将改变应用中创建的任何承包商的名称显示方式。



#### 调用超类中的方法

作为层级结构的一部分，子类有权访问从超类继承的数据和方法。这使得子类的对象可以调用层级中位于其上的类所定义的任何方法。

当超类中的方法名称不同时，调用该方法只是向 `self` 发送一条消息，使用传统的消息发送语法。消息分发机制会自动找到正确的实现。例如，考虑一个表示文件系统条目类型的类。

```
// 文件 FileType.h
@interface FileType : NSObject
{
    // 此处列出实例变量
}
- (NSString*) getExtension;
@end

// 文件 FileType.m
@implementation FileType
- (NSString*) getExtension
{
    NSString *ext = @"";
    // 获取扩展名
    return ext;
}
@end
```

现在假设你希望为文档实现一个新的子类，并将其命名为 `DocumentType`。

```
// 文件 DocumentType.h
@interface DocumentType : FileType
{
    // 此处列出实例变量
}
- (BOOL) isValid;
@end

// 文件 DocumentType.m
@implementation DocumentType
-(BOOL)isValid
{
    if ([@"" isEqualToString:[self getExtension]])
    {
        return false;
    }
    return true;
}
@end
```

在这个例子中，`DocumentType` 类需要使用超类中的 `getExtension` 方法。为此，它只需向 `self` 发送一条消息，使用如下表示法：

```
[self getExtension]
```

现在，假设你正在实现 `getExtension` 方法的一个新版本。在这种情况下，你可能希望能够调用该方法的原始版本。例如，你可能希望获取由 `FileType` 中 `getExtension` 方法计算出的原始扩展名，然后执行某些后处理，以返回一个修改后的扩展名。然而，如果你向 `self` 发送 `getExtension` 消息，最终将导致递归调用，而非调用超类中的实现。

为解决此问题，Objective-C 引入了特殊变量 `super`。`super` 的目的是重定向消息发送机制，以便从超类（而非当前类）开始搜索合适的方法。当向 `super` 发送消息时，编译器能够检测到消息解析机制需要稍作更改，从而使消息能够到达正确的方法。

请注意，当子类重写在超类中声明的某个方法时，可能出于两个原因：

- **用全新的实现替换原始行为**：在这种情况下，新行为完全在子类中编码，无需引用原始实现。这是最容易处理的情况，因为无需与基类交互即可实现修改后的方法。
- **用附加代码扩展原始行为**：在这种情况下，有必要引用你正试图重写的方法的原始实现。

在第二种情况下，程序员可以使用特殊变量 `super` 来引用超类中某个方法的实现。借助此变量，在使用消息分发机制时可以跳过当前类的层级。因此，编译器将生成代码，从父类（而非当前类）开始搜索所需消息的匹配项。因此，使用 `super` 是重用为基类编写的代码的一种常见方式，同时又能为同一方法提供增强的实现。例如，在重写基类的方法时，非常常见的是使用以下惯用法：

```
- (void) overridenMethodName
{
    [super overridenMethodName];
    // 附加代码写在这里
}
```

也就是说，你首先调用基类中的原始方法，然后添加任何必要的附加代码来自定义该方法的行为。

#### 模板方法

方法重写是引入多态行为的一种方式。然而，仅重写单个方法可能不足以实现你所需的定制化。在某些情况下，类的行为由包含多个步骤的算法决定，其中每个步骤都可以独立定制。当这种情况发生时，最佳策略是使用一个模板方法，它封装了关于类所用确切步骤序列的知识。

例如，考虑一个表示厨师的类。该类有一个名为 `prepareRecipe` 的方法，它执行食物准备的主要步骤。

```
// 文件 Cook.h
@interface Cook : NSObject
- (void)prepareRecipe;
- (void)acquireIngredients;
- (void)mixIngredients;
- (void)cookDish;
@end
```

其实现包含了 `prepareRecipe` 的基本框架，以及其余三个方法的空实现。

```
// 文件 Cook.m
@implementation Cook
-(void) prepareRecipe
{
       [self acquireIngredients];
       [self mixIngredients];
       [self cookDish];
}
- (void)acquireIngredients
{
}
- (void)mixIngredients
{
}
- (void)cookDish
{
}
@end
```

这里有一个方法 `prepareRecipe`，由该类的客户端调用。然而，该方法只是一个外壳（或模板），它调用另外三个完成实际工作的方法。要实现一个真正有用的 `Cook` 实例，你需要首先创建一个派生类，然后以合理的方式实现这三个方法（`acquireIngredients`、`mixIngredients` 和 `cookDish`）。

在 `Cook` 中，你拥有一个提供包含三个操作的通用接口的类，以及一个决定这些操作调用顺序的方法。子类需要为此类模板方法的工作提供具体细节。

**注意**：你可以将上面展示的类归类为抽象类。抽象类是指其接口中的一个或多个方法未提供实现的类。这可能是由于以下原因：某个功能在特定层级不存在实现，或者你想强制子类提供自己的实现。当基类需要某些只能由子类提供的信息时，就会出现这种情况。



## 对象初始化

正确的对象初始化是创建新类时需要关注的核心问题之一，同时也直接受到继承关系的影响。毕竟，类是运行时对象实例的模板，但这些实例首先需要成为目标概念的有效表示。

在 Objective-C 中，初始化类最常用的方式是发送 `init` 消息或某个 `initWith` 消息。`init` 方法最初定义在 `NSObject` 类中，任何需要提供自定义初始化功能的类都必须重写该方法。

但当存在多个初始化方法时，协调它们之间的交互方式就变得至关重要。这对于保持对象状态初始化方式的一致性来说是必要的。

你已经见过一些提供备选初始化方法的类示例。一个典型的例子是 `NSString`，它拥有以下初始化方法（以及其他方法）：

```
- (id)init;
- (id)initWithCharactersNoCopy:(unichar *)characters length:(NSUInteger)length freeWhenDone:(BOOL)freeBuffer;
- (id)initWithCharacters:(const unichar *)characters length:(NSUInteger)length;
- (id)initWithUTF8String:(const char *)nullTerminatedCString;
- (id)initWithString:(NSString *)aString;
- (id)initWithData:(NSData *)data encoding:(NSStringEncoding)encoding;
- (id)initWithBytes:(const void *)bytes length:(NSUInteger)len encoding:(NSStringEncoding)encoding;
- (id)initWithBytesNoCopy:(void *)bytes length:(NSUInteger)len encoding:(NSStringEncoding)encoding freeWhenDone:(BOOL)freeBuffer;
```

尽管一个类可能有相当数量的初始化方法，但始终存在一个特定版本被标记为**指定初始化方法**。这是 `init` 或 `initWith` 方法的一个版本，负责对该类的对象执行完整的初始化，并调用父类的初始化方法。客户端可以调用该类的其他 `init` 方法，但这些方法最终会调用指定初始化方法来完成整个初始化过程。

每当你创建一个新的子类时，必须确定指定初始化方法，并使用它来完成对父类的正确配置。通过这种方式，可以确保在子类开始运行之前，基类处于有效状态。因此，新的子类在设计时应使其明确哪个版本的 `init` 方法是默认初始化方法。

例如，考虑一个用于管理图书馆的应用程序中的 `LibraryHolding` 类。该类有三个初始化方法，如下所示：

```
// 文件 LibraryHolding.h
@interface LibraryHolding : NSObject
{
    NSString *patron;
    NSString *title;
}
- (id)init;
- (id)initWithPatron:(NSString*)patron;
- (id)initWithPatron:(NSString*)patron title:(NSString*)aTitle;
@end
```

这里的指定初始化方法是第三个方法，因为它是唯一需要直接调用父类 `init` 方法的初始化方法。你可以在下面的实现中清晰地看到这一点：

```
// 文件 LibraryHolding.m
@implementation LibraryHolding

- (id)init
{
    return [self initWithPatron:@""];
}

- (id)initWithPatron:(NSString *)aPatron
{
    return [self initWithPatron:aPatron title:@""];
}

- (id)initWithPatron:(NSString *)aPatron title:(NSString *)aTitle
{
    self = [super init];
    if (self)
    {
        self->patron = aPatron;
        self->title = aTitle;
    }
    return self;
}

@end
```

一个有用的规则是：如果某个初始化方法拥有比其他所有方法都多的参数，那么它通常会被选为指定初始化方法，因为它可以用来模拟其他初始化方法。现在，假设你想创建一个名为 `PeriodicalHolding` 的派生类，用于表示杂志和报纸等期刊。以下是其可能的接口：

```
// 文件 PeriodicalHolding.h
@interface PeriodicalHolding : LibraryHolding
{
    int issue_number;
}
- (id)initWithTitle:(NSString*)aTitle number:(int)aNumber;
- (id)initWithPatron:(NSString*)patron title:(NSString*)aTitle number:(int)aNumber;
@end
```

以下是相应的实现：

```
// 文件 PeriodicalHolding.m
@implementation PeriodicalHolding

- (id)initWithTitle:(NSString*)aTitle number:(int)aNumber
{
    return [self initWithPatron:@"" title:aTitle number:aNumber];
}

- (id)initWithPatron:(NSString*)aPatron title:(NSString*)aTitle number:(int)aNumber
{
    self = [super initWithPatron:aPatron title:aTitle];
    if (self)
    {
        self->issue_number = aNumber;
    }
    return self;
}

@end
```

再次注意，指定初始化方法仍然是参数最多的那个。第二个初始化方法只是用所需的参数调用指定初始化方法。



## 替换原则

当系统中的某个类继承自另一个类时，它不仅享有一项特权，还承担着一项义务。主要特权是继承基类提供的非私有接口和实现。因此，派生类可以在任何可以使用基类的上下文中使用。例如，`HourlyEmployee` 实际上是一种 `Employee` 对象。因此，只要需要 `Employee` 对象，它就可以被使用：如果一个对象期望接收一个 `Employee` 并向其发送消息，那么它也应该能够对 `HourlyEmployee` 执行同样的操作。

另一方面，继承要求子类承担一项责任：它们需要能够满足基类设定的期望。例如，如果基类声明它可以计算员工薪水，那么子类也需要能够做到这一点。

```
// 文件 Employee.h

@interface Employee : NSObject

{
       NSString *name;
}

- (void) setName:(NSString*) aName;
- (double) calculateSalary;
- (void) provideJobEvaluation;

@end
```

但是，如果新的子类 `HourlyEmployee` 有另一种计算薪水的方式，会发生什么？简单的解决方案就是覆盖 `calculateSalary` 方法并提供新的实现。

```
// 文件 Employee.m

@implementation Employee

- (void) setName:(NSString*) aName
{
       self->name = aName;
}

- (double) calculateSalary
{
       double salary = 0;
       // 执行薪水计算
       return salary;
}

- (void) provideJobEvaluation
{
}

@end
```

这样做是继承的有效用法。但反过来，假设 `HourlyEmployee` 类无法计算薪水。例如，假设薪水是基于佣金的，而该类要到明年年初才能获得这些佣金。当这种情况发生时，基类定义的契约就被打破了，子类将难以模仿其超类。例如，以下是 `calculateSalary` 实现的两种选择：

```
- (double)calculateSalary
{
   return 0;
}
```

```
- (double)calculateSalary
{
       @throw [NSException exceptionWithName:@"salaryException"
                                      reason:@"no salary available"
                                    userInfo:NULL];
}
```

在第一种情况下，应用程序将接收到一个错误的值，因为按小时计酬的员工显然不会拿到零薪水。在第二种情况下，方法抛出异常后，应用程序无法继续运行。在这两种情况下，结果都不是客户端对 `Employee` 对象所期望的。这意味着 `SalaryEmployee` 不适合作为 `Employee` 的子类。

替换原则指出，当一个类派生自一个基类时，子类的对象应该能够在任何需要的情况下替换基类的对象。正如你所见，`HourlyEmployee` 类不符合这个通用原则。它无法像原始类那样响应对 `calculateSalary` 消息。因此，在这种情况下，最好避免直接使用继承。一种可能的解决方案是将薪水计算的责任拆分到一个单独的类中，这样原始类就可以响应诸如 `canCalculateSalary` 之类的消息。总之，有必要考虑子类是否真的能在任何使用基类的上下文中使用，如果不行，则需要进行必要的设计变更。

## 类簇

类簇是一组通过继承关联的类，它们为某个功能提供了一个完整的实现，该功能的实现可能因所使用的具体类型而异。类簇的主要特点是，层级结构中只有顶部的单个类对客户端可见。簇中的所有其他类都是隐藏的，因此无法直接实例化它们。相反，簇中的具体类必须通过主接口来创建，主接口通常包含 `init` 方法以及类方法，这些方法可以针对每种情况返回正确的对象。

类簇提供了一种强大的方法来控制维护和暴露层级结构中大量相关类的复杂性。使用类簇时，你不需要知道实现所需功能的正确版本的确切类名。簇的基类将与派生类协作，以创建满足你需求的确切对象。另一方面，从簇的主类派生出来的类负责实现与本地存储交互的方法的具体版本。在簇的具体实现中需要被覆盖的方法称为原始方法，因为它们提供了类簇用于实现其他高级功能的原始功能。

通过一个用例可以更容易地理解这个定义。假设一个类可以采用两种或多种功能配置，并使用独立的数据存储来决定这些类将如何运作。此外，该类为每种配置提供了公共接口，因此可以统一访问它们。在这种情况下，可以组织这些类，使得只有基类是可见的，而具体功能由其子类实现。

类簇被认为是 Objective-C 中的一种基本设计模式。Foundation 框架中类簇的一个典型例子是 `NSNumber`。虽然它对客户端来说表现为一个单一的类，但 `NSNumber` 的实际实现恰好包含一个类簇，其中主要的抽象类充当具体类的包装器，负责处理对象的状态。例如，在 `NSNumber` 簇的具体类中，你会找到整数、浮点数和布尔值的实现模型。

使用类簇很简单，因为程序员无需知道类初始化器创建的是哪个类。在 `NSNumber` 的例子中，有许多初始化方法，例如命名为 `initWithInteger:`、`initWithFloat:` 和 `initWithChar:` 的方法。这些方法返回一个能够正确表示其存储数据的对象。但是，返回对象的确切子类对用户是隐藏的。

从创建新类簇的角度来看，必须考虑一些实际问题。首先，抽象基类的公共接口必须是客户端与簇中其他类之间唯一的联系点。因此，簇中基类的接口必须包含所有用于与簇中任何成员交互的方法。在抽象类的方法中，你会找到原始方法，这些方法需要在每个派生类中实现。第二类方法，即派生方法，包含那些在基类中实现并在其定义中使用原始方法的方法。因此，这些方法不需要在具体类中被覆盖。

从簇的抽象根类派生的类需要提供自己的实例变量用于数据存储。簇中的抽象类在设计上没有本地存储，因为每个具体类可能有不同的数据存储方式。



### 类簇的另一个示例

另一个类簇的示例可以在集合类中看到，例如 `NSArray`。当你调用 `init` 时返回的对象实现了 `NSArray` 的接口，但它可能来自簇中的不同子类，包括 `NSMutableArray`；这完全取决于 `NSArray` 构造器如何执行对象初始化任务。如你稍后将看到的，类簇是一种通用策略的示例，用于在库或大型应用程序中向其他客户端隐藏实现细节信息。

## 防止子类化

最后，尽管本章讨论的子类化有许多优点，但其错误使用也可能导致合理的问题。因此，在某些情况下，可能有必要阻止对少数特定类进行继承；换句话说，你可能希望阻止一个类成为另一个类的基类。

> **注意**
> 
> 继承的主要缺点之一就是现在所谓的脆弱基类问题。在创建类层次结构时，类通常会使用父类提供的实现信息。然而，不存在所谓完美的设计，对基类的修改最终可能是必要的。这种修改可能会导致依赖这些内部细节的子类停止工作。一个好的层次结构设计应尽量减小这种可能性。

某些语言通过从继承的角度将特定类标记为不可变来直接支持此特性。例如，在 Java 中，可以使用 `final` 关键字标识一个类，这样在应用此语法注解后，开发人员就不允许继承它。在较新的语言中，C# 也允许这种类之间的区分。

Objective-C 的设计者决定不添加避免继承的机制。这个选择很可能是为了保持语言的简洁性，或许也因为这个特性并不常用。然而，即使没有像 Java 中的 `final` 关键字那样的直接支持，仍然可以采取一些措施来防止一个类被子类化。

你可以使用的通用策略涉及降低类的可见性。这里的原理是，你不能子类化一个存在于应用程序中但客户端在编译时无法看到的类。因此，例如，你可以创建一个类，但从不为其提供头文件。在这种情况下，该类只能通过外部接口（如协议或抽象类）访问，但类的内部细节永远不可访问。

让我们看看如何实现这样一个不可继承的类。首先，考虑类 `ConcreteMatrix`。它有很多用于存储矩阵的数据，以及诸如维度、最大条目和其他实用方法等特殊细节。

```objc
@interface ConcreteMatrix2 : NSObject
{
    double *contents;
    int size;
    // other ivars here
}
- (void) initializeMatrix:(double *)vector size:(int)size;
- (void) performMatrixOperation;
// other methods here
@end
```

尽管这是一个通用类，应该可供整个应用程序使用，但该类的内部耦合非常紧密，以至于很难创建正确的子类。要做到这一点，程序员需要了解类的内部详细信息，例如使用的实例变量以及它们何时应该更新等。因此，虽然你希望尽可能广泛地使该类可用，但你并不打算允许它的子类。

解决方案是避免提供对该类的任何直接访问。然而，你仍然希望功能对客户端可访问。因此，你创建一个类，为想要隐藏的对象提供合适的接口。这个外部对象对其他部分可见，但它除了指向实际实现功能的对象的引用外，不包含其他任何东西。

```objc
// file AbstractMatrix.h
@interface AbstractMatrix : NSObject
- (void) initializeMatrix:(double *)vector size:(int)size;
- (void) performMatrixOperation;
+ (AbstractMatrix*) getMatrix;
@end
```

现在，无需了解类内部的任何信息即可使用此版本。请注意，你移除了所有内部数据存储，并添加了一个返回 `AbstractMatrix` 类型新对象的类方法。作为创建抽象类的副作用，你现在需要一个像 `getMatrix` 这样的方法来返回一个新的矩阵对象。这是必要的，因为 `AbstractMatrix` 本身无法被实例化，因为它缺少某些方法的实现。

另一方面，原始的具体类可以隐藏在实现文件中。然而，它仍然需要继承自抽象类，以便提供相同的接口。

```objc
@interface ConcreteMatrix : AbstractMatrix
{
    double *contents;
    int size;
    // other ivars
}
- (void) initializeMatrix:(double *)vector size:(int)size;
- (void) performMatrixOperation;
// other methods here
@end
```

方法 `getMatrix` 将负责创建一个 `ConcreteMatrix` 并将其返回给用户。

```objc
@implementation AbstractMatrix
+ (AbstractMatrix*) getMatrix
{
    return [[ConcreteMatrix alloc] init];
}
// other implementation methods here
@end
```

现在，一个类可以使用诸如 `[AbstractMatrix getMatrix]` 这样的代码来创建 `ConcreteMatrix`。`AbstractMatrix` 接口上列出的所有功能对他们都可用，尽管他们无法访问 `ConcreteMatrix` 的内部。

> **注意**
> 
> 此示例使用的机制与类簇采用的机制类似，尽管这里的目标只是保护提供具体实现的单个类，而不是提供一组符合相同抽象接口的类。



## 多重继承

在前几节中，你接触到的概念被称为单继承，这是一种子类化方式，即新类只能有一个父类。Objective-C 以及 Java 和 Smalltalk 等其他面向对象语言仅支持单继承。而 C++ 等其他语言则可以从多个父类派生出新类。这种子类化方式被称为多重继承。

由于在单继承下创建新类时只允许有一个超类，因此生成的层级结构呈树形，初始基类位于树的根部。然而，在使用多重继承的语言中，层级结构更像一个由类构成的连接网络，这些类可以从两个或更多来源获取数据和方法实现。

在 Objective-C 中，无法创建这样的继承连接网络。原因之一是避免可能影响动态消息派发的开销。试想一下，查找方法实现时，不再需要只在单个类树中查找，而是要在多个不相关的祖先类中查找。

不过，有一些方法可以模拟多重继承的某些便利性。最常用的方法是使用形式化协议来定义类应提供的额外功能。例如，假设你想创建一个具有两种职责的类：既是一辆交通工具，又是一个居住场所。以下是这些职责的列表：

```
// 文件 Vehicle.h
@interface Vehicle
- (void) drive;
@end

// 文件 LivingPlace.h
@interface LivingPlace
- (void) setAddress:(NSString *)address;
@end
```

现在，假设你想定义一个代表船的类，它既是一个`Vehicle`，也是一个`LivingPlace`。你目前面临的问题是 Objective-C 类只能有一个超类。你需要做的第一件事是决定将`Vehicle`还是`LivingPlace`作为`Boat`的基类。另一个问题是如何将第二个类的功能添加到你的新子类中。我将在下一节讨论这个问题的潜在解决方案。

## 模拟多重继承

一种可能的解决方案是将`LivingPlace`设为一个协议，而不是一个类。这样一来，`LivingPlace`可以拥有一个固定的接口，该接口可由任意数量的类实现，即使它们不处于单一层级结构中。以下是该协议的定义：

```
// 文件 LivingPlace.h
@protocol LivingPlace
- (void) setAddress:(NSString *)address;
@end
```

然后，`Boat`可以定义为派生自`Vehicle`的类，同时实现`LivingPlace`协议。

```
@interface Boat : Vehicle <LivingPlace>
{
    NSString *_address;
}
@end

@implementation Boat
- (void) setAddress:(NSString *)address
{
    self->_address = address;
}
@end
```

这个解决方案将解决使用单继承时的问题，但仍然无法提供多重继承的全部功能。毕竟，`LivingPlace`协议只是一个定义良好的接口，没有任何关联的实现。当一个类实现该协议时，它必须为目标功能提供代码。

如果你不仅想提供接口，还想提供默认实现，一个可行的解决方案是为该协议创建一个具体的实现类，供其他类使用。客户端类可以通过创建该默认类的新实例并将其保存为实例变量供后续使用，从而利用这种默认实现。

```
// DefaultLivingPlace.h
@interface DefaultLivingPlace : NSObject <LivingPlace>
{
    NSString *_address;
}
@end

// DefaultLivingPlace.m
@implementation DefaultLivingPlace
- (void) setAddress:(NSString *)address
{
    self->_address = address;
}
@end
```

现在，每当某个类需要实现`LivingPlace`协议时，它都可以依赖默认实现来提供标准方法。

```
@interface Boat : Vehicle <LivingPlace>
{
    NSString *_address;
    DefaultLivingPlace *livingPlaceImp;
}
@end
```

接口的方法仍然需要在类中定义。然而，现在的实现可以简化为一个消息调用，将该消息转发给默认实现。

```
@implementation Boat
- (void) setAddress:(NSString *)address
{
    [self->livingPlaceImp setAddress:address];
}
@end
```

## 继承与委托

在这个例子中，你使用了实现成员变量，而非继承，来提供新的编码特性。这展示了继承与组合之间更普遍的对立关系。在第一种情况下，对象之所以能获得某些功能，是因为它从基类继承了这些功能。在第二种情况下，功能由外部对象提供，并通过实例变量来访问。虽然继承是提供功能的一种更丰富的方式，但事实证明，通过委托进行组合在某些方面更灵活，原因如下：

- 两个类之间无需深层关联：使用继承时，子类可以访问超类的许多细节，例如受保护的变量和方法，这些对外部客户类是不可访问的。当基类发生变化时，这会产生一些问题。使用委托时，你无需担心实现类与其客户类之间的关联，因为你可以依赖语言提供的机制来避免不必要的访问。
- 一个类可以自由地根据需要包含任意数量的对象：因此，一个类可以接收消息，并根据需要将其转发给任何合适的对象。然而，使用继承时，只能有一个超类。

这意味着你不需要直接继承也能享受代码复用的好处。另一方面，委托提供了一些额外的好处，可能会提高代码的灵活性，甚至更容易处理那些通常需要通过多重继承解决的问题。

## 总结

在本章中，你探讨了类继承，这是面向对象编程中最重要的概念之一。Objective-C 为继承提供了出色的支持，学会运用这些可能性将帮助你设计更好的类，同时更有效地使用 Foundation 及其他框架。你已经了解了继承如何通过将实用方法组装到基类中，并在后续通过子类化机制复用它们来帮助避免代码重复。

继承还催生了一些 Objective-C 中常用的模式。例如，你已经了解了类簇如何允许程序员将具体类隐藏在一个由抽象类定义的统一外观之下。Foundation 框架中的类（包括`NSNumber`和`NSArray`）经常使用这种技术。

在某些情况下，你可能不希望从你的基类中创建新类。这样做可以避免你的方法与第三方代码之间的不必要耦合。通过应用本章中回顾的一些策略，例如使用符合明确定义协议的委托，可以阻止子类的创建。

在下一章中，你将探索块语法，这是 Objective-C 的一个独特特性，允许你的代码直接引用任意代码块。使用块，你可以简化一些原本需要大量样板代码的操作，同时使用一种优雅的方法来传递代码。



