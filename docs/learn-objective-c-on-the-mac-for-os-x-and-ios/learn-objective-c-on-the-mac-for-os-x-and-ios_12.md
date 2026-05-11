# 第 13 章 协议

在上一章中，我们讨论了分类和非正式协议的神奇之处。使用非正式协议时，如第 12 章所示，你只需实现想要响应的方法。对于第 12 章中的`NSNetServiceBrowser`委托，我们仅实现了在网络中添加或移除新服务时调用的两个方法，而不必实现`NSNetServiceBrowserDelegate`非正式协议中的其他六个方法。我们也没有在对象中声明任何内容来表明自己可以用作`NSNetServiceBrowser`的委托。一切都以最小的工作量正常运行。

你可能已经猜到，Objective-C 和 Cocoa 还包含了正式协议的概念。在本章中，我们将探讨其工作原理。

## 正式协议

**正式协议**（类似于非正式协议）是一个带名称的方法和属性列表。然而，正式协议要求你显式地采纳它。你通过在类的`@interface`声明中列出协议名称来**采纳**（adopt）该协议。完成这一操作后，你的类就被称为**遵循**（conform）该协议（你可能以为自己是非遵循者）。采纳协议意味着你承诺实现该协议的所有方法。如果你没有这样做，编译器会生成警告。

> **注意**：正式协议类似于 Java 接口。事实上，Objective-C 的协议是 Java 接口的灵感来源。

为什么要创建或采纳正式协议？听起来需要实现每个方法，工作量很大。根据协议的不同，可能还涉及一些繁琐的工作。但通常情况下，协议只有少量方法需要实现，而且无论如何你都必须全部实现才能获得有用的功能集，因此正式协议的要求通常不会成为负担。Objective-C 2.0 增加了一些很好的特性，使得使用协议远没有那么繁重，我们将在本章末尾讨论这些内容。

## 声明协议

让我们看看 Cocoa 声明的一个协议——`NSCopying`。如果你采纳了`NSCopying`，你的对象就知道如何复制自身：

```
@protocol NSCopying
- (id) copyWithZone: (NSZone *) zone;
@end
```

该语法看起来与声明类或分类的语法类似。这里使用`@protocol`而不是`@interface`来告诉编译器：“我将展示一个新正式协议的样子。”该语句后面跟着协议名称。协议名称必须是唯一的。

你也可以拥有父协议，类似于父类。要指定父协议，请在协议名称后的尖括号中声明它。

```
@protocol MySuperDuberProtocol <MyParentProtocol>
@end
```

上述代码表示`MySuperDuperProtocol`扩展了`MyParentProtocol`，因此你必须同时满足两个协议中所有必需方法的实现。你始终可以将`NSObject`用作根协议。不要将其与`NSObject`类混淆。`NSObject`类遵循`NSObject`协议，这意味着所有对象都遵循`NSObject`协议。建议将`NSObject`协议作为你正在定义的协议的父协议。

接下来是一个方法声明列表，每个协议采纳者都必须实现这些方法。协议声明以`@end`结束。协议不引入任何实例变量。

让我们看另一个例子，这是来自 Cocoa 的`NSCoding`协议：

```
@protocol NSCoding
- (void) encodeWithCoder: (NSCoder *) encoder;
- (id) initWithCoder: (NSCoder *) decoder;
@end
```

当一个类采纳`NSCoding`时，该类承诺实现这两个消息。`encodeWithCoder:`用于将对象的实例变量“冻干”到`NSCoder`对象中。`initWithCoder:`从`NSCoder`中提取冻干的实例变量，并用它们初始化一个新对象。这两个方法总是成对实现：如果你永远不会将编码的对象恢复为新对象，那么编码就没有意义；如果你从不编码对象，你就没有任何东西可以用来创建新对象。

## 采纳协议

要采纳协议，你需要在类声明中用尖括号列出该协议。例如，如果`Car`采纳了`NSCopying`，声明如下：

```
@interface Car : NSObject <NSCopying>
{
    // 实例变量
}
// 方法
@end // Car
```

如果`Car`同时采纳了`NSCopying`和`NSCoding`，声明如下：

```
@interface Car : NSObject <NSCopying, NSCoding>
{
    // 实例变量
}
// 方法
@end // Car
```

你可以按任意顺序列出协议，顺序无关紧要。

当你采纳一个协议时，你是在向阅读类声明的程序员传达信息：该类的对象可以做两件非常重要的事情——它们可以编码/解码自身，也可以复制自身。

## 实现协议

关于协议需要了解的内容大致就是这些（除了声明变量时的一些语法细节，我们稍后会讨论）。本章的大部分内容将用于实践，我们将通过练习为`CarParts`采纳`NSCopying`协议。

### Car-bon 副本



让我们一同高呼内存管理的规则：“如果你从`alloc`、`copy`或`new`中获取一个对象，它的引用计数为 1，你需要负责释放它。”我们已经介绍过`alloc`和`new`，但尚未讨论`copy`。`copy`方法的作用是创建对象的副本。`copy`消息会指示对象创建一个全新的对象，并使新对象与接收者相同。

接下来，我们将扩展`CarParts`，使其能够复制汽车（等到底特律听到这个消息吧）。相关代码位于`13.01 CarParts-Copy`项目文件夹中。在此过程中，我们将探讨实现副本创建代码时涉及的一些有趣的微妙之处。

## 制作副本

实际上，有多种不同的方式可以制作副本。大多数对象都会引用——即指向——其他对象。当你创建**浅拷贝**时，不会复制被引用的对象；新的副本仅仅指向已经存在的被引用对象。`NSArray`的`copy`方法进行的就是浅拷贝。当你复制一个`NSArray`时，你的副本只会复制指向被引用对象的指针，而不会复制对象本身。如果你复制了一个包含五个`NSString`的`NSArray`，最终程序中仍然只有五个字符串，而不是十个。在这种情况下，每个对象都有一个指向每个字符串的指针。

另一方面，**深拷贝**会复制所有被引用的对象。如果`NSArray`的`copy`是深拷贝，那么在复制之后，你将拥有十个字符串在程序中运行。对于`CarParts`，我们将使用深拷贝。这样，当你复制一辆汽车时，你可以更改它引用的值（例如轮胎气压），而不会同时改变两辆汽车的气压。

你可以根据特定类的需求，自由地混合使用深拷贝和浅拷贝来复制组合对象。

要复制汽车，我们还需要能够复制引擎和轮胎。程序员们，发动引擎吧！

## 复制引擎

我们要处理的第一个类是`Engine`。为了能够复制引擎，该类需要遵循`NSCopying`协议。以下是`Engine`的新接口：

```objectivec
@interface Engine : NSObject <NSCopying>

@end // Engine
```

由于我们遵循了`NSCopying`协议，就必须实现`copyWithZone:`方法。Zone 是`NSZone`，它是一个内存区域，你可以从中分配内存。当你向对象发送`copy`消息时，它在到达你的代码之前会转换为`copyWithZone:`。在过去，`NSZone`比现在重要得多，但我们仍然像带着一小件行李一样不得不处理它。

以下是`Engine`的`copyWithZone:`实现：

```objectivec
- (id) copyWithZone: (NSZone *) zone
{
    Engine *engineCopy;
    engineCopy = [[[self class] allocWithZone: zone] init];
    return (engineCopy);
} // copyWithZone
```

`Engine`没有实例变量，所以我们只需要创建一个新的`Engine`对象。然而，这并不像听起来那么容易。看看`engineCopy`右侧那个复杂的语句。消息发送嵌套了三层！

这个方法首先获取`self`的类。然后，它向该类发送`allocWithZone:`消息以分配一些内存并创建一个该类的新对象。最后，向这个新对象发送`init`消息以对其进行初始化。让我们讨论一下为什么需要这个复杂的消息嵌套，特别是`[self class]`的部分。

回想一下，`alloc`是一个类方法。`allocWithZone:`也是一个类方法，你可以从其方法声明中的前导加号看出：

```objectivec
+ (id) allocWithZone: (NSZone *) zone;
```

我们需要向一个类发送此消息，而不是向实例发送。我们应该向哪个类发送呢？我们的第一直觉是将`allocWithZone:`发送给`Engine`，像这样：

```objectivec
[Engine allocWithZone: zone];
```

这对`Engine`有效，但对`Engine`的子类无效。为什么不行呢？想想`Slant6`，它是`Engine`的子类。如果你向一个`Slant6`对象发送`copy`消息，最终代码会进入`Engine`的`copyWithZone:`，因为我们最终使用来自`Engine`的复制逻辑。如果你直接将`allocWithZone:`发送给`Engine`类，则会创建一个新的`Engine`对象，而不是`Slant6`对象。如果`Slant6`添加了实例变量，事情可能会变得非常混乱。在这种情况下，`Engine`对象的空间可能不足以容纳额外的变量，从而导致内存溢出错误。

现在你可能明白我们为什么使用`[self class]`了。通过使用`[self class]`，`allocWithZone:`将被发送给接收`copy`消息的对象的类。如果`self`是一个`Slant6`对象，这里就会创建一个新的`Slant6`实例。如果将来程序中添加了某种全新的引擎（比如`MatterAntiMatterReactor`），这种新引擎也能被正确地复制。

方法的最后一行返回新创建的对象。

让我们再次检查一下内存管理。复制操作应返回一个引用计数为 1 的对象（并且不是自动释放的）。我们通过`alloc`获取新对象，它总是返回一个引用计数为 1 的对象，并且我们没有释放它，所以我们在内存管理方面没有问题。

让`Engine`具备复制能力就这些了。我们无需改动`Slant6`。因为`Slant6`没有添加任何实例变量，所以在进行复制时不需要做任何额外工作。多亏了继承，以及在创建对象时使用`[self class]`的技术，`Slant6`对象也可以被复制。

## 复制轮胎

复制轮胎比复制引擎更复杂。`Tire`有两个实例变量（`pressure`和`treadDepth`）需要复制到新的轮胎中，而`AllWeatherRadial`子类引入了两个额外的实例变量（`rainHandling`和`snowHandling`），这些也必须复制到一个新对象中。

首先是`Tire`。

```objectivec
@interface Tire : NSObject <NSCopying>

@property float pressure;
@property float treadDepth;

// … methods

@end // Tire
```

现在是`copyWithZone:`的实现：

```objectivec
- (id) copyWithZone: (NSZone *) zone
{
    Tire *tireCopy;
    tireCopy = [[[self class] allocWithZone: zone] initWithPressure:
                pressure treadDepth: treadDepth];
    return (tireCopy);
} // copyWithZone
```

你可以看到这里像`Engine`一样使用了`[[self class] allocWithZone: zone]`模式。由于我们必须在创建对象时调用 init，所以可以方便地使用`Tire`的`initWithPressure:treadDepth:`将新轮胎的`pressure`和`treadDepth`设置为我们正在复制的轮胎的值。这个方法恰好是`Tire`的指定初始化器，但你不必为了复制而使用指定初始化器。如果你愿意，也可以使用普通的`init`以及存取器方法来更改属性。

**给您的实用提示** 你可以通过 C 指针运算符直接访问公共实例变量，如下所示：

```objectivec
tireCopy->pressure = pressure;
tireCopy->treadDepth = treadDepth;
```

通常情况下，我们倾向于使用 init 方法和存取器方法，以防设置属性涉及到额外的工作（尽管这种情况不太可能发生）。

现在，轮到`AllWeatherRadial`了。`AllWeatherRadial`的`@interface`没有变化：

```objectivec
@interface AllWeatherRadial : Tire

// … properties

// … methods

@end // AllWeatherRadial
```

等等——`<NSCopying>`在哪里？你不需要它，你可能已经猜到了原因。当`AllWeatherRadial`继承自`Tire`时，它一并带来了`Tire`的所有“行李”，包括对`NSCopying`协议的遵循。

不过，我们仍需要实现`copyWithZone:`，因为必须确保`AllWeatherRadial`的雨雪处理实例变量被复制：

```objectivec
- (id) copyWithZone: (NSZone *) zone
{
    AllWeatherRadial *tireCopy;
    tireCopy = [super copyWithZone: zone];
    tireCopy.rainHandling = rainHandling;
    tireCopy.snowHandling = snowHandling;
    return (tireCopy);
} // copyWithZone
```



## 复制自定义对象

### 复制 `AllWeatherRadial`

由于 `AllWeatherRadial` 是可复制类的子类，它无需执行我们之前用到的 `allocWithZone:` 和 `[self class]` 这些操作。该类只需向父类请求一份副本，并期望父类在分配对象时正确处理，使用了 `[self class]`。因为 `Tire` 的 `copyWithZone:` 方法使用 `[self class]` 来确定要创建的对象类型，所以它会创建一个新的 `AllWeatherRadial` 实例，这正是我们想要的。该代码还为我们处理了 `pressure` 和 `treadDepth` 值的复制。现在，是不是很方便？

剩下的工作就是设置处理雨雪路况的属性值。这些属性正是用来完成这项任务的。

### 复制 `Car`

既然我们现在可以复制发动机、轮胎及其子类，是时候让 `Car` 自身也变得可复制了。

正如你所料，`Car` 需要遵循 `NSCopying` 协议：

```objc
@interface Car : NSObject <NSCopying>

// 属性

// … 方法

@end // Car
```

为了履行对 `NSCopying` 协议的承诺，`Car` 必须实现我们的老朋友 `copyWithZone:`。以下是 `Car` 的 `copyWithZone:` 方法：

```objc
- (id) copyWithZone: (NSZone *) zone
{
    Car *carCopy;
    carCopy = [[[self class] allocWithZone: zone] init];
    carCopy.name = self.name;

    Engine *engineCopy;
    engineCopy = [[engine copy] autorelease];
    carCopy.engine = engineCopy;

    for (int i = 0; i < 4; i++)
    {
        Tire *tireCopy;
        tireCopy = [[self tireAtIndex: i] copy];
        [tireCopy autorelease];
        [carCopy setTire: tireCopy atIndex: i];
    }

    return (carCopy);
} // copyWithZone
```

这段代码比我们之前写的稍多一些，但所有内容都与你已经见过的类似。

首先，通过向接收此消息的对象的类发送 `allocWithZone:` 来分配一辆新车：

```objc
Car *carCopy;
carCopy = [[[self class] allocWithZone: zone] init];
```

`CarParts-copy` 目前不包含 `Car` 的子类，但未来可能会有。你永远不知道何时会有人制造出那种能时间旅行的德罗宁汽车。我们可以像之前所做的那样，通过使用 `self` 的类来分配新对象，从而做到面向未来。

我们需要复制汽车的名称：

```objc
carCopy.name = self.name;
```

请记住，`name` 属性会复制其字符串，因此新车将拥有正确的名称。

接下来，制作一份发动机的副本，并告知新车副本使用该副本作为其发动机：

```objc
Engine *engineCopy;
engineCopy = [[engine copy] autorelease];
carCopy.engine = engineCopy;
```

看到那个 `autorelease` 了吗？它是必需的吗？让我们稍微思考一下内存管理。 `[engine copy]` 会返回一个引用计数为 1 的对象。 `setEngine:` 会保留（retain）传入的发动机，使其引用计数变为 2。当新车副本最终被销毁时，`Car` 的 `dealloc` 会释放（release）发动机，因此其引用计数会回到 1。到那时，这段代码早已执行完毕，所以没有代码会执行最后一次 release 以使其被释放。在这种情况下，发动机对象就会泄漏。通过自动释放（autorelease），引用计数会在未来某个时刻自动释放池被清空时减一。

我们是否可以用简单的 `[engineCopy release]` 来代替自动释放？可以。但你必须在调用 `setEngine:` 之后执行 release；否则，发动机副本会在被使用之前就被销毁。选择哪种内存管理方式取决于你的个人喜好。有些程序员喜欢在函数中将内存清理工作集中在一处，而另一些喜欢在创建对象时就自动释放，以免以后忘记释放。这两种方法都是有效的，但请注意，对于 iOS 应用，Apple 建议立即释放而不是使用 `autorelease`，因为你不知道自动释放何时会减少引用计数。

在 `carCopy` 配备新发动机后，一个 `for` 循环会循环四次，复制每个轮胎并将副本安装到新车上：

```objc
for (int i = 0; i < 4; i++)
{
    Tire *tireCopy;
    tireCopy = [[self tireAtIndex: i] copy];
    [tireCopy autorelease];
    [carCopy setTire: tireCopy atIndex: i];
}
```



循环中的代码使用访问器方法依次获取位置 0、位置 1 等位置的轮胎，并在每次循环时依次进行。每个获取到的轮胎会被复制并自动释放，以确保其内存得到妥善处理。接着，汽车副本被指示在相同位置使用这个新轮胎。由于我们精心构造了 `Tire` 和 `AllWeatherRadial` 中的 `copyWithZone:` 方法，这段代码能够与任何一种轮胎正常工作。你会注意到我们没有使用 `NSArray` 的 `copy` 方法，因为它执行的是浅拷贝，而我们需要的是深拷贝。

最后，这里是完整的 `main()` 函数。其中大部分是你在前面章节中见过的旧代码；时髦的新代码以粗体显示：

```c
int main (int argc, const char * argv[]) {
    @autoreleasepool {
        Car *car = [[Car alloc] init];
        car.name = @"Herbie";
        
        for (int i = 0; i < 4; i++) {
            AllWeatherRadial *tire;
            tire = [[AllWeatherRadial alloc] init];
            [car setTire: tire atIndex: i];
            [tire release];
        }
        
        Slant6 *engine = [[Slant6 alloc] init];
        car.engine = engine; [engine release];
        
        [car print];
        
        Car *carCopy = [car copy];
        [carCopy print];
        
        [car release];
        [carCopy release];
    }
    return (0);
} // main
```

在原始汽车被打印输出后，创建了一份副本，并打印了该副本。因此，我们应该得到两组相同的输出。运行程序，你会看到类似这样的内容：

```
Herbie has:
AllWeatherRadial: 34.0 / 20.0 / 23.7 / 42.5
AllWeatherRadial: 34.0 / 20.0 / 23.7 / 42.5
AllWeatherRadial: 34.0 / 20.0 / 23.7 / 42.5
AllWeatherRadial: 34.0 / 20.0 / 23.7 / 42.5
I am a slant-6. VROOOM!

Herbie has:
AllWeatherRadial: 34.0 / 20.0 / 23.7 / 42.5
AllWeatherRadial: 34.0 / 20.0 / 23.7 / 42.5
AllWeatherRadial: 34.0 / 20.0 / 23.7 / 42.5
AllWeatherRadial: 34.0 / 20.0 / 23.7 / 42.5
I am a slant-6. VROOOM!
```

## 协议与数据类型

你可以在实例变量和方法参数所使用的数据类型中指定协议名称。通过这样做，你向 Objective-C 编译器提供了更多信息，从而帮助它检查代码中的错误。

回顾一下，`id`类型表示指向任意类型对象的指针；它是一种通用的对象类型。你可以将任何对象赋值给`id`变量，也可以将`id`变量赋值给任何类型的对象指针。如果在`id`后面跟上协议名称（用尖括号括起来），则是在告诉编译器（以及任何阅读代码的人）：你期望的是任意类型的对象，只要它遵循该协议即可。

例如，`NSControl`有一个名为`setObjectValue:`的方法，它需要一个遵循`NSCopying`协议的对象：

```objectivec
- (void) setObjectValue: (id<NSCopying>) object;
```

编译这段代码时，编译器会检查参数的类型，并给出警告，例如“类 'Triangle' 未实现 'NSCopying' 协议”。这非常方便！

## Objective-C 2.0 新特性

苹果公司永远不会满足于现状。Objective-C 2.0 为协议新增了两个修饰符：`@optional` 和 `@required`。等等，我们刚才是不是说过，如果你遵循了一个协议，就必须实现该协议的所有方法？是的，没错，除非你指定了 `@optional` 关键字。因此，你可以编写像下面这样时髦的代码：

```objectivec
@protocol BaseballPlayer
- (void)drawHugeSalary;
@optional
- (void)slideHome;
- (void)catchBall;
- (void)throwBall;
@required
- (void)swingBat;
@end // BaseballPlayer
```

这段代码的意思是，采用`BaseballPlayer`协议的类必须实现`-drawHugeSalary`和`-swingBat`方法，但可以选择实现`slideHome`、`catchBall`或`throwBall`方法。

既然非正式协议似乎也能正常工作，为什么苹果还要添加这个特性呢？这是因为，在类声明和方法声明中明确表达我们意图的工具箱里，又多了一件利器。假设你在头文件中看到这样一行代码：

```objectivec
@interface CalRipken : Person <BaseballPlayer>
```

你立刻就会知道，我们处理的是一个能赚大钱、能挥棒，并且可能会滑垒、接球或投球的人。如果使用非正式协议，就无法传达这些信息。同样地，你也可以用协议来修饰方法的参数：

```objectivec
- (void)draft:(Person<BaseballPlayer>);
```



