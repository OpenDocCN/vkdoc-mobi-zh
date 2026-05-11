# 第 11 章 属性

还记得在很久以前，我们为实例变量编写访问器方法的时候吗？我们编写了大量样板代码，既要创建`–setBlah`方法来设置对象的`blah`属性（显而易见），又要创建`–blah`方法来获取它。如果属性是一个对象，我们还需要保留新对象并释放旧对象。市面上有一些实用工具，可以将你的类定义转换为方法声明和定义，然后你可以将其粘贴到文件中。但即便如此，编写访问器方法仍然是一项大量令人麻木的工作，而这些精力本可以更好地用于实现你程序中独特而酷炫的功能。

在 Objective-C 2.0 中，Apple 引入了**属性**，它结合了新的编译器指令和新的属性访问语法。新的属性特性大大减少了你必须编写的无脑代码量。在本章中，我们将修改`10.01 CarPartsInit`项目以使用属性。本章的最终代码可以在`11.01 CarProperties`项目中找到。

请记住，Objective-C 2.0 的特性只能在 Mac OS X 10.5（Leopard）或更高版本上使用，所以如果你需要支持较老的系统，必须三思而后行。属性在 Cocoa 的新版本（特别是炫酷的 Core Animation 特性）中大量使用，在 iOS 开发中也频繁使用，因此非常值得熟悉。

### 精简属性值

首先，我们将转换一个相对简单的类`AllWeatherRadial`，使其使用属性。为了让讨论更有趣一点，我们将在`main`函数中添加一些调用，来改变我们创建的`AllWeatherRadial`对象的一些值。我们模拟某人从不同的商店购买了四个打折轮胎，因此四个轮胎的操控特性各不相同。

下面是更新后的`main`函数，新添加的行以粗体显示：

```
int main(int argc, const char * argv[])
{
    @autoreleasepool
    {
        Car *car = [[Car alloc] init];
        for (int i = 0; i < 4; i++)
        {
            AllWeatherRadial *tire;
            tire = [[AllWeatherRadial alloc] init];
            [tire setRainHandling:20+i];
            [tire setSnowHandling:28+i];
            NSLog(@"tire %d's handling is %.f %.f", i,
                  [tire rainHandling], [tire snowHandling]);
            [car setTire:tire atIndex:i];
            [tire release];
        }
        
        Engine *engine = [[Slant6 alloc] init];
        [car setEngine:engine];
        [car print];
        [car release];
    }
    return (0);
}
```

如果你现在运行程序，会得到以下输出，显示了我们新改变的轮胎操控值：

```
tire 0's handling is 20 28
tire 1's handling is 21 29
tire 2's handling is 22 30
tire 3's handling is 23 31
AllWeatherRadial: 34.0 / 20.0 / 20.0 / 28.0
AllWeatherRadial: 34.0 / 20.0 / 21.0 / 29.0
AllWeatherRadial: 34.0 / 20.0 / 22.0 / 30.0
AllWeatherRadial: 34.0 / 20.0 / 23.0 / 31.0
I am a slant-6. VROOOM!
```

### 精简接口

现在让我们看看`AllWeatherRadial`的类接口：

```objective-c
#import <Foundation/Foundation.h>
#import "Tire.h"

@interface AllWeatherRadial : Tire
{
    float rainHandling;
    float snowHandling;
}
- (void) setRainHandling: (float) rainHanding;
- (float) rainHandling;
- (void) setSnowHandling: (float) snowHandling;
- (float) snowHandling;
@end // AllWeatherRadial
```

现在你应该已经对此非常熟悉了。让我们用属性的方式来清理它：

```objective-c
#import <Foundation/Foundation.h>
#import "Tire.h"

@interface AllWeatherRadial : Tire
{
    float rainHandling;
    float snowHandling;
}
@property float rainHandling;
@property float snowHandling;
@end // AllWeatherRadial
```

是不是更简单了？不需要那四个方法定义了。注意，我们增加了一对以`@`符号开头的关键字。回忆一下，`@`符号是“Objective-C 的独特语法即将出现”的信号！`@property`是一个新的编译器特性，用于声明一个新的对象属性。

`@property float rainHandling;`表明`AllWeatherRadial`类的对象有一个`float`类型的属性，名为`rainHandling`。它还表明你可以通过调用`–setRainHanding:`来设置该属性，并通过调用`-rainHandling`来访问该属性。你现在就可以运行程序，其行为与之前完全一样。`@property`所做的仅仅是自动声明该属性的`setter`和`getter`方法。该属性实际上不必与实例变量的名称完全一致，但在大多数情况下是一致的。我们稍后会讨论这个问题。你还可以对属性进行一些额外的配置，我们稍后也会讨论，请耐心等待。

### 精简实现

现在，让我们再次看看`AllWeatherRadial`的实现：

```objective-c
#import "AllWeatherRadial.h"

@implementation AllWeatherRadial
- (id) initWithPressure: (float) p treadDepth: (float) td
{
    if (self = [super initWithPressure: p treadDepth: td])
    {
        rainHandling = 23.7;
        snowHandling = 42.5;
    }
    return (self);
} // initWithPressure:treadDepth

- (void) setRainHandling: (float) rh
{
    rainHandling = rh;
} // setRainHandling

- (float) rainHandling
{
    return (rainHandling);
} // rainHandling

- (void) setSnowHandling: (float) sh
{
    snowHandling = sh;
} // setSnowHandling

- (float) snowHandling
{
    return (snowHandling);
} // snowHandling

- (NSString *) description
{
    NSString *desc;
    desc = [[NSString alloc] initWithFormat:
            @"AllWeatherRadial: %.1f / %.1f / %.1f / %.1f",
            [self pressure], [self treadDepth],
            [self rainHandling],
            [self snowHandling]];
    return (desc);
} // description
@end // AllWeatherRadial
```

在上一章中，我们讨论了`init`方法、指定初始化器、所有的`setter`和`getter`方法，以及`description`方法。现在，我们将毫不留情地删除所有`setter`和`getter`方法，并用两行代码替换它们：

```objective-c
#import "AllWeatherRadial.h"

@implementation AllWeatherRadial
@synthesize rainHandling;
@synthesize snowHandling;

- (id) initWithPressure: (float) p
            treadDepth: (float) td
{
    if (self = [super initWithPressure: p treadDepth: td])
    {
        rainHandling = 23.7;
        snowHandling = 42.5;
    }
    return (self);
} // initWithPressure:treadDepth

- (NSString *) description
{
    NSString *desc;
    desc = [[NSString alloc] initWithFormat:
            @"AllWeatherRadial: %.1f / %.1f / %.1f / %.1f", [self pressure], [self treadDepth],
            [self rainHandling], [self snowHandling]];
    return (desc);
} // description
@end // AllWeatherRadial
```

`@synthesize`是一个编译器特性，意思是“为此属性创建访问器方法”。对于代码行`@synthesize rainHandling;`，编译器会发出`–setRainHandling:`和`–rainHandling`的编译后代码。

**注意** 你可能熟悉代码生成：Cocoa 的访问器编写工具和其他平台上的 UI 构建器会生成源代码，然后进行编译。但`@synthesize`并非代码生成。你永远看不到实现`–setRainHandling:`和`–rainHandling`的源代码，但这些方法会存在并且可以被调用。这使 Apple 能够灵活地更改 Objective-C 中访问器的生成方式，从而实现更安全的实现或更好的性能。

如果你在类中为属性提供了这些方法，编译器就不会再创建它们。编译器只创建缺失的方法。

如果你现在运行程序，会得到与修改前相同的结果。



大多数属性都由一个变量支持，因此当你合成`getter`和`setter`时，编译器会自动创建一个与属性同名的实例变量。请注意，头文件中有两个变量，分别叫做`rainHandling`和`snowHandling`。`setter`和`getter`将使用这些变量。如果你没有声明这些变量，编译器会创建它们。我们可以在两个地方添加实例变量：头文件或实现文件。我们甚至可以混合使用，将一部分放在头文件中，另一部分放在实现文件中。

为什么我们要把它们放在一个地方而不是另一个地方呢？假设你有一个子类，并且想从子类中直接访问这些变量，而不是通过属性访问。在这种情况下，变量需要放在头文件中。如果变量仅用于你的类，你可以将它们移到`.m`文件中，从而清理公共接口。头文件将如下所示：

```objectivec
@interface AllWeatherRadial : Tire

@property float rainHandling;

@property float snowHandling;

@end // AllWeatherRadial
```

实现文件将如下所示：

```objectivec
@implementation AllWeatherRadial

{
    float rainHandling;
    float snowHandling;
}

@synthesize rainHandling;
@synthesize snowHandling;

- (id) initWithPressure: (float)p treadDepth: (float)td
{
    if (self = [super initWithPressure: p treadDepth: td])
    {
        rainHandling = 23.7;
        snowHandling = 42.5;
    }
    return (self);
} // initWithPressure:treadDepth

- (NSString *) description
{
    NSString *desc;
    desc = [[NSString alloc] initWithFormat:
        @"AllWeatherRadial: %.1f / %.1f / %.1f / %.1f",
        [self pressure], [self treadDepth],
        [self rainHandling],
        [self snowHandling]];
    return (desc);
} // description

@end // AllWeatherRadial
```

正如你所看到的，头文件看起来更简洁，代码更少，这通常意味着它会给你带来更少的麻烦。

我们可以更进一步：请记住，如果我们不指定实例变量，编译器会为我们创建它们。因此，我们可以删除这段代码而没有任何负面影响：

```objectivec
{
    float rainHandling;
    float snowHandling;
}
```

（我们可以将整个代码块，包括花括号，都删除。）现在我们已经删除了大量代码，这很可能节省了我们输入和调试的时间。

## 点符号的惊人之处

Objective-C 2.0 的属性引入了一种新的语法糖，使得访问对象属性变得更加容易。这些新特性也让 Objective-C 对习惯使用 C++ 和 Java 等语言的人来说更容易上手。

回顾一下我们添加到`main`函数中来改变轮胎操控值的两行新代码：

```objectivec
[tire setRainHandling: 20 + i];
[tire setSnowHandling: 28 + i];
```

我们可以用以下代码替换它们：

```objectivec
tire.rainHandling = 20 + i;
tire.snowHandling = 28 + i;
```

如果你再次运行程序，你会看到相同的结果。我们使用`NSLog`来报告轮胎的操控值：

```objectivec
NSLog(@"tire %d's handling is %.f %.f", i, [tire rainHandling], [tire snowHandling]);
```

现在我们可以用以下代码替换它：

```objectivec
NSLog(@"tire %d's handling is %.f %.f", i, tire.rainHandling, tire.snowHandling);
```

点符号看起来很像 C 语言中的结构体访问和 Java 中的对象访问——这是有意为之。当你看到点号出现在等号左侧时，相应属性名的`setter`（即`-setRainHandling:`和`-setSnowHandling:`）将被调用。否则，如果你看到点号出现在对象变量旁边，相应属性名的`getter`（即`-rainHandling`和`snowHandling`）将会被调用。

**注意：** 点符号只是调用访问器方法的简写形式。在底层并没有发生任何额外的魔法。在第 18 章中，我们将讨论键值编码，它实际上会使用一些硬核的运行时魔法。属性点符号与键值编码在幕后所做的酷事情之间没有关联。

如果你在使用属性，并且遇到了关于访问非结构体内容的奇怪错误信息，请确保你已经包含了所使用类所需的所有头文件。



关于属性所引入的内容，基本上就是这些了。当然，我们还需要讨论一些额外的场景，以确保正确处理对象属性，并避免同时暴露 setter 和 getter。接下来我们就来谈这些。

## 反对使用属性

到目前为止，我们关注的是标量类型（特别是 `float`）的属性，但同样的技术也适用于 `int`、`char`、`BOOL` 和 `struct`。例如，如果需要，你也可以声明一个 `NSRect` 属性。

对象会带来一些额外的复杂性。回想一下，当对象在我们的存取方法中传递时，我们会对其进行 `retain` 和 `release`。对于某些对象值，特别是字符串值，你总是希望对其进行 `-copy` 操作。然而，对于其他对象值，比如委托（delegate），我们将在下一章讨论，你根本不想 retain 它们。

**注意**：等等，稍等一下。这个拷贝和不 retain 是怎么回事？

你希望对字符串参数进行拷贝。一个常见的错误是从用户界面（例如文本字段）获取一个字符串，并将其用作某个对象的名称。从文本字段中获取的字符串通常是可变字符串，当用户输入新内容时，它们会发生变化。拷贝该字符串可以防止其值意外改变。

那么，关于不 retain 对象呢？有一个特殊情况，称为 **retain 循环**，在这种情况下，引用计数机制会失效。如果你有一个所有者/被所有者关系，比如 `Car` 和 `Engine`，你希望 `Car` retain（拥有）`Engine`，但反之则不然。`Engine` 不应该 retain 它被安装到的那辆 `Car`。如果 `Car` retain 了 `Engine`，而 `Engine` 又 retain 了 `Car`，那么两者的引用计数都不会归零，两者也就永远不会被清理。`Car` 的 `dealloc` 方法不会被调用，直到 `Engine` 在它自己的 `dealloc` 中释放了 `Car`；而 `Engine` 的 `dealloc` 方法也不会被调用，直到 `Car` 的 `dealloc` 释放了 `Engine`。它们就这样僵持在那里，互相凝视，等待对方先眨眼。通用规则是：所有者对象 retain 被拥有的对象，而不是反过来。

让我们给 `Car` 添加一个新功能，这样我们就可以尝试一些新的属性语法了。这会很令人兴奋！我们将给车一个名字。我们先采用老派的方式，使用传统的存取方法。首先是 `Car.h`，新内容以粗体显示：

```objc
@class Tire;

@class Engine;

@interface Car : NSObject

{
    NSString *name;
    NSMutableArray *tires;
    Engine *engine;
}

- (void)setName: (NSString *) newName;
- (NSString *) name;
- (void) setEngine: (Engine *) newEngine;
- (Engine *) engine;
- (void) setTire: (Tire *) tire atIndex: (int) index;
- (Tire *) tireAtIndex: (int) index;
- (void) print;

@end // Car
```

现在，我们添加存取方法的实现（注意我们正在拷贝 `name`），同时为车选择一个默认名称，并在 `description` 中显示它：

```objc
#import "Car.h"

@implementation Car

- (id) init
{
    if (self = [super init])
    {
        name = [[NSString alloc] initWithString:@"Car"];
        tires = [[NSMutableArray alloc] init];
        int i;
        for (i = 0; i < 4; i++) {
            [tires addObject: [NSNull null]];
        }
    }
    return (self);
} // init

- (void) dealloc
{
    [name release];
    [tires release];
    [engine release];
    [super dealloc];
} // dealloc

- (void)setName: (NSString *)newName
{
    [name release];
    name = [newName copy];
} // setName

- (NSString *)name
{
    return (name);
} // name

- (Engine *) engine
{
    return (engine);
} // engine

- (void) setEngine: (Engine *) newEngine
{
    [newEngine retain];
    [engine release];
    engine = newEngine;
} // setEngine

- (void) setTire: (Tire *) tire atIndex: (int) index
{
    [tires replaceObjectAtIndex: index withObject: tire];
} // setTire:atIndex:

- (Tire *) tireAtIndex: (int) index
{
    Tire *tire;
    tire = [tires objectAtIndex: index];
    return (tire);
} // tireAtlndex:

- (void) print
{
    NSLog (@"%@ has:", name);
    for (int i = 0; i < 4; i++)
    {
        NSLog (@"%@", [self tireAtIndex: i]);
    }
    NSLog (@"%@", engine);
} // print

@end // Car
```

然后我们在 `main` 中设置名称：

```objc
Car *car = [[Car alloc] init];
[car setName: @"Herbie"];
```



运行程序，您会看到输出开头的汽车名称。好的，现在开始为`Car`添加属性。以下是完整的`Car.h`文件：

```objc
@class Tire;

@class Engine;

@interface Car : NSObject

{
    NSString *name;
    NSMutableArray *tires;
    Engine *engine;
}

@property (copy) NSString *name;

@property (retain) Engine *engine;

- (void) setTire: (Tire *) tire atIndex: (int) index;

- (Tire *) tireAtIndex: (int) index;

- (void) print;

@end // Car
```

您会注意到简单的访问器声明消失了，取而代之的是`@property`声明。您可以通过添加额外属性来装饰`@property`，以表达您对属性行为的精确意图。由于为`name`添加了`copy`，编译器和类的使用者都知道`name`将被复制。这可以简化使用该类的程序员的开发工作，因为他们知道自己无需复制从文本字段中获取的字符串。另一方面，`engine`仅通过`retain/release`来管理。如果您不提供任何一项，编译器将默认使用`assign`，这对于对象来说通常不是您想要的行为。

**注**：您可以使用其他装饰，例如`nonatomic`，它能在属性不会被用于多线程环境时使访问器更快。桌面机器速度很快，因此将属性设为`nonatomic`并不会带来真正的性能提升，但 iOS 开发者经常使用它来在资源受限的移动设备上榨取更多性能。如果您不希望属性对象被保留，也可以使用`assign`，以帮助避免保留循环。

默认情况下，如果您没有为属性指定任何属性，它们会被赋予默认属性`nonatomic`和`assign`。您只能为可保留指针（即 Objective-C 对象）指定`retain`和`copy`属性。所有其他类型，例如 C 语言和非可保留指针，必须使用`assign`并手动管理内存。

如果您自己提供了`setter`或`getter`（或两者），则不能使用`atomic`属性；必须使用`nonatomic`。

`Car.m`有两个主要变化。`name`和`engine`访问器被删除，并添加了两条`@synthesize`指令：

```objc
@implementation Car

@synthesize name;

@synthesize engine;
```

最后，`main`使用点符号来设置内容：

```objc
Car *car = [[Car alloc] init];

car.name = @"Herbie";

…

car.engine = [[Slant6 alloc] init];
```

### 命名别名（Appellation）

在本章的所有代码中，属性名称与支持该属性的实例变量名称相同。这种模式非常常见，很可能您大部分时间都会使用它。不过，有时您可能希望实例变量使用一个名称，而公开属性使用另一个名称。

假设我们想将`Car`中的`name`实例变量改名为其他名称，比如`appellation`。我们只需在`Car.h`中更改实例变量的名称：

```objc
@interface Car : NSObject

{
    NSString *appellation;
    NSMutableArray *tires;
    Engine *engine;
}

@property (copy) NSString *name;

@property (retain) Engine *engine;
```

然后更改`@synthesize`指令：

```objc
@synthesize name = appellation;
```

编译器仍然会创建`-setName:`和`-name`，但会在其实现中使用`appellation`实例变量。

但是当您编译时，会看到一些错误。您可能还记得，我们直接访问了`name`实例变量，而它已经被更改了。我们可以选择在代码中搜索并替换`name`，或者将直接访问实例变量改为使用访问器。在`init`中，将

```objc
name = @"Car";
```

改为

```objc
self.name = @"Car";
```

这个`self-dot-name`是什么意思？这是一种消除歧义的方式，用于让编译器知道我们想要通过访问器进行跳转。如果我们只使用裸的`name`，编译器会认为我们在直接修改实例变量。要使用访问器，我们可以写`[self setName:@"Car"]`。请记住，点号只是进行完全相同调用的简写，因此`self.name = @"Car"`只是表达同一件事的另一种方式。

最后，我们需要修复第一个`NSLog`调用。



`NSLog (@"%@ has:", self.name);`

现在，我们可以将 `appellation` 重命名为其他名称，比如 `nickname` 或 `moniker`。我们只需要修改实例变量名以及在 `@synthesize` 中使用的名称即可。

由于汽车的子类无需直接访问变量，我们将它们从头文件中移除。我们可以去掉 `name` 和 `engine` 变量，因为这些将由编译器自动创建。同时，我们可以将 `tires` 数组移到实现文件中。这样一来，我们的头文件就更加简洁了（简洁即更优）。

修复完成后，让我们回到 `*AllWeatherRadial.m*` 文件。细心的读者可能会注意到，在描述方法中，我们仍在使用方法来获取轮胎的属性。现在让我们全部改用点语法。调整后的描述方法如下：

```
- (NSString *) description
{
    NSString *desc;
    desc = [[NSString alloc] initWithFormat:
            @"AllWeatherRadial: %.1f / %.1f / %.1f / %.1f",
            self.pressure, self.treadDepth, self.rainHandling, self.snowHandling];
    return (desc);
} // description
```

接下来，我们将修复 `Tire` 类以及 `pressure` 和 `treadDepth` 的属性，并移除 getter 和 setter 方法。我们在不丢失任何功能的前提下简化了 `Tire` 接口（及代码）。

```
@interface Tire : NSObject
@property float pressure;
@property float treadDepth;
- (id) initWithPressure: (float) pressure;
- (id) initWithTreadDepth: (float) treadDepth;
- (id) initWithPressure: (float) pressure
             treadDepth: (float) treadDepth;
@end // Tire
```

我们还从实现文件中移除了 setter 和 getter 方法。清理不必要的冗余代码，剔除可能潜藏错误的代码，这个过程真是令人愉悦。

还有一点：你可能还记得，我们为 `Car` 添加了 `name` 属性，并为其设置了 `copy` 特性。这意味着，当我们赋值给 `name` 时，`Car` 会复制一份字符串并存储它。如果当时未使用 ARC，我们还需要在 `Car` 的 `dealloc` 方法中添加一个 `release`，就像这样：

```
[name release];
```

但由于我们使用了 ARC，就无需为此担心了；编译器会自动帮我们插入释放操作。真是 ARC 神助攻！

## 只读属性

你可能有一个对象，其属性是只读的。这个属性可能是一个动态计算的值（例如香蕉的表面积），也可能是一个你希望其他对象只能读取但不能修改的值（例如你的驾照号码）。你可以通过 `@property` 的更多特性来为这些场景编码。

默认情况下，属性是可变的：你可以对其进行读写操作。属性有一个 `readwrite` 特性，你可以用它来明确指定这点。由于属性默认就是可变的，你通常不会用到它，但当你需要明确表达意图时，它就在那里。我们原本可以在 `*Car.h*` 中使用 `readwrite`：

```
@property (readwrite, copy) NSString *name;
@property (readwrite, retain) Engine *engine;
```

但我们并没有这样做，因为我们通常希望消除、废除并摆脱冗余和重复（以及一遍又一遍地说同样的话）。

回到只读属性的讨论，假设我们有一个属性，比如我们的驾照号码或鞋码，我们不希望任何人修改它。我们可以对 `@property` 使用 `readonly` 特性。示例如下：

```
@interface Me : NSObject
{
    float shoeSize;
    NSString *licenseNumber;
}
@property (readonly) float shoeSize;
@property (readonly) NSString *licenseNumber;
@end
```

当编译器看到 `@property` 被声明为 `readonly` 时，它会为该属性生成一个 getter 方法，但不会生成 setter 方法。`Me` 的使用者可以调用 `–shoeSize` 和 `–licenseNumber`，但如果尝试调用 `-setShoeSize:`，编译器会报错。使用点语法时也会出现同样的情况。

## 我偏要亲力亲为

我们之前提到，属性由变量支持，并且编译器会为你自动生成 getter 和 setter。但如果你不想这样呢？如果你不希望为自己的属性准备变量、getter 和 setter 呢？

对此，你也可以如愿以偿。你可以使用关键字 `@dynamic` 来告诉编译器不要为属性生成任何代码或创建变量。接着前面的例子，让我们为 `Me` 类添加一个 `bodyMassIndex` 属性。

由于身体质量指数是根据一个人的身高和体重计算得出的，我们无法为其存储一个固定值。相反，我们希望创建一个访问器，在运行时动态计算该值。因此，我们使用 `@dynamic` 来声明属性，并告诉编译器无需操心创建变量或 getter——我们自己来处理。

```
@property (readonly) float bodyMassIndex;
@dynamic bodyMassIndex;

- (float)bodyMassIndex
{
    ///计算并返回 bodyMassIndex
}
```

如果你声明了一个动态属性，但尝试调用相应的 getter 或 setter 而未提供实现，则会收到错误提示。

## 我不喜欢这些方法名

有时，你可能不喜欢默认生成的方法名。它们的形式通常是 `blah` 和 `setBlah:`。为了解决这个问题，你可以指定编译器生成的 getter 和 setter 的名称。使用 `getter=` 和 `setter=` 特性来定义你偏好的方法名。需要注意的是，如果你这样做，将会破坏键值编码约定（详见第 19 章），因此你应该有充分的理由才使用这个特性。

以下是一个适用于布尔类型属性的有用模式示例：

```
@property (getter=isHidden) BOOL hidden;
```

这告诉编译器，为 getter 生成 `isHidden`，为 setter 生成 `setHidden:`。

## 可惜，属性并非万能

你会注意到，我们没有将 `Car` 的轮胎方法转换为属性：

```
- (void) setTire: (Tire *) tire atIndex: (int) index;
- (Tire *) tireAtIndex: (int) index;
```

这是因为这些方法不符合属性所涵盖的相当狭窄的方法范围。属性只允许你替换 `–setBlah` 和 `–blah` 方法，但不适用于需要额外参数的方法（例如指定轮胎在汽车上的位置）。

## 总结

在本章中，我们讨论了属性，这是一种减少你在处理对象属性常见操作时需要编写（以及后续阅读）的代码量的方式。使用 `@property` 指令来告知外界：“嘿，这个对象拥有一个名为 xxx、类型为 yyy 的属性。”同时，使用该指令来传递属性的相关信息，如其可变性（`readonly` 或 `readwrite`）。在幕后，编译器会自动为对象属性的 setter 和 getter 生成方法声明。

使用 `@synthesize` 指令来告诉编译器为访问器生成实现代码。你可以控制生成的实现会影响哪一个实例变量。如果你不想使用默认行为，也可以自由编写自己的访问器代码。你可以使用 `@dynamic` 来告诉编译器不要生成变量或代码。

点语法虽然通常在与属性相关的上下文中介绍，但它仅仅是调用对象的 setter 和 getter 的简写形式。点语法减少了你的键盘敲击量，对来自其他语言的开发者来说也更为顺手。

接下来将要介绍的是类别（Categories），这是一种 Objective-C 特有的扩展现有类的方式，即使你没有它们的原始代码也能实现扩展！千万别错过。

