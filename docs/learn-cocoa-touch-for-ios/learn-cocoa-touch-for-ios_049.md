# 第 8 章：管理事件触发时机

### 调度对象（Dispatch Objects）

Grand Central Dispatch 是一个面向对象的框架，但它并非 Objective-C。相反，它采用 C 语言编写，并拥有自己的对象系统。与 Objective-C 类似，它也使用引用计数进行内存管理，但不幸的是，ARC 无法对此类代码提供帮助。创建调度对象通常涉及以下形式的函数，其中 type 是调度对象的类型：

`dispatch_<type>_create()`

例如，要创建一个调度队列，你需要调用 `dispatch_queue_create()`。当你从此函数获取一个队列时，它的引用计数为 1。因此，当你使用完毕后，需要使用 `dispatch_release()` 函数来释放它。你不必对全局队列或主队列调用 `dispatch_release()`，因为它们并非由你创建。如果你需要传递调度对象并保留其值，请使用 `dispatch_retain()` 函数。

### 调度队列（Dispatch Queues）

最常见的创建调度对象类型之一就是调度队列。队列有两种类型：串行队列和并发队列。串行队列一次只运行一个任务，并遵循先进先出的原则。虽然在串行队列中一次只会运行一个任务，但 Grand Central Dispatch 可能同时运行来自两个不同串行队列的任务。顾名思义，并发队列可以同时运行多个任务。Grand Central Dispatch 的特性之一是它会自动调节应用中活跃线程的数量，因此你无需配置在给定并发队列中可同时运行多少个任务。任务按先进先出的顺序启动，但它们的完成顺序取决于每个任务所需的执行时间。

要创建调度队列，请使用 `dispatch_queue_create()` 函数：

```objc
dispatch_queue_t myQueue = dispatch_queue_create("com.learncocoatouch.myQueue", DISPATCH_QUEUE_CONCURRENT);
```

第一个参数是一个 C 字符串，用作该队列的标签。队列集合在所有活跃应用中是全局共享的，因此请务必使用反向 DNS 风格的标签，以避免名称冲突。该标签在调试时也很有用，因为你可以看到出问题的代码正在哪个队列上运行。第二个参数是 `DISPATCH_QUEUE_CONCURRENT` 或 `DISPATCH_QUEUE_SERIAL`，对应你想要创建的调度队列类型。在可用的常量出现之前编写的代码中，你可能会看到使用 `NULL`；在这种情况下，创建的队列为串行队列。你可以像使用全局队列一样，使用此队列来分发任务：

[www.it-ebooks.info](http://www.it-ebooks.info/)

```objc
dispatch_async(myQueue, ^{ NSLog(@"Hello, World!"); });
```

使用完队列后，请务必释放它：

```objc
dispatch_release(myQueue);
```


当您将任务分派到队列时，该任务会保留队列，因此在提交一个或多个任务后可以立即释放队列，无需担心事情发生的顺序。

另一个使用分派队列的有用函数是`dispatch_apply()`。如果您需要多次执行相同的任务，可以使用`dispatch_apply()`（以及其基于函数的对应项`dispatch_apply_f()`）来完成。如果您使用基于块的`dispatch_apply()`函数，该块接受一个参数，该参数对应当前迭代的索引。如果您使用`dispatch_apply_f()`，该计数将作为第二个参数传递给函数。

为了说明这一点，以下是您如何使用`dispatch_apply()`来遍历数组：

```objectivec
NSUInteger count = [myArray count];
dispatch_apply(count, myQueue, ^(size_t idx) {
    id object = [myArray objectAtIndex:idx];
    [object doSomething];
});
```

请注意，我们作为第三个参数传入的块接受一个参数。`size_t`类型本质上是一个整数，因此我们可以将其用作索引来从数组中检索对象。当您使用`dispatch_apply()`时，它会将块分派到给定队列指定次数，然后等待所有块完成后再返回。因为它会等待块完成，所以您可以将`dispatch_apply()`用作`for`循环的快速替代方案，并且当它与并发队列配合使用时，可以因此获得性能提升。

### 分派信号量

我们的 Twitter 示例有一个大问题：由于我们可以在屏幕上同时显示多个单元格，最终会有大量的 URL 连接同时加载。过多的并发连接会对性能产生不利影响。为了解决这个问题，我们将使用分派信号量，这是一种计数信号量，可以充当锁。当您使用`dispatch_semaphore_create()`创建信号量时，需要传入一个数字来初始化信号量的值。要使用信号量，请调用`dispatch_semaphore_wait()`，它会递减信号量的当前值。如果结果值小于 0，该函数会等待，直到值增加后再返回。信号量的值在`dispatch_semaphore_create()`方法中设置，因此要创建一个一次只允许一个访问的信号量，您可以通过调用`dispatch_semaphore_create(0)`来创建它。要增加值，请调用`dispatch_semaphore_signal()`。还可以为`dispatch_semaphore_wait()`指定最大等待时间，以便在无法立即操作时能够做出适当的响应。如果多次调用`dispatch_semaphore_wait()`，则按先进先出的方式确定哪个调用最先返回，类似于锁的行为。

为了限制 Twitter 应用中的并发连接数，我们将在`dispatch_semaphore_create()`中使用值 3。我们还将创建自己的分派队列来处理请求，以帮助清理代码。在 Xcode 中打开 TwitterExample 项目，并导航到`LCTTimelineViewController.m`。向类扩展添加两个变量：

```objectivec
@interface LCTTimelineViewController () {
    NSTimer *_reloadTimer;
    NSArray *_tweets;
    dispatch_queue_t _profileImageQueue;
    dispatch_semaphore_t _profileImageSemaphore;
}

- (void)reloadButtonPressed:(id)sender;
- (void)reloadTweets:(NSTimer *)reloadTimer;
- (void)tweetButtonPressed:(id)sender;

@end
```

请注意，因为分派对象不是 Objective-C 对象，所以它们不是指针。我们将在`initWithStyle:`方法中初始化它们：

```objectivec
- (id)initWithStyle:(UITableViewStyle)style
{
    self = [super initWithStyle:style];
    if (self) {
        [self setTitle:@"Timeline"];
        UIBarButtonItem *reloadButton =
            [[UIBarButtonItem alloc]
                initWithBarButtonSystemItem:UIBarButtonSystemItemRefresh
                                     target:self
                                     action:@selector(reloadButtonPressed:)];
        [[self navigationItem] setLeftBarButtonItem:reloadButton];
        UIBarButtonItem *tweetButton =
            [www.it-ebooks.info](http://www.it-ebooks.info/)
```


