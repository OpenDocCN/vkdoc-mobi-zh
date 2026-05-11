# 块与作用域：重要注意事项

关于块捕获作用域，有一个重要的注意事项需要提及。默认情况下，块无法修改从周围作用域中获取的变量。允许块修改变量需要编译器进行更多的干预。当块被复制到堆上时，如果它可以修改该变量，那么为了保持该值的一致性，对该变量的所有引用也必须相应更改。只要告知编译器需要处理，它完全有能力为你完成这项工作。为此，你需要使用存储限定符，这是创建变量时指定的一个属性。要创建一个块可以写入的`int`值，请在声明中的`int`前添加`__block`（前面是两个下划线）：`__block int myInt = 42;`

完成上述操作后，无论块是否被复制到堆上，它都可以随意写入该变量。得益于`__block`存储限定符，编写那些生命周期超过其创建方法的块变得简单且实用。

块的另一个有用特性是与 Objective-C 对象的交互方式。

## 块保留对象

与任何其他变量一样，你可以在块内部引用来自块周围作用域的指向 Objective-C 对象的指针。即使当你位于 Objective-C 方法中时引用`self`，其行为也符合预期。由于 Objective-C 对象（与`int`等临时变量不同）是在堆上创建并进行引用计数的，仅仅将指针复制到块中是不够的，尤其当块本身的生命周期超过了原始对象引用时。为了解决这个问题，当你在块中引用一个 Objective-C 对象时，它会自动收到`retain`消息，并在块被销毁时收到`release`消息。这让你可以在块中使用对象，而无需担心在块使用完对象之前，对象就被释放了。你还可以将对象与`__block`存储限定符结合使用，以在块内创建对象，并处理预期的内存管理影响。

然而，将对象与块结合使用有一个主要的注意事项：保留循环。可能出现这样一种情况：块引用了一个对象，从而保留了它，但该对象也保留了块，从而导致两个对象都永远不会被释放。让我们看看这种情况是如何发生的，以及如何避免它。考虑一个具有一个属性的对象：该属性是一个不带参数并返回`void`的块。我们将这个类命名为`BlockHolder`：

```objc
typedef void (^SimpleBlock)(void);

@interface BlockHolder : NSObject

@property (copy) SimpleBlock block;

@end
```

请注意，对于块，我们将使用`copy`属性来确保它被复制到堆上。现在让我们看看如何通过一个`BlockHolder`对象创建保留循环：

```objc
BlockHolder *holder = [[BlockHolder alloc] init];

[holder setBlock:^{
    NSLog(@"%@", [holder description]);
}];
```

在这段代码片段中，我们创建了一个`BlockHolder`类的新实例，并将其赋值给`holder`变量。接下来，我们创建了一个要传递进去的块，该块仅将对象的描述打印到控制台。问题出在细节上：由于我们在块中引用了`holder`，它被块保留；而由于`BlockHolder`类使用`copy`属性定义了`block`属性，它会保留该块，从而导致两个对象相互保留。由于保留循环，这两个对象都不会被释放。有两种方法可以解决这个问题，一种是特定的，一种是通用的。首先，由于我们编写了`BlockHolder`类，我们可以修改这个块。不采用无参数的形式，而是将指向块持有者的指针作为参数提供给块。我们使用`self`作为名称来模拟`self`在 Objective-C 方法中的工作方式。新的...


`SimpleBlock`声明如下：

```objc
typedef void (^SimpleBlock)(id self);
```

当我们创建该`block`时，会将`holder`替换为`self`：

```objc
BlockHolder *holder = [[BlockHolder alloc] init];
[holder setBlock:^(id self){
    NSLog(@"%@", [self description]);
}];
```

这能很好地避免保留环。`self`可能会让人有些困惑，因此你可以考虑为参数使用不同的名称，但这是完全避免保留环的好方法。如果你无法控制传入`block`的对象代码，第二种方法会更适用。你需要创建对对象的弱引用，并在`block`中引用该弱引用，从而避免保留环，因为弱引用不会导致额外的`retain`调用。此时，创建`block`的代码如下：

```objc
typedef void (^SimpleBlock)(void);

BlockHolder *holder = [[BlockHolder alloc] init];
__weak BlockHolder *safeHolder = holder;
[holder setBlock:^{
    NSLog(@"%@", [safeHolder description]);
}];
```

`__weak`限定符需要 iOS 5 和 ARC。如果你使用 ARC 但目标平台是 iOS 4.3，请改用`__unsafe_unretained`。

### 将 Block 作为方法参数

关于`block`，最后一点我想谈谈风格。Apple 倾向于遵循一种编码模式：将 Objective-C 方法中的`block`参数放在最后。这有助于保持代码的可读性，因为两个`block`之间夹杂单个参数很容易被忽略。

不正确的风格示例如下：

```objc
[someObject someMethodWithThisParameter:@"ThisParameter"
                  performingThisBlock:^{
                      NSLog(@"This Block");
                  }
                  withThisParameter:YES
                  andThisBlock:^{
                      NSLog(@"Another Block");
                  }];
```

如你所见，很难分辨一个参数在哪里结束、另一个参数在哪里开始。当`block`内部的代码很长时，这个问题会更加严重。

正确的风格示例如下：

```objc
[someObject someMethodWithThisParameter:@"ThisParameter"
                      andThisParameter:YES
                     performingThisBlock:^{
                         NSLog(@"This Block");
                     }
                         andThisBlock:^{
                             NSLog(@"Another Block");
                         }];
```

通过将`block`分组到末尾，代码的可读性大大提高——至少我个人（以及 Apple）这样认为。现在我们已经了解了`block`是什么以及如何使用它们，接下来讨论一下为什么要使用它们。

### 为什么应该使用 Block？

为了说明为什么应该使用`block`，让我们考察几个常见场景，看看在`block`出现之前是如何处理的。然后看看使用`block`后，这些场景是变得更简单、更清晰，还是两者兼得。尽管所有旧方法仍然有效且可用，但 Apple 随每个 iOS 和 Mac OS X 版本发布的新 API 大量采用`block`，并且常常不提供无`block`的替代方案。我们从 iOS 最强大的特性之一开始分析：`UIView`动画。

#### UIView 动画

iOS 中的动画系统极其强大。每个视图都由 OpenGL 支撑的图层绘制，使得非常简单的代码就能创建出高性能、硬件加速的 2D 和 3D 动画。这在`block`出现前是如此，在`block`出现后依然如此，但 iOS 4 中随`block`一同推出的基于`block`的新动画 API 使其变得更加简单。来看一个常见示例：淡出视图。使用旧方法，代码如下（假设要淡出的视图名为`myView`）：

```objc
[UIView beginAnimations:@"animationName" context:NULL];
[UIView setAnimationDuration:1.0];
[myView setAlpha:0.0f];
[UIView commitAnimations];
```

动画系统的基本思想是：在`beginAnimations:context:`和`commitAnimations`之间，你只需设置视图在动画结束时应有的属性；动画系统会自动在`setAnimationDuration:`定义的时间段内，在当前值与目标值之间进行插值。



虽然这几乎是动画最简单的形式，但仍值得看一下基于块的替代方案：

```
[UIView animateWithDuration:1.0
  animations:^{
    [myView setAlpha:0.0f];
  }];
```

如你所见，三个`UIView`类方法调用被替换成了一个。

你不再需要将动画方法放在两个方法调用之间，只需将它们放入作为第二个参数传递的块中即可。即使在代码长度方面，这也是一种改进，但当我们为其添加一些选项时，改进才开始真正显现。首先，假设动画完成后，我们想将现已透明的视图从其父视图中移除。使用旧方法，我们需在动画前导代码中添加两行：

```
[UIView beginAnimations:@"animationName" context:NULL];
[UIView setAnimationDuration:1.0];
[UIView setAnimationDelegate:self];
[UIView setAnimationDidStopSelector:@selector(animationDidStop:finished:context:)];
[myView setAlpha:0.0f];
[UIView commitAnimations];
```

在这段代码中，我们调用`setAnimationDelegate:`来为动画系统提供一个对象，以便动画完成时向其发送消息。然后，我们提供一个选择器供其在发送消息时使用。第一个参数是指向包含动画 ID（前例中的`animationName`）的`NSString`的指针，第二个参数是一个`BOOL`值，表示动画是否完成（在某些情况下，例如视图的父视图在动画完成前已从其视图层次结构中移除，该值将为`NO`），第三个参数是一个大多未使用的上下文指针。然而，我们还没完成；我们需要实现这个新方法：

```
- (void)animationDidStop:(NSString *)animationID
                finished:(BOOL)finished
                 context:(void *)context
{
    [myView removeFromSuperview];
}
```

我们还需要在头文件或类扩展声明中添加一行来声明此方法。事情从这里开始变得冗长。让我们将其与等效的基于块的代码进行比较：

```
[UIView animateWithDuration:1.0
  animations:^{
    [myView setAlpha:0.0f];
  }
  completion:^(BOOL finished) {
    [myView removeFromSuperview];
  }];
```

这已经少了好几行！此方法增加了一个参数，一个在动画完成时运行的块。它唯一的参数是一个`BOOL`值，表示动画是否完成。由于你是在定义动画的地方定义完成处理程序，因此无需跟踪动画 ID。

如果旧式`UIView`动画的复杂性仅限于此，那么基于块的方法已经是巨大的改进。随着代码变得更加复杂，你可能会在单个类中拥有几个不同的动画。在这种情况下，没有块，你将在同一个`animationDidStop:finished:context:`方法中处理这些动画的完成，这意味着你需要使用动画 ID 来确保正确的代码为正确的动画运行。然而，在这里使用块真正的好处是，它将完成代码紧挨着动画代码。你不必记得将其放入`animationDidStop:finished:context:`方法，也不必根据动画 ID 在该方法中查找正确的代码。相反，与单个动画相关的所有内容都在同一个地方。在你编写代码六个月后，如果必须回来修改它，你会庆幸这种代码组织方式，因为它使问题排查变得简单得多。这是普遍倾向于使用块的一个重要原因，也是我们接下来要探讨的重点：使用块作为回调。

## 使用块进行异步回调

在之前的某一章中，我们讨论了使用异步的`NSURLConnection`方法来避免阻塞主线程（这反过来会使应用无响应）。这是一件好事，但这样做的一个潜在症状是，URL 连接调用的这些回调方法对于每个连接都是相同的。如果你的代码创建了 20 个 URL 连接，它们都会发送相同的委托消息，这意味着你需要在那些方法中找出实际发送消息的 URL 连接，以便相应地采取行动。一些开源网络库通过允许你指定一个完成处理程序来解决这个问题。这可能看起来像这样：

```
[MyNetworkingLibrary loadURLWithRequest:aURLRequest
  completionHandler:^(NSData *receivedData,
                      NSURLResponse *response,
                      NSError *error) {
    // 在此处处理响应
  }];
```

就像我们之前做的 URL 加载一样，这会接受一个 URL 请求并发送它，然后接收一些数据、一个响应以及可能返回的错误。具体的**方法因网络库而异**——`MyNetworkingLibrary`并非真实的库名——但核心思想是相同的：当连接完成时，库不再调用委托方法，而是简单地执行你在发出请求时提供给它的块。

与`UIView`动画一样，主要优势在于代码组织。处理请求响应的代码紧挨着最初发出请求的代码，这让你能一眼看到过程中两个步骤发生了什么。与动画一样，连接越多，委托方法就会变得越繁琐，并且随着项目时间推移，它们会变得越难以维护和排查问题。这个优势并不仅限于 URL 加载；任何不需要阻塞主线程的长时间运行进程，都应利用完成处理程序块来编写。

单个对象也可以关联多个块；一些 URL 库允许你定义一个被多次调用的块来报告下载进度。

与 URL 加载非常相似，另一个可以将稍后运行的代码放入块的地方是使用`NSNotificationCenter`。你可以使用块在你注册通知的地方定义要执行的代码，而不是定义一个在触发通知时调用的方法：

```
NSNotificationCenter *nc = [NSNotificationCenter defaultCenter];
[nc addObserverForName:UIApplicationDidReceiveMemoryWarningNotification
                object:nil
                 queue:nil
            usingBlock:^(NSNotification *note) {
    NSLog(@"收到内存警告！");
}];
```

我们稍后会详细说明`queue`参数，但这又一次允许你以更易读的方式组织代码。你无需寻找触发通知时调用的方法，而是可以在提供的块中找到将要执行的代码。这导致你需要编写更少的代码，并且理想情况下，更少的错误。尽管块对于回调很有用，但它们在枚举集合时甚至更有用，我们接下来将讨论这一点。

## 使用块进行枚举

枚举集合中的项是过去几十年几乎所有编程语言中都存在的任务。总体思路很简单：给定一个项集合，依次获取每个项的引用，对其执行一些代码，然后获取下一个，重复直到遍历完所有项。在我们讨论使用块实现此目的之前，让我们看看在 Cocoa Touch 中可以使用的从集合类（数组、字典和集合）中枚举对象的一些工具。

**注意：** iOS 5 添加了一个新的集合类`NSOrderedSet`，它具有数组（其成员有序）和集合（其成员唯一）的特性。我们在这里不会专门讨论有序集合，但你通常可以假设访问其成员的方式与数组相同。



**For 循环**

从集合中枚举元素最传统的方式是使用 `for` 循环，这里以 Objective-C 为例：

```
NSUInteger count = [myArray count];

for (NSUInteger i = 0; i < count; i++) {
    id object = [myArray objectAtIndex:i];
    [object performSomeTask];
}
```

在这个示例中，我们获取数组中的对象数量，然后从第一个到最后一个依次处理它们，向每个对象发送消息，然后移动到下一个对象。这段代码的优点在于它几乎是通用的；从其他编程语言转过来的开发人员几乎总能理解这段代码的作用，以及如何修改它以达到期望的行为。对于任何一个给定的对象，都可以轻松获取它在数组中的当前索引，因为该索引存储在 `i` 变量中。一个缺点是 `NSArray` 的方法 `objectAtIndex:` 返回的是 `id`，因此我们不知道其中所包含对象的类，不过通过内省机制可以很容易地确定这一点。

对于字典来说，使用 `for` 循环就不那么简单了。以下代码会枚举字典中的所有对象并向它们发送消息：

```
NSArray *keys = [myDictionary allKeys];
NSUInteger count = [keys count];

for (NSUInteger i = 0; i < count; i++) {
    id key = [keys objectAtIndex:i];
    id object = [myDictionary objectForKey:key];
    [object performSomeTask];
}
```

如你所见，差别并不太大，但它确实需要先从字典的键数组中获取一个键，然后用这个键来获取对象。如果你有一个 `NSSet`，它没有顺序，因此也没有 `objectAtIndex:` 方法，你就必须使用它的 `allObjects` 方法来获取一个包含其对象的数组，然后遍历返回的数组。

**NSEnumerator**

除了手动使用 `for` 循环遍历集合，还可以使用 Objective-C 的 `NSEnumerator` 类来枚举对象。

`NSEnumerator` 的实例会通过其 `nextObject` 方法持续返回对象，直到集合中的所有对象都被枚举完毕。对于字典，有两种枚举器，一种用于对象，一种用于键。使用 `NSEnumerator` 的方式如下：

```
NSEnumerator *objectEnumerator = [myArray objectEnumerator];
id object;

while ((object = [objectEnumerator nextObject])) {
    [object performSomeTask];
}
```

在这段代码中，我们从数组中获取了一个 `NSEnumerator`。`NSArray` 类定义了 `objectEnumerator` 和 `reverseObjectEnumerator` 方法，后者只是从数组的最后一个元素遍历到第一个元素。接下来，我们创建了一个指向对象的指针，命名为 `object`。我们持续将从枚举器的 `nextObject` 方法返回的值存储到 `object` 中，如果它不为 `nil`，那么 `while` 表达式就会求值为 `true`，我们就会对 `object` 调用 `performSomeTask`。

**注意：** 因为在表达式中使用单个等号（`=`）而不是两个等号（`==`）来判断相等性，比如 `if (a = 42) …`，是一个很常见的编程错误，所以我们在 `while` 语句周围使用两对括号，以此向编译器表明我们知道自己在做什么，从而防止某些编译器版本就潜在的不安全代码发出警告。

与使用 `for` 循环相比，使用 `NSEnumerator` 有一些权衡。你不需要获取集合中元素的总数，但如果你想获取当前元素的索引，就需要做一些额外的工作。

使用 `NSEnumerator` 时有一件事不能做，那就是在枚举可变集合（例如 `NSMutableArray`、`NSMutableDictionary` 或 `NSMutableSet`）的同时更改该集合。如果你需要这样做，你应该先对原始数据创建一个不可变的副本，然后枚举这个副本；或者保存一份你需要进行的更改的列表，在枚举完成后再执行这些更改。例如，如果你想要从名为 `myMutableArray` 的可变数组中移除每一个奇数 `NSNumber` 对象，你可以这样做：

```
NSArray *myArray = [NSArray arrayWithArray:myMutableArray];
NSEnumerator *objectEnumerator = [myArray objectEnumerator];
NSNumber *number;

while ((number = [objectEnumerator nextObject])) {
    if ([number intValue] % 2) {
        [myMutableArray removeObject:number];
    }
}
```

我们不能在枚举 `myMutableArray` 的同时从中移除对象，因此我们枚举 `myArray` 并从 `myMutableArray` 中移除对象。

**快速枚举**

快速枚举是在 Mac OS X 10.5 中引入的，并且从一开始就在 iOS 中可用，它实际上是一个名为 `NSFastEnumeration` 的协议，集合类都遵循该协议。它允许你使用一种特殊的语法来枚举对象：

```
for (NSString *string in myArray) {
    [string performSomeTask];
}
```

快速枚举是带有类型的，因此这里我们将 `myArray` 中的对象转换为 `NSString` 对象。这并不能保证返回的对象就是 `NSString` 实例，只是意味着编译器会将变量 `string` 视为 `NSString *` 类型。你仍然应该查询对象的类，以确保它确实是你所期望的类型。

当将快速枚举用于数组或集合时，它会遍历集合中的对象；当将其用于字典时，它会遍历字典的所有键。快速枚举不仅比 `NSEnumerator` 更高效，而且代码量更少，可读性也更高。它非常类似于 `foreach` 循环，因此来自其他语言的开发人员很可能会理解这段代码的作用。在循环内部，你无法获取数组的计数或当前对象的索引，但如果你需要，可以很容易地获取它们。在 Block 出现之前，快速枚举是遍历 Cocoa Touch 集合类的首选方式，而且它现在仍然是一个相当不错的选择。

与 `NSEnumerator` 一样，在对其使用快速枚举时，你也不能更改可变集合；如果你这样做了，集合会抛出一个异常，你的应用就会崩溃。

**对成员执行选择器**

到目前为止，我们使用枚举的相当刻意造作的示例都是向集合中的对象发送单个消息 `performSomeTask`。如果你只需要这样做，并且你有一个 `NSArray` 或 `NSSet` 对象，那么你可以使用实例方法 `makeObjectsPerformSelector:`。这将向集合中的每个成员发送一条单独的消息。还有一个变体，它接受一个对象并将其作为参数发送给该方法，叫做 `makeObjectsPerformSelector:withObject:`。如果你只需要调用一个具有零个或一个参数的方法，并且该参数是 Objective-C 对象，那么这会很有用。如果你需要做更复杂的事情，你就需要使用此处描述的一种枚举技术。

**使用 Block 进行枚举**

在 Mac OS X 和 iOS 4 中，与 Block 一起引入的，还有集合类上的一些方法，你可以使用它们通过 Block 来枚举其对象。我们之前的示例可以这样编写：

```
[myArray enumerateObjectsUsingBlock:^(id obj, NSUInteger idx, BOOL *stop) {
    [obj performSomeTask];
}];
```

就代码行数而言，这在简洁性方面与快速枚举不相上下。该方法接受一个参数，即一个 Block，该 Block 有三个参数：一个指向对象的指针、该对象在数组中的索引，以及一个指向 `BOOL` 值的指针。第三个参数很有意思。它实际上是供你在想要停止枚举时进行修改的。在 `for` 循环中，你会调用 `break` 来停止枚举，而在使用 Block 时，你会这样设置：

```
*stop = YES;
```

这会填充相应的值并停止枚举。



