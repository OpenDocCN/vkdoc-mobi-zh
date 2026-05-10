# 18. 未来路径

## 摘要

你现在已经读完了《在 Mac 上学习 Cocoa》的最后一章！希望你已经对 Cocoa 的工作原理以及如何利用其各个组件编写各种有趣的桌面应用有了良好的理解。然而，Cocoa 框架其实非常庞大，我们仅仅触及了所涉及的大部分类和概念的皮毛。这本书从未打算成为一本百科全书式的 Cocoa 参考手册（你的电脑上已经随 Xcode 安装了这样的手册），而是旨在成为一本为你指引方向的指南。秉承这一精神，我们将通过概述一些额外的技巧和资源来结束本书，这些内容将帮助你在已学知识的基础上，将 Cocoa 开发工作提升到新的高度。

我们将从详细阐释几个我们曾提及但值得更多关注的设计模式开始。接着，我们将探讨如何使用 Objective-C 以外的语言进行 Cocoa 编程。最后，我们将介绍一些方法，让你能将来之不易的 Cocoa 技能应用到 Mac OS X 桌面以外的领域。

## 更多 Cocoa 特性

在本书中，我们花了大量篇幅讨论 MVC 模式，它帮助我们将应用程序划分为逻辑层；以及委托模式，它允许我们在控制器层内定义某些 GUI 对象的行为，而无需对 GUI 对象本身进行子类化。这些技术利用语言特性和约定来定义使用模式。沿着这个思路，Cocoa 还有更多锦囊妙计。其中之一是**通知**（在某些圈子中被称为*观察者模式*）的概念，它允许一个对象在发生某事件时通知一组其他对象。另一个是使用**块**来简化代码，特别是在使用完整的委托甚至单个方法都可能显得大材小用的时候。我们在本书的其他地方已经见过块的使用，但这里我们也将涉及一些其他有趣的用途。

### 通知

Cocoa 的`NSNotification`和`NSNotificationCenter`类提供了一种方法，让一个对象可以向一组其他对象发送消息，而无需这些对象之间互相了解。它们真正需要知道的只是通知的名称，这个名称可以是任何我们喜欢的字符串。想要被通知的对象会提前注册为特定通知名称的观察者，而想要广播通知的对象则使用该名称向任何正在监听的观察者发送其消息。

例如，假设我们应用程序的多个部分需要在某个特定事件发生时更新，比如一段网络代码读取来自 Web 服务器的响应。使用通知，我们的网络代码不需要知道其他每个想要该信息的对象。相反，我们可以为网络代码和观察者定义一个共同使用的通知名称，最好放在一个所有相关类都能包含的头文件中，如下所示：

```
#define DATA_RECEIVED @"dataReceived"
```

观察者可以在任何时候注册接收通知，但通常这发生在对象的初始化过程中。

```
- (id)init
{
    if ((self = [super init])) {
        [[NSNotificationCenter defaultCenter] addObserver:self
                                                 selector:@selector(receiveNetworkData:)
                                                     name:DATA_RECEIVED
                                                   object:nil];
    }
    return self;
}
```

这告诉应用程序唯一的`NSNotificationCenter`实例：当有人发布一个`DATA_RECEIVED`通知时，它应该通过调用调用者的`receiveNetworkData:`方法来通知它。注意最后一个参数我们传递的是`nil`：如果我们在此处指定了一个对象，那将会把通知观察限制为仅适用于该特定对象。其他任何发布相同通知的对象都不会产生任何效果。为了使它工作，观察者还需要实现注册时指定的方法。这个方法总是接收`NSNotification`本身作为参数。

```
- (void)receiveNetworkData:(NSNotification *)notification
{
  NSLog(@"received notification: %@", notification);
}
```

最后，任何将自己设置为观察者的对象通常应在稍后将自己从观察者列表中移除。一个常见的做法是在任何注册为观察者的类的`dealloc`方法中执行类似以下操作：

```
- (void)dealloc
{
    [[NSNotificationCenter defaultCenter] removeObserver:self];
}
```

那么，通知者本身呢？以我们的情况为例，就是那个将要广播其状态的网络读取对象。处理起来再简单不过了：

```
if (some condition is met) {
    [[NSNotificationCenter defaultCenter]
        postNotificationName:DATA_RECEIVED
                      object:self];
}
```

其思想是通知者可以像这样抛出一个通知，而通知中心负责实际的传递。通知者还可以通过一个字典传递额外的信息，观察者可以获取这些信息，如下所示：

```
// 在通知者中
NSDictionary *info = @{@"data" : someData};
[[NSNotificationCenter defaultCenter]
    postNotificationName:DATA_RECEIVED
                  object:self
                userInfo:info];

// 在观察者中
NSLog(@"received data %@", [[notification userInfo] objectForKey:@"data"]);
```

通知是一种非常实用的方式，可以让应用程序的不同部分在不使用正式 API 的情况下相互通信。我们只需要决定一些字符串来表示各种事件，然后开始观察和发布即可。

### 块

第 11 章和第 17 章介绍了**块**，这是苹果几年前为 C 语言添加的一个特性。我们主要将块与 Grand Central Dispatch 提供的并发特性一起使用，块在其中非常契合，但块还有许多其他用途。似乎在 OS X 的每个主要新版本中，苹果都会扩展几个 Cocoa 类，添加以块为参数的新方法。我们来看看其中的一些。



### 枚举

让我们从简单的开始：枚举。你可能已经熟悉了标准的基于 C 语言的遍历列表方式，也许也用过 `NSEnumerator`，甚至还有自 OS X 10.5 发布以来就提供的快速枚举（“for – in” 循环）。现在，Block 提供了另一种实现相同功能的方式。

```
NSArray *array = @[@"zero", @"one", @"two", @"three"];

// C 风格枚举
int i;
for (i = 0; i < [array count]; i++) {
    NSLog(@"C 枚举访问对象：%@", array[i]);
}

// NSEnumerator，经典的 Cocoa 枚举方式
NSEnumerator *aEnum = [array objectEnumerator];
id classicEnumerated;
while ((classicEnumerated = [aEnum nextObject])) {
    NSLog(@"NSEnumerator 访问对象：%@", classicEnumerated);
}

// "快速枚举"，随 OS X 10.5 发布
id fastEnumerated;
for (fastEnumerated in array) {
    NSLog(@"快速枚举访问对象：%@", fastEnumerated);
}

// "Block 枚举"，随 OS X 10.6 发布
[array enumerateObjectsUsingBlock:^(id blockEnumerated, NSUInteger i, BOOL *stop) {
    NSLog(@"Block 枚举访问对象：%@", blockEnumerated);
}];
```

我们传递给 `enumerateObjectsUsingBlock:` 的 Block 接受三个参数且无返回值。这是该方法声明的 Block 签名，我们必须遵循。传入 Block 的三个参数分别是：数组中的一个对象、该对象在数组中索引的整数值，以及一个通过引用传递的 `BOOL` 值，通过将其设置为 `YES` 可终止枚举。

这样看来，一开始可能并不明显为什么 Block 版本比其他方式更好，但实际上它确实融合了其他所有枚举方式的优点。首先，它提供了当前对象的索引，这是之前基于 Objective-C 的其他方案所不具备的。如果我们想打印数组元素的编号列表，这个特性就非常有用。我们无法告诉你我们为了轻松获取每个对象的索引值而不得已使用 C 风格迭代的次数有多少！此外，该方法还有一个变体，允许我们指定控制枚举运行方式的选项，例如使其并发运行，如下所示：

```
[array enumerateObjectsWithOptions:NSEnumerationConcurrent
    usingBlock:^(id blockEnumerated, NSUInteger i, BOOL *stop) {
    NSLog(@"Block 枚举访问对象：%@", blockEnumerated);
}];
```

这个方法实际上使用了 GCD 将工作分配到所有可用的处理器核心上，这会使我们的应用运行得更快，并更好地利用系统资源。而且我们无需额外成本就能获得这一好处！

`NSSet` 类也存在类似的枚举方法，但没有索引参数（因为根据定义，集合中的对象是无序的）。`NSDictionary` 也通过一些新的方法（如 `enumerateKeysAndObjectsUsingBlock:` 及其支持并发的带选项变体）获得了良好的 Block 支持，让我们可以指定一个同时获取键和值的 Block。这比之前遍历字典内容的枚举方式要好得多，后者通常需要遍历所有键并为每个键查找对应的值。

### 使用 Block 观察通知

我们知道，我们在前几页才刚向你介绍了通知，但猜猜怎么着：苹果已经将 Block 概念应用到了 `NSNotification` 类中，该类提供了一个方法，允许我们指定一个 Block 而不是选择器，如下所示：

```
id observe = [[NSNotificationCenter defaultCenter] addObserverForName:DATA_RECEIVED
  object:nil queue:nil usingBlock:^(NSNotification *notification){
  NSLog(@"收到通知：%@", notification);
}];
```

这在几个方面都很酷。首先，它使我们无需为通知处理代码创建单独的方法，而是可以将处理逻辑内联到设置代码的位置，这可以使我们的代码更易于阅读。另一个很棒的地方（所有 Block 的共同特点）是，因为我们创建的 Block 会从其定义位置捕获上下文，所以它不仅可以访问实例变量，还可以访问在同一方法中更早定义的局部变量。这意味着我们可以在设置时使用一些值来创建 Block，并在稍后 Block 运行时使用这些值，而无需显式地将它们放入实例变量或通过其他手动方式传递。

请注意，此版本返回一个 `id` 类型的值，我们将其赋给一个名为 `observe` 的变量。Block 通知观察者需要像其他观察者一样被移除，因此我们可以在适当的时候调用以下代码来利用这个返回值进行清理：

```
[[NSNotificationCenter defaultCenter] removeObserver:observe];
```

以这种方式使用 Block 时需要注意的一点是，在 `dealloc` 方法中进行清理可能不够好；如果 Block 显式或间接地使用了 `self` 指针，则会产生一种循环引用，导致对象永远无法被释放！这引出了我们的下一个 Block 话题。



#### 弱化 Blocks 的指针

回到上一个话题，假设我们有一个类，包含一个名为 `observation` 的实例变量，以及如下的 `init` 和 `dealloc` 方法：

```
@implementation MyController {
    id observation;
}

- (id)init {
    if ((self = [super init])) {
        observation = [[NSNotificationCenter defaultCenter]
            addObserverForName:@"ESCHATON"
            object:nil
            queue:nil
            usingBlock: ^(NSNotification *notification){
                NSLog(@"notified observer: %@", self);
            }];
    }
    return self;
}

- (void)dealloc {
    [[NSNotificationCenter defaultCenter] removeObserver:observation];
}

@end
```

乍一看这没问题，但其中隐藏着一个微妙的问题。这种设置会形成一种循环引用链，这意味着 `MyController` 实例和 `observation` 对象都将永远不会被释放！原因在于，当执行 `init` 并创建 block（我们将其传递给 `NSNotificationCenter`）时，会发生一系列特殊事件。在底层，ARC 会确保 block 内访问的所有变量都被设置为“block 就绪”状态。这意味着对于普通的 C 类型（`int`、`float` 等），会在 block 内准备一个新的只读变量槽；而对于 Objective-C 对象指针，则会发送一条 `retain` 消息，从而在 block 的整个生命周期内增加其引用计数。

与此同时，赋值给 `observation` 的返回值也会被 ARC 妥善处理，它也会收到一条 retain 消息，以确保其不会消失。最终的结果如下：

-   `MyController` 实例持有对 `observation` 的强引用。
-   `observation` 对象持有对其 block 的强引用，因为它“拥有”该 block。
-   block 持有对我们 `MyController` 实例的强引用。

打破这个循环的方法是将其中一个强引用转为弱引用。最好的做法是如下修改 `init`：

```
- (id)init {
    if ((self = [super init])) {
        __weak id weakSelf = self;
        observation = [[NSNotificationCenter defaultCenter]
            addObserverForName:@"ESCHATON"
            object:nil
            queue:nil
            usingBlock: ^(NSNotification *notification){
                NSLog(@"notified observer: %@", self);
                NSLog(@"notified observer: %@", weakSelf);
            }];
    }
    return self;
}
```

这里我们声明了一个指向 `self` 的弱指针，存储在一个名为 `weakSelf` 的新变量中。这个新指针在 block 内部使用。当编译器看到这一点时，它会理解这是一个弱指针，因此不应被 block 保留。这意味着 `self` 永远不会收到额外的 `retain` 消息，因此它可以在不再使用时被释放。当 `dealloc` 方法执行时，`observation` 对象被释放，它拥有的 block 也随之被释放。

还有一点很重要：一个 block 会捕获其代码中引用的任何对象，但在实例变量的情况下，被保留的并不是实例变量本身，而是 `self` 指针！这是因为实例变量是通过 C 风格的结构体访问的，尽管我们在代码中看不到这一点。实际上，

```
[tableView reloadData];
```

本质上是等同于

```
[self->tableView reloadData];
```

鉴于此，编译器的 ARC 规则要求 `self` 必须被保留。

尽管我们可能不会每天都遇到这种情况，但理解这个机制的工作原理以及我们为何必须采用这种变通方法仍然很重要。那些会被立即执行并释放的 block（例如，与枚举器一起使用时）可能不需要如此细致的审视。然而，一旦我们将 block 传递给另一个对象，并且我们知道它可能是一个长期存在的对象，或者不确定其生命周期时，就应该格外小心地检查我们的 block，确保不会意外地给自己埋下隐患。

#### 过滤

Apple 添加到 Cocoa 中的另一个 block 用法是 `NSArray` 的 `indexesOfObjectsPassingTest:` 方法。这个方法让我们声明一个 block，该 block 会检查一个对象，并根据我们定义的条件，决定该对象是否应被包含在输出的索引集合中（然后可以用于从原始数组中提取“符合条件”的对象）。例如，假设我们有一个人员数组，我们可以这样找出所有名为“Bob”的人：

```
NSArray *people;  // <- 假设此数组已存在

NSIndexSet *bobIndexes = [people indexesOfObjectsPassingTest:
    BOOL ^(id obj, NSUInteger idx, BOOL *stop){
        return [obj.firstName isEqual:@"Bob"];
}];

NSArray *bobs = [people objectsAtIndexes:bobIndexes];
```

尽管 block 的使用起初可能看起来有些棘手，但过一段时间后它们就会变得自然而然。一旦开始使用，你可能会发现越来越多的方式去运用它们。对于每一位 Cocoa 程序员来说，它们都是一个非常重要的工具。

## 用其他语言开发 Cocoa

尽管我们中的一些人热爱 Objective-C，但它并非唯一的选择，有些人更愿意使用其他语言来开发 Cocoa 应用。或许你有一个特定的代码库想要加以利用，或者你只是更喜欢其他某种语言。好消息是，存在一些语言能够很好地与 Objective-C 交互，通过使用该语言与 Objective-C 之间的所谓“桥接”，从而允许进行一定的 Cocoa 开发。

而坏消息是（至少对某些人来说），两种最流行、最广泛使用的语言（C++ 和 Java）并不在此列。你可能会好奇这是为什么。简而言之：C++ 和 Java 过于僵化。它们缺乏与像 Cocoa 这样复杂的 Objective-C 类库进行完全交互所需的运行时内省能力。或许从技术角度看，Java 具备这种能力。实际上，Apple 在 Mac OS X 的最初几个版本中包含了一个用于构建 Cocoa 应用的 Java 桥接。但程序员们并未趋之若鹜，再加上实现和维护该 Java 桥接所面临的技术挑战，使得这件事对他们来说得不偿失，因此 Apple 在几年前放弃了这个项目。而且，由于我们可以通过 Objective-C++ 的形式将 Objective-C 和 C++ 结合起来，对桥接的需求也因此有所降低。当然也存在一些实际限制。例如，我们无法以 C++ 类的形式实现一个 Objective-C 的委托，因此需要创建一些“胶水类”，通常成对出现在边界两边（一个 C++ 类，一个 Objective-C 类），它们各自能够处理自己的世界，并为对方翻译内容。这种方法可行，但编写和维护这样的代码并不愉快。

再回到好消息方面，一批流行的脚本语言（Ruby、Python 和 JavaScript）拥有稳定且可用的桥接，让我们能够使用它们进行真正的 Cocoa 开发。



### PyObjC

最早实现非 Objective-C 语言与 Cocoa 框架桥接的项目之一是 `PyObjC`，它让我们能够用 Python 进行大量的 Cocoa 开发。正如你所知，Objective-C 的语法（尤其是方法名和参数的混合方式）有些特别，而大多数其他语言都没有与之对应的结构。`PyObjC` 所做的是为 Cocoa 中包含的所有 Objective-C 类中的所有方法提供映射，以便我们可以从 Python 中调用它们。这些映射由一个相当简单的公式决定：整个 Objective-C 方法名被合并成一个符号，方法名中的每个冒号都被替换为下划线字符。所有方法参数都放在圆括号之间，就像标准 C 函数一样。所有对象都会被自动桥接，这样我们就可以直接从 Python 使用所有标准的 Cocoa 类。例如，让我们看一行关于通知的代码。这是 Objective-C 版本：

```
[[NSNotificationCenter defaultCenter]
  postNotificationName:DATA_RECEIVED object:self];
```

使用 `PyObjC` 的 Python 版本如下所示：

```
center = NSNotificationCenter.defaultCenter()
center.postNotificationName_object_(DATA_RECEIVED, self);
```

如你所见，缺少了混合的方法名部分和参数确实影响了可读性。此外，整体上它相当“不 Python 化”，所以虽然它在其功能上运行良好，但各方都会有些不满意的地方。

`PyObjC` 似乎在几年前就达到了稳定状态，过去几个版本主要集中在错误修复上。可以从 `http://packages.python.org/pyobjc/` 获取的版本状态良好，并已用于实际项目。

### MacRuby

在 Ruby 方面，有几种桥接到 Objective-C 的方法。多年来，一个名为 `RubyCocoa` 的项目一直可用，它在许多方面与 `PyObjC` 的工作方式类似。然而，`RubyCocoa` 似乎已经停滞不前，大部分发展势头已经转向了一个名为 `MacRuby` 的较新项目。这个由苹果赞助的项目旨在以完全不同的方式将 Ruby 语法引入 Cocoa。`MacRuby` 不是像 `RubyCocoa` 那样在相似类之间进行桥接，而是采取了一种不同的方法，基本上抛弃了所有现有的 Ruby 标准库类，转而使用等价的 Cocoa 类，并且通常为它们提供名称与 Ruby 世界中等价类匹配的新方法。这意味着，经验丰富的 Ruby 开发者在接手 `MacRuby` 项目时，可能会发现他们喜爱的许多类和方法都缺失或略有不同。

`MacRuby` 与 `PyObjC` 和 `RubyCocoa` 的一个有趣区别是，`MacRuby` 会额外努力使方法调用的感觉更接近于它们在 Objective-C 中的方式，同时仍然在 Ruby 语法中工作。这是通过巧妙地使用 Ruby 的关键字参数来实现的，即使用被调用的 Ruby 方法名和参数键来查找匹配的 Objective-C 方法。回到之前的例子：

```
[[NSNotificationCenter defaultCenter]
  postNotificationName:DATA_RECEIVED object:self];
```

在 `MacRuby` 中是这样写的：

```
center = NSNotificationCenter.defaultCenter
center.postNotificationName(DATA_RECEIVED, object:self)
```

当 `MacRuby` 执行第二行时，它会使用方法名（`postNotificationName`）和参数键（`object`）在接收者（`center`）中查找一个名为 `postNotificationName:object:` 的 Objective-C 方法，找到它，并调用它。如果我们没有为 `object` 包含关键字参数，或者包含了其他不属于我们目标底层方法的关键字参数，那么这个方法调用将找不到匹配的 Objective-C 方法并失败。

除此之外，`MacRuby` 还提供了一些其他有趣的功能，例如编译为本地代码，包括即时编译和提前编译。它也已经脱离了传统的 Ruby 虚拟机，转而采用基于 LLVM 构建的新运行时，你们中的一些人会认出这是 Xcode 可以使用的、最新式的编译器之一。

在撰写本文时，`MacRuby` 尚未发布 1.0 版本，但通过 `www.macruby.org` 关注它绝对值得。

### Nu

在这种背景下，另一个有趣的语言是 `Nu`。与 `PyObjC` 和 `MacRuby` 不同，这里并非是将现有语言与 Objective-C 对接的问题。相反，`Nu` 本质上是一种全新的语言（实际上是 Lisp 的一种变体），专为与 Objective-C 互操作而设计，因此它具备同样的交错方法和参数语法。让我们再看一次那个例子。Objective-C：

```
[[NSNotificationCenter defaultCenter]
  postNotificationName:DATA_RECEIVED object:self];
```

`Nu`：

```
((NSNotificationCenter defaultCenter)
  postNotificationName:DATA_RECEIVED object:self)
```

如何！当然，它并非完全一致。即使忽略从方括号到圆括号的变化，在声明类、方法等方面还有许多其他语法变化，这通常源于 `Nu` 作为基于 Lisp 的语言的起源。然而，总的来说，这里有很多东西是 Objective-C 开发者一眼就能认出来的。

除了语言本身，`Nu` 还包括一个命令行 shell（`nush`）、一个类似 `make` 的工具（`nuke`）等等。这些工具组合成一个包，让我们可以启动一个 shell，并使用我们熟悉和喜爱的 Cocoa 类进行一些即兴脚本编写。我们可以与活动对象交互，并采用探索性方法使用 Cocoa 框架。前往 `http://programming.nu` 下载并了解更多关于这个 Cocoa 与 Lisp 之间的有趣结合。



### JavaScript

从某种意义上看，JavaScript 是这里的一个异类。众所周知，JavaScript 可用于编写网页脚本，但你是否知道也可以用脚本来控制你的应用？Mac OS X 自带的 `WebKit` 框架虽然不允许我们完全用 JavaScript 创建整个应用，但它允许我们包含一个脚本层（无论是否带有网页视图），让熟悉 JavaScript 的人能够定义应用中的部分行为。

这一点我们最有可能结合网页视图来使用。通过使用 WebKit 中的 `WebView` 类，我们可以“穿透”到 `WebScriptObject` 对象，这使我们能够从 Objective-C 调用 JavaScript 函数，同时也能将 Objective-C 对象暴露给 JavaScript 解释器，以便 JavaScript 代码可以通过类似 PyObjC 中所描述的方法到函数的映射机制来调用 Objective-C 方法。当我们希望使用 `WebView` 来布局图形用户界面（GUI），但又想在 GUI 中包含一些能够影响 JavaScript 环境之外事物的控件时，这一点尤为有用。

但别急，还有更多功能！除了 WebKit 提供的 JavaScript 支持外，还有一个名为 `JSCocoa` 的第三方方案，它使用与 WebKit 相同的 JavaScript 引擎（`JavaScriptCore`），将 JavaScript 与 Cocoa 完全桥接起来。这使我们能够用 JavaScript 编写完整的 Cocoa 应用，就像用 Python 或 Ruby 一样。`JSCocoa` 的一个优良特性（与本章最后一节描述的 Cappuccino 共享此特性）在于，除了处理 JavaScript 风格函数名与 Objective-C 方法名之间的转换外，`JSCocoa` 实际上还允许我们使用一种与 Objective-C 惊人相似的语法来编写 JavaScript。

```
[[NSNotificationCenter defaultCenter]
  postNotificationName:DATA_RECEIVED object:self];
```

`JSCocoa`:

```
[[NSNotificationCenter defaultCenter]
  postNotificationName:DATA_RECEIVED object:self]
```

没错——唯一的区别就是缺少了分号，因为在一行 JavaScript 代码中，如果行尾本身就是一个有效的语句结束位置，我们就不需要在行末加分号。我们第一个承认这看起来像是一个 JavaScript 奇迹：我们不具备足够的 JavaScript 知识，丝毫不知道这个技巧是如何实现的，而且可能永远也不会知道，但这没关系。要了解更多信息，请访问 [`http://inexdo.com/JSCocoa`](http://inexdo.com/JSCocoa)。

### F-Script

任何关于 Cocoa 编程中替代语言的讨论，如果不提及 F-Script 都是不完整的。与大多数最终旨在让我们用不同语言构建 Cocoa 应用的语言桥接方案不同，F-Script 似乎有几个互补的目的。首先，我们可以将 F-Script 嵌入到我们的应用中，为应用提供一种简单的方式来添加用户脚本功能，包括用于输入和试用基于 Smalltalk 语言（因此与 Objective-C 非常相似）的 F-Script 脚本的 GUI 组件。

F-Script 的另一个，也许是更有用的特性，是它以交互式命令 shell 的形式为我们（开发者）提供的工具，让我们能够探索正在运行的应用。这包括一个图形浏览器，让我们可以在应用的对象之间导航，检查它们的属性，遍历它们与其他对象的关系等等。这就像是将 Xcode 调试器的交互性提升到了极致。

不仅如此，我们还可以将 F-Script 注入到任何正在运行的 Cocoa 应用中，并使用相同的工具来探索其中的对象！虽然在没有目标应用源代码的情况下，此功能用处不大，但能够浏览其他应用的结构可能极具教育意义——同时也会很有趣。所有 Cocoa 开发者都应该对这个工具有所了解。请访问 [`http://www.fscript.org`](http://www.fscript.org) 亲自尝试。

## 移植的 Cocoa

本书主要介绍制作 Mac 软件（标题中已明确指出），但如果我们不指出 Cocoa 及类似 Cocoa 技术可能应用于的其他领域，那对您来说将是一种损失。

### Cocoa Touch

如今，随着 iPhone 开发的迅猛发展，想必阅读本书的每位读者都了解一些 iPhone 开发模式，并且知道它基于 Objective-C。事实上，很有把握地说，你在尝试 Mac 开发之前很可能已经做过一些 iPhone 开发。尽管如此，仍值得提及 iPhone 的 Cocoa Touch 框架以及它与我们一直在介绍的 Cocoa 框架之间的区别。

Cocoa 的核心类分布在两个框架中：`Foundation` 包含所有在各种开发中都有用的基础类（如 `NSString`、`NSArray` 等），而 `AppKit` 则包含所有专门用于桌面 GUI 应用开发的类（如 `NSApplication`、`NSView`、`NSWindow` 等）。在 iPhone 的 Cocoa Touch 中，`Foundation` 框架基本相同，但 `AppKit` 消失了，取而代之的是 `UIKit`。

`UIKit` 的许多基本概念与 AppKit 类似，但做了一些调整，使其能轻松运行在小尺寸、支持加速计、触摸屏的设备上。每个应用都有一个 `UIWindow`，所有绘制都在其中进行，并且该 `UIWindow` 包含一个 `UIView` 子类（如 `UIButton`、`UITextField` 等）的层级结构。虽然 AppKit 唯一的原生用户输入方法是鼠标和键盘，但 `UIKit` 也提供了类似的基于事件的处理，用于处理屏幕上的多点触摸以及设备的移动。

在其他一些方面，`UIKit` 有点像是“AppKit 2.0”。苹果借机舍弃了一些可能在一二十年前看起来不错但现在已显得不必要的 API 和类。例如，将单元格类与视图区分开来的整个概念（如 `NSCell` 类所展示的）现在已经不复存在。`UIKit` 中唯一名称带有 cell 的类是 `UITableViewCell`，它实际上是一个 `UIView` 子类，用于绘制表格视图的一部分（因此得名）。此外，`UIKit` 在属性方面全面拥抱了 Objective-C 2.0，而 AppKit 从未做到，它为大多数可配置的属性提供了声明的属性。

除此之外，Cocoa 中还有一些重要部分在 Cocoa Touch 中不可用（至少在撰写本文时如此）。具体来说，就您在本书中阅读到的内容而言，缺失的最大一块是 Cocoa Bindings（Cocoa 绑定）。KVC 和 KVO 仍然存在，所以如果您有雄心，构建自己的绑定技术的基础条件是具备的。如果不这样做，您会发现任何 iOS 应用中很大一部分都是胶水代码，用于将模型对象中的值推送到各种视图和控件中。


### GNUstep 与 Cocotron

另一个可以运行类 Cocoa 应用的平台是 `GNUstep`，以及它较新的“战友”`Cocotron`。`GNUstep` 项目已经存在了很长时间，其最初的目标是为 Linux 和其他 Unix 机器提供桌面应用程序编程环境。其开发库基于 `OpenStep` API，这是一个已发布的标准，列出了当今 `Foundation` 和 `AppKit` 框架子集中的类和 API。而这又源于 1990 年代中期 NeXT 与 Sun 的合作，当时 Sun 希望获得帮助，为其 Solaris 操作系统提供应用程序开发环境。所有这一切都早于苹果后来对 NeXT 的收购，而 NeXT 的操作系统和软件后来演变成了 Mac OS X。

所以时至今日，`GNUstep` 仍然“活跃在野外”。`GNUstep` 持续发展，通过志愿者重新实现苹果添加到 Mac OS X 中的最新功能来获取新的 API，但其主要目标始终是实现旧的 `OpenStep` API，新增内容只是锦上添花。目前，`GNUstep` 在知名项目方面还谈不上有什么值得炫耀的成果，但作为将 Cocoa 应用移植到其他 Unix 变体或 Windows 的一种方式，[`www.gnustep.org`](http://www.gnustep.org) 上的 `GNUstep` 值得关注。

几年前，出现了一个名为 `Cocotron` 的新替代品。`Cocotron` 的侧重点略有不同（力图跟上苹果所有最新类和 API 的变化），部署目标也不同（主要专注于 Windows 而非 Unix），但如果你打算将应用移植到 Windows，它也值得考虑。它的许可证也比 `GNUstep` 更宽松，这意味着你可以自由地将 `Cocotron` 构建到自己的闭源应用程序中。`Cocotron` 的官方网站 [`www.cocotron.org`](http://www.cocotron.org) 似乎已基本停止更新，但一些开发仍在进行，这可以从该项目在 Google Groups 上的讨论中得到证实，地址是 [`http://groups.google.com/group/cocotron-dev/`](http://groups.google.com/group/cocotron-dev/)。

### Cappuccino/Objective-J

过去几年中另一个有趣的发展是 `Cappuccino` 开发环境。与我们提到的其他环境不同，`Cappuccino` 完全是关于制作 Web 应用，而非桌面应用。因此，`Cappuccino` 应用并非在桌面上运行，而是托管在 Web 服务器上，在 Web 浏览器中运行。`Cappuccino` 开发完全是关于在客户端运行软件，使用在单个 HTML 页面上下文中执行的 JavaScript，而不是像传统 Web 开发框架那样提供一页又一页的内容。

`Cappuccino` 包含的类库在名称和功能上模仿了 `Foundation` 和 `AppKit` 的许多内容，甚至能够实现将 Objective-C 的许多语法在 JavaScript 中实现这一惊人技巧，就像我们在提到 `JSCocoa` 时描述的那样。使用 `Cappuccino` 创建的应用程序可以发布到任何 Web 服务器上，并在任何现代浏览器中运行，就像其他 Web 应用程序一样。这种在 JavaScript 中实现 Objective-C 的方式被称为 `Objective-J`，是使 `Cappuccino` 能够运行的关键部分。

`Cappuccino` 最初以一种奇怪的混合体形式起步，包含一个包含 JavaScript 框架的开源核心，以及一个原本计划成为商业 IDE 的名为 `Atlas` 的工具，而 `Atlas` 本身就是一个 `Cappuccino` 应用程序。然而，在 `Atlas` 发布之前，支持这个想法的公司就被收购了。如今，这个开源项目已经扩展，包含了允许你直接在 Xcode 的 Interface Builder 中为你的 `Cappuccino` 应用构建图形用户界面的工具。请访问 [`www.cappuccino-project.org`](http://www.cappuccino-project.org) 了解最新动态。

## 在一切的终点

以上结束了关于你在提升 Cocoa 技能过程中未来方向的讨论；这也为本书画上了句号。我们希望本书能带你到达你想去的地方，但请不要认为这个落脚点就是最终的目的地！请访问 Mac 版 Cocoa 学习网站，在那里你可以找到与其他 Cocoa 开发者交流的论坛、本书所有示例的源代码以及更多内容。并且一定要访问我们的网站 [`http://learncocoa.org`](http://learncocoa.org)，我们将尽最大努力帮助你学到更多知识！



