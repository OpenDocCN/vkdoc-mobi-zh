# 关于谓词的讨论为*学习 Objective-C*画上句号

我们已经涉猎了诸多领域，从间接寻址和面向对象编程的基础，到键值编码和用`NSPredicate`过滤对象等复杂工具。恭喜你坚持读完整本书！你现在已正式准备好迈入 iOS 和 OS X 编程生涯的下一个阶段。感谢你的参与。

## 附录 A：从其他语言过渡到 Objective-C

许多程序员从其他语言来到 Objective-C 和 Cocoa 时，学习起来颇为困难，因为它的行为方式与大多数主流语言不同。新手 Objective-C 程序员常断言 Objective-C 是一种糟糕的语言，因为它没有他们的最爱语言所具备的 X、Y、Z 特性。抛开功能清单不谈，让我们直面现实：在某些方面，Objective-C 确实有些古怪。

我们可以给 Objective-C 和 Cocoa 新手程序员（甚至那些在其他语言和平台上拥有多年经验的人）的建议是：暂时搁置你对事物*应该*如何运作的先入之见，先按 Objective-C、Cocoa 和 Xcode 自身的规则来接受它们。阅读几本书和教程。当你积累了一些 Objective-C 经验后，就能看出其他语言的哪些技术和方法适用于 Cocoa 和 Objective-C，哪些不适用。没有哪种语言适合所有情况，也没有哪个工具包能完美胜任所有工作。最好的做法是：充分学习，判断特定语言和工具包是否能满足你的需求，并了解其中涉及的各种权衡。

在本附录中，我们将提供一些信息，帮助你顺利从其他主流语言过渡到 Objective-C。

### 从 C 语言过渡

Objective-C 就是普通的 C 语言，外加一些用于处理面向对象特性的附加功能。本书大部分内容都在描述这些附加特性，因此我们在此不再赘述。但有几个有趣的话题值得讨论。

请记住，Objective-C 程序员可以访问 C 语言附带的所有好东西，比如标准 C 库。例如，你可以使用`malloc()`和`free()`来处理动态内存，或者使用`fopen()`和`fgets()`来处理文件。

偶尔，网上会有人问：“我调用了一个使用回调函数执行任务的 ANSI C 库。如何让它调用一个方法呢？”当你使用像 Core Foundation 和 Core Graphics 这样的底层 Apple 框架时，也会遇到这个问题。

简短的答案是：“你不能。”回调只能用于具有库所需签名的 C 函数。实现 Objective-C 方法的函数首先需要`self`和`selector`参数，因此它们永远不会匹配所需的签名。

大多数基于回调的库允许你提供一些用户数据或指针。你可以将其视为一个可以藏身的掩体。当你注册回调时，将某个有意义的指针提供给库，库在调用你的回调时会将这个指针返回给你。

所有 Objective-C 对象都是动态分配的，因此使用对象的地址作为上下文指针是安全的。你无需担心栈上分配的对象会突然消失。在回调内部，你将上下文指针强制转换为对象类型，然后开始向其发送消息。

例如，假设你使用一个虚构的 C API XML 解析库，并在解析 XML 文件时将数据提供给一个名为`TreeWalker`的类。创建一个新的`TreeWalker`：

```objc
TreeWalker *walker = [[TreeWalker alloc] init];
```

然后，创建 XML 解析器：

```objc
XMLParser parser;
parser = XMLParserLoadFile ("/tmp/badgers.xml");
```

为解析器设置回调函数（一个 C 函数），并使用`TreeWalker`对象作为上下文：

```objc
XMLSetParserCallback (parser, elementCallback, walker);
```

然后在解析 XML 文件时，回调函数被调用：

```objc
void elementCallback (XMLParser *parser, XMLElement *element, userData *context)
{
    TreeWalker *walker = (TreeWalker *) context;
    [walker handleNewElement: element inParser: parser];
} // someElementCallback
```

你可以看到，上下文指针被强制转换为对象指针，然后向该对象发送消息。

### 从 C++过渡

C++有很多 Objective-C 缺乏的特性：多重继承、命名空间、运算符重载、模板、类变量、抽象类、标准模板库（STL）等等。如果你怀念这些特性，Objective-C 也有可以替代它们的功能和技术，或者至少可以模拟它们。

例如，你可以使用类别和协议作为多重继承的一种形式，或者用于实现抽象基类。多重继承的一个常见用途是提供接口，以便其他代码可以调用你对象上的特定方法。类别和协议非常适合这种模式。你可以使用协议来提供纯抽象基类。

如果你使用多重继承是为了引入额外的实例变量（在 C++中称为**成员变量**），那么类别和协议就帮不上忙了。为此，你可以使用组合将一个对象包含在另一个对象中，然后使用存根方法将消息重定向到第二个对象（这是 Java 中常用的技术）。你还可以通过重写`forwardInvocation:`方法来模拟多重继承。当对象收到一条它不知道如何处理的消息时，就会调用此方法。通过检查`NSInvocation`对象，你可以判断这条消息是否应该转发给你的“多重继承”对象，然后在必要时发送出去。这种技术可以让你免于编写许多小存根方法。然而，它比真正的多重继承慢得多，并且设置起来可能很麻烦。

其他约定可以取代更多 C++特性，例如，对于不应实例化的抽象类，使用调用`abort`的存根方法，尽管某些特性有较弱的替代方案，例如用名称前缀代替命名空间。

#### C++ vtable 与 Objective-C 动态派发

C++和 Objective-C 之间最大的区别之一在于方法（在 C++中称为**成员函数**）的派发机制。C++使用基于 vtable 的机制来确定虚函数要调用的代码。

你可以把每个 C++对象想象成拥有一个指向函数指针数组的指针。当编译器看到代码想要调用一个虚函数时，它会计算从 vtable 起始位置的偏移量，生成机器代码来获取该偏移量处的函数指针，并将其作为要执行的代码块。这个过程要求编译器在编译时知道调用成员函数的对象类型，以便计算正确的 vtable 偏移量。这种派发非常快，只需几次指针操作和一次读取即可获得函数指针。

Objective-C 的方式（第 3 章中有详细描述）则使用运行时函数在各种类结构中搜索要调用的代码。这种技术可能比 C++方式慢几倍。

Objective-C 以牺牲速度和安全性为代价，换来了灵活性和便利性，这是一种典型的权衡。在 C++模型中，成员函数派发速度很快，而且非常安全，因为编译器和链接器确保所使用的对象能够处理该方法。但 C++方法也可能不够灵活，因为你无法改变你正在处理的对象类型。你必须使用继承来允许不同类的对象对同一条消息做出反应。



关于 C++类的许多信息（例如继承链和组成它的成员）都不会被 C++编译器保留。在运行时，对对象进行泛型处理的能力是有限的。你最多能做的就是在运行时进行`dynamic_cast`，以判断一个对象是否是另一个对象的特定子类。

C++的继承层次结构不能在运行时更改。一旦程序编译并链接完成，它就几乎被固定下来。由于 C++名称修饰（一种利用原始 Unix 链接器执行类型安全链接的方式）的复杂性，动态加载 C++库通常会出现问题。

在 Objective-C 中，一个对象只需要有方法的实现就可以被调用，这使得任意对象都能成为其他对象的*数据源*或*委托*。缺少多重继承可能带来不便，但由于能够向任何对象发送任何消息而无需担心其继承谱系，这一不便大大得到了缓解。

当然，这种向任何对象发送任何消息的能力使得 Objective-C 的类型安全性不如 C++。如果收到消息的对象无法处理该消息，你可能会遇到运行时错误。Cocoa 中没有类型安全的容器。任何对象都可以被放入容器中。

Objective-C 携带了大量关于类的元数据，因此你可以使用反射来检查一个对象是否响应特定的消息。这种做法对于拥有数据源或委托的对象非常常见。通过首先检查委托是否响应某条消息，你可以避免可能遇到的一些运行时错误。你还可以使用*类别（Category）*向其他类添加方法。

由于这些元数据的存在，逆向工程分析程序中所使用的类变得更加容易。你可以确定实例变量、它们在对象结构中的布局以及该类定义的方法。即使剥离可执行文件中的调试信息，也无法移除 Objective-C 的元数据。如果你有高度机密的算法，你可能希望用 C++实现它们，或者至少混淆它们的名称——例如，不要使用像`SerialNumberVerifier`这样的类名或方法名。

在 Objective-C 中，你可以向`nil`（零）对象发送消息。无需检查你的消息发送是否针对`NULL`。向`nil`发送消息是空操作。向`nil`发送消息的返回值取决于方法的返回类型。如果方法返回一个指针类型（例如对象指针），返回值将是`nil`，这意味着你可以安全地将消息链接到`nil`对象——`nil`只会继续传递下去。如果方法返回一个与指针大小相同或更小的`int`，它将返回零。如果它返回一个`float`或结构体，结果将是未定义的。因此，你可以使用`nil`对象模式来避免测试对象指针是否为`NULL`。另一方面，这种技术可能会掩盖错误，并导致难以追踪的 bug。

Objective-C 中的所有对象都是动态分配的。没有基于栈的对象、临时对象的自动创建和销毁，也没有类类型之间的自动类型转换，因此 Objective-C 对象比基于 C++栈的对象更重。这就是为什么像`NSPoint`和`NSRange`这样的小型轻量实体是结构体而非一等对象的原因之一。

最后，Objective-C 是一种非常松散的语言。C++拥有`public`、`protected`和`private`成员函数，而 Objective-C 完全支持`public`、`protected`和`private`实例变量，但对成员函数没有任何保护。任何知道方法名称的人都可以向该对象发送该消息。利用 Objective-C 的反射特性，你可以查看给定对象支持的所有方法。即使方法从未在头文件中出现，它们也是可调用的，并且你无法可靠地找出是哪个对象在调用该方法，因为消息发送可能来自 C 函数（如本附录前面所述）。

如你所见，你无需在子类中重新声明你覆盖的方法，也无需将它们标记为`virtual`。任何方法都可以在子类或类别中被覆盖。关于这是否是一个好主意，存在两种学派。一派认为重新声明可以向读者提供关于该类对其父类做了哪些更改的信息，而另一派则认为这些只是实现细节，类的使用者无需理会，并且不值得因为覆盖了一个新方法而导致所有依赖类重新编译。

Objective-C 没有类变量。你可以通过使用文件作用域的全局变量并为它们提供访问器方法来模拟类变量。一个示例类声明可能如下所示（其中包含实例变量声明和方法声明等其他内容）：

```
@interface Blarg : NSObject
{

}
+ (int) classVar;
+ (void) setClassVar: (int) cv;

@end // Blarg
```

然后实现如下所示：

```
#import "Blarg.h"

static int g_cvar;

@implementation Blarg

+ (int) classVar
{
    return (g_cvar);
} // classVar

+ (void) setClassVar: (int) cv
{
    g_cvar = cv;
} // setClassVar

@end // Blarg
```

Cocoa 对象层次结构有一个共同的祖先类：`NSObject`。当你创建一个新类时，你几乎总是会继承`NSObject`或一个现有的 Cocoa 类。C++的对象层次结构通常是拥有不同根节点的多个树。

### Objective-C++

有一种方法可以兼得两者的优势。Xcode 附带的 Clang 编译器支持一种称为 Objective-C++的混合语言。此编译器允许你自由混合使用 C++和 Objective-C 代码，只有少数几个小限制。当你需要时，可以获得类型安全和底层性能；在合适的情况下，可以利用 Objective-C 的动态特性以及 Cocoa 工具包。

一个常见的开发场景是将应用程序的所有核心逻辑放入一个可移植的 C++库中（如果你正在构建跨平台应用），并使用平台的原生工具包编写用户界面。Objective-C++对这种开发风格大有裨益。你获得了 C++的性能和类型安全，而用户则获得了使用与其平台无缝集成的原生工具包创建的应用程序。

要让编译器将你的代码视为 Objective-C++，请在源文件中使用`*.mm`文件扩展名。`*.M`扩展名也有效，但 Mac 的 HFS+文件系统不区分大小写但保留大小写，因此最好避免任何类型的大小写依赖。

就像物质和反物质一样，Objective-C 和 C++的对象层次结构不能混合。因此，你不能拥有一个继承自`NSView`的 C++类，也不能拥有一个继承自`std::string`的 Objective-C 类。

你可以将指向 Objective-C 对象的指针放入 C++对象中。由于所有 Objective-C 对象都是动态分配的，你不能在类中嵌入完整的对象，也不能在栈上声明它们。你需要在 C++的构造函数中（或任何方便的地方）对任何 Objective-C 对象执行`alloc`和`init`，并在析构函数中（或其他地方）释放它们。因此，以下是一个有效的类声明：

```
class ChessPiece
{
    ChessPiece::PieceType type;
    int row, column;
    NSImage *pieceImage;
};
```

你也可以将 C++对象放入 Objective-C 对象中：

```
@interface SWChessBoard : NSView
{
    ChessPiece *piece[32];
}

@end // SWChessBoard
```



## 内嵌于 Objective-C 的 C++ 对象

C++ 对象可以内嵌在 Objective-C 对象中，而非仅仅通过指针关联。当 Objective-C 对象被分配内存时，内嵌的 C++ 对象会调用其构造函数；当 Objective-C 对象被释放时，则会调用其析构函数。

## 异常处理

`NSExceptions` 和 C++ 异常是相互兼容的：你可以自由地抛出和捕获异常块。在异常展开过程中，C++ 析构函数和 `@finally` 块都会被正常执行。此外，`catch( . . . )` 和 `@catch( . . . )` 可以捕获并重新抛出任何异常。

## 如果你来自 Java 背景

与 C++ 类似，Java 拥有许多 Objective-C 不具备或实现方式不同的特性。例如，经典的 Objective-C 没有垃圾回收器，而是采用 `retain`/`release` 机制以及基于 `ARC` 的自动释放池。如果你愿意，可以在 OS X 的 Objective-C 程序中启用垃圾回收。

Java 的接口类似于 Objective-C 的形式化协议；两者都要求实现一组方法。Java 有抽象类，但 Objective-C 没有。Java 有类变量，而在 Objective-C 中，你可以使用 `static` 文件作用域的全局变量，并通过访问器来操作它们，具体方法如“来自 C++ 背景”一节所示。Objective-C 对公有和私有方法的限制相当宽松。正如我们之前提到的，只要一个对象支持某个方法，它就可以被调用，即使该方法没有出现在任何外部形式（如头文件）中。Java 允许你将类声明为 `final`，从而阻止任何子类的创建。Objective-C 则走向了另一个极端，它允许你在运行时向任何类添加方法。

在 Objective-C 中，类的实现通常分为两个文件：头文件和实现文件。不过，对于小型私有类，这种分离并非必需，正如你在本书的一些代码中所见。头文件（扩展名为 `*.h*`）包含与类相关的公有信息，例如任何新的枚举、类型、结构体以及将被使用该类的代码所引用的对象。其他代码通过预处理器（使用 `#import`）导入此文件。Java 没有 C 预处理器，后者是一种文本替换工具，会在 C、Objective-C 和 C++ 源代码被提交给编译器之前，自动对其进行处理。当你看到以 `#` 开头的指令时，就知道这一行是向预处理器发出的命令。实际上，C 预处理器对 C 语言家族一无所知；它只是进行盲目的文本替换。预处理器可以是一个非常强大——同时也非常危险——的工具。许多程序员认为 Java 缺少预处理器反而是一种优势。

在 Java 中，几乎所有的错误都通过异常来处理。在 Objective-C 中，错误处理取决于你使用的 API。Unix API 通常返回一个 `–1` 值，并设置全局错误号 (`errno`) 为特定的错误码。Cocoa API 通常仅在出现编程错误或无法进行清理的情况下抛出异常。Objective-C 语言提供了类似于 Java 和 C++ 的异常处理特性：`@try`、`@catch` 和 `@finally`。

在 Objective-C 中，空（零）对象被称为 `nil`。你可以向 `nil` 发送消息，而无需担心 `NullPointerException`。向 `nil` 发送消息是一个空操作，因此你无需检查消息发送是否指向了 `NULL`。关于向 `nil` 发送消息的更多讨论，请参见前面的“来自 C++ 背景”一节。

在 Objective-C 中，你可以通过分类向现有类添加方法，从而在运行时改变类的行为。Objective-C 中没有所谓的最终类；你可以子类化任何类，只要拥有该类的头文件即可，因为编译器需要知道父类定义的对象大小。

在实践中，你在 Objective-C 中进行子类化的频率远低于 Java。通过分类和动态运行时（允许向任何对象发送任何消息）等机制，你可以将功能集成到更少的类中，也可以将功能放入最合理的类中。例如，你可以为 `NSString` 添加一个分类，实现诸如反转字符串或去除所有空白字符的功能。然后，你就可以在任何 `NSString` 对象上调用该方法，无论该对象来自何处。你无需局限于自己的字符串子类来提供这些功能。

通常，在 Cocoa 中需要子类化的情况只有以下几种：创建一个全新的对象（位于对象层次结构顶端）、从根本上改变一个对象的行为，或者使用一个需要子类化的类（因为该类本身不提供任何有用的开箱即用功能）。例如，Cocoa 中用于创建用户界面组件的 `NSView` 类，其 `drawRect:` 方法没有实现。你需要继承 `NSView` 并重写该方法，才能在视图中进行绘制。但对于许多其他对象，则会使用委托和数据源机制。由于 Objective-C 可以向任何对象发送任何消息，一个对象无需是特定的子类，也无需符合特定的接口，因此单个类可以同时作为多个不同对象的委托和数据源。

由于数据源和委托方法是在分类中声明的，你无需实现所有方法。在 Objective-C 的 Cocoa 编程中，很少有空的存根方法，或者为了避免编译器警告而仅仅为了适配形式化协议而调用内嵌对象中相同方法的方法。

当然，能力越大，责任也越大。使用 Objective-C 的手动 `retain`、`release` 和 `autorelease` 内存管理系统，你可能很容易不经意间制造出棘手的内存错误。为其他类添加分类是一个非常强大的机制，但如果滥用，会使你的代码难以理清，并且无法交给他人使用。此外，Objective-C 基于 C 语言，因此你会继承 C 语言的所有历史包袱，以及在使用预处理器时带来的风险，包括可能出现的与指针相关的内存错误。

## 如果你来自 BASIC 背景

许多程序员是从 Visual Basic 或 REALbasic 开始学习编程的，当他们转向 Cocoa 和 Objective-C 时，可能会感到困惑。

BASIC（Visual 和 REAL）环境提供了一个集成的开发环境，构成了完整的工作空间。Cocoa 则将你的开发工作分为两个环境：Interface Builder 编辑器和文本编辑器，两者都集成在 Xcode 中。你使用 Interface Builder 创建用户界面，并告知用户界面在特定对象上调用哪些方法，然后将控制逻辑编写到在 Xcode 文本编辑器（或 TextMate、BBEdit、emacs，或你喜欢的任何文本编辑器）中编辑的源代码里。

在 BASIC 中，用户界面元素及其相关的代码紧密集成。你将代码块放入按钮和文本字段中，使它们按你期望的方式工作。你可以将这些代码提取到一个公共类中，并让按钮中的代码与这个类通信，但在大多数情况下，BASIC 编程涉及将代码直接写在用户界面元素上。如果不小心，这种风格可能会导致程序混乱，逻辑分散在许多不同的元素上。BASIC 编程通常通过修改对象的属性来使其按你期望的方式运行。

在 Cocoa 中，你会发现界面与界面背后的逻辑之间存在清晰的分离。你拥有一组相互通信的对象。你不再是设置对象的某个属性，而是要求对象去改变其自身的属性。这种区别很微妙但很重要。你在 Cocoa 中花费的大部分思考时间，是在琢磨需要发送什么消息，而不是需要设置什么属性。



## BASIC 的生态与过渡

`BASIC`在第三方控件和支持代码方面拥有非常丰富的市场。通常，你可以直接购买现成的组件并将其集成到你的代码库中，而不是自行构建。

## 从脚本语言转向 Objective-C

从脚本语言（如`Perl`、`PHP`、`Python`和`Tcl`）转来的程序员，在过渡到`Objective-C`和`Cocoa`世界时，往往面临最艰难的转变。

脚本语言在程序员便利性方面表现出色，例如：非常强大的字符串处理能力、自动内存管理（无论是通过引用计数还是垃圾回收）、极快的开发迭代周期、灵活的类型系统（能轻松在数字、字符串和列表之间切换），以及大量可供下载和使用的包。脚本语言的运行时环境通常也非常灵活，允许你按需设计自己的对象类型和控制结构。

如果你来自脚本语言领域，`Objective-C`在许多方面会让你感觉像是倒退了一大步。与 20 世纪 90 年代发展起来的脚本语言相比，它是一门 80 年代的语言。字符串处理可能非常痛苦，因为内置的正则表达式功能缺失。使用`printf()`风格格式化字符串，差不多就是`Cocoa`能提供的最高级字符串操作了。尽管`Objective-C`已经引入了`ARC`和垃圾回收，但你在网上看到的大量现有代码仍然使用手动内存管理技术（通过`retain`和`release`）。开发过程包含编译和链接阶段，这导致从修改代码到看到结果之间存在延迟。你必须手动处理不同的类型，例如整数、字符数组和字符串对象。此外，你还得应对`C`语言带来的所有历史遗留问题，比如指针、位运算以及容易引发的内存错误。

为什么还要忍受这些痛苦去使用`Objective-C`？性能是一个原因：根据应用类型的不同，`Objective-C`的性能可能优于脚本语言。另一个重要优势是能够访问原生用户界面工具包（`Cocoa`）。大多数脚本语言支持最初为`Tcl`语言开发的`Tk`工具包。这个包虽然可用，但它不具备`Cocoa`所提供的用户界面功能的深度和广度。最重要的是，使用`Tk`构建的应用通常不具备 iOS 或 Mac 程序的外观和感觉。

不过，你也可以通过使用脚本桥接，实现两全其美。存在`Objective-C`与`Python`（称为`PyObjC`）以及`Ruby`（`RubyObjC`）之间的桥接，因此这两种脚本语言可以成为一等公民。当你使用这些桥接时，可以在`Python`或`Ruby`中对`Cocoa`对象进行子类化，并访问`Cocoa`的所有功能。

## 总结

`Objective-C`和`Cocoa`与任何其他编程语言和工具包都不同。`Objective-C`拥有一些源自其动态运行时分发特性的巧妙功能和特性。你可以在`Objective-C`中完成其他语言无法做到的事情。

与此同时，`Objective-C`缺少一些多年来其他语言已经添加的便利功能。特别是，强大的字符串处理、命名空间和元编程是这些其他语言具备、而`Objective-C`不具备的特性。

编程中的一切都归结于权衡取舍。你必须决定，相比于你当前选择的语言，在`Objective-C`中获得的收益是否值得你失去的东西。对我们来说，能够使用`Cocoa`构建应用，这本身就完全值得花时间和精力去熟悉`Objective-C`。

## 索引

![image](img/9781430241881_square_fmt.jpg)  A

- 访问器（Accessor）方法
- 防御性编程
- 引擎
- 扩展 carparts
- init() 和 main() 函数
- 修改器（mutator）
- 设置器和获取器方法
- 轮胎



Application Kit (AppKit)

AppDelegate 实现

Cocoa 应用程序项目
- 创建
- MSCAppDelegate
- 反向域名格式
- Subversion (SVN)

代理 @interface

成品

连接设置
- 操作
- CaseTool 程序
- 输出口

界面构建器
- MainMenu.xib 文件
- nib 文件
- 对象库
- 用户界面
  - 添加项目
  - 清理窗口
  - 编辑
  - 标签
  - 库
  - 文本字段

自动引用计数 (ARC)
- 苹果的解决方案
- 桥接转换
- ARC 管理的对象
- dealloc 方法
- ROP
- 结构体和联合体
- 类型
  - 转换
- 文件选择
- 垃圾回收
- 介绍屏幕
- Objective-C ARC
- Xcode
  - 特性
  - 垃圾回收
  - 要求
  - 强引用
  - 弱引用
- 经典内存泄漏
- 父节点与子节点
- 限制
- 引用计数增加
- 零化弱引用
- Xcode

![image](img/9781430241881_square_fmt.jpg) B

块 (Blocks)
- 自动绑定与管理绑定
- __block 变量
- 闭包
- 函数指针
- 全局变量
- 局部变量
- 方法/函数
- Objective-C 对象
- 参数变量
- typedef
- 变量

![image](img/9781430241881_square_fmt.jpg) C

C++
- 异常
- 特性
- 混合语言
- 继承
- 元数据
- 基于虚函数表 (vtable) 的机制

类别 (Categories)
- 类扩展
- 类方法
- 文件创建
- @implementation
- 实现文件
- @interface



ITunesFinder 项目

daap 字符串

委托方法

ITunesFinder

NSNetServiceBrowser

NSNetService 对象

运行循环

局限性

方法创建

NSString

项目

访问器方法

类的声明

前向引用

main()

内存管理

方法

NSLog()

NSString

协议与委托

用途

选择器

源文件组和目标

分离实现

C 语言

Cocoa 应用程序项目

创建

MSCAppDelegate

反向域名格式

Subversion (SVN)

组合

访问器方法

防御性编程

引擎

扩展 CarParts

init() 与 main() 函数

赋值方法

设置方法与获取方法

轮胎

继承

含义

NSLog()

描述方法

if 语句

init 方法

输出

指针

打印方法

进程

形状程序

并发

调度

Grand Central Dispatch

内存管理

上下文

终结器函数

引用计数对象

任务

互斥锁

操作队列

块操作

调用

objective-C

POSIX 线程

队列

应用主线程

死锁

调度队列

先进先出 (FIFO)

串行

类型

选择性能

同步

Cox, Brad

![image](img/9781430241881_square_fmt.jpg)  D

调试

断点

调用堆栈


caveman debugging  
controls  
datatips  
debugger  
GUI program  
meaning  
program data  
program running  
program state  
stack trace  
stepping method  
subtle symbolism  

![image](img/9781430241881_square_fmt.jpg) E  
异常处理  
自动释放池  
捕获类型  
启用 Objective-C  
关键字  
含义  
内存管理  
抛出/引发异常  
僵尸异常  

![image](img/9781430241881_square_fmt.jpg) F  
文件处理  
编码与解码  
`+archivedDataWithRootObject`  
`decodeSomethingForKey` 方法  
`encodeWithCoder`  
`initWithCoder`  
`NSCoding` 协议  
序列化与反序列化  
`Thingie`  
`thing1` 方法  
属性列表  
修改对象  
`NSData`  
`NSDate`  
plist  
`writeToFile:atomically:` 方法  

Foundation 工具包  
块  
装箱与拆箱  
类簇  
枚举国度  
快速枚举  
`FileWalker`  
几何类型  
可变数组  
`NSArray`  
`arrayWithObjects`  
断点窗口  
Cocoa 类  
调试器  
异常  
特性  
限制  
对象  
程序输出  
`NSDictionary`  
`NSNull`  
`NSNumber`  
`NSValue`  
项目模板代码  
范围  
单例架构  
坚实基础  
字符串  
内置方法  
类方法  
比较政治学  
工厂方法  
格式与参数  
大小写不敏感训练  
可变性  
大小很重要  
子字符串  
`UIKit`  
包装类

好的，遵照您的指示，以下是排版后的内容。

![image](img/9781430241881_square_fmt.jpg) I

间接引用

数组形状结构

colorName() 函数

drawCircle()

drawRectangle()

drawShapes()

drawTriangle() 函数

main() 声明

文件名

传递的参数与制表符

argv 数组

命令行参数

fopen() 和 fgets()

for 循环

实现面向对象

数组，形状

圆与类

绘制代码

draw() 函数

drawRectangle() 函数

drawShapes()

矩形

形状对象

含义

面向对象编程

几何形状

实现

过程式编程

形状-过程式程序的 main() 方法

变量

继承

Circle 和 Rectangle 类

实现

接口

形状-对象架构

超类

类代码

组合

drawShapes() 函数

脆弱基类问题

实例变量

方法分派

重写的方法

重构

超类方法

语法

术语

![image](img/9781430241881_square_fmt.jpg) J

Java

![image](img/9781430241881_square_fmt.jpg) K

键值编码（KVC）

聚合攻击

-addCar: 方法

Garage 的接口

NSArrays

丝滑操作符

批处理

Car-Value-Coding 项目

文件系统路径

处理未定义的键

nil 值

维修站

类方法

Garage 接口

#imports

main()

丝滑操作符

平均值

计数

@min 和 @max

总和

并集

valueForKey: 和 setValueforKey:

![image](img/9781430241881_square_fmt.jpg) M


内存管理。 *另请参阅* 自动引用计数 (ARC)

Cocoa 对象

并发

上下文

终结器函数

引用计数对象

任务

异常处理

自动释放池

捕获类型

启用 Objective-C

关键字

含义

内存管理

抛出/引发

僵尸异常

垃圾回收

内存泄漏

对象生命周期

访问器

动作池

自动释放

销毁

对象所有权

池，自动释放

引用计数/保留计数

悬挂对象

池清理

资源管理

规则

单例

临时对象

![image](img/9781430241881_square_fmt.jpg) N

NSPredicate。*参见* 谓词

![image](img/9781430241881_square_fmt.jpg) O

对象初始化

分配

初始化

init 方法

含义

方法

ARC

CarPartsInit

数组

init

快速返回

更新 main() 文件

便捷初始化器

指定初始化器

AllWeatherRadial

便捷

子类

轮胎的初始化器

垃圾回收

规则

Objective-C

Apple Cocoa 和 Cocoa Touch

应用程序文件夹

基本编程

Block_copy() 和 Block_release() 函数

布尔类型

areIntsDifferent() 函数

BOOL 行为

比较

第一个函数

第二个函数 / boolString()

真/假值

C++

异常

特性

混合语言

继承

元数据

基于虚函数表 (vtable) 的机制

章节详情

C 语言



解构

框架

头文件

#import

main() 函数

名称冲突

NSLog() 与 @ 字符串

NS 前缀

NSString

字符串

开发者工具

Hello, world

命令行工具

基础工具

程序输出

项目选项

Xcode 工具安装

@implementation 部分

Circle 类

draw 方法

隐藏参数

实例变量

方法声明

setBounds 和 setFillColor

实例化

@interface 部分

Circle 类

冒号

函数

中缀表示法

方法声明

setBounds

setFillColor

Java

Mac 应用商店

对象

Objective-C 2.0 特性

操作队列

概述

编程语言

协议

脚本语言

shapes-object

面向对象编程（OOP）

间接性

数组形状结构

文件名

几何形状

实现面向对象

含义

过程式编程

shapes-procedural 程序的 main() 方法

变量

Xcode，文件路径

objective-C

扩展 shapes-object

@implementation 部分

实例化对象

@interface 部分

编程技巧

术语

![image](img/9781430241881_square_fmt.jpg)  P

谓词

数组运算符

比较与逻辑运算符

创建

评估

格式说明符

燃料过滤器

键值编码

LIKE 运算符

概述

SELF 足够的

字符串运算

属性

神奇的点

处理值



实现

接口

对象

命名 Spring

blah 与 setBlah 方法

描述

@dynamic 关键字

@property

只读

保留循环

标量类型——浮点数

@synthesize

setBlah 与 -blah 方法

协议

完全拷贝

拷贝生成代码

深拷贝

引擎

主类 CarCopy

浅拷贝

轮胎

拷贝操作

数据类型

委托

正式协议

采纳

声明

实现

@interface 声明

![image](img/9781430241881_square_fmt.jpg)  Q

队列

应用程序主线程

调度队列

先进先出 (FIFO)

内存管理

上下文

终结器函数

引用计数对象

任务

操作

块操作

调用

Objective-C

系列

类型

![image](img/9781430241881_square_fmt.jpg)  R

可保留对象指针 (ROP)

![image](img/9781430241881_square_fmt.jpg)  S

源文件组织

CarParts-Split

依赖关系，Car 的头文件

类

导入与继承

重新编译

接口与实现

目录

文件创建

项目窗口

区段与指令

Xcode

main() 函数

静态分析器

carsCopy

比较错误

关注捣乱者

死存储

降序

车库对象

问题列表

内存泄漏

逻辑错误

内存泄漏

非保留对象

过度释放

产品菜单

保留对象

返回语句

返回空值 (无返回值)



synchronization

![image](img/9781430241881_square_fmt.jpg) U

- 用户界面
    - 添加项目
    - 清理窗口
    - 编辑
    - 标签
    - 库
    - 文本字段
    - 用户界面工具包 (UIKit)
- CaseTool 程序
    - 应用类型
    - iPhone
    - iOS/OS X
    - 位置
    - NSString
    - 产品名称
    - 项目
    - 模板
    - Xcode 项目创建
    - 基础工具包
    - 模型-视图-控制器
    - nib 文件
    - 参数
    - awakeFromNib
    - 按钮名称
    - 连接
    - 控件拖拽连接
    - 事件选项
    - @interface 和 @end
    - iPad 用户界面
    - iPhone 屏幕
    - 标签对象
    - 布局
    - 小写按钮
    - NSString 方法
    - 输出口
    - 弹出窗口
    - resultsField 选项
    - 圆角矩形按钮
    - 创建第二个按钮
    - 文本字段
    - 文本字段对象
    - textField 输出口
    - 大写按钮
    - viewDidUnload

![image](img/9781430241881_square_fmt.jpg) X

Xcode
- 代码补全
    - AllWeatherRadial 类
    - 探索代码感知
    - 帮助窗口
    - 占位符
    - 选择占位符
- 公司名称
- 调试
    - 断点
    - 调用栈
    - 洞穴人调试
    - 控制项
    - 数据提示
    - 调试器
    - GUI 程序
    - 含义
    - 程序数据
    - 程序运行
    - 程序状态
    - 堆栈跟踪
    - 步进方法
    - 微妙象征
- 能量
- 焦点条
- 装订线
- 大型应用
- 缩进
- 信息
    - 文档窗口
    - 帮助菜单
    - NSString
- 键盘快捷键
- 括号拥抱
- 批量编辑
    - 编辑代码
    - 项目范围查找和替换
    - 重构
- 导航代码
    - emacs
    - session
- 导航栏
- 项目和源代码编辑器
- 符号
- 窗口
