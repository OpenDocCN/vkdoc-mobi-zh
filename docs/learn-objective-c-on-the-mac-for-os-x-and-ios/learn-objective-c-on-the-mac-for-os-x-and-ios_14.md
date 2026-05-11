# 第十四章

### 块与并发

在本章中，我们将讨论**块**，这是一个增强函数功能的 Objective-C 特性。你可以在运行 iOS（4.0 及更高版本）和 OS X（10.6 及更高版本）的应用中使用块。我们还将讨论并发，即如何利用能够同时执行多项任务的现代设备。

### 活到老学到老，玩转块

块对象（通常简称为“块”）是 C 函数的扩展。除了函数通常包含的代码外，块还包含变量绑定。块也被称为**闭包**。

块有两种绑定类型：自动绑定和管理绑定。**自动绑定**使用栈上的内存，而**管理绑定**则在堆上创建。

由于块实际上是在 C 语言层面实现的，因此它们可用于各种基于 C 的语言，包括 Objective-C、C++和 Objective-C++。

块在 Xcode 的 GCC 和 Clang 工具中可用，但并非 ANSI C 标准的一部分。关于块的提案已提交给 C 编程语言标准组。

### 块与函数指针

在深入探讨块之前，我们先花点时间谈谈传统的函数指针。为什么？因为块的语法借用了函数指针的语法。所以如果你知道如何声明函数指针，你就知道如何声明块。与函数指针一样，块具有以下特征：

*   返回类型可推断或显式声明
*   类型参数列表
*   名称

让我们声明一个函数指针：

```
void (*my_func)(void);
```

这个非常基础的函数指针不接受任何参数，也不返回任何结果。要将其转换为块定义，我们只需将 `*`（星号）替换为 `^`（脱字符），像这样：

```
void (^my_block)(void);
```

你使用脱字符运算符来声明块变量，并标记块实现的开始。就像函数一样，块的主体包含在 `{}`（大括号）中。



“给我看代码，”你说？代码来了（你可以在 *14.01 Square* 中找到这段代码）：

```
int (^square_block)(int number) =
^(int number) {return (number * number);};
int result = square_block(5);
printf("Result = %d\n", result);
```

这个特定的 block 接收一个整数并返回该数字的平方。等号前面部分是 block 定义，等号后面部分是实现。我们可以一般性地表述如下：

```
<returntype> (^blockname)(list of arguments) = ^(arguments){ body; };
```

编译器会推断 block 字面量的返回类型，因此你可以省略它。如果 block 没有参数，你也可以省略它们。所以 block 通常可以非常紧凑简洁，就像下面这个来自 *14.02 Hello Blocks* 的例子：

```
void (^theBlock)() = ^{ printf("Hello Blocks!\n"); };
```

## 使用 Block

由于你将 block 声明为一个变量，你可以像使用函数一样使用它，正如你在 *14.01 Square* 中所见：

```
int result = square_block(5);
```

毫无疑问，你注意到这行代码中没有脱字符。这是因为只有在**定义** block 时才使用脱字符，而不是在**调用**它时。

Block 还有一个很酷的特性，可能会让你愿意用它来代替函数：它们可以访问与 block 在同一（局部）作用域中声明的变量。这意味着，在 block 创建时可用的任何变量都可以被访问：

```
int value = 6;
int (^multiply_block)(int number) = ^(int number)
{return (value * number);};
int result = multiply_block(7);
printf("Result = %d\n", result);
```

## 直接使用 Block

当你想要使用 block 时，大多数情况下你不需要创建 block 变量。相反，你通常直接在代码中内联创建一个 block 实例。通常，你需要一个将 block 作为参数的方法或函数（参见以下来自 *14.04 Sorting Array* 的代码）。

```
NSArray *array = [NSArray arrayWithObjects:
@"Amir", @"Mishal", @"Irrum", @"Adam", nil];
NSLog(@"Unsorted Array %@", array);
NSArray *sortedArray = [array sortedArrayUsingComparator:^(NSString
*object1, NSString *object2) {
    return [object1 compare:object2];
}];
NSLog(@"Sorted Array %@", sortedArray);
```

你只需创建一个 block，然后设置好它，之后就不用管了。

## 使用 typedef

正如你所见，那种冗长的定义变量语句可能有点令人生畏。输入其中一条时很容易出错。幸运的是，`typedef` 可以解救你（参见 *14.05 Typedefed Blocks*）。

```
typedef double (^MKSampleMultiply2BlockRef)(double c, double d);
```

这个语句定义了一个名为 `MKSampleMultiply2BlockRef` 的 block 变量，它接收两个 `double` 类型的参数并返回一个 `double` 类型的值。有了这个 `typedef`，你可以像这样使用这个变量：

```
MKSampleMultiply2BlockRef multiply2 = ^(double c, double d)
{ return c * d; };
printf("%f, %f", multiply2(4, 5), multiply2(5, 2));
```

如果你看看 *14.06 More Typedefs*，你会发现我们定义了好几种不同类型的 block 变量。

```
typedef void (^MKSampleVoidBlockRef)(void);
typedef void (^MKSampleStringBlockRef)(NSString *);
typedef double (^MKSampleMultiplyBlockRef)(void);
```

## Block 与变量

当声明一个 block 时，它会捕获它在创建时刻的状态。Block 可以访问函数使用的标准类型的变量：

* ![image](img/9781430241881_square_fmt.jpg) 全局变量，包括包含作用域内的局部静态变量。
* ![image](img/9781430241881_square_fmt.jpg) 全局函数（当然，不完全是变量）。
* ![image](img/9781430241881_square_fmt.jpg) 来自包含作用域的参数。
* ![image](img/9781430241881_square_fmt.jpg) 函数级别的 `__block` 变量（即与声明 block 相同的层级）。这些是可变的变量。
* ![image](img/9781430241881_square_fmt.jpg) 包含作用域内的非静态变量作为常量被捕获。
* ![image](img/9781430241881_square_fmt.jpg) Objective-C 实例变量。
* ![image](img/9781430241881_square_fmt.jpg) Block 内部的局部变量。

我们将更详细地探讨每种变量类型。

## 局部变量

局部变量与 block 在同一作用域中声明。让我们看看这个例子，它来自 *14.06 More Typedefs*：

```
typedef double (^MKSampleMultiplyBlockRef)(void);
double a = 10, b = 20;
MKSampleMultiplyBlockRef multiply = ^(void){ return a * b; };
NSLog(@"%f", multiply());
a = 20;
b = 50;
// 你认为它会打印什么？
NSLog(@"%f", multiply());
```

你认为第二个 `NSLog` 语句会打印什么？有人猜 1000 吗？就像 Alex Trebek 会说的，“抱歉，不对。”为什么？因为这些变量是局部的，block 会复制它们并在 block 定义时保存它们的状态。所以在这个例子中，`NSLog` 会打印 200。

## 全局变量

在前一节的局部变量例子中，我们说变量的作用域与 block 相同。你可以将这些变量标记为 `static`（全局）来让它们按照你可能期望的方式工作。

```
static double a = 10, b = 20;
MKSampleMultiplyBlockRef multiply = ^(void){ return a * b; };
NSLog(@"%f", multiply());
a = 20;
b = 50;
NSLog(@"%f", multiply());
```

## 参数变量

Block 中的参数变量的行为方式与传递给函数的参数变量相同。

```
typedef double (^MKSampleMultiply2BlockRef)(double c, double d);
MKSampleMultiply2BlockRef multiply2 = ^(double c, double d) { return c * d; };
NSLog(@"%f, %f", multiply2(4, 5), multiply2(5, 2));
```

## `__block` 变量

局部变量在 block 内作为常量被捕获。如果你想要能够修改它们的值，你必须将它们声明为可变的。因此，例如，以下代码将无法编译：

```
double c = 3;
MKSampleMultiplyBlockRef multiply = ^(double a, double b) { c = a * b; };
```

编译器会对你报错，错误信息如下：

```
Variable is not assignable (missing __block type specifier)
```

要修复编译错误，你需要将变量 `c` 标记为 `__block`，如上面那条友善的编译器消息所建议的那样（参见 *14.07 Mutable Variables*）：

```
__block double c = 3;
MKSampleMultiplyBlockRef multiply = ^(double a, double b)
{ c = a * b; };
```

有些变量不能声明为 `__block`。这些限制适用：

* ![image](img/9781430241881_square_fmt.jpg) 不能是可变长度数组
* ![image](img/9781430241881_square_fmt.jpg) 不能是包含可变长度数组的结构体

## Block 局部变量

这些变量的行为方式与函数内部的局部变量相同。

```
void (^MKSampleBlockRef)(void) = ^(void){
    double a = 4;
    double c = 2;
    NSLog(@"%f", a * c);
};
MKSampleBlockRef();
```

## Objective-C 对象

Block 在 Objective-C 中是第一等公民，这意味着你可以像对待任何其他对象一样对待它们。使用 block 时你会遇到的最大问题很可能是内存管理。正如我们刚刚讨论的，在 block 内部访问 Objective-C 对象时你必须小心。一如既往，有一些规则可以帮助你进行内存管理。



## 对象引用与内存管理

- `![image](img/9781430241881_square_fmt.jpg)` 如果你引用一个 Objective-C 对象，它会被保留。
- `![image](img/9781430241881_square_fmt.jpg)` 如果你通过引用访问实例变量，`self`（执行该方法的对象）会被保留。
- `![image](img/9781430241881_square_fmt.jpg)` 如果你通过值访问实例变量，该变量会被保留。

第一条规则很简单，但另两条之间存在细微差别。让我们查看 `14.08 Objective Blocks` 中的示例。在该项目中，找到 `ProcessStrings.m`。

```
NSString *string1 = ^{
    return [_theString stringByAppendingString:_theString];
};
```

在这个示例中，`_theString` 是声明该 block 的类的一个实例变量。由于实例变量在 block 中被直接访问，因此容器对象（`self`）被保留。现在来看另一个示例：

```
NSString *localObject = _theString;
NSString *string2 = ^{
    return [localObject stringByAppendingString:localObject];
};
```

在这个示例中，我们采用了间接方式：创建实例变量的本地引用，并在 block 体中使用该引用。这次被保留的是 `localObject`，而不是 `self`。

由于 block 是对象，你可以向它们发送所有内存管理消息。在 C 层面，你必须使用 `Block_copy()` 和 `Block_release()` 函数来正确管理内存。`14.09 Block Copy` 将演示如何复制 block。

```
MKSampleVoidBlockRef block1 = ^{
    NSLog(@"Block1");
};
block1();

MKSampleVoidBlockRef block2 = ^{
    NSLog(@"Block2");
};
block2();

Block_release(block2);
block2 = Block_copy(block1);
block2();
```

## 并发，或保持同步

到目前为止，我们讨论的大部分代码都是按顺序一个接一个地执行。现在，我们讨论如何突破这一限制。

你运行 Xcode 的 Mac 至少有两个处理器核心，甚至可能更多。即使是最新的 iOS 设备也拥有多核心。这意味着你可以同时处理多件事情。Apple 提供了多种 API 来利用多核心优势。同时执行多个任务的程序被称为**并发**程序。

利用并发的最基本技术是使用 POSIX 线程来处理程序中可以独立执行的不同部分。POSIX 线程同时提供 C 和 Objective-C API。编写并发应用程序需要创建多个线程，而编写多线程代码颇具挑战性。由于线程是底层 API，你必须手动管理它们。根据硬件和其他运行中软件的不同，所需线程数量的条件也会变化。处理所有这些情况相当棘手，一旦你评估了问题的范围，可能会认为自己不使用多线程代码反而更好。

Apple 来拯救你了！为了减轻多核编程的负担，Apple 引入了 Grand Central Dispatch（简称 GCD）。这项技术为你承担了大部分线程管理的负担，让你可以专注于为任务编写代码。要使用 GCD，你只需提交 block 或函数作为线程运行。GCD 是一项系统级技术，因此你可以在代码的任何层面使用它。GCD 决定需要多少线程以及何时调度它们运行。由于它在系统层面运行，它可以平衡你的应用负载与所有其他运行中的程序，使计算机或设备运行更高效。

## 同步

好吧，我们来打个比方。你在一条多车道高速公路上行驶，其他车辆经过你，而你正在超越一些较慢的车辆。想象一下，在你的计算机上，多条执行路径以同样的方式进行。就像交通一样，有些执行路径耗时很长，有些则很快。在高峰时段，当你想驶上高速公路时，你必须等待前方的车辆，一次只能有一辆车汇入车道。车道和交通信号控制着车流，使其尽可能平稳地运行。走出我们的汽车，进入计算机内部，当交通由进程组成时，我们如何管理？我们使用同步机制，例如标志或**互斥锁**来门控访问。

> **注意** “Mutex”是“**mut**ual **ex**clusion”（互斥）的缩写，指的是确保没有两个线程能同时进入其临界区的问题。

Objective-C 提供了一个语言级别的关键字 `@synchronized`。该关键字接受一个参数，通常是正在被修改的对象。

```
@synchronized(theObject)
{
    // Critical section.
}
```

这确保了临界代码段在不同线程间被串行访问。

当你定义属性且没有指定 `nonatomic` 关键字作为属性特性时，编译器会生成强制执行互斥的 getter 和 setter。如果你不在意，或者知道该属性的值不会被多个线程访问，你可以添加 `nonatomic` 特性。为什么要这样做？原因是为了挤出一丝性能提升。这如何实现？编译器会生成 `@synchronize(mutex, atomic)` 来确保互斥。以这种方式设置代码和变量存在开销，这会使对这些属性的访问比直接访问慢得多。

## 选择性能

如果你只是想让某些代码在后台执行，`NSObject` 提供了相应的方法。这些方法名称中都包含 `performSelector:`。最简单的是 `performSelectorInBackground:withObject:`，它在后台执行你的一个方法。它通过创建线程来运行你的方法。这些方法的定义有一些限制：

- `![image](img/9781430241881_square_fmt.jpg)` 由于这些方法在自己的线程上运行，你必须为 Cocoa 对象创建一个自动释放池。主自动释放池与主线程关联。
- `![image](img/9781430241881_square_fmt.jpg)` 这些方法不能返回值，并且要么不接受参数，要么接受一个对象作为参数。换句话说，这些方法必须具有以下签名之一：

```
- (void)myMethod;
- (void)myMethod:(id)myObject;
```

考虑到这些限制，我们的方法实现如下（`14.10 Selectors`）：

```
- (void)myBackgroundMethod
{
    @autoreleasepool
    {
        NSLog(@"My Background Method");
    }
}
```

或

```
- (void)myOtherBackgroundMethod:(id)myObject
{
    @autoreleasepool
    {
        NSLog(@"My Background Method %@", myObject);
    }
}
```

要在后台执行你的方法，只需调用 `performSelectorInBackground:withObject:`，如下所示：

```
[self performSelectorInBackground:@selector(myBackgroundMethod)
                      withObject:nil];
[self performSelectorInBackground:@selector(myOtherBackgroundMethod:)
                      withObject:arugmentObject];
```

就这样，完成了。方法执行完毕后，Objective-C 运行时会负责清理并销毁线程。请注意，你不会收到方法执行完毕的通知：这是最基本、最精简的版本。如果你想做更复杂的事情，需要继续阅读，了解调度队列的精彩世界。

## 每天我都排队

GCD 使用**调度队列**，这些队列类似于线程但使用起来简单得多。你只需编写代码，将其分配给队列，然后让系统执行它。你可以异步或同步执行任意代码。有三种类型的队列：



- ![image](img/9781430241881_square_fmt.jpg) *串行队列*：每个串行队列按照任务分配的顺序一次只执行一个任务。你可以创建任意数量的此类队列，它们会并行运行，每个队列一次执行一个任务。
- ![image](img/9781430241881_square_fmt.jpg) *并发队列*：每个并发队列同时执行一个或多个任务。任务按照分配到队列的顺序启动。你不能创建并发队列，而是使用系统提供的三个队列之一。
- ![image](img/9781430241881_square_fmt.jpg) *主队列*：这是应用程序可用的主队列。该队列在程序的主线程上执行任务。

接下来，我们将讨论这几种队列以及如何使用它们。

## 一碗串行队列

有时，你会遇到一系列需要按特定顺序执行的任务。此时可以使用串行队列。任务执行顺序是先进先出（FIFO）：只要任务是异步提交的，队列就能确保任务按可预测的顺序执行。这类队列永远不会发生死锁。

不是发型 **死锁（Deadlock）** 是一种令人不快的状态，两个或多个竞争任务互相等待对方完成。现实中，当汽车同时到达四向停车路口时，你就能观察到这种现象。

让我们快速创建一个串行队列：
```
dispatch_queue_t my_serial_queue;

my_serial_queue = dispatch_queue_create

("com.apress.MySerialQueue1", NULL);
```
第一个参数是队列名称，第二个参数提供队列的属性（尽管当前未使用，且必须为 `NULL`）。队列创建后，我们就可以向其分配任务。队列是引用计数对象，因此在本节后续内容中，我们将不可避免地讨论内存管理问题。

## 并发调度队列

并发调度队列非常适合可并行运行的任务。并发队列采用先进先出（FIFO）原则，一个任务可以在前一个任务完成之前就开始执行。同时运行的任务数量是不可预测的：它会根据系统当前运行的其他任务而变化。因此，每次运行程序时，并发任务的数量可能不同。

**注** 如果你需要确保每次都运行相同数量的任务，可以使用 `Threads` API 手动管理线程。

每个应用程序可以获得三个并发队列：高优先级、默认优先级和低优先级。要获取这些队列的引用，需调用 `dispatch_get_global_queue`（参见 *14.11 Hello Queue*）。
```
dispatch_queue_t myQueue;

myQueue = dispatch_get_global_queue

(DISPATCH_QUEUE_PRIORITY_DEFAULT, 0);
```
其他优先级选项包括 `DISPATCH_QUEUE_PRIORITY_HIGH`和 `DISPATCH_QUEUE_PRIORITY_LOW`。第二个参数当前始终为 `0`。由于这些是全局队列，你无需管理它们的内存。你不需要保留这些队列的引用——只需在需要时使用该函数获取访问权限即可。

## 直下主街

使用 `dispatch_get_main_queue` 来访问与应用程序主线程关联的串行队列。
```
dispatch_queue_t main_queue = dispatch_get_current_queue(void);
```
由于此队列与主线程关联，你必须非常谨慎地调度该队列上的任务，因为它们会阻塞主应用程序。通常，你使用此队列进行同步：提交多个任务，并在所有任务完成后执行某些操作。

## 我是杰克的调度队列

你可以通过调用 `dispatch_get_current_queue()` 来了解某个块在哪个队列上运行。如果在块对象外部调用此函数，它将返回主队列。
```
dispatch_queue_t myQueue = dispatch_get_current_queue();
```

## 队列也需要内存管理

调度队列是引用计数对象。你可以使用 `dispatch_retain()` 和 `dispatch_release()` 来修改队列的保留计数。你可能已经猜到，这些函数类似于常规对象的 `retain` 和 `release`。你只能对创建的队列使用这些函数，而不能用于全局调度队列。事实上，如果将这些消息发送给全局队列，它们会被忽略，所以不会造成任何损害。如果你正在编写使用垃圾收集的 OS X 应用程序，则必须手动管理这些队列。

## 队列的上下文

你可以为调度对象（包括调度队列）分配一个全局数据上下文。你可以将几乎任何类型的数据（如 Objective-C 对象或指针）分配给此上下文。系统只知道上下文包含与你的队列关联的数据。你必须自己管理上下文数据的内存。你需要在需要时分配内存，并在队列被释放前清理数据。一旦分配了上下文数据，就可以使用 `dispatch_set_context()` 和 `dispatch_get_context()` 函数。
```
NSMutableDictionary *myContext =

[[NSMutableDictionary alloc] initWithCapacity:5];

[myContext setObject:@"My Context" forKey:@"title"];

[myContext setObject:[NSNumber numberWithInt:0] forKey:@"value"];

dispatch_set_context(_serial_queue,

(__bridge_retained void *)myContext);
```
在这个例子中，我们创建了一个字典作为上下文，但我们可以使用任何指针类型。我们甚至可以直接分配原始内存并加以利用。

在最后一行，我们需要声明队列必须跟踪此对象。我们使用 `__bridge_retained` 来增加 `myContext` 的保留计数。

### 清理函数

设置好上下文对象的数据后，何时释放它？简单的答案是，你并不确切知道上下文对象何时消失。为了处理这种情况，你可以告诉对象在释放时调用一个函数，类似于类的 `dealloc` 方法。函数的签名必须如下所示：
```
void function_name(void *context);
```
我们将创建一个示例函数，在上下文对象即将被释放时调用，通常称为**终结器**函数：
```
void myFinalizerFunction(void *context)

{

NSLog(@"myFinalizerFunction");

NSMutableDictionary *theData = (__bridge_transfer

NSMutableDictionary*)context;

[theData removeAllObjects];

}
```
让我们稍微讨论一下 `__bridge_transfer` 关键字。此关键字将对象内存的管理权从全局池转移到我们的函数。当我们的函数终止时，ARC 会减少其保留计数，当保留计数降至零时，对象将被释放。但如果对象未被释放，`myContext` 将一直存在，直到宇宙热寂（大致如此）。

我们如何从块内部访问上下文？
```
NSMutableDictionary *myContext = (__bridge NSMutableDictionary *)

dispatch_get_context(dispatch_get_current_queue());
```
为什么我们在这里添加了 `__bridge` 关键字？这是在告诉 ARC，我们不关心上下文的内存管理。我们希望系统继续管理它。

## 添加任务

向队列添加任务有两种方式：
- ![image](img/9781430241881_square_fmt.jpg) *同步添加*：队列会等待任务完成。
- ![image](img/9781430241881_square_fmt.jpg) *异步添加*：任务被添加后，函数立即返回，无需等待任务完成。这种技术更受青睐，因为它不会阻塞其他代码的运行。

你可以提交一个块或一个函数在队列上执行。共有四个调度函数：包括 `sync` 和 `async`，分别用于块或函数。我们将逐一查看每种类型的调度函数。

**注** 为避免死锁，你绝不应在同一个队列上运行的任务中调用 `dispatch_sync` 或 `dispatch_sync_f`。

## 使用 Dispatch 进行编程




向队列中添加任务最简便的方式是通过代码块实现。该代码块必须为 `dispatch_block_t` 类型，其定义是不接受参数且不返回任何值：

```
typedef void (^dispatch_block_t)(void)
```

现在我们来添加异步代码块。这是一个函数，它接受两个参数：队列和代码块。

```
dispatch_async(_serial_queue, ^{ NSLog(@"Serial Task 1"); });
```

如果愿意，你也可以提前定义一个代码块，而不是内联定义。

```
dispatch_block_t myBlock = ^{ NSLog(@"My Predfined block"); };
dispatch_async(_serial_queue, myBlock);
```

若想同步添加函数，请使用 `dispatch_sync`。

正如我们之前所说，我们也可以将函数添加到队列中。函数的原型必须如下所示：

```
void function_name(void *argument)
```

以下是一个示例函数：

```
void myDispatchFuction(void *argument)
{
    NSLog(@"Serial Task %@", (__bridge NSNumber *)argument);
    NSMutableDictionary *context = (__bridge NSMutableDictionary *)
    dispatch_get_context(dispatch_get_current_queue());
    NSNumber *value = [context objectForKey:@"value"];
    NSLog(@"value = %@", value);
}
```

接下来，我们需要将这个函数添加到队列中。`dispatch` 函数接受三个参数：队列、需要传递的任何上下文，以及函数本身。如果你不需要向函数传递额外信息，可以直接传递 `NULL`。

```
dispatch_async_f(_serial_queue, (__bridge void *)[NSNumber numberWithInt:3],
                 (dispatch_function_t)myDispatchFuction);
```

队列创建完成后，它便立即就绪并等待任务。当我们添加一个任务时，队列会对其进行调度。若要同步添加任务，请调用 `dispatch_sync_f`。

如果出于任何原因想要暂停队列，可以调用 `dispatch_suspend()` 并传入该队列。

```
dispatch_suspend(_serial_queue);
```

一旦暂停了队列，便可以通过调用 `dispatch_resume()` 来恢复它。你仿佛成为时间和空间的主宰者。

```
dispatch_resume(_serial_queue);
```

## 操作队列

你可能已经注意到，这些内容相当底层。你可能会问，Objective-C 发生了什么？你最近不是刚读过一本关于这门语言的书吗？实际上，在 Objective-C 层面有一些称为操作的 API，它们能让使用队列变得更加简单。

要使用操作，首先需要创建一个操作对象；然后将其分配给操作队列，让队列执行它。创建操作有三种方式：

- **`NSInvocationOperation`**：当你已经有一个执行任务的类，并希望它在队列中执行时，使用此方式。
- **`NSBlockOperation`**：这与使用代码块调用 `dispatch_async` 类似，用于执行代码块。
- **自定义操作**：如果你需要对操作类型有更多灵活性，可以创建自己的自定义类型。你必须通过子类化 `NSOperation` 来定义你的操作。

我们将花些时间和笔墨（对于电子书读者来说，可能是电子）来讨论每种技术。

### 创建调用操作

`NSInvocationOperation` 会调用你类中的一个选择器来执行任务。如果你已经构建了一个带有选择器的类，这种方法会非常方便（参见 *14.12 节 Objective 队列*）。

```
@implementation MyCustomClass
- (NSOperation *)operationWithData:(id)data
{
    return [[NSInvocationOperation alloc] initWithTarget:self
                                                selector:@selector(myWorkerMethod:) object:data];
}

// 这是执行实际工作的方法
- (void)myWorkerMethod:(id)data
{
    NSLog(@"My Worker Method %@", data);
}
@end
```

一旦你将操作添加到队列中，并且该任务准备就绪可以执行时，类的 `myWorkerMethod:` 方法就会被调用。

### 创建块操作

如果你有一段希望在队列上执行的代码块，可以创建此操作并让队列运行它。

```
NSBlockOperation *blockOperation =
[NSBlockOperation blockOperationWithBlock:^{
    // 执行我的工作
}];
```

这些操作中的代码块与我们之前在调度队列中使用的类型相同。一旦你用第一个代码块创建了一个块操作，就可以使用 `addExecutionBlock:` 方法添加更多代码块。根据队列类型（串行或并发），这些代码块将分别以串行或并行方式运行。

```
[blockOperation addExecutionBlock:^{
    // 做一些额外的工作
}];
```

### 将操作添加到队列

创建操作后，需要将代码块添加到队列中。这次我们不使用前一节中的 `dispatch_queue_t`，而是使用 `NSOperationQueue`。`NSOperationQueue` 总是并发执行操作。它还会考虑依赖关系，因此如果一个操作依赖于另一个操作，它们将按顺序执行。

如果你想确保始终串行执行操作，可以将最大并发操作数设置为 1，任务将按先进先出顺序执行。在将操作添加到队列之前，需要一种方式来引用该队列。你可以创建一个队列，或者使用预定义的队列，例如当前队列：

```
NSOperationQueue *currentQueue = [NSOperationQueue currentQueue];
```

或者主队列：

```
NSOperationQueue *mainQueue = [NSOperationQueue mainQueue];
```

以下是我们如何创建自己的队列：

```
NSOperationQueue *_operationQueue = [[NSOperationQueue alloc] init];
```

现在我们有了一个队列，可以使用 `addOperation:` 来添加操作：

```
[theQueue addOperation:blockOperation];
```

或者，我们可以直接添加一个要执行的代码块，而无需创建操作对象：

```
[theQueue addOperationWithBlock:^{
    NSLog(@"My Block");
}];
```

一旦你将操作添加到队列中，它就会被调度并执行。

## 总结

在本章中，我们介绍了代码块，这是 Objective-C 中相对较新的补充，它增强了函数的功能。代码块允许你将变量绑定到函数上，从而创建可在程序中使用的对象。代码块在实现并发方面尤其有用。

并发主题广泛且复杂。在本章中，我们描述了一些在 OS X 和 iOS 中可供程序使用的并发特性。

Apple 的 Grand Central Dispatch (GCD) 特性为你的应用提供了一种利用并发的方式，而无需将所有时间都花在系统底层编程上。你应该尝试使用 GCD 和其他并发编程特性，来探索你的应用可能实现的功能以及你感到舒适的做法。

随着你技能的提升和 Apple 添加更多工具，你的应用将能并行执行更多任务，从而使其响应更快。然而，总会有一个收益递减点，此时为应用增加并行处理所付出的代价（在额外编码和调试时间上）超过了其带来的好处。

在你更多处理并发任务时，请注意防范创建死锁（相互依赖从而永远无法完成的任务）以及其他严重错误。这些可能是你不想发生的事情。

