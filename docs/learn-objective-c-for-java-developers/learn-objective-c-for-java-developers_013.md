# 排版后的 Markdown 文档

我强烈建议你完整阅读第一部分。第二和第三部分可以通读，也可以先快速浏览，稍后再回头查阅解决方案。最后一部分的高级主题针对特定场景，例如处理 iPhone 的内存管理器，你可以根据需要深入学习。许多章节从基础开始，然后逐步深入更冷僻的特性，因此一旦你掌握了所需内容，可以随意跳到下一章。

### 前提条件

本书假定你具备一定的 Java 编程经验。你应该熟悉 Java 语言的基础知识，理解类、对象、继承和接口的概念，并掌握核心 Java 类的使用。如果对个别 Java 技术（如内省和异常处理）有一定了解会有所帮助，但这些并非学习 Objective-C 对应内容的绝对必要条件。虽然我希望你已经熟悉设计模式，但它们并非学习前提。

### 下载源代码

本书的源代码可在 `http://www.apress.com` 本书主页的“下载”部分获取。欢迎访问 Apress 网站并下载所有代码。你也可以在网站上查看勘误表，并查找来自 Apress 的相关书籍。

### 联系作者

你可以通过 `james@objectivec4java.com` 联系我。

xxv

[www.it-ebooks.info](http://www.it-ebooks.info/)

第 1 部分

■ ■ ■

语言

[www.it-ebooks.info](http://www.it-ebooks.info/)

**第 1 章**

■ ■ ■

## 引言

欢迎阅读《*Java 开发者学习 Objective-C*》。本书将帮助你从 Java 编程过渡到 Objective-C 编程，Objective-C 是用于为苹果公司的 Mac OS X 系列计算机和消费产品开发应用程序的主要语言。你将学会用一种充满活力且灵活的语言进行高效编程，这种语言驱动着当今许多尖端的应用程序和移动设备。更重要的是，本书将向你展示如何利用你在 Java 中学到的编码实践、设计模式和问题解决技能，并将其应用于 Objective-C。

### 什么是 Objective-C？

那么，Objective-C 到底是什么，它又有何特别之处呢？Objective-C 在标准 C 语言的基础上引入了对象的概念。它通过添加少量新关键字和类似 SmallTalk 的方法调用语法，提升了 C 语言的层次。最终形成了一种具有卓越特性的面向对象编程语言：

- 现代的面向对象设计范式
- 先进的编译器
- 卓越的性能
- 直接访问 C 和 C++ API
- 动态行为

与大多数其他面向对象语言不同，Objective-C 并非创造一种全新的语言——它是 C 语言的一个严格超集。Objective-C 的关键字和语法与常规 C 语言截然不同。代码清单 1-1 展示了一段简短的 Objective-C 代码，以及等价的 Java 代码以供比较。基本的控制语句使用的是纯 C 语言。方括号之间的语句调用的是 Objective-C 方法。

***代码清单 1-1.** Java 与 Objective-C 语法示例* Java

```
public void setSize( Dimension size )
{
   if (size.height!=0 && size.width!=0) {
      if (!this.size.equals(size)) {
         super.setSize(size);
         for ( MapItem i: mapItems )
            i.resize();
      }
   }
}
```

Objective-C

```
- (void)setFrameSize:(NSSize)size
{
   if (size.height!=0.0 && size.width!=0.0) {
      if (!NSEqualSizes(self.size,size)) {
         [super setFrameSize:size];
         [mapItems makeObjectsPerformSelector:@selector(resize)];
      }
   }
}
```

Objective-C 是如何诞生的，以及它为何如此特别，这是一个有趣的故事。

#### 历史

Brad Cox 和 Tim Love 是 Objective-C 背后的主要推动者。它最初被称为“C 语言中的面向对象编程”（OOPC）。其目标是将需要解释器的 SmallTalk 的功能添加到 C 语言中，而无需设计一种全新的语言。1986 年，Cox 发布了当时已更名为 Objective-C 的首份正式描述。1988 年，NeXT Computer 采用 Objective-C 作为其主要开发语言。NeXT 丰富了 Objective-C，创建了广泛的类集合，这些类成为新应用程序、开发工具（尤其是 Interface Builder）以及其操作系统重要组成部分的基础。NEXTSTEP 操作系统最终发展出了 OpenStep API，该 API 定义了一套可移植到多个平台的一致对象和接口。OpenStep 的移植版本，以 OPENSTEP 为首，运行在 Sun 的 Solaris、微软的 Windows NT 及其他系统上。GNU（GNU's Not Unix）项目最终创建了一个名为 GNUstep 的开源实现。

Objective-C 和 NEXTSTEP 共同被誉为创新的开发环境，是少数真正实现了面向对象设计承诺的环境之一。应用程序的设计和创建快速而灵活。但由于种种原因——最显著的是 C++ 背后的行业惯性——NeXT 和 Objective-C 仍然只相当于一种新奇事物，一个本可以实现何种成就的光辉范例，倘若它们曾被该行业的主要部分所接纳的话。

这一门槛终于在 1996 年被跨越，那一年苹果公司收购了 NeXT Computer、NEXTSTEP 操作系统以及其整套基于 Objective-C 的开发工具。苹果公司将 NEXTSTEP 作为其全新 Mac OS X 操作系统的基石。这些对象框架被重新命名为“Cocoa”，此后成长并成熟为一套丰富而强大的工具集——不仅适用于个人计算机应用程序，也适用于像 iPhone 这样创新的消费设备。

#### 一种现代面向对象语言

Objective-C 实现了一个相当了不起的壮举。这门语言本身是精简的，几乎到了贫瘠的程度，然而它却成功实现了一个丰富的对象范式，足以与远为复杂的语言相媲美。这门语言易于学习，也易于实现。许多功能只需通过创建新类即可添加或增强，而无需修改语言本身。例如，使用 Objective-C，你也许某天早上醒来，就决定用一种新方式来实例化对象。Objective-C 并不定义对象如何创建或销毁——这是由运行时框架通过类方法提供的。你不太可能想重新定义对象实例化，但这个例子突显了语言的灵活性。

#### 先进的编译器

由于 Objective-C 是 C 语言之上的一层薄薄的封装，它紧随 C 语言变化的潮流。随着新特性、优化、目标处理器和其他技术被添加到 C 语言中，Objective-C 也随之受益。这使得 Objective-C 能够与现代化技术和方法保持同步。

当今的编译器技术也意味着 Objective-C 代码具有非凡的可移植性。不久前，为一个平台编写的 C 代码极不可能在另一个不同的系统或架构上编译和运行。这正是 Java“一次编写，到处运行”设计的乐趣之一。如今，一个 C 编译器只需轻轻拨动（命令行）开关，就能针对数十种不同的处理器和硬件。一个典型的例子：不久前苹果公司决定将其整个个人计算机产品线从摩托罗拉/IBM 处理器转向英特尔处理器。数千万行 Objective-C 代码被移植到一个全新的架构上，而对开发流程几乎没有造成干扰。



#### 性能

苹果公司将 Cocoa 框架移植到 iPhone 嵌入式处理器上，再次展现了他们的实力；若有机会，他们或许还会将整个软件库迁移至其他处理器。如今，苹果维护着一个单一的 Objective-C 源代码库，该代码库会定期重新编译，以在至少五种不同的处理器架构上运行。Objective-C 在实践中（即便不是在原则层面）实现了“一次编写，随处运行”的理念。

#### 性能

编程语言性能与基准测试的争论向来争议不断，但很少有人会质疑 C 语言是当今最快的高级计算机语言。虽然许多语言声称速度与之相近，但若不借助手动代码优化或取巧手段，几乎不可能超越 C 语言的性能。难怪几乎所有解释器——包括 Java 虚拟机本身——都是用 C 或 C++ 编写的。

由于 Objective-C 本质上是 C 语言，你可以将应用性能优化到硬件的极限。从基于对象的简单设计起步非常容易。如果性能分析表明方案不够快，可以使用 C 语言代码片段进行优化，或者将代码完全用 C 语言重写。若仍嫌不够，C 编译器还允许你直接访问操作系统内核、图形协处理器、向量单元指令，甚至是原始机器码。如果你的目标是打造最快速的应用程序，Objective-C 绝不会成为阻碍。

使用 Objective-C 编程还意味着你可以直接调用庞大的 C 语言 API 库。当今可用的 POSIX C 函数代表着业界最成熟、最稳定、最安全的代码之一。

#### 动态性

Objective-C 常被描述为一种*动态*语言。这个词很难定义——毕竟，每个程序在某种程度上都是动态的。这个词确实描述了 Objective-C 语言本身的某些特性，但更多时候指的是 Objective-C 开发者所遵循的设计模式。

Objective-C 语言比 Java 和 C++ 等语言更具动态性。在 Java 中，你在类中定义的变量和方法与运行时完全一致。而在 Objective-C 中，类定义更具可塑性。其他可能由你或他人开发的对象和框架，可以为你的类和对象增添新能力。反之，你也可以为其他类（甚至系统类）添加新功能。

Objective-C 的另一个迷人特性是运行时系统能够动态修改对象的行为。观察者模式就是一个特别生动的例子。在 Java 中，可观察对象负责维护一组监听对象，并在其属性发生变化时通知它们。而在 Objective-C 中，当一个对象想要观察另一个对象的属性时，它会向键值观察框架发出请求。该框架会*自发地对目标对象进行子类化*，用代码包装其 setter 方法，以通知感兴趣的观察者属性变化。这种非凡的能力意味着*每一个*对象都是可观察的，无需编写一行代码，也无需你提前做任何设计工作。Java 和 Objective-C 之间的宏观对比如清单 1-2 所示。键值观察框架自动完成了为每个对象维护观察者集合并发送请求通知的所有工作。你只需声明属性，并请求在属性发生变化时接收通知即可。

**清单 1-2.** Java 和 Objective-C 中的观察者模式

```java
public interface SecurityGateListener
{
    void gateStateChanged( SecurityGate gate );
}

public class SecurityGate
{
    private HashSet listeners;
    private boolean open;

    public void addListener( SecurityGateListener listener )
    {
        listeners.add(listener);
    }

    public void removeListener( SecurityGateListener listener )
    {
        listeners.remove(listener);
    }

    private void fireStateChange( )
    {
```



```java
for (SecurityGateListener listener: listeners)

listener.gateStateChanged(this);

}

public boolean getOpen()
{
    return open;
}
```

```java
public void setOpen(boolean open)
{
    if (this.open != open) {
        this.open = open;
        fireStateChange();
    }
}
```

[www.it-ebooks.info](http://www.it-ebooks.info/)

## 第 1 章 ■ 简介

```java
class SecurityManager implements SecurityGateListener
{
    SecurityGate gate;
    SecurityManager()
    {
        gate = …
        gate.addListener(this);
    }
    public void gateStateChanged(SecurityGate gate)
    {
        // 安全门状态发生变化……
    }
}
```

```objective-c
@interface SecurityGate : NSObject {
    BOOL open;
}
@property BOOL open;
@end
```

…

```objective-c
@implementation SecurityManager
- (id)init
{
    if ((self = [super init]) != nil) {
        gate = …
        [gate addObserver:self forKeyPath:@"open" options:0 context:NULL];
    }
    return self;
}
- (void)observeValueForKeyPath:(NSString*)keyPath
                     ofObject:(id)object
                       change:(NSDictionary*)change
                      context:(void*)context
{
    if (object == gate) {
        // 安全门状态发生变化……
    }
}
@end
```

Objective-C 应用程序的动态特性往往更多源于开发者采用的设计模式，而非语言本身的固有特性——尽管 Objective-C 确实让这些模式更容易被采纳。以自定义视图对象实现复制和粘贴方法为例，Cocoa 框架定义了一个名为*响应者链*的机制。该链始于当前拥有用户焦点的视图对象，例如自定义视图对象所显示的某些选中文本或图形。这个链条由该对象、包含它的视图对象、包含该视图的窗口，以及最终包含所有窗口的单个应用程序对象组成。当用户从菜单中选择“粘贴”命令时，Cocoa 会检查链中的对象，找到第一个实现了 `-paste:` 方法的对象。同理，菜单项本身会根据响应者链中是否存在实现了 `-paste:` 方法的对象，自动启用或禁用。这种安排不需要任何对象去“寻找”粘贴命令事件，也不用向菜单项注册自己。粘贴命令之所以生效，仅仅是因为某个对象实现了 `-paste:` 方法。

但你可能会问，如果粘贴命令需要条件启用呢？很简单：你的自定义视图对象应该实现 `-validateMenuItem:` 方法。响应者链中实现了*该方法*的对象会被查询，以确定哪些命令应为用户启用。否则，Cocoa 框架将自行决定。

这里的重点不在于对象*是什么*，而在于它*做什么*。对象的能力或角色是通过检查它实现了哪些方法来确定，而不是看它属于哪个类，也不要求它向其他对象注册自己。这种哲学源于面向方面编程（AOP）。Objective-C 开发者将这些方法称为*非正式协议*。

最终结果是一个流畅的应用程序，它能够根据程序中对象的功能是否实现，自行调整并响应用户。一个对象对另一个对象所需了解的信息极少，它与其他视图对象的关系也同样简单。在 Objective-C 中，对象实现更加简单、封装性更强、假设更少，通常也更易于重用。

### 开发者生产力

基于上述所有原因，苹果将 Objective-C 视为其“秘密武器”。与其他大型软件公司相比，苹果能够用更少的开发者，更快地开发出高性能、前沿的应用程序。他们公开表示 Objective-C 是其成功的关键要素之一。

当你开始使用 Objective-C 时，尤其是在构建 GUI 应用程序时，你会发现自己的生产效率竟能如此之高。

### 学习一门新语言

计算机系统和软件不断变化，计算机语言也随之演变。



您可能会多次更换计算机编程语言。在我的职业生涯中，我已经至少更换过八次主要编程语言，期间还涉猎了十多种其他语言。

精通一门新语言需要大量投入，因此人们自然会抵制从自己已熟悉且高效的语言迁移。然而，学习新语言的回报往往是对新机会、新技术和新市场的获取。

`Objective-C`是苹果`Mac OS X`操作系统的首选编程语言，并且（截至撰写本文时）是为苹果`iPhone`和`iPod touch`产品开发原生应用程序的唯一语言。鉴于变化不可避免，任何能最小化向新编程语言过渡痛苦的东西都值得欢迎。这正是我写这本书的原因。

我花了好几年才精通`Java`。又花了好几年精通`Objective-C`。直到之后，我才开始发现两者之间的惊人相似之处。这些相似之处并不存在于语言本身——`Java`和`Objective-C`就像月亮和曼哈顿一样不同。相似之处在于解决方案，即`Java`和`Objective-C`的架构师如何解决问题。

有效的软件开发，与其说是掌握语言的语法细节（尽管这显然很重要），不如说是创造解决方案的能力。作为一名经验丰富的`Java`程序员，你已经积累了一套解决各种常见编程问题的工具箱。当你开始使用一门新语言时，你会抛弃这些技能，转而采用一套全新的工具和技术——这很讽刺，因为你往往是在解决与之前相同的问题。

本书旨在通过提供一种“翻译服务”，让你已经理解的`Java`解决方案与`Objective-C`中的等效解决方案之间建立关联，从而减少这种痛苦。问题是一样的，你会发现解决方案的理念也惊人地相似。经常截然不同的是技术手段。

举个例子，考虑在图形显示器上绘制字符串的方法。在大多数面向对象的语言中，实现此方法的合理位置是在绘图对象类中——即负责在图形上下文中绘制基本形状的类。在`Java`中，这个类是`java.awt.Graphics`，绘制字符串的方法是`drawString(String,int,int)`。在`Cocoa`中，基础图形上下文类是`NSGraphicsContext`。但在其中你找不到任何绘制原语。绘制字符串的方法位于`NSString`类本身。这些方法是`-drawAtPoint:withAttributes:`和`-drawInRect:withAttributes:`。

习惯了`Java`工作方式的你，可能会有一两个疑问。你可能会想，系统的原始字符串类是如何被子类化以包含`-drawAtPoint:withAttributes:`方法的。或者，你可能想知道是哪个白痴系统架构师认为将领域特定的图形绘制原语放入基础字符串类是个好主意。答案都不是。`Objective-C`允许将任意方法附加到*其他*类上。在这种情况下，`AppKitFramework`（所有`GUI`应用程序的核心框架）将一个`-drawAtPoint:withAttributes:`方法附加到了基础字符串类上。这被称为**类别**（category），它颠覆了方法组织的常规规则。如果你曾在各种`NSGraphics`类中寻找`-drawString:AtPoint:withAttributes:`方法，你——就像我一样——会浪费数小时。最终在`NSString`类中找到它们，是我第一次接触`Objective-C`的类别，并永远改变了我对类方法组织的看法。这也改变了我查阅文档的习惯。

但我学到的真正宝贵的一课是：`Java`和`Objective-C`仍然相似多于不同。两者都采用了**模型-视图-控制器**（Model-View-Controller）设计模式。两者都使用可视化容器对象的层次结构来组织窗口内容。每个容器都有一个`-draw`（`paint()`）方法，当需要渲染其内容时会被调用。`-draw`方法使用图形上下文对象来实际绘制线条、多边形、字符串和图像。差异源于`Java`和`Objective-C`语言的设计。`Objective-C`允许你将方法附加到其他类，而`Java`则不允许。这改变了类作者组织方法的方式，但并未改变它们的基本目的。作为一名有成就的`Java`开发者，你不需要关于图形容器的课程，也不需要解释如何或何时绘制线条。你需要知道的是：在`Java`中，字符串绘制原语位于`java.awt.Graphics`类中；而在`Cocoa`中，它们位于`NSString`类中。你还需要知道类别如何将方法附加到另一个类，以及如何实现你自己的类别。

### 术语与文化冲击

如果你从`C`或`C++`世界转向`Java`，那么迁移到`Objective-C`不会是一次翻天覆地的转变。然而，如果你是一名纯粹的`Java`程序员，你会对`Objective-C`中的某些东西感到奇怪、失望、困惑甚至震惊。

有两个主要的学习障碍。第一个是哲学。`Java`是一种高度结构化的语言，旨在生成健壮、可预测的代码。`Java`试图通过施加一系列鼓励良好编程实践的规则来保护程序员免受自身错误的影响。相比之下，`Objective-C`是一种极简主义和实用主义的语言，赋予了程序员巨大的自由和自主权。它几乎不保护你免受常见且可能具有破坏性的编程错误的影响。这种对比让我想起一个关于安全剪刀和手术刀的老笑话：你无法用安全剪刀进行手术，但之后也不必数手指。

大量的思考、努力和学术研究都投入到设计能够“强制”你编写良好代码的语言上。个人而言，我认为这种方法有些误导。在任何语言中，草率的编程都是草率的编程。`Java`和`Objective-C`都需要开发人员具备纪律性——只是方式不同。如果你坚持一些一致的做法，可以在`Objective-C`中编写出与`Java`一样健壮和可靠的应用程序。这些做法将在每一章中重点介绍。

第二个障碍是术语。有很多新术语需要学习。许多新术语是你已经知道的同义词。在正文中，像*协议*（protocol）（接口）这样的新术语后面会用括号括起其对应的`Java`术语。一旦你知道了“协议”是“接口”的同义词，正文将只使用“协议”这一术语。

### 定义“更好”

任何对两种技术的讨论，都不可避免地会引发比较，并试图确定哪一种“更好”。我想这是人之常情。出于多种原因，我尽量在此避免此类比较。

`Java`和`Objective-C`在某些方面是截然不同的语言。在其他方面，它们又几乎完全相同。每种语言都有其特点，使你能够编写出好的代码。每种语言也都有其局限性和缺陷，使得某些任务变得笨拙甚至不可能。出于截然不同的原因，我喜欢用这两种语言编写代码。我认为`Java`和`Objective-C`是*可比较的*语言，但我不认为任何一种在类别上更优越。

各种“更好”的评估问题在于，它们很少具有普遍性。当有人说某物更好时，通常意味着“对我更好”或“对这个特定应用更好”。



### 摘要

我相信你能提出令人信服的理由，证明梅赛德斯-奔驰 C 级轿车是有史以来最好的汽车之一。你的论点可以得到一系列令人印象深刻的设计选择、工程规格、安全特性等的支持。这是一个很有说服力的观点——除非你需要拖运半吨砖块驶过一条土路。在这种情况下，一辆豪华的梅赛德斯轿车可能成为完成这项任务最糟糕的车辆。

几年前，我开始着手开发一个针对 Macintosh 系统的备份解决方案项目。

我最初用 Java 启动了这个项目。这并不令人意外，因为我曾认为 Java 是有史以来最好的编程语言。尽管 Mac OS X 对 Java 提供了出色的支持，但我很快意识到，我无法用 Java 开发出一个令人信服的解决方案。一个可行的商业应用程序需要使用原生编程语言，而当时的选择是 `C`、`C++` 和 `Objective-C`。这个项目就是我那半吨重的砖块。

`Objective-C` 并没有取代我对 Java 的热爱。相反，它与我已有的知识并驾齐驱，成为一种有力的替代方案。在某些情况下，Java 无疑优于 `Objective-C`。而在另一些情况下，情况则恰好相反。我不会执着于哪种工具最好，而是专注于哪种工具最适合手头的工作。

`Objective-C` 是一门成熟且强大的语言，具有非常实际的优势。但你不必像编程初学者那样去学习它。本书的其余部分旨在利用你现有的 Java 知识和经验，将其重新聚焦到 `Objective-C` 上，让你尽快成为一名高效的 `Objective-C` 开发者。

[www.it-ebooks.info](http://www.it-ebooks.info/)

