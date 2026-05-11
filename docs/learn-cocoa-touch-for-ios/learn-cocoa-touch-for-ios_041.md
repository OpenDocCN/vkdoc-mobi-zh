# 使用 Block 编写现代代码

根据数组的大小，使用此方法对其内容进行并发排序可以带来巨大的性能提升。与枚举一样，设备拥有的处理器核心越多，排序速度就越快。因此，现在像这样编写数组排序代码，可以确保在任何未来的 iOS 设备上获得最大性能，无论其拥有多少内核。

回顾一下，`block` 允许你编写更好的代码。你可以使用它们在某些情况下替代回调，使回调代码靠近其他相关代码；用于枚举和排序数组；以及在需要 `block` 的新 API 中使用。既然我已经论证了使用 `block` 的好处，那么接下来让我们看看如何将 `block` 添加到自己的 API 中。

## 在你的代码中使用 Block

在你的代码中使用 `block` 主要有两种方式，区别在于 `block` 的生命周期。第一种也是最简单的方式是，简单地将一个 `block` 作为方法参数接受，并在方法中的某个时刻调用它：

```
- (void)expensiveOperationWithCompletionHandler:(void(^)(void))handler
{
    [self performExpensiveOperation];
    if (handler != NULL) {
        handler();
    }
}
```

在此方法中，我们使用 `handler` 参数来存储一个 `block`，该 `block` 在方法的主要操作完成后被调用。注意，我们必须检查 `block` 是否为 `NULL`，因为像这样调用此方法是完全合法的：

```
[self expensiveOperationWithCompletionHandler:NULL];
```

如果我们没有检查 `NULL`，应用将会崩溃。

**注意：** 关于当 `block` 指向空时，使用 `NULL` 还是 `nil` 更合适，一直存在一些争论。在本书中，我将坚持使用 `NULL`，但如果你在其他代码中看到 `nil`，那也是可以接受的。

这个方法之所以如此简单，是因为当 `handler` 存在于栈帧中时，栈帧永远不会退出。即使 `block` 是在栈上创建并且从未移动到堆上，它在这里仍然是有效的。在你的 API 中调用 `block` 的第二种方法则绕过了这个问题。

对于第二种方法，你需要首先为 `block` 创建一个属性，使用 `copy` 属性来确保它被复制到堆上：

```
@property (copy) void(^completionHandler)(void);
```

那么 `expensiveOperation` 方法可能是这样的：

```
- (void)expensiveOperation
{
    [self performExpensiveOperation];
    if ([self completionHandler] != NULL) {
        [self completionHandler]();
    }
}
```

即使 `block` 是一个属性，我们也要将其与 `NULL` 进行检查，以确保不会出错。将 `block` 存储在属性中不仅允许你将其存储在堆中并在必要时调用它，还允许你从多个方法以及多次调用同一个 `block`。一个常见的例子可以在一些开源网络库中找到：当你创建一个加载 URL 的请求时，你可以指定一个 `block` 在进度更新时运行。

现在我们已经介绍了这些方法，让我们看看如何改进第 6 章中的 Twitter 客户端以利用 `block`。

## 使用 Block 更新 TwitterExample

在我们之前的 `TwitterExample` 代码中，我们可以使用 `block` 进行一些优化，以稍微清理代码。在我们的 Twitter 控制器类中，有一个 `authorizeAccount` 方法，用于将用户登录到 Twitter 并授权我们的应用使用他们的 Twitter 账户。目前，该方法只是调用了 `ACAccountStore` 的 `requestAccessToAccountsWithType:completionHandler:` 方法后就返回了，但一旦该方法完成，它并没有做任何特别的事情。

因此，我们在应用加载时尝试授权应用，并希望它在我们的视图控制器加载推文之前完成。你可能已经注意到应用启动时加载了一个空白的推文列表，但当用户点击“重新加载”按钮时却能完美加载；这就是原因。让我们更改该方法，使其拥有自己的完成处理程序，这样我们就可以在账户获得授权时利用 `block` 来执行代码。

### 添加完成处理程序

启动 Xcode 并打开 `TwitterExample` 项目。打开 Twitter 控制器的头文件 `LCTTwitterController.h`（请记住，如果你的类前缀不是 `LCT`，文件名将会不同），并修改 `authorizeAccount` 的声明，删除被删除的行并添加粗体显示的行：

```
@interface LCTTwitterController : NSObject

+ (id)sharedInstance;
- (void)authorizeAccount;
- (void)authorizeAccountWithCompletionHandler:(void(^)(void))handler;
- (void)getTweetsInUserTimelineWithCompletionHandler:(void(^)(NSArray *tweets))handler;

@end
```

现在，打开相应的实现文件（`LCTTwitterController.m`），并修改 `authorizeAccount` 方法，删除被删除的行并添加粗体显示的行：

```
- (void)authorizeAccount
- (void)authorizeAccountWithCompletionHandler:(void (^)(void))handler
{
    if (_twitterAccount == nil) {
        ACAccountType *accountType = [_accountStore
                                      accountTypeWithAccountTypeIdentifier:ACAccountTypeIdentifierTwitter];
        NSUserDefaults *userDefaults = [NSUserDefaults standardUserDefaults];

        [_accountStore requestAccessToAccountsWithType:accountType
                                 withCompletionHandler:^(BOOL granted, NSError *error) {
            if (granted) {
                NSArray *twitterAccounts =
                [_accountStore accountsWithAccountType:accountType];

                if ([twitterAccounts count] > 0) {
                    _twitterAccount = [twitterAccounts objectAtIndex:0];
                    NSString *identifier = [_twitterAccount identifier];
                    [userDefaults setObject:identifier
                                     forKey:kSavedTwitterAccountKey];
                    [userDefaults synchronize];
                }
            }

            if (handler != NULL) {
                handler();
            }
        }];
    }
    else {
        if (handler != NULL) {
            handler();
        }
    }
}
```

需要注意的重要一点是，我们必须在调用 `handler` 之前检查它的值。如果我们在调用方法时传入了 `NULL`，但方法没有检查 `handler` 的值是否为 `NULL`，那么当我们尝试执行 `block` 时，应用将会崩溃。这是一个简单的改变，但它允许我们在账户授权后执行任意代码。如果我们已经有一个账户，那么该方法将调用完成处理程序而不做其他事情，从而允许已保存的登录像以前一样继续工作。

在我们将它投入使用之前，我们需要移除对 `authorizeAccount` 的现有调用，因为该方法已不存在。打开应用委托的实现文件（`LCTAppDelegate.m`），并通过删除被删除的行来移除 `application:didFinishLaunchingWithOptions:` 方法中授权账户的行：

```
- (BOOL)application:(UIApplication *)application
didFinishLaunchingWithOptions:(NSDictionary *)launchOptions
{
    self.window = [[UIWindow alloc] initWithFrame:[[UIScreen mainScreen] bounds]];
    // Override point for customization after application launch.
    self.window.backgroundColor = [UIColor whiteColor];
    [self.window makeKeyAndVisible];

    [[LCTTwitterController sharedInstance] authorizeAccount];

    LCTTimelineViewController *viewController =
    [[LCTTimelineViewController alloc] initWithStyle:UITableViewStylePlain];
    UINavigationController *navigationController =
    [[UINavigationController alloc] initWithRootViewController:viewController];
    [[self window] setRootViewController:navigationController];

    return YES;
}
```

幸运的是，Xcode 应该已经提醒你这一行有问题。现在我们在应用委托中不再使用 `LCTTwitterController`，我们可以移除它的 `#import` 指令。在文件顶部，像这样删除被删除的行：

```
#import "LCTAppDelegate.h"
```



```objectivec
#import "LCTTwitterController.h"
#import "LCTTimelineViewController.h"
```

现在是保存工作的好时机。接下来，我们将在 `LCTTimelineViewController` 的视图控制器生命周期中添加一个步骤，以便在加载推文前完成账户授权。打开 `LCTTimelineViewController.m`，并通过添加粗体所示的代码行来修改 `viewWillAppear:` 方法：

```objectivec
- (void)viewWillAppear:(BOOL)animated
{
    [super viewWillAppear:animated];
    [[LCTTwitterController sharedInstance]
        authorizeAccountWithCompletionHandler:^{
            [[LCTTwitterController sharedInstance]
                getTweetsInUserTimelineWithCompletionHandler:^(NSArray *tweets) {
                    _tweets = tweets;
                    [[self tableView] performSelectorOnMainThread:@selector(reloadData)
                                                      withObject:nil
                                                   waitUntilDone:NO];
                }];
        }];
}
```

这里我们有一个嵌套在另一个块中的块。一旦第一个方法的完成处理器被触发，第二个方法就会被调用，最后当它的完成处理器被触发时，我们重新加载表格视图。在 Xcode 中运行应用；它应该在授权账户后启动时加载推文。这很不错，但在加载过程中没有任何反馈，只有一个空白的表格视图。让我们添加一些视觉提示，表明应用正在工作。

## 添加活动指示器

指示应用正在进行网络调用的一种方法是使用设备状态栏上的网络活动指示器。这通过 `UIApplication` 类修改 `networkActivityIndicatorVisible` 属性来实现。让我们在加载推文时完成这项工作。在 Xcode 中打开 `LCTTwitterController.m`，并通过添加粗体所示的代码行来修改 `getTweetsInUserTimelineWithCompletionHandler:` 方法，以使用网络活动指示器：

```objectivec
- (void)getTweetsInUserTimelineWithCompletionHandler:(void(^)(NSArray *tweets))handler
{
    NSString *timelinePath = @"https://api.twitter.com/1/statuses/home_timeline.json";
    NSURL *timelineURL = [NSURL URLWithString:timelinePath];
    TWRequest *timelineRequest = [[TWRequest alloc] initWithURL:timelineURL
                                                    parameters:nil
                                                 requestMethod:TWRequestMethodGET];
    [timelineRequest setAccount:_twitterAccount];

    [[UIApplication sharedApplication] setNetworkActivityIndicatorVisible:YES];

    [timelineRequest performRequestWithHandler:^(NSData *responseData,
                                                  NSHTTPURLResponse *urlResponse,
                                                  NSError *error) {
        [[UIApplication sharedApplication] setNetworkActivityIndicatorVisible:NO];
        if (responseData) {
            id topLevelObject = [NSJSONSerialization JSONObjectWithData:responseData
                                                               options:0
                                                                 error:NULL];
            if ([topLevelObject isKindOfClass:[NSArray class]]) {
                if (handler != NULL) {
                    handler(topLevelObject);
                }
            }
        }
    }];
}
```

构建并运行应用，你应该会注意到状态栏中的网络活动指示器。这是 iOS 应用中近乎通用的设计，用于告知用户应用正在执行网络操作。不过，如果你正在查看表格视图，它并不太显眼。让我们通过在加载推文时更新视图控制器的标题来解决这个问题。在 Xcode 中，打开 `LCTTimelineViewController.m`，并通过添加粗体所示的代码行来修改 `viewWillAppear:` 方法：

```objectivec
- (void)viewWillAppear:(BOOL)animated
{
    [super viewWillAppear:animated];

    LCTTwitterController *twitterController = [LCTTwitterController sharedInstance];
    NSString *title = [self title];
    [self setTitle:@"Authorizing…"];

    [twitterController authorizeAccountWithCompletionHandler:^{
        [self performSelectorOnMainThread:@selector(setTitle:)
                               withObject:@"Loading Tweets…"
                            waitUntilDone:NO];

        [twitterController getTweetsInUserTimelineWithCompletionHandler:^(NSArray *tweets) {
            [self performSelectorOnMainThread:@selector(setTitle:)
                                   withObject:title
                                waitUntilDone:NO];
            _tweets = tweets;
            [[self tableView] performSelectorOnMainThread:@selector(reloadData)
                                              withObject:nil
                                           waitUntilDone:NO];
        }];
    }];
}
```



