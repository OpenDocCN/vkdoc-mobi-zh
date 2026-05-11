# 第 7 章：使用 Block 编写现代代码

如你所见，这个 Block 返回一个`BOOL`，接受两个参数，而我们在实现中返回一个`BOOL`。你还可以在一个`typedef`内部嵌套另一个`typedef`；如果我们想添加一个`HelperBlock`类型作为第三个参数，可以这样定义两个类型：

```objc
typedef NSString *(^HelperBlock)(void);
typedef BOOL (^ResultHandler)(id result, NSError *error, HelperBlock helperBlock);
```

这是一种能极大提高代码可读性的快速简便方法。

## Block 的内存管理

Block 的一个独特特性是其内存管理语义。Block 在栈上创建，就像临时变量一样。当创建它们的作用域结束时，Block 会随栈帧的其余部分一起销毁。考虑以下示例：

```objc
if (x == 5) {
    void (^myBlock)(void) = ^{
        [self doSomethingCool];
    };
}
```

当这段代码中的`if`语句执行完毕时，`myBlock`就会被销毁。如果我们想在`if`语句之外使用`myBlock`呢？你可能会认为以下代码能够工作：

```objc
void (^myBlock)(void) = NULL;
if (x == 5) {
    myBlock = ^{
        [self doSomethingCool];
    };
} else {
    myBlock = ^{
        [self doSomethingElse];
    };
}
myBlock();
```

问题在于，当存储在`myBlock`中的 Block 被创建时，它位于`if`语句或`else`语句内部，因此当语句执行完毕时 Block 仍然会被销毁。正因如此，当我们试图执行`myBlock`变量时，它保存的将是垃圾数据，从而导致崩溃。为了解决这个问题，我们需要将 Block 从栈移动到堆上。这可以通过`Block_copy()`函数实现，该函数会将 Block 从栈复制到堆。由于它移动了 Block 在内存中的位置，原始 Block 的位置就不再有效。一旦复制了 Block，请务必只使用`Block_copy()`的返回值，而不是原始的 Block。以下是在关闭 ARC 的情况下前面代码的正确版本；我们稍后会讨论 ARC 和 Block：

```objc
void (^myBlock)(void) = NULL;
if (x == 5) {
    myBlock = Block_copy(^{
        [self doSomethingCool];
    });
} else {
    myBlock = Block_copy(^{
        [self doSomethingElse];
    });
}
myBlock();
Block_release(myBlock);
```

如你所见，我们将`Block_copy()`的返回值存储到`myBlock`中，将其移动到堆上并避免其被销毁。Block 是引用计数的，因此每次调用`Block_copy()`时，我们也必须调用`Block_release()`；就像 Objective-C 对象一样，当 Block 的保留计数达到 0 时，它会被销毁。你还可以对 Block 调用`Block_retain()`来增加其保留计数，但需要注意的是，对基于栈的 Block 调用`Block_retain()`并不会将其复制到堆上。同样重要的是，对已复制到堆上的 Block 调用`Block_copy()`只会增加保留计数；实际上并不会再次复制该 Block。

因此，仅使用`Block_copy()`而从不使用`Block_retain()`应该不会引发任何问题，但单独使用`Block_retain()`可能会让你在应用崩溃时花费大量时间调试。就像对 Objective-C 对象进行内存管理一样，ARC 也会帮助管理 Block 的内存。它是如何做到的呢？通过将 Block 视为对象。

## Block 是对象

你可能已经注意到 Block 和 Objective-C 对象之间的一些相似之处。它们都有类似的、基于引用计数的内存管理环境，而且我们将 Block 存储到变量中的方式，与将对象存储到指针中的方式非常相似。这些相似之处也并非表面功夫。虽然它们不像`NSArray`那样是成熟的对象，但由于一些幕后的技巧，Block 与 Objective-C 运行时兼容。这使得你可以向 Block 发送消息。之前的代码可以这样编写（仍处于关闭 ARC 的状态）：

```objc
void (^myBlock)(void) = NULL;
if (x == 5) {
    myBlock = [^{ [self doSomethingCool]; } copy];
} else {
    myBlock = [^{ [self doSomethingElse]; } copy];
}
myBlock();
[myBlock release];
```

如你所见，现在 Block 成为`copy`和`release`消息的接收者，它们分别等同于`Block_copy()`和`Block_release()`。我们也可以对 Block 调用`retain`，效果与预期一致。

Block 可以作为对象使用这一事实，其影响远不止简化内存管理代码。首先，如果你使用 ARC，编译器会为你自动编写内存管理代码，它足够智能，会在适当的时候将 Block 复制到堆上。仅此一点就能通过消除一些开发者错误来减少崩溃。利用这一特性的另一个有用方法是，将 Block 放入`NSArray`和`NSDictionary`等集合类中。这为 Block 带来了一些非常巧妙的使用模式。你可以像使用栈一样使用`NSArray`，将 Block 推入或弹出以执行，或者在`NSDictionary`中使用 Block，将特定操作绑定到特定键。如果你将 Block 用于不期望接收 Block 的 API（例如将 Block 添加到数组中），请确保将 Block 的副本放入数组，如以下示例所示：

```objc
[myArray addObject:[^{
    [self doSomething];
} copy]];
```

这种灵活性蕴含着巨大的力量，但更强大的是 Apple 在 Block 实现中注入的一些额外魔法。

## Block 捕获作用域

我们目前看到的 Block 只能从一个地方获取数据：它们的参数。如果你想要编写一个处理图像、将其调整为特定尺寸的 Block，那么你至少需要给它一个图像参数和一个尺寸参数。如果这是将数据传入 Block 的唯一方式，那么对于复杂过程来说，参数列表不仅会变得非常长，而且 Block 实际上也不会比方法或函数有用多少。

幸运的是，Block 还能够捕获其周围的作用域，并在执行过程中使用它！你可以创建一个简单的 Block，它引入一个字符串并将其打印到控制台：

```objc
NSString *message = @"Hello, World!";
void (^blockLog)(void) = ^{
    NSLog(@"%@", message);
};
blockLog();
```

在这里，`blockLog`这个 Block 使用了`message`变量的值，但并非来自参数！当 Block 被创建时，它会捕获被引用的周围作用域中的值，从而无需将所有变量都定义为参数。然而，这里的内存管理并不总是一目了然的；一个作为临时变量创建但在 Block 中被引用的`int`，应当在整个 Block 生命周期内对 Block 保持可访问。但如果 Block 被复制到堆上，它可能比创建该`int`的栈帧存活得更久，导致该`int`已被销毁。

为了解决这个问题，当你将 Block 复制到堆上时，它会同时复制基于栈的变量。这是完全自动的，因此你无需担心维护在 Block 中引用的基于栈的变量列表；编译器会帮你维护这个列表。这正是 Block 的优势开始显现的地方，因为像这样捕获作用域是函数或方法无法做到的。你必须将所有变量作为参数传递进去。



