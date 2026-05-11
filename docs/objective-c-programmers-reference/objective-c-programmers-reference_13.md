# 7. 动态绑定

## 摘要

Objective-C 的最大优势之一是它允许程序员根据实际需求选择速度和灵活性的正确组合。用这种语言编写的代码可以享受编译器检查表达式和方法调用的安全性，编译器在创建可执行文件的过程中会验证每条语句。然而，许多特性是由运行时驱动的，这允许程序员将一些决策推迟到代码执行时。

Objective-C 的运行时系统是程序员在动态和静态编程行为之间切换的主要工具。在本章中，你将探索运行时系统提供的一些特性。你将看到程序员有很大的空间来定义如何响应发送给对象的消息。例如，通过选择器操作方法是最强大的特性之一，被高级程序员广泛使用。

你还将看到如何允许在运行时将消息转发给其他对象。凭借这种能力，可以设计出无缝结合两个或多个现有接口的软件。最后，你将看到如何使用这些相同的特性来提高使用 Objective-C 消息发送机制的代码性能。

## 方法选择器

在 Objective-C 中创建新方法时，请记住方法由消息名、返回类型和一组形式参数定义。方法的这些元素在稍后的消息调用中被使用。当你向对象发送消息时，你隐式地告诉编译器你正在寻找哪种方法名、返回类型和参数。

特定消息调用所针对的方法标识在面向对象应用程序中非常重要。为了找到正确的实现，编译器首先查找已知消息的列表，并找到与请求完全匹配的那个。

由于 Objective-C 是一种动态语言，关于如何响应消息的决定是在运行时而不是编译时做出的。因此，与基于静态编译的语言（如 C++）不同，编译器需要某种方式在程序执行期间内部表示消息。这种能力通过使用选择器来提供，选择器是消息的运行时表示。选择器也可以存储为 `SEL` 类型的变量，因此可以很容易地被程序员创建和操作。

从程序员的角度来看，选择器是一个标识符，可以在运行时用来确定你要寻找哪条消息。每条消息都有一个关联的选择器：你可以检索和存储该选择器，并使用它来执行消息调用。以下是你可能希望在程序中访问方法选择器的几个原因：

- **调用通用方法**：此功能的一个可能用途是进行通用方法调用，该调用可以根据作为参数传递的方法选择器而变化。类似于函数指针，选择器允许在不确切知道将要响应的方法名称的情况下进行方法调用。
- **存储选择器以备后用**：你的目标可能是拥有一个选择器列表，这些选择器将在稍后使用，例如响应事件。以这种方式使用的方法被称为回调。这是与 UI 应用触发的事件进行交互的常见方式，你将在后续章节中看到。
- **自省**：某些应用程序需要确定与特定类关联的方法。如果你有这种需求，选择器可能是定义类如何响应传入消息的正确方式。



### 一个简单示例

为了更好地理解选择器的工作原理，我们先从一个在实现中使用选择器的示例类开始。

```
// 文件 Selectors.h
@interface Selectors : NSObject
- (void) printValue:(id)obj;
- (void) createSelector;
@end

// 文件 Selectors.m
@implementation Selectors
- (void) printValue:(id)obj
{
    NSLog(@"打印对象的值: %@", obj);
}
- (void) createSelector
{
    SEL sel = @selector(printValue:);
    // 在此处使用选择器
}
@end
```

`Selectors` 类向您展示了一个如何为通用方法获取选择器的示例。所涉及的方法是 `printValue:`，它简单地将参数 `obj` 打印到日志窗口（当方法名后附加冒号时，如 `printValue:`，表示该方法需要一个参数）。而 `createSelector` 方法则负责获取第一个方法的选择器，并将其存储在局部变量 `sel` 中。为此，您需要使用 Objective-C 提供的一个特殊运算符，该运算符返回传递给它的消息的选择器。

当您需要将现有方法转换为选择器值时，可以随时使用 `@selector` 运算符。从某种意义上说，`@selector` 返回一个类似于指针或引用的对象，因为它之后可用于标识该消息，并将其作为参数传递给代码的其他部分。

一旦拥有选择器，您就可以在与该类型参数兼容的方法上使用它。使用选择器最简单的方法是在特定对象上调用它。例如，可以通过 `performSelector:` 方法来实现，如下所示：

```
// 文件 Selectors.m
@implementation Selectors
- (void) displayData
{
    NSLog(@"值为 1");
}
- (void) createSelector
{
    SEL sel = @selector(displayData);
    // 在此处使用选择器
    [self performSelector:sel];
    // 这应该会打印 "值为 1"
}
@end
```

`performSelector:` 方法能够使用提供的选择器在目标对象上触发消息调用。因此，在上面的示例中，代码行  
`[self performSelector:sel];`  
等同于直接调用该方法，例如：  
`[self displayData];`

显然，在这个例子中，增加这一层间接调用并没有带来任何额外的好处，但在实际应用中，选择器可能会被代码的另一部分检索到，然后作为参数传递过来。在这种情况下，当前类将无法获知目标消息的信息。这是一种常见的情况，在 Cocoa 框架的多个领域中都会遇到，您将在接下来的章节中看到。

### 带参数的调用

如果您想执行一个带参数的消息，那么需要使用 `performSelector:` 方法的一个稍微修改过的版本，以便能够将所需的参数传递给目标方法。为此，您可以使用 `performSelector:withObject:` 版本，如下所示：

```
// 文件 Selectors.m
@implementation Selectors
- (void) printValue:(id)obj
{
    NSLog(@"打印对象的值: %@", obj);
}
- (void) createSelector
{
    SEL sel = @selector(printValue:);
    // 在此处使用选择器
    [self performSelector:sel withObject:@"参数"];
    // 这应该会打印 "对象的值是 参数"
}
@end
```

这种机制在您希望允许一个类在特定事件发生时决定调用哪个消息时非常有用。例如，假设类 `MailService` 能够向其他类发送邮件。一种可能性是将目标对象保存在符合特定协议（例如 `MailReceiver`）的实例变量中。当消息可用时，便发送特定的消息，在此例中为 `onMailIsAvailable:` 消息。让我们先看看类接口会是什么样子。

```
// 文件 MailService.h
@protocol MailReceiver <NSObject>
- (void) onMailIsAvailable:(id)mail;
@end

@interface MailService : NSObject
{
    id <MailReceiver> _receiver;
}
@property(retain) id <MailReceiver> receiver;
- (void) processMail;
@end
```

这里定义了 `MailReceiver` 协议，其中包含一个名为 `onMailIsAvailable:` 的单一方法，当邮件可供处理时，`MailService` 将调用此方法。
然后需要一个 `MailService` 的接口。该类将包含一个属性，该属性存储一个指向名为 `_receiver` 的对象的指针。符号 `id <MailReceiver>` 表示该对象符合 `MailReceiver` 协议。该类还包含一个 `processMail` 方法，该方法执行邮件处理的主要任务。
当调用 `processMail` 方法时，它会检索邮件数据，并在接收者对象上调用 `onMailIsAvailable:` 消息。

```
// 文件 MailService.m
@implementation MailService
- (void) processMail
{
    id myMail = nil;
    // 执行其他操作来初始化 myMail...
    [_receiver onMailIsAvailable:myMail];
}
@end
```

在这里，您使用 `MailReceiver` 协议定义的消息将 `myMail` 数据传输给感兴趣的对象。



### 使用选择器

到目前为止，你看到的邮件处理系统设计与前几章讨论的技术是一致的。不过，它也存在一些问题。

-   它要求在类接口定义时使用协议。这在很多情况下是可行的，但并非总是如此。例如，假设你正在为现有类编写一个分类扩展。在这种情况下，你无法更改该类在其原始接口定义中实现的协议。
-   所使用的方法名称始终相同：这限制了你响应`MailService`消息的方式。例如，假设你想为不同类型的消息订阅两个`MailService`对象。在这种情况下，两个`MailService`对象都会调用同一个方法，你将很难区分这些调用。
-   它需要协议定义带来的额外复杂性。有时你可能希望使用一个更简单的解决方案，该方案可在几个类似的地方使用。消除对额外协议的需求是处理此类情况的更好方法。即使使用了非正式协议，上述两个问题依然存在。

使用选择器提供了另一种解决这些问题的方法，同时避免了引入新协议所带来的许多问题。简而言之，其理念是允许客户端传递一个选择器来标识目标方法。当预期的事件或条件触发时，该方法将被调用。这样，就可以有多个目标方法响应尽可能多的`MailService`对象。

每个`MailService`实例可以拥有不同的选择器，因此能以不同方式响应。当接收到发送给特定选择器的消息调用时，`MailReceiver`能够确定是哪个特定的`MailService`版本产生了该调用。让我们看看如何实现这种方法。

```objc
// 文件 MailService.h

@interface MailService : NSObject

@property(retain) id receiver;
@property SEL selector;

- (void) processMail;

@end
```

`MailService`接口已更新，包含一个额外的属性：`selector`属性，它持有在邮件可用时想要发送的消息的选择器。同时注意，接收者不再需要被限制于特定的协议。由于你不会直接调用任何方法，因此无需为此对象请求特定的接口。

```objc
// 文件 MailService.m

@implementation MailService

- (void) processMail
{
    id myMail = nil;
    // 执行其他操作来初始化 myMail...
    if (_selector && _receiver)
    {
        [_receiver performSelector:_selector withObject:myMail];
    }
}

@end
```

实现部分需要进行一些修改以利用新属性。一旦邮件数据可供处理，你检查`receiver`和`selector`属性是否已设置。然后，你在接收者对象上调用`performSelector:withObject:`，传入选择器作为参数，以及存储在`myMail`中的数据。

你还可以通过检查接收者是否真正实现了该选择器，来略微提高`processMail`方法的安全性。这种测试可以避免接收者对象未实现选择器所指定方法而产生的编程错误。这可以通过使用`respondsToSelector:`方法来实现，该方法检查一个对象能否处理对给定选择器的消息调用。

```objc
- (void) processMail
{
    id myMail = nil;
    // 执行其他操作来初始化 myMail...
    if (_selector && _receiver)
    {
        if ([_receiver respondsToSelector:_selector])
        {
            [_receiver performSelector:_selector withObject:myMail];
        }
        else
        {
            NSLog(@"error: the receiver doesn't respond to the given selector");
        }
    }
}
```

这里，你检查选择器是否可以作为消息发送给`_receiver`。如果不可能，则记录一条错误消息，供开发者稍后追踪。

最后，以下代码展示了如何使用这种基于选择器的实现：

```objc
// 文件 MailReceiverImp.h

@interface MailReceiverImp : NSObject
{
    MailService *_service1;
    MailService *_service2;
}

- (void) receiveMail1:(id)data;
- (void) receiveMail2:(id)data;
- (void) setupServices;

@end
```

首先，你定义一个示例类，它将拥有两个邮件服务。这将展示如何使用基于选择器的方法独立处理这些服务。`MailReceiverImp`类有两个实例变量用于存储正在使用的`MailService`对象。该类还有两个可以接收邮件数据的方法。最后，你声明一个`setupServices`方法，该方法负责邮件处理子系统的初始设置。

```objc
// 文件 MailReceiverImp.m

@implementation MailReceiverImp

- (void) receiveMail1:(id)data
{
    NSLog(@"receiving email data from source 1");
}

- (void) receiveMail2:(id)data
{
    NSLog(@"receiving email data from source 2");
}

- (void) setupServices
{
    _service1 = [[MailService alloc] init];
    _service1.selector = @selector(receiveMail1:);
    _service1.receiver = self;

    _service2 = [[MailService alloc] init];
    _service2.selector = @selector(receiveMail2:);
    _service2.receiver = self;
}

@end
```

类的实现部分提供了使用两个`MailService`对象的细节。两个`receiveMail`方法类似，此处列出仅为了完整性。真正的实现会对接收到的数据执行不同的操作。而`setupServices`方法则扮演了有用的角色。其目标是分配并初始化两个服务。然后，你设置每个服务所期望的两个属性：`selector`和`receiver`。接收者始终是`self`，因为你希望直接处理邮件数据。然而，如果你希望将结果发送给另一个对象，情况可能会有所不同。

你还将`selector`属性设置为不同的方法，每个`MailService`对象对应一个。这些选择器通过`@selector`关键字获取，并应用于每个目标方法。

此类代码的最大优势在于，它可以处理任意数量的`MailService`对象。例如，不再受特定协议的限制。类所需做的只是添加一个新方法，使用`@selector`关键字获取其关联的选择器，并将其传递给服务对象。

正如你将在下一节中看到的，这种策略不仅在你自己的用户定义类中很有用，而且在与你 Cocoa 框架交互时也同样有用。事实上，使用选择器处理事件是 Cocoa 和 Cocoa Touch 库中 GUI 编程使用的基本设计模式之一。



## Cocoa 中的 Target-Action

选择器的一个常见用途是定义为特定消息设置目标，该消息通常是在响应事件时生成的。因此，在选择器策略在 UI 代码中有着巨大的应用潜力。这种设计模式被称为 target-action，并且常常在 Cocoa 框架中用于处理需要由应用程序类处理的事件。

Cocoa UI 库使用了 target-action 设计模式，以实现事件目标与生成事件的 UI 控件之间更高层次的抽象和独立性。与我们上一节讨论的应用程序类似，UI 工具包的目标是允许每个类成为多个 GUI 控件的目标。同时，GUI 控件无需了解目标所实现的任何特定协议。

这些需求的结果是一种设计模式，其中 GUI 控件对象具有两个属性：一个 target（目标）和一个 action（动作）。target 属性指定当期望的事件发生时，将接收消息的对象。

例如，考虑一个具有单个窗口和一个按钮的应用程序，该按钮可用于触发用户定义的动作。虽然我还没有描述如何在 Cocoa 中创建此类应用程序（请参阅最后一章以获取完整示例），但下面是仅用于设置和处理事件所需的基本代码：

```
// 文件 ButtonTest.h
@interface ButtonTest : NSObject
- (void) doWhenButtonPressed:(id)button;
- (void) setupButton:(NSButton *)aButton;
// 此处可添加其他方法
@end
```

在 `ButtonTest` 类中，你声明了将在此处讨论的两个方法。`doWhenButtonPressed:` 方法是响应外部事件而被调用的，它将在用户按下按钮时执行。另一方面，`setupButton:` 方法用于初始化 target-action 机制，以便将按钮按下事件与目标对象关联起来。这些方法可以按如下方式声明：

```
// 文件 ButtonTest.m
@implementation ButtonTest
- (void) doWhenButtonPressed:(id)button
{
        NSLog(@"我们收到了一个按钮按下事件");
}
- (void) setupButton:(NSButton *)aButton
{
        [aButton setAction:@selector(doWhenButtonPressed:)];
        [aButton setTarget:self];
}
// 此处可添加其他方法
@end
```

`setupButton:` 方法包含了实现的重要部分。该方法的参数是一个 `NSButton` 对象，这是在 Cocoa 中用于表示用户界面按钮的类。你可以假设这个方法是在其他地方被调用的，可能是在同一个类中，并且以一个 `NSButton` 对象作为参数。第一步是设置 action，即按钮按下事件触发时将调用的方法。为了以通用的方式做到这一点，你为 `doWhenButtonPressed:` 方法传递一个选择器。

实现中的第二步是设置在上一行中定义的 action 的 target。在这种情况下，你希望 target 是同一个对象，因为你使用的选择器对应的是在 `ButtonTest` 类中实现的方法。因此，按钮对象将使用 `self` 作为在 `setAction:` 方法中提供的消息选择器的目标。

尽管我的示例适用于 `NSButton`，但此处使用的技术是完全通用的。任何 UI 控件都可以使用 target-action 模式来设置事件源和事件消费者之间的事件处理机制。这是一种非常灵活的机制，它允许不同的类库彼此通信，而无需了解它们的工作方式甚至公共接口。这种机制的一大优点是它减少了你需要维护的类和协议的数量，这与那些无法在对象之间提供动态链接的其他语言不同。

## 动态响应消息

你已经了解了类如何将通用消息发送到目标对象，甚至无需知道消息的名称。这是通过使用选择器实现的。基于类的设计的另一个重要方面是能够以灵活的方式响应发送给对象的消息。

在 Objective-C 中，有一个标准选项可以创建方法，并通过语言运行时自动将消息路由到这些方法。然而，在某些情况下，你可能希望更改对象所接受的方法的分发方式。例如，考虑一个需要将消息中继到辅助类的类。一种选择是为你想要复制的辅助类中的每个方法创建一个新方法，正如你将在下一节中看到的那样。

### 创建代理对象

例如，考虑一个名为 `MortgageSecurity` 的原始类，它负责与抵押贷款相关的计算。它的接口可以定义如下：

```
// 文件 MortgageSecurity.h
@interface MortgageSecurity : NSObject
- (void)calculateDebt;
- (void)reapraise;
- (void)calculateTaxes;
@end
```

```
// 文件 MortgageSecurity.m
@implementation MortgageSecurity
- (void)calculateDebt
{
        // 计算抵押贷款中剩余债务的代码
}
- (void)reappraise
{
         // 用于重新评估抵押贷款价值的代码
}
- (void)calculateTaxes
{
        // 计算抵押贷款税额的方法
}
@end
```

有三个方法用于计算与抵押贷款相关的数量，例如税额和总债务，以及重估价值。这是一个复杂的类，因此你对每个方法的内部工作原理不感兴趣。然而，你想要做的是为原始类提供一个简单的代理对象。这在例如你想要创建一个门面对象以便通过更易用的接口隐藏真实类时是必需的。

代理类将包含对 `MortgageSecurity` 类的引用以及重定向消息的必要代码。

```
// 文件 SecurityProxy.h
@interface SecurityProxy : NSObject
@property(retain) MortgageSecurity *security;
- (void)calculateDebt;
- (void)reappraise;
- (void)calculateTaxes;
@end
```

这里的接口与 `MortgageSecurity` 的接口非常相似。你想要提供原始类中存在的核心功能，因此你需要复制其接口。该类还包含一个属性，可用于存储指向 `MortgageSecurity` 对象的指针，以便后续用于路由传入的消息。

`SecurityProxy` 的实现包含了重定向消息调用所需的代码。

```
// 文件 SecurityProxy.m
@implementation SecurityProxy
- (void)calculateDebt
{
        [_security calculateDebt];
}
- (void)calculateTaxes
{
        [_security calculateTaxes];
}
- (void)reappraise
{
        [_security reappraise];
}
@end
```

正如你所见，`SecurityProxy` 中的每个方法都只是作为 `MortgageSecurity` 类中对应方法的前端。它们使用 `security` 属性作为消息的目标，并调用完全相同的方法。如果传入了任何参数，你还需要将这些参数提供给目标对象。

`SecurityProxy` 的客户端将无法察觉该类与 `MortgageSecurity` 之间的差异，因为它们提供了相同的接口。然而，这两个类之间的依赖关系很大。如果 `MortgageSecurity` 接口的任何细节发生变化，那么也需要在 `SecurityProxy` 中做出相应的更改。没有这样手动修改，代理类的假象就会被打破，这两个类将开始拥有不同的接口。



### 使用 forwardInvocation

如您所见，使用这种编程风格维护代理类非常耗时。不过，有一种基于 Objective-C 动态特性的更好解决方案。您需要一种方式来接收通用消息并将其路由到目标对象，而无需在接口中重复消息名称。

这种功能由**转发调用机制**提供。转发调用是运行时调用一个特殊方法 `forwardInvocation:` 的过程，用于决定如何处理类接口中未包含的消息。任何实现了 `forwardInvocation:` 方法的 Objective-C 类都能使用这种动态特性，当目标类中找不到某个方法时，运行时系统会自动调用该方法。

因此，重写上述类的一种更简单方法是使用转发调用机制来接收和处理发送给 `MortgageSecurity` 类的消息。这将产生以下接口：

```
@interface SecurityProxy : NSObject

- (void)forwardInvocation:(NSInvocation *)anInvocation;

@property (retain) MortgageSecurity *security;

@end
```

`SecurityProxy` 的新接口仍然包含一个存储 `MortgageSecurity` 对象的属性。然而，`MortgageSecurity` 同样支持的方法列表消失了，取而代之的是单一方法 `forwardInvocation:`。因此，新代理类的实现只依赖于 `forwardInvocation:` 的内部工作原理。

```
@implementation SecurityProxy

- (void)forwardInvocation:(NSInvocation *)anInvocation
{
        SEL selector = anInvocation.selector;
        if ([_security respondsToSelector:selector])
        {
                [anInvocation invokeWithTarget:_security];
        }
        else
        {
                [super forwardInvocation:anInvocation];
        }
}

@end
```

`forwardInvocation:` 的实现需要在所有 Objective-C 类中遵循类似的模式，这是因为它与消息分发系统的交互方式决定的。第一个任务是决定是否要处理该消息。为此，您可以从 `NSInvocation` 对象中检索消息的选择器。

在本例中，您要进行的测试是针对 `MortgageSecurity` 类的接口。因此，您向 security 属性发送 `respondsToSelector:` 消息，以确定该消息是否应被处理。如果该消息存在于 security 对象上，则使用 `invokeWithTarget:` 来执行方法调用。

注意：`forwardInvocation:` 方法对 `NSInvocation` 对象使用的是 `invokeWithTarget:` 而不是 `performSelector:`，即使您也有一个选择器。这种差异的原因在于 `performSelector:` 无法处理任意数量的参数。另一方面，`NSInvocation` 对象包含由运行时系统提供的参数列表，并且拥有发送消息及其参数所需的内部逻辑。此外，通过 `forwardInvocation:`，Objective-C 运行时负责将所有返回值存储在 `NSInvocation` 对象上，并将它们作为返回值传回消息调用的地方，这是使用 `performSelector:` 无法做到的。

如果 `forwardInvocation:` 收到的消息无法由 security 对象处理，那么您需要调用父类的 `forwardInvocation:`。这样做是为了不改变 `forwardInvocation` 的任何默认行为，包括 `NSObject` 上的标准实现。

## 模拟多重继承

`forwardInvocation:` 方法可用于许多情况，即某个方法的功能由另一个类提供。当这种情况发生时，使用 `forwardInvocation:` 的类负责将消息中继到目标对象，该对象最终将响应此消息。如您所见，`forwardInvocation:` 与 Objective-C 的标准方法分发机制有许多相似之处。因此，您可以将此方法视为实现自定义继承机制的一种方式。使用此类特性，您可以设计出能够响应来自两个甚至更多类消息的对象。

例如，考虑上一节讨论的 `SecurityProxy` 类。该类能够响应 `MortgageSecurity` 接口中的任何消息。从客户端的角度来看，`SecurityProxy` 充当了一种 `MortgageSecurity` 对象的角色。

然而，您可能希望进一步扩展此示例。假设您还想响应第二个接口 `FixedIncomeSecurity` 的请求，其定义如下：

```
// 文件 FixedIncomeSecurity.h

@interface FixedIncomeSecurity : NSObject

- (void)calculateAmortization;
- (void)calculateEquivalentBond;

@end
```

此接口提供了两种您希望在 `SecurityProxy` 类上支持的额外计算方法。不幸的是，Objective-C 不提供多重继承（如前一章所述），这意味着您无法使用标准消息分发机制来实现这一点。

在这种情况下，解决方案以修改使用 `forwardInvocation:` 方法的形式出现。您将扩展 `forwardInvocation:` 实现中有效选择器的测试范围，从而允许处理属于您想要支持的两个类中任一个的成员消息。以下代码展示了 `SecurityProxy` 修改后接口的内容：

```
// 文件 SecurityProxy.h

@interface SecurityProxy : NSObject

- (void)forwardInvocation:(NSInvocation *)anInvocation;

@property (retain) MortgageSecurity *mortgageSec;
@property (retain) FixedIncomeSecurity *fixIncomeSec;

@end
```

在这里，您添加了一个名为 `fixIncomeSec` 的新属性，它将保存一个指向 `FixedIncomeSecurity` 对象的指针，以便在需要时该类能够获得所需的行为。此外，修改后的类还有一个新的实现部分，如下所示：

```
// 文件 SecurityProxy.m

@implementation SecurityProxy

- (void)forwardInvocation:(NSInvocation *)anInvocation
{
        SEL selector = anInvocation.selector;
        if ([_mortgageSec respondsToSelector:selector])
        {
                [anInvocation invokeWithTarget:_mortgageSec];
        }
        else if ([_fixIncomeSec respondsToSelector:selector])
        {
                [anInvocation invokeWithTarget:_fixIncomeSec];
        }
        else
        {
                [super forwardInvocation:anInvocation];
        }
}

@end
```

`forwardInvocation:` 中的代码已更改，以提供对 `FixedIncomeSecurity` 中方法的支持。这是以类似于之前为 `MortgageSecurity` 方法实现的方式完成的。`fixIncomeSec` 属性使用 `respondsToSelector:` 消息进行测试。最后，无法识别的消息被提交给 `NSObject` 中的默认 `forwardInvocation:` 方法。



### 实现 `respondsToSelector:`

请注意，尽管上述实现提供了多继承的许多优点，但仍需谨慎，不要忘记其中的重要差异。例如，`SecurityProxy` 与你在本节中看到的两个安全类之间并不存在父子关系。因此，编译器将无法进行在使用单一超类派生类时所允许的标准类型检查。

另一方面，Objective-C 的运行时可以通过使用动态消息来进一步调整类之间的关系。之前已经遇到过其中一种消息：`respondsToSelector:`，它用于判断一个对象是否能够响应特定消息。

你一直在使用 `respondsToSelector:` 来判断一个对象是否能够通过其对应的选择器来响应某个特定的方法调用。然而，如果另一个对象以 `SecurityProxy` 为目标进行相同的方法调用，它将收到否定响应。结果为 `NO`，因为这些方法没有一个真正属于 `SecurityProxy` 所支持的消息列表。

如果你希望为 `SecurityProxy` 增加这一层额外的支持，可以决定修改 `respondsToSelector:` 的实现来解决此问题。一种可行的实现方式如下所示：

```objc
// 文件 SecurityProxy.h

@interface SecurityProxy : NSObject

- (void)forwardInvocation:(NSInvocation *)anInvocation;
- (BOOL)respondsToSelector:(SEL) sel;

@property (retain) MortgageSecurity *mortgageSec;
@property (retain) FixedIncomeSecurity *fixIncomeSec;

@end
```

接口已被修改以容纳新方法 `respondsToSelector:`，该方法将用于判断某个消息能否被 `SecurityProxy` 类处理。此方法将覆盖现有的标准实现，该标准实现仅对已定义为类接口一部分的方法返回 `true`。

```objc
// 文件 SecurityProxy.m

@implementation SecurityProxy

- (BOOL)respondsToSelector:(SEL) sel
{
    if ([super respondsToSelector:sel])
    {
        return YES;
    }
    if ([_fixIncomeSec respondsToSelector:sel]
        || [_mortgageSec respondsToSelector:sel])
    {
        return YES;
    }
    return NO;
}

- (void)forwardInvocation:(NSInvocation *)anInvocation
{
    SEL selector = anInvocation.selector;
    if ([_mortgageSec respondsToSelector:selector])
    {
        [anInvocation invokeWithTarget:_mortgageSec];
    }
    else if ([_fixIncomeSec respondsToSelector:selector])
    {
        [anInvocation invokeWithTarget:_fixIncomeSec];
    }
    else
    {
        [super forwardInvocation:anInvocation];
    }
}

@end
```

`respondsToSelector:` 的新实现现在不仅能够检查编译时定义的方法（使用 `NSObject` 提供的标准实现），还能检查另外两个对象 `mortgageSec` 和 `fixIncomeSec`，以确定该方法对 `SecurityProxy` 类是否有效。以下是一个示例，展示了客户端代码如何使用此特性：

```objc
- (void) testSecurityMethods
{
    SecurityProxy *sp = [[[SecurityProxy alloc] init] autorelease];
    sp.mortgageSec = [[[MortgageSecurity alloc] init] autorelease];
    sp.fixIncomeSec = [[[FixedIncomeSecurity alloc] init] autorelease];

    // 此方法在编译时定义
    BOOL res = [sp respondsToSelector:@selector(forwardInvocation:)];
    NSLog(@"结果为 %d", res); // 结果为 1

    // 此方法动态定义
    res = [sp respondsToSelector:@selector(calculateEquivalentBond)];
    NSLog(@"结果为 %d", res); // 结果为 1
}
```

这段代码整合了之前描述的类，并展示了如何使用它们来回答关于自身能力的简单问题。方法 `testSecurityMethods` 首先创建了一个 `SecurityProxy` 类的实例。为了使该对象正常工作，它需要另外两个对象：一个 `MortgageSecurity` 实例和一个 `FixedIncomeSecurity` 实例。这两个实例分别被赋值给 `SecurityProxy` 的对应属性 `mortgageSec` 和 `fixIncomeSec`。最后，向 `SecurityProxy` 实例发送 `respondsToSelector:` 消息，第一次查询 `forwardInvocation:`，这是一个确实在公共接口中定义的方法。而第二次请求则检查 `calculateEquivalentBond` 方法，该方法属于 `FixedIncomeSecurity` 类。两次请求都返回 `YES`，正如你在设计新 `respondsToSelector:` 方法时所期望的那样。



## 避免方法调用

如果你是一位经验丰富的 C 语言程序员，在了解本章描述的所有动态特性后，你可能会对 Objective-C 中消息传递的性能产生一些想法。毕竟，你知道运行时会通过搜索目标对象所属的类层次结构来尝试找到方法的实现。此外，如果搜索失败，运行时还会尝试使用 `forwardInvocation:` 方法，让对象有机会“再次响应”该消息。

所有这些运行时行为无疑都会带来开销。好消息是，苹果公司一直在优化这种搜索机制的实现。即便如此，在某些情况下，使用标准动态派发的成本仍然过高。事实证明，Objective-C 也针对此问题提供了合理的解决方案，其基础仍然是巧妙地使用选择器来获取方法的实现。

要理解如何在某些情况下最小化甚至避免方法派发的开销，有必要了解方法是如何实现的。方法实现本质上就是一个带有两个特殊参数的常规 C 函数：第一个参数是指向对象的指针，也就是你所熟知的 `self`。第二个参数是方法调用中使用的选择器，任何方法都可以访问它，并能通过 `_cmd` 特殊变量来引用。

当你在类的 `@implementation` 部分创建方法时，这两个参数是隐藏的，但它们始终存在。事实证明，你可以获取到方法派发时实际被调用的 C 函数的指针。运行时使用 `IMP` 类型来引用这种实现函数。对 `IMP` 函数指针的自动处理，只是语言在编译时为我们安排的众多细节之一。

为了了解如何利用这个特性来提升性能，让我们看一个例子。`UseIMP` 类有一个名为 `simpleMethod:` 的方法，它仅将整数值打印到日志屏幕。

```
// 文件 UseIMP.h

@interface UseIMP : NSObject

- (void) simpleMethod:(int)param;

- (void) callSimpleMethod;

@end
```

在该类的第一个版本中，有一个名为 `callSimpleMethod` 的方法，它会重复调用 `simpleMethod`。

```
// 文件 UseIMP.m

@implementation UseIMP

- (void)simpleMethod:(int)param

{

// 这里是实现代码

NSLog(@"method called with value %d", param);

}

- (void) callSimpleMethod

{

int i;

for (i=0; i<10; ++i)

{

[self simpleMethod:i];

}

}

@end
```

这个类本身没有问题，但仍有性能提升的空间。主要问题在于，在循环内调用同一个方法会迫使运行时系统重复执行相同的派发序列。虽然对于少量的重复来说这不算什么，但随着迭代次数的增加，开销会变得非常高。

为了避免这个问题，你可以直接获取 `simpleMethod:` 的实现函数，并将其存储到一个 `IMP` 变量中。新的接口将增加一个方法。

```
// 文件 UseIMP.h

@interface UseIMP : NSObject

- (void) simpleMethod:(int)param;

- (void) callSimpleMethod;

- (void) callWithImplementation;

@end
```

`callWithImplementation` 的代码如下所示：

```
typedef void (*MY_FUNC)(id, SEL, int);

- (void) callWithImplementation

{

SEL selector = @selector(simpleMethod:);

IMP imp_func = [self methodForSelector:selector];

MY_FUNC myFunc = (MY_FUNC)imp_func;

int i;

for (i=0; i<10; ++i)

{

myFunc(self, selector, i);

}

}
```

首先，我们为一个类型定义了一个名称，该类型表示一个接收 `id`、选择器和一个整数的 `void` 函数。在 `callWithImplementation` 的定义中，首先获取 `simpleMethod:` 的选择器。然后，使用 `methodForSelector:` 获取实现，这个方法返回一个 `IMP`（实际上只是一个函数指针）。接着，我们将这个函数指针转换为一个可以直接使用所需参数调用的类型，并将其存储在 `myFunc` 变量中。一旦函数指针被赋值，就可以用它来直接调用 `simpleMethod` 的内容。

请注意，通过直接调用 `methodForSelector:` 返回的函数指针，你绕过了整个动态派发机制。动态派发仅在调用 `methodForSelector:` 时运行一次。在之后的过程中，你所做的只是对函数的直接指针访问，其执行速度几乎与普通函数调用相同。因此，这段代码的性能远优于对 `simpleMethod:` 进行重复消息调用。随着重复次数的增加，运行时性能的差异会变得更加显著。

最后，这里有一些测试 `UseIMP` 类的示例代码：

```
- (void) testUseImp

{

UseIMP *useImp = [[[UseIMP alloc] init] autorelease];

[useImp callWithImplementation];

}
```

调用 `testUseImp` 会在日志窗口中依次显示十行文本，正如预期的那样。

总的来说，上面的例子为你提供了一个基于抑制动态消息派发来提升性能的基本思路。如果你有一段需要优化的代码，可以遵循以下步骤：

-   确定是否可以消除动态派发。当在特定上下文中，同一消息被大量发送给同一个对象时，这通常是可行的。为了有一个更好的基线，请使用性能分析器来确定你的代码大部分时间花在了哪里。
-   一旦选定了某个方法，就可以使用 `methodForSelector:` 来获取该特定对象的 `IMP` 指针。请注意，`methodForSelector:` 本身使用的是运行时系统来查找目标方法的实现，因此结果保证是正确的。
-   使用上面获取的 `IMP` 函数进行直接函数调用。可以通过直接传递两个隐藏参数（`self` 和方法选择器）以及方法所需的任何其他参数来完成。

在应用内部循环中，对少数方法调用重复此过程后，就有可能显著提升 Objective-C 代码的性能。然而，由于使用此技术需要进行底层操作，除非你的应用程序存在真正的性能瓶颈，否则应避免使用它。请记住，对于大多数桌面软件来说，只有在极少数情况下，Objective-C 代码的性能才会成为需要这样处理的问题。



## 总结

在本章中，我讨论了 Objective-C 提供的一些动态派发特性。拥有这些特性是使用具备丰富运行时系统的语言的优势之一。通过动态派发，不仅能够从基于单继承的标准消息派发系统中获益，还能在必要时突破其某些规则。

你了解了选择器（selector）的工作原理，并通过示例看到如何利用它们使类更具通用性，减少对特定接口的依赖。我们讨论了一些在 Cocoa 框架中使用选择器的场景，这些场景在客户端与服务之间建立连接，而无需在两者之间添加任何显式的编译时引用，从而允许它们在运行时进行连接。

我在本章中还讨论了 `forwardInvocation:` 方法，它为程序员提供了一种干净的方式，用以处理在编译时无法识别的消息。利用这一机制，可以拦截那些已被接收但未与类接口中任何成员匹配的消息。

通过 `forwardInvocation:`，你可以决定将无法识别的消息发送给能够正确响应的其他对象。你了解到，当这种情况发生时，一个类实际上采纳了另一个类的接口。其结果可被理解为一种模拟的多继承形式，在这种情况下，你可以有选择地从两个或更多类中采纳所需的接口部分。

至此，你已经创建了许多对象，但并未过多考虑它们在使用结束后如何存储或释放。尽管 Objective-C 没有自动垃圾回收机制（至少默认情况下没有），但它拥有强大内存管理模式，可帮助程序员完成节约内存这一重要任务。这将是下一章的主题。

