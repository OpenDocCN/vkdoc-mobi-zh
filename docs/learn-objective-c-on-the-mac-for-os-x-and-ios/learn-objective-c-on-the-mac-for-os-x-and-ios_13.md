# 这段代码清楚地揭示了什么样的人会被征召去打棒球。如果你从事 iOS 开发，你会注意到 Cocoa 中那些非正式协议都变成了包含大量 `@optional` 方法的正式协议。

## 委托机制即将登场

既然我们已经介绍了 Objective-C 中的协议，接下来我们来讨论**委托**，这是一种常与协议配合使用的特性。委托是一种设计模式，允许一个对象指定另一个对象来处理特定任务。

如果你看过大量 Objective-C 代码，很可能已经见过委托的实例。例如，`UITableView` 和 `NSTableView` 有一个由委托处理的 `dataSource` 属性。对象之所以知道委托能处理任务，是因为该委托遵循了处理该任务的协议。

我们用一个现实世界的例子来说明。假设在你的编程工作中，有位负责创建 iOS 或 OS X 应用的上司。你的老板会亲自完成所有工作吗？不太可能。相反，老板会把项目拆分成小块，自己完成部分工作，然后根据其他程序员的专长，将剩余工作分配（委托）给他们。有些员工擅长某些任务，而另一些员工则更擅长其他任务。正因为老板明智，所以总能选对适合的人选来完成恰当的工作。

其实在上一章中你已经见过委托的例子。在 *12.04 iTunesFinder* 示例中，`ITunesFinder` 类就是 `NSNetServiceBrowser` 的委托。

`NSNetServiceBrowser` 如何信任该对象能执行所需的任务？我们来看看 `NSNetServiceBrowser` 的委托方法。

- `- (id <NSNetServiceBrowserDelegate>)delegate;`
- `- (void)setDelegate:(id <NSNetServiceBrowserDelegate>)delegate;`

第一个方法返回当前设置的委托，如果未设置则返回 nil。第二个方法用于设置委托。参数 `delegate` 的类型告诉我们，只要对象遵循了预期的协议，我们就可以将其设为委托。

如果你查看 `NSNetServices.h`，会看到该协议包含不少方法，而且所有方法都是可选的。我们可以实现这些可选方法的全部、部分或完全不实现。

我们定义 `ITunesFinder` 类时声明了它可以处理协议所要求的任务。

```
@interface ITunesFinder : NSObject <NSNetServiceBrowserDelegate>

@end // ITunesFinder
```

如果我们去掉 `<NSNetServiceBrowserDelegate>` 部分，然后将该对象设为委托，会发生什么？编译器会抱怨“你无法处理这个方法！”并产生错误（好吧，也许它用的措辞稍有不同）：

```
Sending 'ITunesFinder *__strong' to parameter of incompatible type 'id<NSNetServiceBrowser
Delegate>'
```

让我们看看 *13.02 委托* 中的示例。这里有三个对象：`Worker1`、`Worker2` 和 `Manager`。工人们有一些必须完成的强制工作，同时他们也可以选择做额外的工作来给老板留下好印象。另外请记住，这个项目使用了 ARC，所以我们在完成后不需要像在没有 ARC 时那样向对象发送 `release` 方法。

```
int main(int argc, const char * argv[])
{
    @autoreleasepool
    {
        Manager *manager = [[Manager alloc] init];
        Worker1 *worker1 = [[Worker1 alloc] init];
        manager.delegate = worker1;
        [manager doWork];

        Worker2 *worker2 = [[Worker2 alloc] init];
        manager.delegate = worker2;
        [manager doWork];
    }
    return 0;
}
```

运行这个程序时，我们会看到以下输出：

```
Worker1 doing required work.
I am a manager and I am working
Worker2 doing required work.
Worker doing optional work.
I am a manager and I am working
```

`Manager` 完成自己的工作，同时也要求工人们执行强制工作。如果工人们能够执行可选工作，他们也会照做。

```
- (void)doWork
{
    [delegate doSomeRequiredWork];
    if(YES == [delegate
        respondsToSelector:@selector(doSomeOptionalWork)])
    {
        [delegate doSomeOptionalWork];
    }
    [self myWork];
}
```

`Manager` 通过委托让工人们完成他们的强制工作和可选工作。代码会先询问委托是否实现了对应方法，如果实现了，则让委托处理该方法。最后，`Manager` 完成自己的工作。

这段代码可能会给你一个启发：为什么不在调用每个方法之前先询问该对象是否拥有这个方法？这样一来，程序就不会因为调用不存在的方法而崩溃。这样做确实可行，但你会付出巨大的代价——程序运行速度会变得极慢。因此，最好只对委托执行这种检查。

## 总结

在本章中，我们介绍了正式协议的概念。你可以在 `@protocol` 代码块中列出一组方法来定义正式协议。对象通过在 `@interface` 语句中类名后的尖括号内列出协议名称来采用该正式协议。当对象采用正式协议时，它承诺实现协议中列出的所有必需方法。如果你没有实现协议中的所有方法，编译器会通过给出警告来帮助你信守承诺。

在此过程中，我们还探讨了面向对象编程中的一些细微差别，特别是在复制存在于类层次结构中的对象时出现的问题。

现在，恭喜你！你已经掌握了 Objective-C 语言的绝大部分内容，并深入探讨了面向对象编程中经常出现的许多主题。你已经为学习 Cocoa 编程或开展自己的项目打下了坚实的基础。在下一章中，我们将学习块（blocks），这是一个相对较新的 Objective-C 特性，能让你用函数实现更强大的功能。我们还将讨论并发，这能让你让计算机和移动设备同时处理多项任务。

