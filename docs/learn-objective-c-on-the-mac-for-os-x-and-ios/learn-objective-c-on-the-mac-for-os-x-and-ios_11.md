# 排版后的文档

接下来，`main()` 会记录它已开始浏览并启动一个运行循环：

```
NSLog (@"begun browsing");

[[NSRunLoop currentRunLoop] run];
```

运行循环是 Cocoa 的一种结构，它会阻塞（即不执行任何处理），直到发生有趣的事件为止。在此场景中，“有趣”意味着网络服务浏览器发现了一个新的 iTunes 共享。

除了监听网络流量外，运行循环还处理其他事务，例如等待用户事件（如按键或鼠标点击）。`run` 方法实际上不会返回，它会一直运行下去，因此其后的代码永远不会被执行。不过，我们将其保留在此处，以便代码的阅读者了解我们注意到了正确的内存管理（我们可以构建一个仅运行特定时长的运行循环，但那样的代码更复杂，并且对我们讨论委托没有实质帮助）。因此，以下是实际上不会执行的清理代码：

```
}

return (0);

} // main
```

现在，我们有了网络服务浏览器和一个运行循环。浏览器会发送网络数据包来寻找特定服务，然后这些数据包会带着“我在这里”的信息返回。当这些数据包返回时，运行循环会告诉网络服务浏览器：“这里有一些给你的数据包。” 浏览器随后查看这些数据包，如果它们来自之前未发现的服务，就会向委托对象发送消息，告知其所发生的事情。

现在，我们来审视委托 `ITunesFinder` 的代码。`ITunesFinder` 类的接口非常简洁：

```
#import <Foundation/Foundation.h>

@interface ITunesFinder : NSObject <NSNetServiceBrowserDelegate>

@end // ITunesFinder
```

请记住，我们*不必*在 `@interface` 中声明方法。委托对象只需实现它感兴趣被调用的方法即可。

注意，我们在 `NSObject` 后面写了 `<NSNetServiceBrowserDelegate>`。这告诉编译器和其他对象，`ITunesFinder` 类实现了该名称的协议中的方法，或遵循了该协议（更多内容将在下一章讨论）。目前，我们添加它的目的仅仅是为了让编译器不产生警告。

该实现包含两个方法。首先是预备部分：

```
#import "ITunesFinder.h"

@implementation ITunesFinder
```

然后是第一个委托方法：

```
- (void) netServiceBrowser: (NSNetServiceBrowser *) b
         didFindService: (NSNetService *) service
              moreComing: (BOOL) moreComing {
    [service resolveWithTimeout: 10];
    NSLog (@"found one! Name is %@",
           [service name]);
} // didFindService
```

当 `NSNetServiceBrowser` 发现一个新服务时，它会向委托对象发送 `netServiceBrowser:didFindService:moreComing:` 消息。浏览器作为第一个参数传递（与 `main` 中 `browser` 变量的值相同）。如果你有多个服务浏览器同时进行搜索，检查此参数可以让你确定是哪一个发现了内容。

第二个参数传递的 `NSNetService` 对象是描述所发现服务的对象，例如一个 iTunes 共享。最后一个参数 `moreComing` 用于指示一批通知是否已完成。为什么 Cocoa 的设计者要包含这个 `moreComing` 参数？如果你在一个拥有一百个 iTunes 共享的大型大学网络上运行此程序，那么该方法会被调用 99 次，其中 `moreComing` 的值为 `YES`，然后最后一次调用时的值为 `NO`。在构建用户界面时，这个信息非常有用，因为它能让你知道何时刷新窗口。随着新的 iTunes 共享出现和消失，这个方法会被反复调用。

`[service resolveWithTimeout: 10]` 告诉 Bonjour 系统去获取该服务的所有有趣属性。具体来说，我们需要共享的名称，比如“斯科特的酷炫旋律（Scott's Groovy Tunes）”，这样我们才能将其打印出来。`[service name]` 可以获取共享的名称。

iTunes 共享可能会随时出现和消失，比如当人们将笔记本电脑置于睡眠状态或离开网络时。`ITunesFinder` 类实现了第二个委托方法，当网络服务消失时，该方法会被调用：



- (void) `netServiceBrowser:(NSNetServiceBrowser *) b didRemoveService:(NSNetService *) service moreComing:(BOOL) moreComing`  
{  
    `[service resolveWithTimeout: 10]`;  
    `NSLog (@"lost one! Name is %@", [service name])`;  
}  

这段代码与`didFindService`方法完全相同，只是当某个服务不再可用时，它会记录日志。

现在运行程序，看看会发生什么。Waqar 的网络中有一台名为 `Waqar Malik’s Home Library` 的古老 Mac mini，它在家中共享 iTunes 音乐。这会生成以下输出：

```  
begun browsing  
found one! Name is Waqar Malik’s Home Library  
```  

我们在笔记本电脑上启动 iTunes，并以库名称 `indigo` 共享音乐：

```  
found one! Name is indigo  
```  

在笔记本电脑上退出 iTunes 后，`ITunesFinder` 会告诉我们：

```  
lost one! Name is indigo  
```  

## 委托与分类  

那么，这个委托机制和分类有什么关系呢？委托凸显了分类的另一个用途：可以发送给委托的方法，被声明为 `NSObject` 上的一个分类。以下是 `NSNetService` 委托方法的部分声明：

```  
@interface NSObject (NSNetServiceBrowserDelegateMethods)  

- (void) `netServiceBrowserWillSearch:(NSNetServiceBrowser *) browser`;  
- (void) `netServiceBrowser:(NSNetServiceBrowser *) aNetServiceBrowser didFindService:(NSNetService *) service moreComing:(BOOL) moreComing`;  
- (void) `netServiceBrowserDidStopSearch:(NSNetServiceBrowser *) browser`;  
- (void) `netServiceBrowser:(NSNetServiceBrowser *) browser didRemoveService:(NSNetService *) service moreComing:(BOOL) moreComing`;  

@end  
```  

通过将这些方法声明为 `NSObject` 的一个分类，`NSNetServiceBrowser` 的实现可以将这些消息发送给*任意*对象，无论该对象实际属于哪个类。这也意味着，只要实现了相应的方法，任何类型的对象都可以成为委托。

**注意：** 像这样在 `NSObject` 上放置分类后，任何类型的对象都可以用作委托对象。无需继承自专门的 `serviceBrowserDelegate` 类（就像在 C++ 中那样），也无需遵循特定的接口（就像在 Java 中那样）。

在 `NSObject` 上放置分类被称为创建**非正式协议**。如你所知，计算机术语中的“协议”是一组管理通信的规则。非正式协议只是一种表达方式，相当于说：“这里有一些你可能想要实现的方法，这样你就可以用它们做一些酷炫的事情。”在 `NSNetServiceBrowserDelegateMethods` 非正式协议中声明的一些方法，我们并未在 `ITunesFinder` 中实现。这没问题。对于非正式协议，你可以只实现你想用的方法。

你可能已经猜到，还存在正式协议的概念。我们将在下一章中介绍。

## 响应选择器  

你可能会问自己：“`NSNetServiceBrowser` 怎么知道它的委托能否处理发送给它的那些消息呢？”你可能遇到过当尝试发送一个对象无法理解的消息时，出现的 Objective-C 运行时错误：

```  
-[ITunesFinder addSnack:]: selector not recognized  
```  

那么 `NSNetServiceBrowser` 是如何避免这个问题的呢？它并没有避开。`NSNetServiceBrowser` 会先向对象询问：“你能响应这个选择器吗？”如果可以，它才会发送这条消息。

什么是**选择器**？它只是方法的名称，但以一种特殊方式编码，供 Objective-C 运行时用于快速查找。你可以使用编译器指令 `@selector()` 来指定一个选择器，并将方法名称放在括号内。例如，`Car` 的 `setEngine:` 方法的选择器是：

```  
@selector(setEngine:)  
```  

而 `Car` 的 `setTire:atIndex:` 方法的选择器则是：

```  
@selector(setTire:atIndex:)  
```  

`NSObject` 提供了一个名为 `respondsToSelector:` 的方法，用于查询对象是否能响应给定的消息。以下代码片段使用了 `respondsToSelector:`：

```  
Car *car = [[Car alloc] init];  
```  



## 代码示例与选择器

```
if ([car respondsToSelector: @selector(setEngine:)])
{
    NSLog (@"yowza!");
}
```
这段代码会打印“yowza!”，因为`Car`对象确实响应`setEngine:`消息。

现在来看另一段代码：
```
ITunesFinder *finder = [[ITunesFinder alloc] init];
if ([finder respondsToSelector:@selector(setEngine:)])
{
    NSLog (@"yowza!");
}
```
这次不会出现“yowza!”。`ITunesFinder`没有`setEngine:`方法。

为了获取所需信息，`NSNetServiceBrowser`会调用`respondsToSelector:@selector(netServiceBrowser:didFindService:moreComing:)`。如果委托能够响应这条消息，浏览器就会发送该消息；否则，浏览器暂时忽略该委托，继续正常运行。

## 选择器的其他用途

选择器可以被传递，用作方法的参数，甚至存储为实例变量。这可以带来非常强大和灵活的构造。

Foundation 框架中的`NSTimer`类可以重复向对象发送消息，这在游戏中非常有用——例如，当你希望怪物定期向玩家移动时。创建新的`NSTimer`对象时，你需要指定它要发送消息的目标对象，以及一个选择器来指明要调用的方法。例如，你可以让一个计时器调用游戏引擎的`moveMonsterTowardPlayer:`方法，或者让另一个计时器调用`animateOneFrame:`方法。

## 本章小结

本章介绍了分类（categories）。分类提供了一种向现有类添加新方法的方式，即使你没有这些类的源代码。

除了向现有类添加功能外，分类还允许将对象的实现分散到多个源文件甚至多个框架中。例如，回想一下`NSString`的数据处理方法：这些方法实现在 Foundation 框架中，与 UIKit 和 AppKit 中的绘图方法分离。

分类允许你声明非正式协议（informal protocols）。非正式协议是`NSObject`上的一个分类，列出了对象可能能够响应的方法。非正式协议用于实现委托（delegation）——一种让你轻松定制对象行为的技术。在此过程中，你还学习了选择器（selectors），它们是在代码中指示特定 Objective-C 消息的一种方式。

接下来是 Objective-C 的正式协议（formal protocols），它们是非正式协议的“升级版”。

---

