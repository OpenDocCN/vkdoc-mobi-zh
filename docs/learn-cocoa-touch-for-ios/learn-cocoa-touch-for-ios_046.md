# 第 8 章：管理事件发生时的情况

此方法将确保在时间线不可见时我们不会重新加载它。

现在，构建并运行应用。每大约 15 秒，它应该会更新你的 Twitter 时间线，无需手动刷新。我们已经介绍了使用`performSelector:`方法族来控制代码何时运行，接下来让我们看看一种更简单的实现自动重载的方法：使用`NSTimer`类。

**使用定时器安排代码**

使用`performSelector:withObject:afterDelay:`的一个缺点是，每次你想执行选择器时都需要调用该方法。在我们的 TwitterExample 应用中，每次调用`reloadTweets`，我们也都调用了`scheduleTweetRefresh`来将另一次`reloadTweets`调用加入队列。这不仅繁琐，而且会导致刷新间隔略长于 15 秒。对于 Twitter 应用来说，精确计时并不重要，但在某些情况下，按特定间隔运行代码是很重要的。`NSTimer`类允许你安排代码运行，与`performSelector:`方法族非常相似，但其优点在于它可以按照你指定的任何间隔自动重复执行方法。你可以使用类方法`scheduledTimerWithTimeInterval:target:selector:userInfo:repeats:`创建一个定时器，该方法会创建定时器并同时为其安排运行。要每隔 30 秒在一个对象上调用一个名为`update:`的方法，定时器的创建代码如下：

```
NSTimer *timer = [NSTimer scheduledTimerWithTimeInterval:30.0
                                                   target:myObject
                                                 selector:@selector(update:)
                                                 userInfo:nil
                                                  repeats:YES];
```

前三个参数相当直观：重复选择器的时间间隔（以秒为单位）、要调用选择器的目标对象以及选择器本身。此方法的参数是一个指向触发的`NSTimer`对象的指针。`userInfo`参数允许你随定时器对象传递一个任意的`NSDictionary`，如果你需要将附加数据与定时器一起传递，这将非常有用。`repeats`参数指定了定时器在触发后是否应继续重复。虽然此方法不是创建定时器的唯一方式，但它为你完成了大部分工作。当我们稍后在本章讨论运行循环时，我们将会...

[www.it-ebooks.info](http://www.it-ebooks.info/)

