# 第 8 章：管理事件发生时的情况



## 创建 `NSTimers` 的其他方式

我们还将介绍一些创建 `NSTimers` 的其他方式。如果我们想在定时器中附带一个字符串，可以像下面这样使用 `userInfo` 字典：

```objectivec
NSDictionary *userInfo = [NSDictionary dictionaryWithObject:@"foo"
                                                    forKey:@"myString"];

NSTimer *timer = [NSTimer scheduledTimerWithTimeInterval:30.0
                                                 target:myObject
                                               selector:@selector(update:)
                                               userInfo:userInfo
                                                repeats:YES];
```

相应的 `update:` 方法可以像下面这样访问 `userInfo` 字典：

```objectivec
- (void)update:(NSTimer *)timer
{
    NSString *myString = [[timer userInfo] objectForKey:@"myString"];
    // ...
}
```

你可以根据需要向 `userInfo` 字典中添加任意数量的对象，从而通过定时器传递复杂的数据。

如果你创建了一个重复执行的定时器，它会持续运行直到收到 `invalidate` 消息，如下所示：

```objectivec
[timer invalidate];
```

如果你创建了一个不重复执行的定时器，它会在首次触发后立即自行失效。定时器调用的方法的第一个参数必须是一个指向 `NSTimer` 的指针，因为定时器会将自身作为第一个参数传递。

现在，让我们修改 `TwitterExample` 项目，利用 `NSTimer` 来实现自动刷新时间线。我们将在 `viewWillAppear:` 方法中，在时间线填入初始推文列表后创建定时器。这样就不需要 `scheduleTweetRefresh` 方法了，同时还需要为 `reloadTweets` 方法添加一个 `NSTimer` 参数。

我们还需要一个实例变量来存储指向定时器的指针。为此，按如下方式修改 `LCTTimelineViewController.m` 中的类扩展：

```objectivec
@interface LCTTimelineViewController () {
    NSTimer *_reloadTimer;
    NSArray *_tweets;
}

- (void)reloadButtonPressed:(id)sender;
- (void)reloadTweets:(NSTimer *)reloadTimer;
- (void)scheduleTweetRefresh;
- (void)tweetButtonPressed:(id)sender;

@end
```

**注意：** 我们在类扩展中定义了两个私有实例变量：`_reloadTimer` 和 `_tweets`。我们也可以将它们定义为属性，而且你可能会听到更推荐这种做法的建议，因为直接访问实例变量可能导致内存管理问题。这个建议是在 ARC 出现之前提出的。然而，使用 ARC 后，编译器会确保你不会少保留对象，因此直接访问实例变量更加安全。

现在你可以完全删除 `scheduleTweetRefresh` 方法。接下来，修改 `reloadTweets` 方法，添加参数并移除对 `scheduleTweetRefresh` 的调用：

```objectivec
- (void)reloadTweets:(NSTimer *)reloadTimer
{
    LCTTwitterController *twitterController = [LCTTwitterController sharedInstance];
    [twitterController getTweetsInUserTimelineWithCompletionHandler:^(NSArray *tweets) {
        _tweets = tweets;
        [[self tableView] performSelectorOnMainThread:@selector(reloadData)
                                          withObject:nil
                                       waitUntilDone:NO];
        [self performSelectorOnMainThread:@selector(scheduleTweetRefresh)
                              withObject:nil
                           waitUntilDone:NO];
    }];
}
```

我们不再需要重新安排更新，因为我们要创建的定时器会处理所有的调度。我们将在 `viewWillAppear:` 方法中创建它，同时移除调用 `scheduleTweetRefresh` 的旧代码。按如下方式修改该方法：

```objectivec
- (void)viewWillAppear:(BOOL)animated
{
    [super viewWillAppear:animated];
    
    LCTTwitterController *twitterController = [LCTTwitterController sharedInstance];
    NSString *title = [self title];
    [self setTitle:@"正在授权…"];
    
    [twitterController authorizeAccountWithCompletionHandler:^{
        [self performSelectorOnMainThread:@selector(setTitle:)
                              withObject:@"正在加载推文…"
                           waitUntilDone:NO];
        [twitterController getTweetsInUserTimelineWithCompletionHandler:^(NSArray *tweets) {
            [self performSelectorOnMainThread:@selector(setTitle:)
                                  withObject:title
                               waitUntilDone:NO];
            _tweets = tweets;
            [[self tableView] performSelectorOnMainThread:@selector(reloadData)
                                              withObject:nil
                                           waitUntilDone:NO];
            [self performSelectorOnMainThread:@selector(scheduleTweetRefresh)
                                  withObject:nil
                               waitUntilDone:NO];
            _reloadTimer = [NSTimer scheduledTimerWithTimeInterval:15.0
                                                           target:self
                                                         selector:@selector(reloadTweets:)
                                                         userInfo:nil
                                                          repeats:YES];
        }];
    }];
}
```

最后，当视图消失时，我们需要使更新定时器失效。此时，我们将它设置为 `nil`，因为失效的定时器不能重复使用。我们还需要移除之前取消已调度选择器的旧代码。按如下方式修改 `viewWillDisappear:`：

```objectivec
- (void)viewWillDisappear:(BOOL)animated
{
    [super viewWillDisappear:animated];
    [NSObject cancelPreviousPerformRequestsWithTarget:self
                                            selector:@selector(reloadTweets)
                                              object:nil];
    [_reloadTimer invalidate];
    _reloadTimer = nil;
}
```

构建并运行应用程序。和之前一样，它应该会自动刷新，但这次几乎正好每隔 15 秒刷新一次。

定时器在 Cocoa Touch 应用中非常有用。例如，如果你有一个显示倒计时的标签，就可以使用定时器定期更新标签的值。定时器的性能足以让你以非常高的频率安排它们更新。例如，对于 UI 更新，你可以将时间间隔设置为 1/60 秒，这样你的 UI 将以每秒 60 帧的速度更新；任何比这更快的更新可能都是过度的。

关于重复定时器，需要注意的一个重要问题是它们不会叠加。定时器会安排代码运行，但如果代码已经安排好但尚未执行，它不会重新安排自己；一个定时器最多只能被安排运行一次。因此，定时器对于需要绝对确保代码运行特定次数的高性能场景并不适用。我们接下来会详细讨论这一点，涉及运行循环的相关内容。

## 运行循环

Cocoa Touch 应用是事件驱动应用的一个很好的例子。在大多数情况下，应用启动后会执行任何初始化所需的操作，然后……什么都不做——直到用户与之交互。没有用户交互时，应用会处于等待状态，除了某些例外情况，比如任何 `NSTimer` 对象触发时。它是如何做到这一点的呢？如果你在编写一个等待事件的系统，可能会像下面这样编写：

```objectivec
BOOL stop = NO;
while (stop == NO) {
    BOOL result = [self eventHappened];
    if (result == YES) {
        [self processEvent];
        stop = YES;
    }
}
```

这段代码会持续调用 `eventHappened` 方法来检查事件是否发生。如果发生了，它会调用其他代码；如果没有，则重复循环。虽然这段代码能工作，但效率极低。它会 100% 处于忙碌状态，要么反复调用 `eventHappened`，要么处理事件。更快的处理器只会让 `eventHappened` 被调用更多次。更糟糕的是，这段代码会完全阻塞任何后续执行，直到 `stop` 被设置为 `YES`，因此我们无法轻松地将多个循环链式组合起来。

iOS 和 Mac OS X 通过使用*运行循环*来解决这个问题，它能够以更高效的方式处理事件。运行循环的动作大致如图 8-1 所示。

**图 8-1.** *运行循环的基本周期*

运行循环会持续循环执行这些任务，但效率远高于简单的 `while` 循环。当它遇到用户输入或需要触发的定时器时，它会运行与该事件关联的代码，然后返回到循环的开头。所有自动释放的对象会在运行循环结束时被释放，然后循环回到开头。



这张图涵盖了主要部分，但运行循环中还会发生其他事情，例如网络连接接收数据和端口接收数据。不过，大多数情况下，你只需要通过添加代码来处理事件（例如按钮按下时）或创建计时器，来直接与运行循环的行为打交道。

你可以通过调用 `[NSRunLoop currentRunLoop]` 随时获取当前的运行循环。

即使当前线程尚不存在运行循环，此方法也会创建一个。

这在创建计时器时非常有用。到目前为止，我们见过一些便利方法，这些方法能自动将计时器安排到当前运行循环上，但你也可以自己来实现。下面展示了如何创建一个计时器并将其安排到运行循环上：

```
NSTimer *timer = [[NSTimer alloc] initWithFireDate:[[NSDate date]
dateByAddingTimeInterval:60.0]
interval:60.0
target:self
selector:@selector(reload:)
userInfo:nil
repeats:YES];
```

`[[NSRunLoop currentRunLoop] addTimer:timer forMode:NSDefaultRunLoopMode];`

这里有几个新内容需要讲解。首先是 `NSDate` 对象的使用。`NSDate` 仅仅是对象中某个特定时间的表示形式。`[NSDate date]` 返回一个代表当前时间的 `NSDate` 对象。我们在之前的例子中用它来获取当前日期，然后向其发送 `dateByAddingTimeInterval:` 方法，以获取一个比当前时间提前 60 秒的新日期。通过将这个日期设置为计时器的触发日期，我们可以控制计时器首次触发的时间。

接下来，我们通过 `NSRunLoop` 的类方法 `currentRunLoop` 获取当前运行循环，并将计时器添加到其中。模式参数定义了我们要添加计时器的运行循环模式。运行循环始终有一个模式，通常是 `NSDefaultRunLoopMode`，但有时它会切换模式，例如在积极处理用户输入时。通常，你会想要使用 `NSDefaultRunLoopMode`。

在 `NSRunLoop` 上，一个值得了解的有用方法是 `runUntilDate:` 方法。假设你正在主线程上执行一个冗长复杂的计算。这会导致 UI 卡顿，但你不想这样。由于你知道用户交互是在主运行循环中处理的，你可以强制它在继续执行之前先处理这些事件。如果你需要执行某项操作 1000 次，但又希望用户界面保持响应，可以这样做：

```
for (NSUInteger i = 0; i < 1000; i++) {
    [self performSomeTask];
    [[NSRunLoop currentRunLoop] runUntilDate:[NSDate date]];
}
```

每次循环执行时，都会调用 `performSomeTask` 方法，然后运行循环开始运行。起初，传递 `[NSDate date]`（它返回当前时间）可能看起来会让运行循环立即退出，但事实并非如此；实际上，它会运行整个循环并处理在当前时间之前发生的所有事件。如果用户在 `performSomeTask` 执行期间点击了按钮或计时器触发了，这些事件会在 `runUntilDate:` 方法返回之前被处理。

运行循环依赖于拥有输入源（例如 iOS 设备的屏幕，或 Mac 上的键盘和鼠标）来保持活跃。如果运行循环没有输入源或计时器，它就会退出。

关于运行循环的构成还有更多内容，但这些信息已经足够满足我们的需求了。

当你使用 Cocoa Touch 时，运行循环会为你自动创建和销毁，因此只要你知道如何与它们交互，这就足够了。最后一个有趣的小细节是查看 iOS 程序中 `main` 函数的实现。

对于任何 iOS 应用，你都会找到一个类似如下的 `main.m` 文件：

```
int main(int argc, char *argv[])
{
    @autoreleasepool {
        return UIApplicationMain(argc, argv, nil,
            NSStringFromClass([LCTAppDelegate class]));
    }
}
```

`UIApplicationMain` 函数就是开始运行你应用的函数。它会创建一个运行循环，该循环开始处理事件，并持续运行直到你的应用退出。



我们已经讨论过运行循环，这涵盖了大部分关于如何控制代码在单任务场景下执行的内容。然而，如今许多设备都拥有多个处理器核心，这意味着你可以同时运行多段代码！接下来，我们将探讨多线程代码的工作原理、编写方式以及如何安全地实现。

## 多线程代码

个人电脑刚开始普及时，处理器速度以相当可预测的速度提升。通常被称为摩尔定律，其速度大约每 18 个月翻一番。然而，随着时间的推移，从处理器进步中获得的速度提升开始放缓。为了加快运行速度，处理器制造商开始制造多核处理器，让它们能同时处理多件事情。处理器不再仅仅是变得更快，而是通过增加核心数量来让你能完成更多工作。从 iPhone 4S 和第二代 iPad 开始，即便是移动设备也在 iOS 中提供了双核处理能力，让你的应用能够有效地同时利用多段代码。但这种能力并非凭空而来；为了充分利用多个处理器核心，你必须编写相应的应用代码。否则，你的应用将只能在一个核心上运行，而其他核心则闲置未用。你可以自己判断哪种选择能提供最佳用户体验。

### 在另一个线程上运行代码

所有 Cocoa Touch 应用都从一个线程启动：主线程。这是调用 `UIApplicationMain()` 的线程，是 UI 回调（例如，表视图数据源方法）被调用的线程，也是你用来配置应用 UI 的线程。你甚至可能将应用的大部分代码都运行在主线程上；有些任务并不会从多线程中获益太多。

当你有一个长时间运行的任务时，不建议在主线程上执行，在某些情况下，这可能会导致系统终止你的应用。

避免这种情况的最简单方法是在一个新的线程上调用你的长时间运行任务：

```
[NSThread detachNewThreadSelector:@selector(longRunningTask)
                    toTarget:self
                  withObject:nil];
```

这相当于调用 `[self longRunningTask]`，不同之处在于它会创建一个新线程并在其上运行 `longRunningTask` 方法。需要注意的是，当你创建一个新线程时，会为该线程创建一个新的运行循环，因为每个线程都有自己的运行循环（应用中的主运行循环只是主线程的运行循环）。因此，在使用任何会自动释放对象的函数之前，你需要创建自己的自动释放池。由于你无法确定系统框架是否会自动释放对象，最好立即执行此操作。`longRunningTask` 应该这样实现：

```
- (void)longRunningTask
{
    @autoreleasepool {
        // 任务代码写在这里。
    }
}
```

通过用 `@autoreleasepool` 指令包裹方法代码，我们可以捕获此线程中任何自动释放的对象，并适当处理它们。

如你所见，创建一个新线程相当简单。但正确执行则需要一些工作。

### 线程安全

如果操作不当，在多线程上运行代码可能很危险。如果你在一个线程上写入一个整数，同时在另一个线程上读取它，那么读取操作可能返回旧值、新值，或者一个既非旧值也非新值的垃圾值，这可能导致应用崩溃或显示错误的值。编写代码来应对这些问题的概念称为线程安全。其中一项最简单的线程安全做法是在声明属性时将其声明为 `atomic`。当你这样做时（通过省略 `nonatomic` 关键字，因为没有 `atomic` 关键字），可以确保属性访问是线程安全的；如果你尝试同时进行读写操作，其中一个操作会等待另一个完成。



## 第八章：管理事件发生的时机

仅仅做到这一点还不足以实现真正的线程安全，但它能防止你在尝试读取属性时意外覆盖它。你经常会看到人们默认使用`nonatomic`属性，因为这样能获得轻微的性能提升，但如果你在多线程环境下进行任何操作，就应该从使用`atomic`属性开始。

可写的`atomic`属性有一个副作用：如果你使用合成（synthesized）的 getter 方法，就无法再定义自己的 setter 方法；这样做会破坏提供线程安全的内置机制。如果你需要在设置属性时运行代码，有两个选择：使用键值观察（KVO）来监听代码的变化，或者自己编写线程安全的代码。

你可以合成访问器方法并使用 KVO 来避免自己编写代码。然而，如果你需要自己编写，可以使用`NSLock`类创建锁，以此来强制实现线程安全所需的同步机制。让我们考虑一个假设的类`ThreadSafeObject`，它有一个属性`name`。我们希望编写自定义的`setName:`实现，因此我们将使用锁来限制对其的访问。接口看起来像这样：

```
@interface ThreadSafeObject : NSObject {

NSLock *_nameLock;

NSString *_name;

}

@property (atomic, copy) NSString *name;

@end
```

在设置或获取`name`的值之前，我们将使用`_nameLock`来确保线程安全。让我们逐步介绍`ThreadSafeObject`类的实现，从`init`方法开始：

```
@implementation ThreadSafeObject

- (id)init

{

self = [super init];

if (self) {

_nameLock = [[NSLock alloc] init];

}

return self;

}
```

如你所见，创建`_nameLock`对象很简单，不需要特殊方法；它只是一个常规的初始化。接下来是`setName:`方法：

```
- (void)setName:(NSString *)newName

{

[_nameLock lock];

_name = [newName copy];

[_nameLock unlock];

}
```

当我们调用锁的`lock`方法时，如果锁是未锁定状态，`lock`方法会立即返回；如果锁已经被锁定，则会等待直到它被解锁，然后将其锁定并返回。如果有多个方法试图锁定一个已经被锁定的锁，它们会按照先到先得的原则依次锁定。关键点在于，一个锁在代码中只能在一个位置被锁定——所以完成操作后一定要解锁，否则你将无法再次使用它。接下来是我们的最后一个方法`name`：

```
- (NSString *)name

{

NSString *nameToReturn;

[_nameLock lock];

nameToReturn = _name;

[_nameLock unlock];

return nameToReturn;

}

@end
```

请注意，我们不能在解锁锁之前返回，因此我们将`_name`的值存储在一个临时变量`nameToReturn`中。在这里也使用锁，完善了`name`属性的线程安全性，因为任何对`name`和`setName:`的组合调用都能成功执行，而不会相互覆盖。线程安全是一个复杂的话题，但锁和`atomic`属性是入门该领域的快速且高效的方法。

### 运行大量任务

通过创建新线程在后台执行任务，其能力是有限的。假设你有一个包含 1000 个对象的数组，并且希望对每个对象执行一个耗时任务。你当然可以创建一个后台线程，并在该线程上遍历数组中的对象，但如果你使用的是双核设备，你可能希望一次处理两个对象来加快速度。一个简单的实现可能会为每个对象分离一个线程，就像这样：

```
NSUInteger count = [myArray count];

for (NSUInteger i = 0; i < count; i++) {

id obj = [myArray objectAtIndex:i];

[NSThread detachNewThreadSelector:@selector(longRunningTask) toTarget:obj withObject:nil];

}
```



这种方式的缺陷在于，我们很快就会开启 1,001 个线程。每个线程都会带来一定的内存开销，因此额外创建 1,000 个线程成本高昂。在 iPhone 有限的资源下，还没等开启这么多线程，你的应用就会被系统终止，所以这不可行。

一种略为明智的实现方式是，先获取设备中的处理器核心数量，然后保持相应数量的线程开启，每当一个任务完成时，处理数组中的下一个对象。这避免了线程过多的问题，并确保始终有任务在执行。然而，这种方法并未考虑处理器的实际繁忙程度。设备可能正在后台运行某些代码——例如检查邮件、播放音乐等——单纯用任务填满处理器反而会影响这些后台操作。

当你遇到这类独立任务时，无需自己编写上述代码，可以直接使用内置类 `NSOperationQueue` 将操作加入队列，由它来自动执行。你可以通过 `NSOperationQueue` 实现之前的代码，如下所示：

```
NSOperationQueue *queue = [[NSOperationQueue alloc] init];
NSUInteger count = [myArray count];
for (NSUInteger i = 0; i < count; i++) {
    id obj = [myArray objectAtIndex:i];
    [queue addOperationWithBlock:^{
        [obj performLongTask];
    }];
}
```

使用 `NSOperationQueue` 可以让操作并发执行，同时尊重系统及其当前任务。它会根据处理器自动调整操作规模，从而减少你需要编写的代码量。

## 示例：Twitter 头像图片

接下来，我们将运用新学到的知识，在我们的 Twitter 示例中应用它。目标是加载时间线中用户的头像图片。首先，我们采用完全初级的方式，同步加载图片。完成之后，再使用后台线程来加载，最后用操作队列来实现。

打开 `LCTTimeLineViewController.m`，按如下方式修改 `tableView:cellForRowAtIndexPath:` 方法：

```
- (UITableViewCell *)tableView:(UITableView *)tableView
         cellForRowAtIndexPath:(NSIndexPath *)indexPath
{
    static NSString *CellIdentifier = @"Cell";
    UITableViewCell *cell = [tableView
                             dequeueReusableCellWithIdentifier:CellIdentifier];
    if (cell == nil) {
        cell = [[UITableViewCell alloc]
                initWithStyle:UITableViewCellStyleSubtitle
                reuseIdentifier:CellIdentifier];
    }
    // 配置 cell...
    NSDictionary *tweet = [_tweets objectAtIndex:[indexPath row]];
    [[cell textLabel] setText:[tweet objectForKey:@"text"]];
    [[cell detailTextLabel] setText:[[tweet objectForKey:@"user"]
                                     objectForKey:@"name"]];
    NSString *profileImageURI = [[tweet objectForKey:@"user"]
                                  objectForKey:@"profile_image_url"];
    NSURL *profileImageURL = [NSURL URLWithString:profileImageURI];
    NSURLRequest *profileImageURLRequest = [NSURLRequest
                                            requestWithURL:profileImageURL];
    NSURLResponse *response = nil;
    NSError *error = nil;
    NSData *imageData = [NSURLConnection
                         sendSynchronousRequest:profileImageURLRequest
                         returningResponse:&response
                         error:&error];
    UIImage *image = [UIImage imageWithData:imageData];
    [[cell imageView] setImage:image];
    return cell;
}
```

构建并运行应用。如果你的网络连接速度很快，可能甚至察觉不到应用为每一行加载图片时的卡顿；但如果网速较慢，你一定会明显感受到影响。与其在等待图片下载时阻塞 UI，不如使用 `NSOperationQueue` 来将图片排队下载。在 `LCTTimelineViewController.m` 文件顶部的类扩展中，为队列添加一个实例变量：

```
@interface LCTTimelineViewController () {
    NSTimer *_reloadTimer;
    NSArray *_tweets;
    NSOperationQueue *_profileImageQueue;
}
- (void)reloadButtonPressed:(id)sender;
- (void)reloadTweets:(NSTimer *)reloadTimer;
- (void)tweetButtonPressed:(id)sender;
@end
```



接下来，我们在`initWithStyle:`方法中初始化队列：

```
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
        [[UIBarButtonItem alloc]
         initWithBarButtonSystemItem:UIBarButtonSystemItemCompose
         target:self
         action:@selector(tweetButtonPressed:)];

        [[self navigationItem] setRightBarButtonItem:tweetButton];

        _profileImageQueue = [[NSOperationQueue alloc] init];
    }
    return self;
}
```

可以看到，这里的设置代码并不多。接下来，我们使用操作队列来下载图片。在`tableView:cellForRowAtIndexPath:`中，按如下方式修改方法：

```
- (UITableViewCell *)tableView:(UITableView *)tableView
         cellForRowAtIndexPath:(NSIndexPath *)indexPath
{
    static NSString *CellIdentifier = @"Cell";

    UITableViewCell *cell = [tableView
                             dequeueReusableCellWithIdentifier:CellIdentifier];
    if (cell == nil) {
        cell = [[UITableViewCell alloc]
                initWithStyle:UITableViewCellStyleSubtitle
                reuseIdentifier:CellIdentifier];
    }

    // Configure the cell...
    NSDictionary *tweet = [_tweets objectAtIndex:[indexPath row]];
    [[cell textLabel] setText:[tweet objectForKey:@"text"]];
    [[cell detailTextLabel] setText:[[tweet objectForKey:@"user"]
                                     objectForKey:@"name"]];

    NSString *profileImageURI = [[tweet objectForKey:@"user"]
                                 objectForKey:@"profile_image_url"];
    NSURL *profileImageURL = [NSURL URLWithString:profileImageURI];
    NSURLRequest *profileImageURLRequest = [NSURLRequest
                                            requestWithURL:profileImageURL];

    [_profileImageQueue addOperationWithBlock:^{
        NSURLResponse *response = nil;
        NSError *error = nil;
        NSData *imageData = [NSURLConnection
                             sendSynchronousRequest:profileImageURLRequest
                             returningResponse:&response
                             error:&error];
        UIImage *image = [UIImage imageWithData:imageData];
        [[cell imageView] setImage:image];
        [[cell imageView] performSelectorOnMainThread:@selector(setImage:)
                                           withObject:image
                                        waitUntilDone:NO];
        [cell performSelectorOnMainThread:@selector(setNeedsLayout)
                               withObject:nil
                            waitUntilDone:NO];
    }];

    return cell;
}
```

可以看到，我们只是将执行网络请求的代码包装在一个 block 中，然后将该 block 传递给操作队列。由于这个 block 不会在主线程上运行，因此我们随后在主线程上设置 cell 的 image view 中的图片，因为这是 UI 操作。接下来，我们在主线程上调用 cell 的`setNeedsLayout`。当从`tableView:cellForRowAtIndexPath:`返回 cell 时，其 image view 还没有图片，因此我们需要调用`setNeedsLayout`来强制 cell 调整其子视图以适应图片。

构建并运行应用，你会注意到表格视图单元格的内容异步加载。对于网络连接速度较慢的用户来说，这一点尤其好，因为他们在等待图片加载时仍然可以滚动表格视图。

现在，尽管创建自己的线程和使用操作队列可以很简单，但事实上，它们并不是最简单也不是最适合的并发代码执行方法。在 Mac OS X Snow Leopard 和 iOS 4.0 中，Apple 引入了一个新的低级框架来管理并发性，称为 Grand Central Dispatch，它在几乎所有方面都改进了并发代码。

**注意：** 如果你想模拟较差的网络条件，Apple 提供了一个名为 Network Link Conditioner 的工具。它不是默认 Xcode 安装的一部分，而是一个可选的开发者工具，你可以用它来在 Mac 上模拟较差的网络。

## Grand Central Dispatch



一般来说，你编写的代码可以分解为离散的任务：从服务器下载图片、将 JSON 响应解析为模型对象、根据用户输入计算值等等。通过将代码分解为更小的块，可以更轻松地管理它们、编写出更好的代码，也更易于更改代码的某一部分工作方式。Apple 的 Grand Central Dispatch 框架通过根据系统繁忙程度自动运行你的各个任务，帮助你管理代码的执行时间。

就像使用 `NSOperationQueue` 一样，你可以将单个任务添加到队列中，然后队列会执行这些任务直到全部完成。使用 Grand Central Dispatch，你将拥有比操作队列更大的灵活性。无需手动创建队列并向其添加项目，你可以根据优先级将项目添加到共享的全局队列，以及一个在主线程上运行任务的队列。我们先来看后一种情况。

`dispatch_async()` 函数接受一个调度队列作为第一个参数，要执行的代码块作为第二个参数。我们稍后将更详细地介绍调度队列，但目前，宏 `dispatch_get_main_queue()` 足以在此处返回主队列。在代码块中，我们正常调用这两个方法，知道它们会在主线程上执行。

当你向队列派发一个任务时，有两种方式：同步和异步。异步函数（例如 `dispatch_async()`）在调度任务后立即返回，而同步函数（例如相应的 `dispatch_sync()` 函数）会等待任务完成后再返回。

**注意：** 使用 Grand Central Dispatch 的同步函数时要格外小心。如果你在已经处于主队列上时，又向主队列同步派发一个任务，该任务将永远不会开始，因为主队列正在等待该任务完成。

你不必一定要将代码块与 Grand Central Dispatch 一起使用。如果你更愿意传递函数指针而不是代码块，替代方法 `dispatch_async_f()` 和 `dispatch_sync_f()` 与它们基于代码块的对应方法功能相同，只是多了一个参数：一个上下文指针，该指针作为第一个参数传递给函数。编写新代码时，你通常会使用代码块，但如果你有大量兼容的 C 函数，这允许你转而使用这些函数。

在 `TwitterExample` 代码中，还有几个地方可以用 Grand Central Dispatch 替换 `performSelector:` 方法。在 `LCTTimelineViewController.m` 中，按如下方式修改 `viewWillAppear:` 方法：

```objc
- (void)viewWillAppear:(BOOL)animated
{
    [super viewWillAppear:animated];

    LCTTwitterController *twitterController = [LCTTwitterController
        sharedInstance];
    NSString *title = [self title];
    [self setTitle:@"Authorizing…"];

    [twitterController authorizeAccountWithCompletionHandler:^{
        [self performSelectorOnMainThread:@selector(setTitle:)
                               withObject:@"Loading Tweets…"
                            waitUntilDone:NO];
        dispatch_async(dispatch_get_main_queue(), ^{
            [self setTitle:@"Loading Tweets…"];
        });

        [twitterController
            getTweetsInUserTimelineWithCompletionHandler:^(NSArray *tweets) {
                [self performSelectorOnMainThread:@selector(setTitle:)
                                       withObject:title
```



`waitUntilDone:NO`;

`_tweets = tweets;`

`[[self tableView] performSelectorOnMainThread:@selector(reloadData) withObject:nil waitUntilDone:NO];`

```
dispatch_async(dispatch_get_main_queue(), ^{
    [self setTitle:title];
    [[self tableView] reloadData];
});
```

`_reloadTimer = [NSTimer scheduledTimerWithTimeInterval:5.0 target:self selector:@selector(reloadTweets:) userInfo:nil repeats:YES];`

