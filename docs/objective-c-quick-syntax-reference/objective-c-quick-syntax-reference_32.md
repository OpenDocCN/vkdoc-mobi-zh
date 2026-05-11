# 背景处理

摘要

当你的程序需要同时执行多个任务时，可以使用后台处理。Objective-C 中的后台处理是通过名为`NSOperationQueue`的 Foundation 类完成的。

## 背景处理定义

当你的程序需要同时执行多个任务时，可以使用后台处理。Objective-C 中的后台处理是通过名为`NSOperationQueue`的 Foundation 类完成的。

`NSOperationQueue`管理操作列表，并决定如何调度运行操作所需的资源。操作是代码块。操作可以同时执行，也可以逐个执行。

假设你想计数到 10,000 并将其打印到日志中。为此，你可以编写如下代码：

```
for (int y=0; y<=10000; y++) {
    NSLog(@"y = %i", y);
}
```

这可以正常工作。如果你在应用中运行此代码，你将在控制台日志中看到一长串`y`值。

```
y = 0
. . .
y = 9998
y = 9999
y = 10000
```

现在，假设你还想从 20,000 倒数到 0。如果你只是编写另一个循环，那么你必须等待 10,000 计数完成后才能进行 20,000 计数。但如果你使用后台队列，则可以同时执行这两个任务，并且可能耗时相同。

以下是如何使用后台队列进行设置：

```
NSOperationQueue *background = [[NSOperationQueue alloc] init];
[background addOperationWithBlock:^{
    for (int i1=20000; i1>0; i1--) {
        NSLog(@"i1 = %i", i1);
    }
}];
for (int y=0; y<=10000; y++) {
    NSLog(@"y = %i", y);
}
```

你可以使用`alloc`和`init`函数创建队列，然后使用`addOperationWithBlock:`消息将代码块添加到队列中。如果你运行此代码，日志将打印出类似以下内容：

```
i1 = 20000
y = 0
i1 = 19999
y = 1
i1 = 19998
y = 2
y = 3
i1 = 19997
i1 = 19996
. . .
y = 9997
i1 = 10001
y = 9998
y = 9999
i1 = 10000
i1 = 9999
y = 10000
```

