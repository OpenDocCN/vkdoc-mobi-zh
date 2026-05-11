# 2. 类

**摘要**

在面向对象编程语言（如 Objective-C）中，类是应用程序的构建块。它们是用于定义对象的语法结构，而对象则由方法和变量组成。对于刚接触该语言的程序员来说，理解面向对象编程特性是 Objective-C 中所有技术的关键基础，这一点至关重要。

如果没有正确理解面向对象设计概念，就很难熟练使用 Mac OS X 或 iOS 上的库，因为这些库大量使用了 OOP。因此，在本章开头，我将对 OOP 进行简明概述。我的首要目标之一是解释 OOP 与其他流行编程范式有何不同。虽然结构化编程试图通过简单函数来分解程序，但 OOP 将函数与数据的结合视为编程抽象的真正定义性概念。因此，OOP 语言避免了将数据与操作这些数据的方法分离开来。使用对象进行编程的另一个巨大优势是：可以向其他对象发送消息，并让这些对象自行决定响应该消息的最佳方式。

在初步介绍 OOP 之后，本章将详细描述 Objective-C 中类的概念。特别关注语法方面——尤其是如何从现有类创建新对象。我将解释如何将数据关联到这些类，然后继续为对象添加方法。正如你将在示例中看到的，Objective-C 中的方法也可以关联到类本身。

Objective-C 中类的主要特性之一是它们如何被整齐地分解为两个独立文件：一个包含接口，另一个包含实现细节。这种清晰的分离使得只共享类的关键方面成为可能，同时隐藏客户端不需要的任何实现信息。在本章中，你将看到用于创建类的完整语法细节，这些类为应用程序的其他部分提供了这种简单的接口。

最后，我提供了如何定义实例属性的示例，这是一种简单的机制，在将数据暴露给其他对象的同时，保留对数据如何解释和保存的控制。这些是 UI 对象的主要构建块，学会如何定义它们将使你能够创建灵活的类，这些类可以立即响应应用程序其他部分的变化。例如，属性用于维护同一对象的独立视图，同时确保更新立即反映在数据的每个视图中。

## 面向对象编程

本章的主要目标是讨论 Objective-C 中面向对象编程的主要特性。OOP 是一种编程风格，它鼓励我们根据所需功能将代码组织成定义的类。“类”这个词是一个很好的比喻，因为我们试图将代码和数据分类为合适的抽象。

与结构化编程等标准实现技术不同，OOP 提供了一种灵活的机制，通过向其他对象发送消息来执行代码。OOP 应用程序不是直接调用函数（正如我们到目前为止所做的那样），而是告知其他对象它们希望执行什么操作。通过发送消息而不是使用编译时定义的函数，对象可以自由地动态修改它们响应消息的方式。

OOP 提供的消息传递机制还有助于分离应用程序中不同区域关注点。使用这种策略，对象可以只专注于它所提供的基本功能。同时，对象可以请求系统中其他现有对象的任何附加功能。

类可以被解释为对象的原型。在 Objective-C 中，类由两部分定义，分别由关键字 `@interface` 和 `@implementation` 引入。这些定义包含了语言创建运行时对象所需的所有信息。

对象是特定类的运行时实例化。在本书讨论的大多数上下文中，实例等同于运行时对象。因此，你会看到对象是通过对现有类进行显式实例化而创建的。



### 为什么对象如此重要？

在过去 30 年中，面向对象编程语言因其先进的程序分解和扩展机制而走到了编程研究和实践的前沿。虽然像`C`这样的老语言确实可以模拟面向对象语言的所有特性，但要达到同样的效果，需要很高的复杂性，而这在使用面向对象编程时是完全不必要的。

因此，目前大多数应用软件都是为`Objective-C`这样的面向对象语言设计的。由于对象具有重要的实际意义，有必要了解面向对象编程与其他编程范式之间的主要差异。

面向对象编程的经典定义确定了面向对象语言中应具备的一系列功能。由于如今面向对象语言种类繁多，除了对象及其相关方法的概念外，很难再找出所有语言都共有的特性。尽管如此，面向对象的经典定义仍包含一些重要元素，这些元素在`Objective-C`以及许多其他面向对象语言中都可用。

**封装**：这意味着对象应该将数据和代码包含在同一单元中。例如，定义一个财务报表的对象将包含财务报表所使用的数据，以及可用于操作财务报表的代码。

**继承**：类可以从其他类继承变量和代码。这种关系称为继承。被继承的类被称为超类、父类或基类。继承关系是递归的，因此会形成一个类层次结构。在`Objective-C`中，只支持单一继承；也就是说，类只能从一个类继承数据和代码（但通过使用协议机制可以使其更灵活，你将在后续章节中看到这一点）。

**多态性**：这是面向对象编程中的一个核心概念，因为它允许同一条消息根据接收消息的对象以不同方式进行响应。`Objective-C`的语法允许使用基类指针来引用派生类或基类的对象。这种灵活的机制让程序员无需确切定义接收者将如何反应以及会产生什么结果，就能向对象发送消息。接收消息的对象可以选择根据自己的需要来解读消息。这提供了扩展性，如果不使用对象，是很难实现的。

在过去的几十年中，面向对象编程已被用于定义许多重要技术的基本构建块。例如，苹果公司曾通过`Objective-C`利用面向对象编程的优势来设计 Mac OS X 操作系统的界面。最近，iOS 的所有用户界面和系统代码也都是使用`Objective-C`创建的。

既然你已经了解了使用面向对象编程的优势，让我们来看看`Objective-C`如何支持面向对象的主要概念，包括如何定义带有变量的新类及其方法。

## 使用对象

一个`Objective-C`程序会大量使用对象。尽管你始终可以创建结构体和函数（如前一章所述），但应用程序应该使用对象，以符合`Objective-C`框架的要求，并能够使用该语言提供的扩展选项。

你需要理解的第一件事是如何正确使用已有的对象。当你向一个类发送创建消息时，就会在`Objective-C`中创建一个对象。然后，该对象将变为活动状态，你就可以开始向它发送消息了。

实际上，你操作的是指向对象的指针，因为无法直接创建某个特定类的变量。考虑一下`Objective-C`中的字符串类`NSString`。

> **注意**：苹果公司提供的`Objective-C`框架中的所有类都以两个字母开头，作为通用前缀。`NS`前缀被 Foundation 框架中的所有类使用。自从`Objective-C`还是 NextSTEP 公司开发的产品起，这两个字母就开始使用了。其他框架使用不同的字母组合。特别是，应用开发者编写的代码应避免使用这个前缀。

```
void printMessage(NSString *text)
{
   NSLog(@"Message is %@", text);
}
```

在这个例子中，有一个`NSString`类的对象，它实现了字符串类型。对象总是通过指针来操作，因为你无法确切知道传递给函数的对象的具体类型，只能知道它派生自`NSString`。这是多态性的基本原则，并且在操作对象时始终遵循。

你可以对对象做的最重要的事情就是向它发送消息。消息发送的语法如下：

```
[<对象指针> <消息>];
```

例如，考虑以下函数：

```
void printMessage(NSString *text)
{
   int len = [text length];
   NSLog(@"Message length is %d", len);
}
```

在这个例子中，你使用了`text`（一个指向字符串对象的指针），并向它发送了`length`消息，该消息返回一个整数值。`length`消息是`NSString`对象接口的一部分，因此只要你有可用的`NSString`对象，就可以调用它。

### 方法参数

上面讨论的`length`消息非常简单，没有任何参数。如果一个消息有一个参数，它会在消息名称后面加上冒号，然后传递该参数，如下所示：

```
void printsubstr()
{
        NSString *text = @"My Example String";
        NSString *substr = [text substringToIndex:5];
        NSLog(@"substring is %@", substr); // 输出 "My Ex"
}
```

在这个例子中，向`text`对象发送了`substringToIndex`消息。该消息被定义为返回一个新字符串，其中包含原始字符串中从开头到指定字符索引（不包含该索引处的字符）的所有字符。在这个函数的最后一行，你使用了`NSLog`函数将生成的子字符串打印到控制台。

如果一个消息需要两个参数，它会有特定的格式来描述每个参数的用途。下面是一个使用`stringByReplacingOccurrencesOfString:withString`消息的例子：

```
NSString *replaceOne(NSString *myString)
{
        return [myString
                stringByReplacingOccurrencesOfString:@"1"
                withString:@"one"];
}
```

正如消息名称所明确指出的，它的目标是通过将第一个参数的出现替换为第二个参数来返回一个新字符串。参数总是在冒号分隔符之后提供。对于消息所需的任意数量的参数，都是如此。

> **注意**：许多习惯了`C++`或`Java`等语言中方法和函数工作方式的程序员，会觉得`Objective-C`在需要为每个参数添加额外关键字时非常冗长。然而，选择长格式的语法来书写消息也有一些优点。首先，它促使创建参数数量较少的方法。这是一个好的副作用，因为无论使用何种语法，大量的参数都可能导致函数难以理解。其次，`Objective-C`消息是自文档化的，因为它们必须使用词语来描述每个参数。



### `id` 类型

Objective-C 面向对象特性中的一个独特之处在于使用了一种名为 `id` 的通用对象类型。`id` 类型的变量可以用于持有指向任何对象的指针，而与其原始类型无关。Objective-C 编译器也允许你向该变量发送任何消息，因为消息分发过程将在运行时而非编译时执行。

`id` 类型被用于一些重要的消息中，例如 `alloc`，它为新的对象分配内存。该消息的返回类型是 `id`，因此它可以用来创建任何类型的对象。

由于 `id` 可以作为对象的通用类型，因此了解何时使用 `id` 或特定指针类型（例如 `NSString *`）非常重要。使用明确定义的类型的优势在于，编译器可以精确地知道哪些消息可用于该类型。如果向 `NSString` 的实例发送了一条不正确的消息，编译器会向用户显示错误消息。因此，如果代码假定某特定类型是所需消息的唯一可能目标，那么使用该特定类型的对象会更简单。

另一方面，在许多情况下，你可能希望编写适用于通用类型的代码。此外，一些库对象会返回 `id` 类型。在这些情况下，必须使用 `id` 而不是明确定义的类型。

## 创建新对象

创建新对象是一个两步过程。第一步是向所需的类发送请求新对象的消息。这是通过 `alloc` 消息完成的，它为对象分配足够的空间并返回一个指向它的指针。

第二步包括初始化新创建的对象。这是由实例方法执行的任务。存在一个名为 `init` 的标准方法，任何继承自 `NSObject` 的类都会继承该方法。对象也可以定义自己的 `init` 方法。更复杂的对象也可以通过专门用途的方法进行初始化，例如在需要额外参数时。

> **注意**
> 请记住，Objective-C 中的类也是对象。一旦定义了一个新类（你将在下一节中看到），编译器会自动创建一个代表该类的运行时对象。就像任何其他对象一样，类可以有自己的方法。此外，这些方法可以独立于该类的任何实例运行。

为了说明这一点，请考虑使用 `NSArray` 和 `NSNumber` 类创建数字数组的过程：

```
NSArray *createArray()
{
    NSMutableArray *array = [[NSMutableArray alloc] init];
    for (int i=0; i<3; ++i)
    {
        NSNumber *number = [[NSNumber alloc] initWithInt: i+1];
        [array addObject:number];
    }
    return array;
}
```

在这个例子中，首先通过向 `NSMutableArray` 类发送 `alloc` 消息来创建一个可变数组。然后，通过发送 `init` 消息要求对象初始化自身。虽然你想要返回一个 `NSArray` 对象，但你创建了一个 `NSMutableArray`，因为它允许你根据需要添加元素。多态性允许你将 `NSMutableArray` 当作 `NSArray` 来使用。在循环内部，你为 1 到 3 之间的每个值创建一个 `NSNumber` 对象。你首先向该类发送 `alloc` 消息，然后使用 `initWithInt`，这是 `NSNumber` 类的专用初始化方法。它接收一个整数，该整数随后成为 `NSNumber` 存储的值。

## 定义新类

通过创建一对元素来定义一个类：接口和实现。接口通常放在一个单独的头文件中，用于向用户定义类的内容。然后该接口可供应用程序的其他模块使用。

根据该接口定义，其他类将收到关于哪些消息可以发送给特定对象的详细描述。例如：

```
@interface Employee : NSObject
{
   // 此处列出变量（如果有）
}
// 消息列表
@end

@implementation Employee
// 消息实现列表
@end
```

> **注意**
> 在苹果相关平台上编程时，应始终使用 `NSObject` 作为超类。这在定义新类时并非强制要求，因为可以创建独立于 `NSObject` 的类。例如，你可能希望使用不同的语义定义自己的对象层次结构。然而，在实践中，你总是希望使用 `NSObject` 提供的通用方法，它被设计为系统中所有对象的公共基石。

`@interface` 关键字用于引入一个接口。该关键字后跟类名，并且可以选择性地加上一个冒号和对象所使用的超类名称（如果有的话）。

`@implementation` 关键字用于开始类的定义。它作为 `.m`（消息）文件的一部分使用，并包含对象响应的每条消息的定义。

> **注意**
> Objective-C 使用的保留字都以 `@` 字符为前缀，这在 C 的语法中是不使用的。这种做法避免了与现有 C 代码可能发生的冲突。这对于将现有 C 代码集成到 Objective-C 中的程序员来说是一种便利。例如，`@class` 是一个关键字，因此仍然可以使用名称 “class”，它是 C 应用程序中的合法名称。通过这样做，Objective-C 避免了将代码从 C 移植到 C++ 时存在的一个常见问题。每当 Objective-C 使用不带 `@` 字符的关键字时，这些关键字仅限于少数上下文，因此不会与应用程序定义的名称发生冲突。

在接下来的几节中，你将了解如何向类定义中添加实例变量和实例方法。



## 实例变量  

实例变量用于存储特定对象的数据。成员变量的定义特征是它们为特定类的每个对象而定义。从这个意义上说，它们类似于纯 C 结构体中存储的数据。另一方面，实例变量在类的实现内部是可访问的。  

例如，一个 `Employee` 类会为该类的每个对象存储数据。一个 `Employee` 对象通常会有诸如年龄、薪水和地址之类的实例变量。这些实例变量会以如下方式表示：  

```
@interface Employee : NSObject
{
   int salary;
   NSString *address;
   int age;
}
@end
```

一旦在类的接口中声明了实例变量，它便可在实现中使用。例如，假设你声明一个名为 `incomeTax` 的新方法。  

```
@implementation Employee
-(double) incomeTax:(double)taxBracket
{
   return salary * taxBracket;
}
@end
```

实例变量可以被指定为 public 或 private，从而限制其他类的访问权限。私有变量只能被同一类的方法使用，而公有变量则可以被任何使用该类对象的代码访问。这种机制使得应用程序开发者可以更改类的内部实现，同时减少对使用该类的其他代码所造成后果的担忧。  

要将实例变量定义为私有，只需在变量定义之前使用关键字 `@private`。类似地，可以通过使用 `@public` 关键字使变量成为公有。如果你希望某个实例变量可被该类及其子类访问，请使用 `@protected` 关键字。默认情况下，实例变量是私有的；也就是说，它们仅对该类本身可用，而不能被其后代类使用。一旦使用了某个访问权限关键字，它将影响其后声明的所有变量，直到通过出现另一个关键字来更改访问级别。  

还可以使实例变量仅在其定义的包中可用。为此，应使用关键字 `@protected`。其用法与标记私有变量和受保护变量的方式类似。  

> **注意**  
> 通常情况下，Objective-C 程序员不会大量使用公有或受保护的实例变量。由于其动态特性，Objective-C 中的类是通过其方法，并利用消息传递机制来使用的。这意味着直接访问变量的作用有限，在实践中应避免使用。  

## 添加和定义实例方法  

方法是定义类行为的主要机制。正是类中的方法集定义了对象可以直接响应的消息（对于派生类，这还包括其超类中的方法）。因此，面向对象编程的主要目标之一是只包含与你想向用户公开的功能直接相关的方法。  

当对象接收到特定消息时，实例方法会定义对象实例的行为。例如，假设你想响应 `sayHello` 消息。可以按如下方式实现：  

```
@interface SimpleObject : NSObject
-(void) sayHello;
@end

@implementation SimpleObject
-(void) sayHello
{
   NSLog(@"hello");
}
@end
```

这个示例展示了一个通过其单一方法提供有限功能的类。要访问该方法，你可以创建一个 `SimpleObject` 类的对象，并发送 `sayHello` 消息。  

```
SimpleObject *object = [[SimpleObject alloc] init];
[object sayHello];
```

如前所述，方法使用分段语法进行声明，其中每个参数由一个描述其含义的词引出。方法 `sayHello` 没有任何参数，因此其名称后面没有冒号。另一方面，考虑下面所示的 `printText` 方法：  

```
@interface SimpleObject2 : NSObject
-(void) printText:(NSString *)message;
-(void) printText:(NSString *)message withSuffix:(NSString *)suffix;
@end

@implementation SimpleObject2
-(void) printText:(NSString *)message
{
        NSLog(@"message is: %@", message);
}

-(void) printText:(NSString *)message withSuffix:(NSString *)suffix
{
        NSLog(@"message is: %@%@", message, suffix);
}
@end
```

在这里，新类定义了一个能够响应 `printText` 消息的对象。当向此对象发送消息时，你需要在冒号分隔符后提供一个参数，如下所示：  

```
void printTextExample()
{
        SimpleObject2 *obj = [[SimpleObject2 alloc] init];
        [obj printText:@"My message"];
}
```

类似地，当需要两个或多个参数时，它们会在相应的冒号之后给出，该冒号用于分隔参数关键字和实际参数。以下是使用 `printText:withSuffix:` 消息的示例。  

```
void printTextAndSuffixExample()
{
        SimpleObject2 *obj = [[SimpleObject2 alloc] init];
        [obj printText:@"My message" withSuffix:@"here"];
}
```



### 消息与函数的区别

在 Objective-C 中，向对象发送消息与调用函数之间存在细微差别。执行函数时，程序员和编译器都确切知道执行结果会调用哪些代码。例如，调用 `printf` 这类函数时，你清楚会执行一段定义明确的代码，将参数打印到日志中。同样，调用 `exp` 函数时，可以确定会调用数学库中计算指数的函数。

然而，处理对象时，向对象发送消息并不能保证每次都会执行特定的代码段。当你向非自己创建的对象发送消息时尤为如此。这种差异与多态的概念有关。多态意味着任何对象都能以不同方式响应消息——以最适合其目标的方式。例如，`InkJetPrinter` 对象对 `print` 消息的响应方式可能与 `LaserPrinter` 对象不同。然而，只有这些对象需要理解这些方法的实现有何不同。面向对象语言在发送消息的对象和接收消息的对象之间提供了这种分离层次。

考虑以下示例，其中创建了一个 `Employee` 类：

```
@interface Employee : NSObject
{
    // 在此添加任何必要的变量
}
-(double) calculatePay;
@end

@implementation Employee
-(double) calculatePay
{
   return 0;
}
@end
```

你还有一个小时工类，代表按工作小时数支付薪酬的员工。

```
@interface HourlyEmployee : Employee
{
   int numWorkedHours;
   double hourlySalary;
}
@end

@implementation HourlyEmployee
-(double) calculatePay
{
   return numWorkedHours * hourlySalary;
}
@end
```

最后，还有全职员工，其薪酬按年计算。

```
@interface FullTimeEmployee : Employee
{
   double yearlySalary;
}
@end

@implementation FullTimeEmployee
-(double) calculatePay
{
   return yearlySalary / 12;
}
@end
```

从设计角度看，共有三个类：基类 `Employee` 以及两个派生类 `HourlyEmployee` 和 `FullTimeEmployee`。这些类都实现了 `calculatePay` 方法。但每个类中使用的实现方式各不相同。在第一个类中，只有一个基础实现，返回值为零，因为没有关于所描述的 `Employee` 类型的信息。

第二个类的实现依赖于 `numWorkedHours` 和 `hourlySalary` 这两个变量。这些信息用于计算需要支付给小时工的薪酬。第三个类也派生自 `Employee`，但该例中薪酬计算更简单，因为全职员工按月领取年薪。

假设你按如下方式使用这些类：

```
void send_payment_message(Employee *employee)
{
   double total = [employee calculatePay];
   NSLog(@"salary total is %d", total);
}
```

在这种情况下，虽然传递给函数的值类型是 `Employee`，但你无法确定实际使用的是哪种 `Employee`：它可以是原始 `Employee` 类的对象，也可以是派生类之一。每种情况下，存储在 `total` 中的值都不同，导致计算依赖的信息也不同。这之所以可行，是因为 Objective-C 中的消息仅在运行时解析。

### 多态消息

当消息发送给对象时，运行时系统会确定哪个方法可用于响应特定消息。如果找到合适的方法，将用它来处理该消息。如果未找到匹配消息的方法，运行时系统将搜索其超类（父类）。如果在超类中也未找到，则此过程会继续，直到在某个超类中找到匹配消息的方法，或生成错误为止。

在上述示例中，对象通过继承与 `Employee` 类相关联。因此，编译器可以确保该特定消息存在有效实现。但情况并非总是如此，因为编译器的验证并非严格必要。为了能够发送编译时未知的消息，Objective-C 允许向通用类型 `id` 的对象发送任何消息。例如，可以有以下函数：

```
void send_pay_message(id object)
{
   double total = [object calculatePay];
   NSLog(@"salary total is %lf", total);
}
```

这段代码与上述示例非常相似，但现在对象被声明为仅 `id` 类型，即可用于任何对象的通用类型，而不是固定的 `Employee` 类型。在这种情况下，不执行编译时检查，但运行时系统仍会执行与之前相同的工作。唯一的区别是编译器无法保证消息在运行时能被正确发送。因此，程序员需要足够小心，仅向可能响应该消息的对象发送特定消息（在此例中，仅使用能响应 `calculatePay` 消息的对象）。

### 特殊变量 `self`

实现实例方法时，有必要访问存储在实例变量中的数据。这可以通过使用 `self` 变量来实现。在方法内部，`self` 是一个特殊变量，被设置为指向对象本身的指针。因此，它不仅可作为消息的目标，还可用于访问实例变量。例如，考虑以下定义：

```
@interface Person : NSObject
{
    NSString *firstName;
    NSString *lastName;
}
- (void)setFirstName:(NSString *)firstName;
- (void)setLastName:(NSString *)lastName;
- (void)printFullName;
- (void)setFirstName:(NSString *)first lastName:(NSString*)last;
@end

@implementation Person
- (void)setFirstName:(NSString *)aFirstName
{
    self->firstName = aFirstName;
}
- (void)setLastName:(NSString *)aLastName
{
    self->lastName = aLastName;
}
- (void)printFullName
{
    NSLog(@"Full name is %@ %@", self->firstName, self->lastName);
}
- (void)setFirstName:(NSString *)first lastName:(NSString*)last
{
   [self setFirstName:first];
   [self setLastName:last];
}
@end
```

注意此处你可以通过变量 `self` 访问 `firstName` 和 `lastName` 的值，该变量只是指向当前对象的指针。虽然这很有用，但需要注意的是，即使不使用 `self`，实例变量也可以在方法内部自由访问。例如，`setLastName` 方法也可以写成：

```
- (void)setLastName:(NSString *)aLastName
{
    lastName = aLastName;
}
```

在此处展示的类实现中，变量 `self` 的第二个用途是作为同一对象中其他消息的目标。由于经常需要调用同一对象中的方法，特殊变量 `self` 至关重要。没有对当前对象的引用，就无法发送可由自身类中其他方法响应的消息。



## 定义类方法

类方法与实例方法的不同之处在于，它是由发送给类名称的消息触发的，而不是发送给特定对象。因此，即使在创建该类的任何一个对象之前，也可以向类发送消息。这是执行对象创建和初始化的一种简单而有效的方式。例如，考虑以下使用 `NSString` 类的代码：

```
NSString *copyString(NSString *string)

{

        NSString *copy = [NSString stringWithString:string];

        return copy;

}
```

此函数的目标是复制原始字符串，返回一个指向具有相同文本内容的 `NSString` 类型新对象的指针。为此，你向 `NSString` 类发送一条消息，要求它创建一个新的字符串对象，并使用作为参数传入的单个字符串进行初始化。请注意，结果是要求类执行所有这些任务：`NSString` 实例不负责最终结果。

让我们看一个例子，演示如何向类接口和实现添加一个名为 `performClassTask` 的新类方法。要定义类方法，使用的语法与实例方法定义几乎相同。唯一的区别在于，此类方法以加号（`+`）开头，而不是减号（`-`）。

```
@interface MyClass : NSObject

+ (void) performClassTask;

@end

@implementation MyClass

+ (void) performClassTask

{

   // 实现代码写在这里

}

@end
```

实现类方法时的另一个区别是，它不能通过 `self` 关键字访问对象的实例变量。这在逻辑上是合理的，因为此类方法并非作为对象的一部分运行。类方法实际上是附着在类对象上的，在应用程序中，每个类只存在一个这样的类对象。在这种情况下，变量 `self` 指的是那个类对象。

正如你在第一个示例中看到的，类方法是创建和初始化特定类的新实例的一种便捷方式。由于它们在类级别上操作，因此可以在对象存在之前被调用。因此，可以使用类方法来执行创建复杂对象所需的任意数量的前期步骤。

要查看类方法概念的多个示例，只需看看 Foundation 框架中一些最常见的类。例如，`NSString` 类有许多类方法，它们基于客户端提供的输入来创建和初始化一个新字符串。以下是一个示例代码，它基于一个现有的指向字符的指针来创建一个新字符串：

```
NSString *stringFromCArray(const char *text)

{

        return [NSString stringWithUTF8String:text];

}
```

如你所见，消息是直接发送给 `NSString` 类的。然后，类方法负责分配一个新对象，并使用所提供的指针正确地初始化它。这个例子还展示了如何转换以 C 语言编写的遗留代码中存储的文本。由于 C 语言没有对象，字符串被表示为由空字符（零值）结尾的简单字符数组。虽然这是一种表示字符串的快速方式，但它也难以操作，并且需要手动维护字符串使用的内存。相比之下，`NSString` 对象用起来要容易得多，并且可以以类似于系统中其他对象的方式更新。

## init 方法

`init` 方法在类的其他方法中执行一个重要功能。它是在对象创建之后立即调用的方法，位于两步的 `alloc`/`init` 配对中。`init` 方法在 `NSObject` 类中声明，但在新类中经常需要重写其定义。

你可能想要重新定义 `init` 方法的原因之一是为对象中的一个或多个实例变量提供自定义的初始化。默认情况下，实例变量如果是数字，则被初始化为零；如果是对象，则被初始化为 `nil`（指针的空值）。另一方面，可能有必要或希望将实例变量初始化为对类来说更合理的其他值。

例如，假设你有一个代表 `Cash`（现金）的类。这个类仅有的两个实例变量是 `quantity`（数量）和 `currency`（货币）。对于 `quantity` 来说，默认值为零是可以的。然而，对于 `currency`，你希望默认值是 USD。以下是实现方法：

```
@interface Cash : NSObject {

        double quantity;

        NSString *currency;

}

@end

@implementation Cash

- (id)init {

    self = [super init];

    if (self) {

        currency = @"USD";

    }

    return self;

}

@end
```

首先，你定义了类的接口，其中声明了两个实例变量。请注意，不必在类接口中将 `init` 添加为一个方法：`init` 已经通过继承成为了接口的一部分，因为 `NSObject` 是其父类。

接下来，你在 `@implementation` 部分为 `init` 方法提供了一个实现。该实现必须遵循此处展示的类似结构，以满足类使用者的期望。因此，第一个操作需要通过特殊变量 `super` 调用父类中的 `init` 函数。其次，你检查 `self` 是否指向一个有效的对象，如果是肯定的情况，则将其变量 `currency` 初始化为值 `@"USD"`。

任何需要定义初始化方法的类都需要遵循相同的过程。你也可以声明接受参数的初始化方法。例如，一个接收货币名称作为参数的 `init` 方法可以编码如下：

```
- (id)initWithCurrency:(NSString*)aCurrency {

    self = [super init];

    if (self) {

        currency = [NSString stringWithString:aCurrency];

    }

    return self;

}
```

在这种情况下，你仍然会调用父类中的 `init` 方法，但现在使用参数 `aCurrency` 来初始化 `currency` 实例变量。

## 定义属性

实例变量用于存储与对象关联的数据。实例方法用于实现特定类对象所提供的功能。这两个元素结合在一起，足以实现面向对象编程的所有主要优势。然而，在某些情况下，结合方法和变量一起使用会很有帮助。这就是将属性引入该语言的原因。

属性是一种语法，用于简化创建设置和获取与对象关联的特定值的方法。虽然属性在使用上与实例变量类似，但它不需要与任何特定变量关联。属性是为需要被应用程序妥善处理的数据提供访问的一种实用方式。

例如，考虑某些数据在应用程序的两个或多个区域中使用的情况。因此，每次数据更改时，最好触发一个更新机制。在这种情况下，可以使用属性为数据访问提供简单的接口。

```
@interface Person : NSObject

{

        NSString *name_;

}

@property(readwrite,retain,nonatomic) NSString *name;

@end

@implementation Person

-(void) setName:(NSString*)aName

{

        name_ = aName;

        // 通知 UI 名称已更改

}

-(NSString *)name

{

        return name_;

}

@end
```

在这个例子中，你有一个 `Person` 对象，它包含一个 `name` 属性（我稍后会解释括号中的属性）。与该属性关联的有两个方法：第一个方法用于设置属性，第二个方法返回当前存储的值。为了存储属性的内容，类 `Person` 使用了一个名为 `name_` 的实例变量。



## 使用属性

一旦这些定义准备就绪，你就可以在代码中使用该属性，如下所示：

```
-(void)useProperty:(NSString*) newName
{
   Person *myPerson = [[Person alloc] init];
   myPerson.name = @"new property value";
}
```

请注意，这段代码是如何将属性当作结构体中的字段来使用的。这是一种特殊的语法，只适用于属性，因为 `myPerson` 是一个指针，而指针没有数据成员（因为它们不是结构体）。实际上，这种语法的作用是调用 `Person` 类中的 setter 方法。每当这种情况发生时，`setName:` 方法中的代码就会执行必要的操作来更新用户界面（UI）。

当然，你也可以通过直接调用方法来实现同样的效果，如下所示：

```
-(void)usePropertyAgain:(NSString*) newName
{
   Person *myPerson = [[Person alloc] init];
   [myPerson setName: @"new property value"];
}
```

类似地，对该属性执行读取访问将会调用 getter 方法。

```
-(void)readProperty:(NSString*) newName
{
   Person *myPerson = [[Person alloc] init];
   myPerson.name = @"new property value";
   NSLog(@"The name is now: %@", myPerson.name);
}
```

### 合成属性

总的来说，属性是简化类中暴露功能接口的一种绝佳方式。当属性的变更会导致应用程序其他部分也发生变更时，这一点尤为明显。当某个特定数据是根据存储在对象中的变量计算得出时，也可以使用属性机制。例如，你可以创建一个名为 `temperatureInCelsius` 的属性，它与名为 `temperatureInFahrenheit` 的属性共享相同的数据。唯一的区别在于，getter 和 setter 方法负责在这两种表示之间进行转换。

如果你所做的只是在实例变量中存储和检索数据，那么属性的实现可以简化。在这种情况下，Objective-C 提供了一种默认机制，可用于自动创建 getter 和 setter 方法。因此，上述属性的实现可以简化为以下形式：

```
@implementation Person
@synthesize name = name_;
@end
```

在这里，你不需要直接实现任何 getter 或 setter 方法。编译器会自动完成这项工作，而你只需要提供实例变量的名称（在这个例子中，变量名为 `name_`）。

```
@interface Person : NSObject {
        NSString *name_;
}
@property(readwrite,retain,nonatomic) NSString *name;
@end

@implementation Person
@synthesize name = name_;
@end
```

最后，Objective-C 提供了一种更简单的实现属性的方式。通过将实例变量完全交由编译器定义即可实现。在这种情况下，前面的例子可以修改为：

```
@interface Person : NSObject
@property(readwrite,retain,nonatomic) NSString *name;
@end

@implementation Person
- (void) useName
{
        NSLog(@"name is %@", self.name);
}
@end
```

这样，即使没有使用 `@synthesize` 分配任何实例变量，`useName` 方法也能访问该属性并打印其值。在这种情况下，相应的实例变量会被自动生成，默认情况下，该变量使用与属性相同的名称，并在名称前加上一条下划线。

### 属性特性

如上所示，属性具有许多特性，这些特性描述了在 `@implementation` 部分中属性将如何被合成或定义。以下是属性一些最常见特性的列表：

*   **readwrite**：此特性用于标识可被客户端代码读取和修改的属性。也就是说，其他对象可以使用 `对象.属性` 的表示法来读取和设置相应的属性（这是默认特性）。
*   **readonly**：此特性可用于标记一个不可修改的属性。当你只想提供对对象中包含的特定数据的访问权限，同时又不想允许修改其值时，此特性很有用。例如，某个属性的值可能由其他实例变量合成而来——这意味着你不希望允许对其独立修改。
*   **atomic**：此特性在处理多线程代码时很有用。它表示由此属性定义的操作一旦开始就不能被中断。例如，多个线程将需要在单个事务中读写值。请注意，如果不需要原子操作，此选项可能会导致不必要的开销，但它也是默认的赋值方式。
*   **nonatomic**：与上述特性相反，它表示操作可以被中断。在编写不需要原子操作的单线程应用程序时很有用。
*   **getter**：此特性可用于为 getter 方法自定义名称。例如，你可能希望使用一个已经定义好的方法。编译器会将所有使用 `对象.属性` 表示法的读取访问路由到此方法。正确的语法是 `getter=方法名`。
*   **setter**：此特性可用于为 setter 方法自定义名称。该特性的用法与上述 `getter` 类似。正确的语法是 `setter=方法名`。
*   **copy**：setter 方法会存储传递给它的参数对象的副本，而不是对象本身。这可以用来避免对现有对象的额外引用，并将对副本的修改与原始对象隔离开来。
*   **retain**：使用此选项时，数据会被保留以进行内存管理。该属性中之前存储的任何其他对象都将被释放。
*   **assign**：此特性表示传递给 setter 方法的参数数据将被直接赋值给属性所使用的变量。

**注意**

上述部分选项涉及对象管理内存的方式。如果你不理解这些内存管理策略之间的区别，无需担心：我将在第 8 章中详细讨论这些实现技术。目前，只需记住可以使用不同的特性来决定属性 setter 如何存储和释放数据即可。



## 类与文件结构

Objective-C 使用两种主要的源代码文件：头文件和模块实现文件。头文件用于包含类的接口部分。其他元素，如常量和辅助类型定义，也可以包含在头文件中。

第二种类型的文件是模块定义，以 `.m` 扩展名保存。模块定义包含了类的大部分实现内容。它不仅包含实现部分，还包含模块所需的任何其他函数和类型定义。

头文件对于提供类的公共接口是必需的。项目中的其他模块将通过其公共接口访问该类。因此，它们需要访问定义此接口的头文件。

有两种方式可以访问头文件中的定义。首先，是传统的 C 风格包含。例如，要以 C 风格包含标准 I/O 库，可以使用：

```
#include <stdio.h>
```

这对于包含应用程序中传统和遗留代码所需的定义是必要的。然而，Objective-C 提供了第二种包含头文件的方法。这种第二种风格用于简化接口的创建。你可以使用 `import` 指令来包含此类接口，如下所示：

```
#import <Foundation/NSObject.h>
```

这将导入 Foundation 框架中包含的 `NSObject.h` 头文件。将类组织到框架中是很常见的，而 `FrameworkName/ObjectName.h` 这种表示法是记住对象可能所在位置的简便方法。

以下是一个完整示例，展示了一个简单类的头文件和实现文件可能是什么样的。首先，一个 `Loan` 类的头文件，其中表示贷款的常见属性，如 `principal` 和 `interest`。

```
// 文件 Loan.h

@interface Loan : NSObject
{
    double principal;
    double interest;
}

- (double)getMonthlyPayment;

@end
```

这是相应的方法实现文件：

```
// 文件 Loan.m

#import "Loan.h"

@implementation Loan

- (double)getMonthlyPayment
{
    return principal * (interest/12);
}

@end
```

一旦你将类的代码拆分为这两个文件，头文件 `Loan.h` 就是唯一需要提供给其他对象的部分。这意味着对实现文件 `Loan.m` 的修改不会影响系统中其他类的编译方式，除非接口以某种方式发生了更改。

使用这种将接口与实现分离的方法，你可以做到的一件很棒的事情是，以独立于接口的方式更新实现。例如，你可以决定改变 `getMonthlyPayment` 的实现方式。假设，你希望将利息计算放在一个单独的方法中，而不是像现在这样使用内联表达式。这将产生以下实现文件：

```
// Loan.m

#import "Loan.h"

@implementation Loan

- (double) interestPerMonth
{
    return interest/12;
}

// 更新后的月供计算
// 使用了新的 interestPerMonth 方法。
- (double) getMonthlyPayment
{
    double monthlyInterest = [self interestPerMonth];
    return principal * monthlyInterest;
}

@end
```

注意新的计算是如何使用另一个名为 `interestPerMonth` 的方法的。虽然结果仍然相同，但这种更改带来的新组织方式允许未来进行其他更改。例如，根据借款人的财务评分，你可能需要使用更复杂的技术来计算月利息。现在这是可行的，因为利息的计算已经从月供的计算中分离出来了。

这个示例的另一个方面值得注意：如果某个方法仅在实现内部使用，则无需将其添加到接口中。这是一个重要的事实，因为它有助于减少 `@interface` 部分的条目数量。只有其他类可访问的方法才需要包含在接口部分，而本地使用的方法只需出现在实现部分。只要你在第一次使用某个方法之前添加它，这就足够了。在后面的章节中，你将看到将内部方法添加到现有实现中的更通用方式。

## 总结

类提供了面向对象编程的基本构建块。由于 OOP 是 Objective-C 的主要关注点，因此理解类的概念非常重要。在本章中，我介绍了在 Objective-C 中创建和操作类所需的一切知识。

首先，你看到了 OOP 如何成为软件开发的重要工具。OOP 的概念（如封装、多态和继承）通过允许在项目的设计和开发阶段提供适当的抽象级别，为现代软件的设计奠定了基础。OOP 在过去 30 年中一直被使用，而 Objective-C 是一种完全拥抱 OOP 社区所提出概念的语言。

你学习了如何使用 `@interface` 和 `@implementation` 部分在 Objective-C 中定义新类。类的语法允许将这些定义拆分为公共头文件和私有实现文件。

你还了解了如何定义提供任何对象完整功能所需的接口变量和方法。实例方法可以访问在类中声明的实例变量。它们还可以通过使用 `self` 变量访问同一类中的其他方法。

最后，你学习了属性，这是一种简化对对象进行数据设置和读取过程的有效方式。属性非常灵活，为类的编写者提供了多种与实例变量交互以及拦截对这些属性的更改和访问的方式。例如，你可以通过使用 `@synthesize` 关键字来提供此功能。

通过在本章中获得的关于类和对象的基本理解，你已经准备好深入探索 Objective-C 框架提供的对象了。你将在下一章开始这一探索，该章将讨论字符串和容器。



