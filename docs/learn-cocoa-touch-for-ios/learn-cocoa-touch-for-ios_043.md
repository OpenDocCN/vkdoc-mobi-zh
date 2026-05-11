# 第 8 章：管理事件的发生时机

到目前为止，本书中你都是以相当常规的方式让代码执行。你向对象发送消息，与该消息选择器对应的方法运行并返回，然后代码继续执行。但这仅仅触及了 Objective-C 和 Cocoa Touch 能力的皮毛；你可以稍后运行代码，同时运行两段代码，或者在特定事件发生前什么都不运行。在本章中，我们将讨论如何管理代码运行时的具体细节，并深入探讨其重要性。我们将介绍如何编写代码以充分利用最新的多核处理器，使用 *定时器* 重复执行代码，以及使用 **运行循环** 这一苹果公司高效的事件等待机制。在此过程中，我们还将涵盖诸如线程安全以及如何优化代码速度等主题。首先，让我们在更宏观的讨论背景下，回顾一下目前所做的工作。

## 发送消息

当你向对象发送消息时，你实际上是在运行一个方法。我们从第 1 章开始就一直在发送消息，但我们并没有真正探究其运行机制。现在让我们来探究一下；了解这一点将有助于你理解后面的讨论。当你发送消息时，方法的名称被称为**选择器**。选择器由 `SEL` 数据类型表示，它只是与方法名称相对应的字符串。考虑以下发送给名为 `myArray` 的 `NSArray` 对象的消息：

```
[myArray count]
```

此例中的选择器是 `count`。当这段代码执行时，Objective-C 运行时会在一个表中查找该方法以找到其地址。找到地址后，它便会执行该方法。这个过程会被高度缓存以提高性能，但当你第一次在某个类上调用特定方法时，它会查找该方法的地址并将其缓存起来供后续使用。

### 消息的底层实现

方法的实现实际上是一个 C 函数。`NSArray` 的这个特定方法 `count` 返回一个无符号整数，所以你可能认为相应的 C 函数会是这样声明的：

```
NSUInteger count();
```

有趣的是，当 Objective-C 代码被编译时，这些 C 函数会在开头附加两个额外的参数：`self` 和 `_cmd`。`self` 就是你一直在使用的、用来引用正在执行方法的对象的东西，而 `_cmd` 是被执行的选择器，这对调试很有帮助。作为 C 函数的 `count` 方法会这样声明：

```
NSUInteger count(id self, SEL _cmd);
```

当该方法第一次被调用时，该函数的地址会被查找并缓存起来。

这为什么重要呢？在编程语言及其各自优缺点的讨论中，一个常见的说法是 Objective-C 比 C 或 C++ 慢。事实是，由于每个 Objective-C 方法都由编译器实现为一个 C 函数，Objective-C 代码实际上非常快。话虽如此，查找函数地址的开销并非为零。考虑以下 `if` 语句：

```
for (NSUInteger i = 0; i < [myArray count]; i++) {
    NSLog(@"%@", [[myArray objectAtIndex:i] description]);
}

NSUInteger count = [myArray count];
for (NSUInteger i = 0; i < count; i++) {
    NSLog(@"%@", [[myArray objectAtIndex:i] description]);
}
```

在第一个示例中，每次循环迭代时，它都会调用 `myArray` 的 `count` 方法来检查 `i` 是否小于计数值。在第二个示例中，我们将 `count` 返回的值存储到一个临时变量中，然后在循环中与之比较。第二个示例有可能快得多，因为它不会在每次循环中都调用方法。当你编写实际的 Objective-C 代码时，一定要考虑这样的情况。本章的大部分内容将聚焦于应用的性能，但无论有多少技巧来管理代码的执行时机，慢代码终究是慢代码。

### 手动执行选择器

大多数 Cocoa Touch 开发人员最早学会管理代码调用时机的方法之一，就是 `UIControl` 类的**目标-动作**模式。当你将一个目标和动作传递给 `UIButton` 的实例，并将其分配给 `UIControlEventTouchUpInside` 事件时，其效果是显而易见的。你也可以通过 `NSObject` 上的 `performSelector:` 系列方法以类似方式调用方法。下面的例子在名为 `myTableView` 的 `UITableView` 实例上调用了 `reloadData` 方法：

```
[myTableView performSelector:@selector(reloadData)];
```

现在，这本身与直接调用 `myTableView` 的 `reloadData` 方法相比并无真正优势。然而，`performSelector:` 的一些变体开始显现其真正的威力。UIKit 类的原则之一是所有 UI 代码都应在应用的主线程上运行；也就是说，你不应该在后台线程上更新 UI（如果你不确定什么是线程，不用担心；我们将在本章后面深入讨论）。下面这行代码设置了一个标签的文本，但它是在主线程上完成的：

```
[myLabel performSelectorOnMainThread:@selector(setText:)
                      withObject:@"Hello, World!"
                      waitUntilDone:NO];
```

这等同于已经在主线程上调用这行代码：

```
[myLabel setText:@"Hello, World!"];
```

当 `waitUntilDone` 参数设置为 `NO` 时，会导致方法立即返回，这在您传递的选择器对应一个长时间运行的方法时非常有用。

`performSelector:` 中包含对象参数的变体只允许你执行带一个参数的选择器。如果你要调用的方法需要多个参数，则无法使用此方法。对于这些方法，我们将在本章后面看到更好的调用方式。

### 在后台调用选择器

在主线程上调用方法的反面是在后台线程上调用它。如果你有一些长时间运行的任务并在主线程上调用它，它会阻塞用户界面更新，直到任务完成。为避免这种情况，你可以使用 `performSelectorInBackground:withObject:` 在后台线程上调用该任务：

```
[myObject performSelectorInBackground:@selector(longRunningTask) withObject:nil];
```

这将在单独的线程上执行 `longRunningTask` 方法，从而允许主线程继续处理用户界面更新。但是，在从后台线程更新 UI 时务必小心；正如我们之前讨论的，始终确保 UI 更新在主线程上执行。

[www.it-ebooks.info](http://www.it-ebooks.info/)



你的应用程序的用户界面（UI）会在任务处理时挂起。这不仅会带来糟糕的用户体验，而且如果你的应用界面挂起时间过长，系统会将其终止。如果你有长时间运行的任务，这种自动终止将迫使你采取措施。

我们将在本章后面讨论在后台运行代码的不同方法，但最简单的方法是使用`performSelector:`方法的另一种变体：

```
[myObject performSelectorInBackground:@selector(longRunningTask:)
                          withObject:@"foo"];
```

这一行会在后台调用`myObject`的`longRunningTask:`方法。

虽然我们将在本章关于线程的部分更详细地讨论线程安全，但请确保在你于后台调用的方法中，任何 UI 更新都发生在主线程上，并使用`performSelectorOnMainThread:withObject:waitUntilDone:`，根据需要执行。

**延迟调用选择器**

到目前为止，我们执行的所有代码都是立即运行的。

但这并不总是你想要的；也许你希望向用户展示某些内容一段时间，或者你希望在一段时间后刷新显示的信息。`performSelector:`方法族中有一个方法可以在一定延迟后执行选择器：

```
[myObject performSelector:@selector(update)
              withObject:nil
              afterDelay:5.0];
```

这一行会在五秒延迟后调用`myObject`的`update`方法（不带参数）。当你使用`performSelector:withObject:afterDelay:`时，该方法会在当前线程上被调用，因此如果你在后台调用的某个方法中调用此方法，它也会在后台运行。

如果你需要取消之前使用上述方法安排执行的选择器，可以使用`NSObject`类方法`cancelPreviousPerformRequestsWithTarget:selector:object:`，并传入与安排选择器时相同的参数。要取消之前的更新选择器安排，你可以这样使用该方法：

```
[NSObject cancelPreviousPerformRequestsWithTarget:myObject
                                        selector:@selector(update)
                                          object:nil];
```

确保这三个参数匹配非常重要，否则你安排的选择器将不会被取消。

[www.it-ebooks.info](http://www.it-ebooks.info/)

