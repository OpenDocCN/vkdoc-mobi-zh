# 类文档指南

类文档会给出关于哪些方法可以被重写的指导。在某些情况下，这是必需的：如果你创建了一个绘制自身内容的`UIView`类，必须重写`-drawRect`方法来显示该视图。在其他情况下，重写方法提供了灵活性和自定义能力。例如，通过`-layoutSubviews`方法，重写可以让你显式地控制界面元素的位置，这是使用默认机制无法实现的。

## 循环：好与坏

使用变量进行循环可能是你最早学习的编程概念之一。你会很高兴地知道，Objective-C 有`for`循环——而且其语法比纯 C 更好。

你将在下一章全面了解`NSArray`类，但目前你只需要知道它是一个管理零个或多个对象的类。当你有多个对象时，很可能你需要在某个时候遍历它们的内容。

你可以使用标准 C 中的老式`for`循环：

```
NSArray *myArray = [NSArray arrayWithObjects:@"THIS", @"IS", @"AWESOME!!!", nil];
NSUInteger count = [array count];
NSUInteger i;
for (i = 0; i < count; i++) {
    NSString *element = [myArray objectAtIndex:i];
    if ([element isAwesomeString]) {
        NSLog(@"CONGRATULATIONS!!!!!!!!!");
    }
}
```

但 Objective-C 有一种叫做**快速枚举**的构造，可以省去大量输入：

```
NSArray *myArray = [NSArray arrayWithObjects:@"THIS", @"is!", @"AWESOME!!!!", nil];
for (NSString *element in myArray) {
    if ([element isAwesomeString]) {
        NSLog(@"CONGRATULATIONS!!!!!!!!!");
    }
}
```

更重要的，这种枚举形式由运行时优化，消除了循环不变式引入错误的可能性。

这是另一个聪明而简洁的代码示例——更简单、更安全、更快。

## 你的异常代码

作为开发者，你知道事情并不总是按计划进行。不符合预期的代码是你生活中源源不断的“乐趣”。幸运的是，Objective-C 提供了编译器指令来帮助你处理代码块中的异常：

- 使用`@try`标记可能抛出异常的代码。
- `@catch()`用于指定异常发生时运行的代码。如果需要处理不同类型的异常，可以使用多个块。
- 如果你有无论是否发生异常都需要运行的代码，可以使用`@finally`。

以下是使用所有三个指令的示例：

```
NSArray *myArray = [NSArray array]; // 一个没有元素的数组

@try {
    // 数组为空，所以这一行会失败：
    [myArray objectAtIndex:0];
}
@catch (NSException *exception) {
    NSLog(@"name = %@, reason = %@", [exception name], [exception reason]);
}
@finally {
    NSLog(@"Glad that's over with...");
}
```

这将在控制台日志中生成以下输出：

```
name = NSRangeException, reason = *** -[NSCFArray objectAtIndex:]: index (0) beyond bounds (0)
Glad that's over with...
```

如果你没有捕获异常，你的应用程序会退出，并将用户突然送回 iPhone 的主屏幕。捕获异常是一件好事。

当你的代码需要生成一个异常时，使用`NSException`类。假设你遇到了一个不够“awesome”的字符串，你可以像这样抛出一个异常：

```
[[NSException exceptionWithName:@"AwesomeException" reason:@"Not awesome enough" userInfo:nil] raise];
```

你可以使用`userInfo`参数，如果你需要随异常提供一些额外信息（例如，有问题的字符串）。

**提示：** 在调试时，在即将抛出异常之前设置断点通常很有帮助。这样，你可以在错误发生的位置看到堆栈跟踪。在`objc_exception_`处添加一个断点。



