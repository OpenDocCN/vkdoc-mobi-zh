# 4. 类别与协议

## 摘要

在 Objective-C 中，类别可用于向类添加方法，即使你无法完全访问该类的原始源文件。

例如，使用类别可以向系统中的通用类（如 `NSString`、`NSNumber` 甚至 `NSObject`）添加新方法。只要该类别已加载到应用程序中，这样做将使添加的方法对原始类的任何用户都可用。

如果你需要在新库的上下文中向新类添加复杂功能，或者只是尝试添加一个便捷方法，类别可能会派上用场，这也是 Objective-C 编程如此高效的原因之一。

与其他遵循强类型机制的面相对象语言相比，可适应接口的设计是 Objective-C 的另一个优势领域。通过声明协议，可以创建独立于特定类的接口，因此，任何希望遵守协议声明中预先设定的一组特定消息的客户端都可以使用这些接口。

在本章中，我将探讨这两种机制——类别和协议，以及如何使用它们来创建更好的应用程序。首先，你将研究如何创建受益于类别的新类。在本章的第二部分，你将了解协议如何在 Apple 的库中被使用，以及客户端代码如何从中受益。

## 类别

由于其面相对象的哲学，Objective-C 促进了接口与实现之间的清晰分离。这种关注点分离的一个方面是通过添加方法来扩展类的能力。这可以通过使用类别来实现，这是一种灵活的机制，可供类设计者以及特定类接口的客户端使用。例如，使用类别可以在无需重新编译原始类文件的情况下添加新方法。这在处理第三方提供的现有库时非常有帮助。同样的原则可用于操作 Apple 提供的框架中的类，例如 Cocoa 和 Cocoa Touch。

例如，假设你需要添加一个可应用于任何字符串的方法。一种简单的方法是创建 `NSString` 的子类，并将该方法附加到该子类上。然后你可以通过实例化这个新类来创建字符串，这些字符串将能够访问你声明的新方法。由于你可以依赖 Objective-C 运行时处理多态性，每一个接受字符串的方法也将接受你的新类。

然而，这种解决方案并不完全令人满意，因为在许多情况下，你无法控制 `NSString` 实例的创建方式：这类字符串经常由其他方法返回。另一个问题是，使用自定义类会失去一些好处，例如使用字符串字面量的可能性。

另一种处理方式是创建一个 `NSString` 的新类别。在这种情况下，你可以将此方法作为 `NSString` 类接口的一个组成部分来添加。通过导入新类别，项目中任何使用 `NSString` 的其他类都将能够访问添加的方法，就像它是原始字符串类的一部分一样。

## 注意

通过类别扩展现有类是 Cocoa 框架中常用的技术。通过这种方式，Cocoa 允许 `NSString` 等类整合那些仅在特定库上下文中有意义的特性。一个众所周知的例子是，文本绘制框架为 `NSString` 添加了一个类别，提供了直接在屏幕上绘制字符串的能力。



### 创建新分类

要声明一个新的分类，你可以使用与普通类实现部分非常相似的语法。但在分类中，你需要将分类名称添加到类名后面的括号内，以便编译器识别后续声明是在定义原始类的扩展，而非类本身。

通过分类添加方法后，该方法在所有方面都表现得如同直接在原始类中声明一样。例如，你可以使用与调用目标类原始方法相同的语法来调用这些方法。从被分类扩展的原始类派生出的子类，也会继承以这种方式定义的方法。

以下是一个为 `NSString` 类实现扩展的分类示例。该分类仅包含一个实用方法：`reverse`，它返回一个与原字符串顺序相反的新字符串。

```
@interface NSString (utility)
- (NSString *) reverse;
@end
```

注意类名后跟随的分类名称，这将其声明与标准接口区分开来。其实现也很直接。

```
@implementation NSString (utility)
- (NSString *) reverse
{
        NSMutableString *resultString = [[[NSMutableString alloc] init] autorelease];
        int n = (int)[self length];
        for (int i=n-1; i>=0; --i)
        {
                [resultString appendFormat:@"%c", [self characterAtIndex:i]];
        }
        return resultString;
}
@end
```

这段代码使用了 `NSMutableString` 类，该类允许通过 `appendFormat:` 等方法向字符串添加数据。相比之下，`NSString` 是不可变对象，这与上一章讨论的许多集合类的情况一致。

分类不仅用于扩展标准框架中的现有类。通常，设想你刚创建了一个类，其中包含一些实现特定职责的方法。然而，在使用该类一段时间后，你发现可以为该类添加第二种类型的职责，使其对使用者更实用。但问题在于，你希望尽量保持原始接口的简洁性。

在这种情况下，Objective-C 提供了分类的概念，作为一种在不影响原始代码和接口的前提下，将额外功能封装到类中的可能方式。同样的技术可以应用于你创建的类，也可以应用于现有类。

### 使用分类头文件

分类可用于扩展系统中任何类的行为。然而，要享受新分类的优势，客户端代码需要能够访问其声明。

这可以通过标准的头文件包含机制实现。按照常见做法，原始类的接口存储在项目中的头文件里。例如，`Employee` 类的头文件应命名为 `Employee.h`。现在，假设你想通过声明一个名为 `ExecutiveOptions` 的新分类来扩展 `Employee` 类。在这种情况下，你需要创建一个相应的头文件 `Employee+ExecutiveOptions.h`，其中包含新分类的定义。头文件的名称是类名与分类名的组合。

注意

这种头文件命名约定的逻辑在于避免与其他类冲突。例如，系统中可能存在另一个名为 `ExecutiveOption` 的类。这种情况下，两个头文件同名，会使用户难以同时使用该类及其分类。类似地，仅使用类名而不加后缀可能导致命名冲突。

分类头文件的命名约定也有助于系统维护。遵循此约定，任何分类文件文件名中都带有加号，这便于在项目中查找所有分类的列表。

然而，必须注意，并非所有分类都存放在单独的头文件中。出于与有时将相关类存放在同一头文件中相同的原因，将分类添加到包含其他类声明的文件中可能更方便。Foundation 和 Cocoa 框架中经常出现这种情况。其目的在于尽量减少程序员为使用一组相关类和分类而需要导入的头文件数量。



### 类别与私有方法

在 Objective-C 类中，可以声明具有不同可见性级别的实例变量。对于公共变量，任何拥有该对象指针的类或函数都可以访问其内容。而私有实例变量则仅对类本身可用。最后，Objective-C 还提供了受保护的实例变量，它们可以被定义它们的类以及任何继承自该类的类读取或写入。这些可访问性级别提供了一定的保证，即编译器会强制实施特定变量的可见性。这反过来又促进了更好的代码组织，因为一个实例变量将只被代码库的一小部分可见或修改。

如果方法和实例变量也拥有相同的可访问性级别，那将会很棒。然而，Objective-C 的设计不允许以这种方式限制消息。消息仅在运行时解析，这意味着编译器无法像对实例变量那样，在编译期间强制执行此类访问规则。

虽然没有办法对方法强制执行访问规则，但你可以使用类别作为一种简单的方法来减少那些你希望私有的方法的可见性。这是可行的，因为可以创建一个类别作为有效的方法，来封装一个或多个仅在一个文件的局部上下文中已知的方法。

为了实现这一点，你需要在实现文件的开头定义一个新的类别。你可以为该类别使用任何名称。传统上，人们为此目的使用名称 `Private`，但 Objective-C 也允许类别名称为空，从而形成匿名类别。在这种情况下，你无需担心为类别命名的问题。但使用匿名类别最大的优势在于，无需在文件中创建独立的实现部分，从而减少了需要维护的代码量。

匿名类别的内容将由你想设为私有的所有方法组成。这些方法只能由属于原始类且包含在同一实现文件中的代码访问。下面是一个示例，你使用匿名类别实现了一个 `Library` 类。该类别中唯一的方法是 `getPrivateBookTitles`。

```
// 文件: Library.h

@interface Library : NSObject

{
        // 实例变量在此
}

// 标准方法在此

@end

// 文件: Library.m

@interface Library ()

- (NSArray *) getPrivateBookTitles;

@end

@implementation Library

- (NSArray *) getPrivateBookTitles

{
        NSArray *books = @[];
        // 查找私有书籍的代码
        return books;
}

@end
```

即使使用匿名类别，也必须牢记，此机制并未为以这种方式声明的方法提供运行时保护。毕竟，类的用户仍然可以发送一条消息，该消息将被解析为此类类别中的某个方法。请记住，消息分发机制的运作方式并不关心消息的目标，只要消息能够成功分发即可。如果你真的不希望某些代码被外部类执行，实现这一目标的最佳方法是使用静态 C 函数，因为 C 函数不受 Objective-C 的消息分发规则约束。

### 向类别添加属性

正如你所见，类别可以通过使用简单的语法，用新方法扩展现有类，让我们的生活更加轻松。然而，当谈到增强类的功能集时，通常需要一些额外的内存来存储与新功能关联的数据。例如，一个 `Employee` 类可能希望存储每个人的健康信息。方法 `addHealthRecords:` 需要使用一个或多个实例变量来根据需要跟踪这些信息。

但是，无法直接将新的实例变量添加到类别中。毕竟，类别的目的是提供新方法，而不是从根本上改变对象的内容。然而，在添加匿名类别时，Objective-C 为此规则提供了一个例外。

请记住，匿名类别基本上是一种添加未在类公共接口中命名的方法的方式。因此，编译器将匿名类别视为原始类本身的一部分，并使用其中包含的属性定义作为合成实例变量的基础。这种属性及其合成变量的组合，为我们提供了一种间接添加数据的方式，这些数据可以在主实现部分声明的方法中使用。

下面是一个展示其工作原理的示例。再次考虑一个用于模拟实体图书馆的类，其中包含馆内书籍的信息。

```
// 文件: Library.h

@interface Library : NSObject

{
// 实例变量在此
}

// 方法在此

@end

// 文件: Library.m

@interface Library ()

@property(retain) NSString * privateCollectionName;
@property(retain) NSNumber * numericProperty;
- (void) setValues;

@end

@implementation Library

@synthesize numericProperty = _numberOfBooks; // 赋予一个不同的名称

- (void) setValues

{
self->_privateCollectionName = @"MyLibrary"; // 由编译器生成
self.numberOfBooks = @1000; // 通过 @synthesize 声明
NSLog(@"将馆藏集名称设置为 %@", self.privateCollectionName);
}

@end
```

这段代码中有一个头文件，包含了 `Library` 类的主接口（为简洁起见，未显示该类中的方法和实例变量）。`Library.m` 实现文件包含一个匿名类别，其中声明了两个属性：`privateCollectionName` 和 `numericProperty`。最后，`Library` 类中的方法在 `@implementation` 部分内部定义。

由于你使用的是匿名类别，因此同样需要在类的 `@implementation` 部分添加方法 `setValues` 的定义。类似地，编译器使用的 `@synthesize` 声明会生成一个名称与属性指定的名称不同的实例变量。

第一个属性（`privateCollectionName`）使用编译器合成的标准变量名。因此，无需 `@synthesize` 声明。实例变量由编译器自动创建并命名为 `_privateCollectionName`。请注意，你在方法 `setValues` 中直接引用了这个实例变量。

然而，第二个属性的内部名称与 `@property` 声明的名称不同。例如，这可能会发生，因为你想要一个更易于输入的内部名称，而其属性名则保持更具描述性。在本例中，该属性称为 `numericProperty`，而合成的实例变量称为 `_numberOfBooks`。

`setValues` 方法展示了如何使用这两种类型的实例变量。每个属性既可以作为实例变量访问，也可以直接作为属性访问。实例变量的语法通过使用 `self` 作为对象的有效指针来实现。因此，它使用 `->` 符号。另一方面，属性语法使用点号来指示编译器应在何处生成特殊代码以获取或设置属性内容。



## 协议

协议允许类通过一个众所周知的接口进行交互。许多类需要仅通过调用几个实现所需功能的方法来与其他服务（或一组服务）进行交互。解决此问题的一种方法是定义一个实现所需功能的基类，然后让大量子类继承相同的接口。

然而，这种解决方案在大型系统中，随着可用类和服务的数量增加，扩展性会变得很差。由于你需要定制一个类与系统其他部分交互的方式，实现服务的需求可能会导致应用程序中的类数量膨胀。在像 C++ 这类可扩展选项较少的语言中，这种类型的泛滥在 Objective-C 中是不必要的。

Objective-C 提供的一种解决方案是声明一个协议，并要求某些类实现该协议以与特定服务进行交互。通过这种方式，客户端可以通过实现新的协议来扩展现有类，而无需为此创建一个全新的类。

声明协议有两种方式。第一种方法定义了一个非正式协议，它仅仅是对象之间的一种通信机制，通过使用类别来文档化。

实现协议的第二种方式是使用特定的语法。这种技术不仅支持定义新协议，还支持一个类实现一个或多个协议。

### 非正式协议

非正式协议只是一种定义一组方法的方式，这些方法应存在于类中以便提供服务。之所以称之为非正式，是因为它并非由特定的语法定义，而仅仅是期望客户端类会定义适当的方法。例如，假设你正在编写一个与放贷方交互的应用程序。有几种放贷方的类，例如银行、信用合作社和私人投资者。然而，任何此类放贷方都会向其客户提供一组方法，例如：

`- (BOOL) canLend:(float)amount;`

`- (BOOL) lendRequest:(float)value toLoaner:(NSString*)loanerId;`

第一个方法用于确定放贷方是否能够贷出所需金额。第二个方法将尝试为请求的金额和提供的借款人标识满足借贷请求。当借贷过程成功完成时，`lendRequest:toLoaner:` 的返回值为 `YES`。

任何想要充当放贷方的类都需要提供这两个方法。然而，你的接口并未精确定义它们将如何提供这些方法。实际上，有几种选项可以检测和使用上述方法的正确实现。你要了解的第一种技术基于在 `NSObject` 中定义的类别。

如你所知，`NSObject` 是 Objective-C 中大多数对象的基类。通过定义 `NSObject` 的一个类别，你可以为系统中的所有对象提供特定的行为。因此，你可能只想为上述方法添加一个空的实现。以下是一个类别的示例：

```
@interface NSObject (MoneyLender)

- (BOOL) canLend:(float)amount;

- (BOOL) lendRequest:(float)value toLoaner:(NSString*)loanerId;

@end
```

这是新类别的接口，声明了你希望添加到系统所有对象中的两个新方法。实现将是空的，这样你就可以在任何对象上调用这些方法：

```
@implementation NSObject (MoneyLender)

- (BOOL) canLend:(float)amount

{

return NO;

}

- (BOOL) lendRequest:(float)value toLoaner:(NSString*)loanerId

{

return NO;

}

@end
```

这些方法对请求简单地返回 `NO`。这意味着调用这些方法是安全的，知道它们只有在存在真实实现（而非你刚刚创建的空实现）时才会返回 `YES`。

现在缺少的是调用这些方法的方式。传统做法包括接收一个指向目标对象的指针。这会在调用接口的类中体现出来。假设这个类叫做 `InvestmentAdvisor`，其中有一个方法 `findLoan`。该类还有一个名为 `loanSource` 的属性。

```
@interface InvestmentAdvisor : NSObject

{

// 其他 ivars 在此

}

@property (nonatomic, retain) NSObject *loanSource;

- (void) findLoan:(float)amount;

@end
```

此类的实现将使用存储在 `loanSource` 属性中的值，并尝试为特定客户找到贷款。

```
@implementation InvestmentAdvisor

- (void) findLoan:(float)amount

{

if ([self.loanSource canLend:amount])

{

BOOL result = [self.loanSource lendRequest:amount

toLoaner:@"InvestmentClient"];

if (result)

{

NSLog(@"贷款申请已被接受");

}

}

}

@end
```

你首先使用存储在 `self.loanSource` 中的值来判断给定的来源能否贷出所需的金额。如果可能，则向放贷方发送 `lendRequest:` 消息以完成贷款流程。如果一切顺利，你就在系统日志中打印一条消息。

**注意**

在上面的代码中，你使用了 `loanSource` 属性，但不知道其值是否已设置。尽管如此，代码仍然有效，因为 Objective-C 处理向 `nil` 指针发送消息的方式。如果消息的目标是 `nil`，那么编译器生成的代码会自动为返回 `BOOL` 的消息返回 `NO`。类似地，返回数字的消息默认返回零。这些针对空指针的特殊处理规则的好处是，你可以避免在每次使用变量或属性时检查 `nil`。这也减少了在 C 和 C++ 代码中非常常见的空指针异常数量。

现在假设你想创建一个实现 `MoneyLender` 接口的类。要实现这一点，你只需要定义你自己的类，并包含 `MoneyLender` 类别中的两个方法。示例如下：

```
// MyLoanSource.h

@interface MyLoanSource : NSObject

- (BOOL) canLend:(float)amount;

- (BOOL) lendRequest:(float)value toLoaner:(NSString*)loanerId;

// 其他方法在此

@end
```

我只展示了属于协议的方法，但一个真实的实现会包含其他几个方法。以下是相应的实现：

```
// MyLoanSource.m

#import "MyLoanSource.h"

@implementation MyLoanSource

#define MAX_LOAN 1000 * 1000

- (BOOL) canLend:(float)amount

{

return amount < MAX_LOAN;

}

- (BOOL) lendRequest:(float)value toLoaner:(NSString*)loanerId

{

if ([@"" isEqualToString:loanerId])

{

return NO;

}

if (value > MAX_LOAN)

{

return NO;

}

return YES;

}

@end
```

你仍然需要编写代码，将上面定义的对象作为输入提供给 `LoanAdviser` 类。

```
- (void) useLoanSource {

MyLoanSource *loanSource = [[MyLoanSource alloc] init];

InvestmentAdvisor *advisor = [[InvestmentAdvisor alloc] init];

advisor.loanSource = loanSource;

[advisor findLoan:10000];

}
```

这段代码首先创建一个 `MyLoanSource` 类型的对象，它实现了由 `LoanSource` 定义的协议。然后，下一行创建一个顾问对象，负责使用贷款来源。`loanSource` 属性被设置为这个新创建的对象。最后，你发送 `findLoan:` 消息，贷款金额为 10,000 美元。作为此消息的结果，将使用贷款来源，并且请求的贷款被满足。



#### 检查协议合规性

虽然上述技术作为实现协议的一种方法效果良好，但它有一个小缺点。通过在 `NSObject` 类上创建新类别，你在某种程度上降低了通用消息派发的效率，影响了其他所有人。原因是，这种非正式协议要求所有派生自 `NSObject` 的类都包含指定的两个方法。当协议数量增加时，这会成为一个问题。不难想象，`NSObject` 的派发表中的大部分方法都可能由空协议方法组成。

为了避免这种情况，你可以在运行时检查所需方法是否存在，而不是让这些方法成为系统中每个对象的一部分。这需要在发送消息的代码部分做更多工作，但肯定能让使用该类的其他开发者感到更轻松。下面是 `InvestmentAdvisor` 类的一个修改版本，它检查贷款源是否支持所需的方法：

```
- (void) findLoan:(float)amount
{
        SEL canLendSel = @selector(canLend:);
        SEL lendReqSel = @selector(lendRequest:toLoaner:);
        if([self.loanSource respondsToSelector:canLendSel] &&
           [self.loanSource respondsToSelector:lendReqSel])
        {
                if ([self.loanSource canLend:amount])
                {
                        BOOL result = [self.loanSource lendRequest:amount
                                                        toLoaner:@"InvestmentClient"];
                        if (result)
                        {
                                NSLog(@"The loan application was accepted");
                        }
                }
        }
}
```

`findLoan:` 的新版本与旧版本的不同之处在于方法开头的测试。首先，为你要调用的方法创建两个选择器。

**注意**

选择器是方法内部表示的名称。通过选择器，可以操作和获取关于方法的信息。例如，可以回答“某个类是否实现了这个方法？”这样的问题。选择器变量的类型始终是 `SEL`。

一旦检索到选择器，你就可以使用 `respondsToSelector:` 消息来确定作为 `loanSource` 传入的对象是否能够响应你想要发出的请求。如果结果为真，你就可以执行 `findLoan:` 原始版本中包含的相同代码。

使用这个 `findLoan:` 的新定义，你现在可以从定义中移除 `MoneyLender` 这个类别。由于只有当这些方法被定义为给定对象的一部分时，你才会调用该类别中的任何方法，因此无需向每个 `NSObject` 类型的对象添加这些方法的空实现。

在你完全移除 `MoneyLender` 类别后，会注意到 `findLoan:` 新版本的最后一个问题：编译器会显示一个警告，提示无法确定 `loanSource` 是否能响应发送的消息。这是因为在编译时无法确定这些方法能否被成功派发。不过，你不必担心这个问题，因为 `if` 语句保证了在运行时这将是成立的。然而，通过使用运行时函数直接调用选择器，可以避免这些警告。但目前你暂时不需要为此担忧。

### 正式协议

调用非正式协议中定义的方法，如同你之前所做的那样，为定义接口提供了一种清晰的解决方案。然而，通过使用 `NSObject` 类别或检查对象是否能响应特定消息，你只是利用了 Objective-C 运行时的灵活性。事实上，这种机制并未明确表达代码的意图，即定义对象之间一个完善的协议。

为了提高可读性并简化编码，Apple 引入了协议的正式语法，使用关键字 `@protocol`。采用这种语法，前面的示例可以用以下协议声明来编写：

```
@protocol MoneyLender
- (BOOL) canLend:(float)amount;
- (BOOL) lendRequest:(float)value toLoaner:(NSString*)loanerId;
@end
```

乍一看，这里基于类别的声明与正式协议声明之间没有太大区别，除了明显使用了 `@protocol` 关键字之外。然而，一旦以这种方式声明了 `MoneyLender`，那些希望实现协议中所包含方法的客户端类就可以使用它了。

因此，这种声明的一个主要区别在于，你希望它能够被客户端类所知，因此需要将协议声明放在一个头文件中。相比之下，定义为 `NSObject` 类别的非正式协议可以保持为使用它的类的私有内容。

另一个区别在于类如何实现协议。对于非正式协议，你唯一需要做的就是为所需方法提供一个有效的实现。对于正式协议，你需要明确指示该类实现了该协议。这是通过使用尖括号将协议列表括起来，并紧跟在基类名称之后来实现的。示例如下：

```
@interface AccreditedLoanSource : NSObject <MoneyLender>
{
// ivars go here
}
// other methods here
@end
```

`AccreditedLoanSource` 类的接口定义与其他类类似，不同之处在于它实现了 `MoneyLender` 协议。当协议被标识为接口的一部分时，Objective-C 编译器会检查协议中的所需方法是否在实现中。以下是实现部分的一个示例，仅包含两个实现存根：

```
// file AccreditedLoanSource.m
@implementation AccreditedLoanSource
- (BOOL) canLend:(float)amount
{
return YES;
}
- (BOOL) lendRequest:(float)value toLoaner:(NSString*)loanerId
{
return YES;
}
@end
```

**注意**

从代码打包的角度来看，正式协议与非正式协议还有另一个区别。后者无需通过头文件提供公共接口。但对于正式协议，这不仅是可取的，而且是必要的。在定义新类的接口时，编译器需要看到协议的声明。

使用正式协议的下一步是定义变量和属性，以确保协议得到实现。这是通过声明一个变量并在变量类型后使用尖括号来完成的。如果你只需要一个实现协议中方法的对象，那么通常将对象类型定义为 `NSObject <ProtocolName>`。

使用协议的 `InvestmentAdvisor` 的修改版本可以写成：

```
@interface InvestmentAdvisor : NSObject
{
// other ivars here
}
@property (nonatomic, retain) NSObject <MoneyLender> *loanSource;
- (BOOL) findLoan:(float)amount;
@end

@implementation InvestmentAdvisor
- (BOOL) findLoan:(float)amount
{
BOOL canLend = [self.loanSource canLend:amount];
if (canLend)
{
BOOL result = [self.loanSource lendRequest:amount
toLoaner:@"InvestmentClient"];
if (result)
{
NSLog(@"The loan application was accepted");
return YES;
}
}
return NO;
}
```



请注意，在此版本的 `findLoan:` 中，你无需担心 `canLend:` 和 `lendRequest:toLoaner:` 方法是否已定义。一旦编译器知道属性 `loanSource` 指向一个实现了该协议的对象，这两个方法就可以在编译时进行验证。最后一步则是创建正确的对象，并利用它来初始化 `loanSource` 属性。

```
- (void) useLoanSource
{
    AccreditedLoanSource *loanSource = [[AccreditedLoanSource alloc] init];
    InvestmentAdvisor *advisor = [[InvestmentAdvisor alloc] init];
    advisor.loanSource = loanSource;
    BOOL res = [advisor findLoan:10000];
    NSLog(@"result is %d", res);
    [advisor release];
    [loanSource release];
}
```

请注意，这里唯一真正的区别在于创建 `loanSource` 对象的方式，该对象现在提供了对 `MoneyLender` 协议的完整实现。

**注意**

一个实例变量或属性，如果它实现了当前类所需的某些功能，并由外部使用者提供，则称为委托。例如，`loanSource` 属性就是 `InvestmentAdvisor` 类的委托。要求委托实现特定协议是 Objective-C 库中常用的一种惯用方式。例如，Cocoa 框架要求用户定义委托，以提供与 GUI 对象相关的大部分功能。

## 可选方法

在正式协议中，可以定义方法是必需的还是可选的。编译器会检查必需方法，如果该方法不存在，则会发出警告。可选方法可以在实现协议的类中省略。

可以为协议成员声明两种不同的必需级别。

- **必需方法**：这些方法需要在每个实现该协议的对象中都存在。你可以将这些方法视为提供定义该协议的基本功能。未经限定包含在协议中的方法被视为必需方法。此外，任何跟在 `@required` 关键字之后的方法都被认为是必需的。
- **可选方法**：这些方法可能提供一些对于协议定义并非必需的功能。实现协议的类可以自由选择包含或排除这些方法。可选方法通过 `@optional` 关键字来定义。所有跟在 `@optional` 关键字之后的方法都被视为可选方法。

当使用可选方法时，你需要确定它们在目标对象中是否已被定义。实现此目的的方法与你使用非正式协议时的做法类似：只需向目标对象发送一条 `respondsToSelector:` 消息，以检查该方法是否可用。例如，假设你想向 `MoneyLender` 协议添加一个可选方法。

```
@protocol MoneyLender
@optional
- (BOOL) canExtendLoan;
@required
- (BOOL) canLend:(float)amount;
- (BOOL) lendRequest:(float)value toLoaner:(NSString*)loanerId;
@end
```

现在，你可以编写使用 `canExtendLoan:` 方法的代码，但前提是需先测试其是否存在。

```
- (BOOL) checkLoan
{
    if ([self.loanSource respondsToSelector:@selector(canExtendLoan)])
    {
        if ([self.loanSource canExtendLoan])
        {
            NSLog(@"The loan can be extended");
            return YES;
        }
    }
    return NO;
}
```

## 总结

在本章中，我介绍了关于设计高效 Objective-C 类时两个最重要的主题。使用分类和协议，可以扩展你自己创建的类的能力，以及系统自带或第三方供应商提供的库中已有的其他类的能力。

分类支持向现有类的接口添加方法。只要你提供了扩展方法的代码——以及包含分类定义的头文件——其他类就可以使用分类方法，就好像它们是原始类的一部分一样。这为定制通用类（例如 `NSArray` 或 `NSString`）提供了一种很好的方式，使其包含在你的应用程序上下文中很有用的代码。

协议为类提供了一种定义有限接口的方式，该接口将仅由特定服务使用。协议可以通过仅列出一个或多个需要成为接口一部分的方法来以非正式方式定义。要实现这样的非正式协议，类可以通过一个分类（通常附加到 `NSObject` 上）提供一组空的实现。第二种技术是使用 Objective-C 提供的运行时 API，检查目标对象上是否存在必需的方法。

正式协议使用类似于类声明的语法进行定义。然而，其主要目标是定义一个简单的接口，该接口可以由其他类独立实现。Objective-C 库在很大程度上依赖协议来提供服务，而无需为每个新功能添加新类。例如，Cocoa 使用协议作为定义 UI 组件委托的主要工具。

分类和协议展示了 Objective-C 的一些动态特性，但它们只是触及了可能性的表面。在下一章中，你将更深入地研究动态绑定的概念，并了解它如何帮助你实现目标。

