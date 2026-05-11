# 第 6 章：集成网络与 Web 服务

```
- (id)init
{
    self = [super init];
    if (self) {
        _accountStore = [[ACAccountStore alloc] init];
        // 如果我们之前保存了账户，现在加载它。
        NSString *accountId =
        [[NSUserDefaults standardUserDefaults]
         stringForKey:kSavedTwitterAccountKey];
        if (accountId) {
            _twitterAccount = [_accountStore
                               accountWithIdentifier:accountId];
        }
    }
    return self;
}

- (void)authorizeAccount
{
    if (_twitterAccount == nil) {
        ACAccountType *accountType = [_accountStore
                                      accountTypeWithAccountTypeIdentifier:ACAccountTypeIdentifierTwitter];
        NSUserDefaults *userDefaults = [NSUserDefaults
                                        standardUserDefaults];
        [_accountStore requestAccessToAccountsWithType:accountType
                                withCompletionHandler:^(BOOL granted,
                                                        NSError *error) {
            if (granted) {
```



`NSArray *twitterAccounts =`
`[_accountStore accountsWithAccountType:accountType];`
`if ([twitterAccounts count] > 0) {`
    `_twitterAccount = [twitterAccounts objectAtIndex:0];`
    `NSString *identifier = [_twitterAccount identifier];`
    `[userDefaults setObject:identifier forKey:kSavedTwitterAccountKey];`
    `[userDefaults synchronize];`
`}`

`- (void)getTweetsInUserTimelineWithCompletionHandler:(void(^)(NSArray *tweets))handler {`
    `NSString *timelinePath = @"https://api.twitter.com/1/statuses/home_timeline.json";`
    `NSURL *timelineURL = [NSURL URLWithString:timelinePath];`
    `TWRequest *timelineRequest = [[TWRequest alloc] initWithURL:timelineURL parameters:nil requestMethod:TWRequestMethodGET];`
    `[timelineRequest setAccount:_twitterAccount];`
    `[timelineRequest performRequestWithHandler:^(NSData *responseData, NSHTTPURLResponse *urlResponse, NSError *error) {`
        `if (responseData) {`
            `id topLevelObject = [NSJSONSerialization JSONObjectWithData:responseData options:0 error:NULL];`
            `if ([topLevelObject isKindOfClass:[NSArray class]]) {`
                `if (handler != NULL) {`
                    `handler(topLevelObject);`
                `}`
            `}`
        `}`
    `}];`
`}`
`@end`

现在你已经编写了这些方法，让我们逐一解析它们。首先，在`sharedInstance`中，我们维护了一个`_sharedInstance`指针。使用`static`限定符确保我们只拥有一个指针；每次调用`sharedInstance`时，`_sharedInstance`都是同一个指针，不过在创建`TwitterController`的共享实例后，它会指向一个新的内存地址。

`init`方法从用户默认设置数据库中加载已保存的账户 ID（如果存在的话），这让我们能够缓存用户的账户，从而避免每次运行应用时都要用户重新授权。接下来，`authorizeAccount`方法请求对所有 Twitter 账户的授权。如果收到授权，它会将找到的第一个 Twitter 账户信息保存到用户默认设置数据库中，以便日后检索。在实际发布的应用中，如果用户有多个账户，你需要询问用户要使用哪一个账户。

时间线检索方法执行网络连接任务，这些操作对你来说应该并不陌生。它创建一个带有 URL 的请求，设置一些参数，然后执行该请求。不同之处在于，这里的请求类型是`TWRequest`，这是一种为与 Twitter 交互而专门设计的请求。`setAccount:`方法以 Twitter 账户作为参数，这使得`TWRequest`对象能够为你处理所有授权代码，从而免去你数不清的麻烦和头痛。该方法还使用一个 block 来处理来自网络的响应，我们利用响应数据构建一个`NSArray`，后续再将其解码为推文。目前，我们将这个`NSArray`传递给另一个函数，该函数作为参数以 block 形式传入此方法。现在我们已经为 Twitter 功能创建了一个简单的控制器，接下来让我们围绕它创建一些用户界面。

在 Xcode 中，选择 File → New → New File…或按下 ⌘+N。在左侧栏中选择 Cocoa Touch，然后在右侧窗格中选择 Objective-C 类。点击 Next，将这个新类命名为`LCTTimelineViewController`，并使其成为`UITableViewController`的子类。保持“Targeted for iPad”和“With XIB for user interface”两项均不勾选的状态。点击 Next，将文件保存到磁盘。打开实现文件（`LCTTimelineViewController.m`），在文件顶部添加一行，导入`LCTTwitterController`头文件：

```
#import "LCTTimelineViewController.h"
#import "LCTTwitterController.h"
```

Xcode 应已在`#import`行下方为你创建了一个类扩展。如果是这样，请复制粗体部分的代码，为我们的推文数组添加一个实例变量；否则，请复制整个代码块，并将其直接放置在最后一个`#import`指令的下方：



```objectivec
@interface LCTTimelineViewController () {

NSArray *_tweets;

}

@end
```

由于我们使用了 `UITableViewController` 模板，部分表格视图方法应该已经存在。从模板中找到 `numberOfSectionsInTableView:` 的实现。如果该方法不存在，请将下方完整的方法（删除划线部分）放置在主 `@implementation` 和 `@end` 指令之间（而非类扩展中）；否则，移除划线代码并添加粗体代码：

```objectivec
- (NSInteger)numberOfSectionsInTableView:(UITableView *)tableView
{
#warning 可能不完整的方法实现。
    // 返回分区数量。
    return 0;
    return 1;
}
```

这个表格视图将只有一个分区，时间线中的每条推文对应分区中的一行。找到已有的 `tableView:numberOfRowsInSection:` 实现，并按如下方式修改；若不存在，则在 `numberOfSectionsInTableView:` 之后实现它。

```objectivec
- (NSInteger)tableView:(UITableView *)tableView
numberOfRowsInSection:(NSInteger)section
{
#warning 不完整的方法实现。
    // 返回分区中的行数。
    return 0;
    return [_tweets count];
}
```

这里使用 `_tweets` 数组中的推文数量来确定表格视图的行数。最后，找到 `tableView:cellForRowAtIndexPath:` 的实现，并按如下方式修改；或在 `tableView:numberOfRowsInSection:` 之后实现它。

```objectivec
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

    // 配置单元格...
    NSDictionary *tweet = [_tweets objectAtIndex:[indexPath row]];
    [[cell textLabel] setText:[tweet objectForKey:@"text"]];
    [[cell detailTextLabel] setText:[[tweet objectForKey:@"user"]
                                     objectForKey:@"name"]];

    return cell;
}
```

该方法将推文文本和用户显示名称放入单元格中。

**注意：** 随着 Twitter 修改其 API，`text` 和 `name` 键可能会随时间变化。如果无法正常使用，请查阅 Twitter 文档。

在测试之前，我们还需要做两件事：显示视图控制器并将推文加载到其中。首先，仍在 `LCTTimelineViewController.m` 中，在你刚刚创建的方法之后（但在最后的 `@end` 行之前）实现 `viewWillAppear:` 方法：

```objectivec
- (void)viewWillAppear:(BOOL)animated
{
    [super viewWillAppear:animated];
    [[LCTTwitterController sharedInstance]
     getTweetsInUserTimelineWithCompletionHandler:^(NSArray *tweets) {
         _tweets = tweets;
         [[self tableView] performSelectorOnMainThread:@selector(reloadData)
                                           withObject:nil
                                        waitUntilDone:NO];
     }];
}
```

再次说明，虽然 `getTweetsInUserTimelineWithCompletionHandler:` 的 block 参数语法可能令人困惑，但我们将在下一章详细讲解。此外，我们将在未来关于性能和多线程的章节中解释 `performSelectorOnMainThread:withObject:waitUntilDone:` 方法。接下来，让我们将这个视图控制器显示在屏幕上。打开应用委托实现文件（`LCTAppDelegate.m`），在顶部添加一行以导入视图控制器的头文件：

```objectivec
#import "LCTAppDelegate.h"
#import "LCTTwitterController.h"
#import "LCTTimelineViewController.h"
```

然后，修改 `application:didFinishLaunchingWithOptions:` 方法，添加粗体行以显示视图控制器：

```objectivec
- (BOOL)application:(UIApplication *)application
didFinishLaunchingWithOptions:(NSDictionary *)launchOptions
{
    self.window = [[UIWindow alloc] initWithFrame:[[UIScreen mainScreen]
                                                    bounds]];
```



```objectivec
// 应用启动后的自定义覆盖点

self.window.backgroundColor = [UIColor whiteColor];

[self.window makeKeyAndVisible];

[[LCTTwitterController sharedInstance] authorizeAccount];

LCTTimelineViewController *viewController =

[[LCTTimelineViewController alloc]

initWithStyle:UITableViewStylePlain];

[[self window] setRootViewController:viewController];

return YES;

}
```

构建并运行应用程序。当应用启动时，您会看到一个提示视图，要求您授权应用使用您的 Twitter 帐户。授权成功后，您将看到来自您关注账户的最新推文列表。恭喜！您已经成功创建了第一个 Twitter 应用。不过有一个小问题：如果用户在尚未授权账户时加载了表视图控制器，它将无法加载任何推文。为解决此问题，我们先添加一个重新加载按钮。在 Xcode 中，返回应用委托实现文件（`LCTAppDelegate.m`）中刚修改的方法，将视图控制器嵌入导航控制器：

```objectivec
- (BOOL)application:(UIApplication *)application

didFinishLaunchingWithOptions:(NSDictionary *)launchOptions

{

self.window = [[UIWindow alloc] initWithFrame:[[UIScreen mainScreen]

bounds]];

// 应用启动后的自定义覆盖点

self.window.backgroundColor = [UIColor whiteColor];

[self.window makeKeyAndVisible];

[[LCTTwitterController sharedInstance] authorizeAccount];

LCTTimelineViewController *viewController =

[[LCTTimelineViewController alloc] initWithStyle:UITableViewStylePlain]; UINavigationController *navigationController =

[[UINavigationController alloc]

initWithRootViewController:viewController];

[[self window] setRootViewController:navigationController];

[www.it-ebooks.info](http://www.it-ebooks.info/)

第 6 章：集成网络与 Web 服务

return YES;

}
```

现在，打开视图控制器的实现文件（`LCTTimelineViewController.m`），修改 `initWithStyle:` 方法：

```objectivec
- (id)initWithStyle:(UITableViewStyle)style

{

self = [super initWithStyle:style];

if (self) {

[self setTitle:@"时间线"];

UIBarButtonItem *reloadButton =

[[UIBarButtonItem alloc]

initWithBarButtonSystemItem:UIBarButtonSystemItemRefresh

target:self

action:@selector(reloadButtonPressed:)];

[[self navigationItem] setLeftBarButtonItem:reloadButton];

}

return self;

}
```

接着，在文件顶部的类扩展中声明 `reloadButtonPressed:` 方法：

```objectivec
@interface LCTTimelineViewController () {

NSArray *_tweets;

}

- (void)reloadButtonPressed:(id)sender;

@end
```

最后，在对象的 `@implementation` 主代码块（不是类扩展）中，于 `@end` 指令之前实现 `reloadButtonPressed:` 方法：

```objectivec
- (void)reloadButtonPressed:(id)sender

{

[[LCTTwitterController sharedInstance]

getTweetsInUserTimelineWithCompletionHandler:^(NSArray *tweets) {

_tweets = tweets;

[[self tableView] performSelectorOnMainThread:@selector(reloadData)

withObject:nil

waitUntilDone:NO];

[www.it-ebooks.info](http://www.it-ebooks.info/)

第 6 章：集成网络与 Web 服务

}];

}
```

再次构建并运行应用，您应该能够使用该按钮刷新列表。然而，一个好的 Twitter 客户端还应允许用户发布推文。让我们添加这个功能。为此，我们将使用 Twitter 框架中的另一个类 `TWTweetComposeViewController`。停留在视图控制器的实现文件中，再次修改 `initWithStyle:` 方法，添加一个发布推文的按钮：

```objectivec
- (id)initWithStyle:(UITableViewStyle)style

{

self = [super initWithStyle:style];

if (self) {

[self setTitle:@"时间线"];

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

}
```


```objc
[[self navigationItem] setRightBarButtonItem:tweetButton];
```

```objc
}

return self;

}
```

照例，在类扩展中添加一行代码，声明按钮将要调用的动作方法：

```objc
@interface LCTTimelineViewController () {

    NSArray *_tweets;

}

- (void)reloadButtonPressed:(id)sender;

- (void)tweetButtonPressed:(id)sender;

@end
```

接下来，在文件顶部添加一行代码，导入 Twitter 框架头文件：

```objc
#import "LCTTimelineViewController.h"

#import <Twitter/Twitter.h>

#import "LCTTwitterController.h"
```

最后，在 `reloadButtonPressed:` 之下、`@end` 指令之前，实现 `tweetButtonPressed:`：

```objc
- (void)tweetButtonPressed:(id)sender

{

    TWTweetComposeViewController *viewController =

        [[TWTweetComposeViewController alloc] init];

    [self presentModalViewController:viewController animated:YES];

}
```

大功告成！运行你的应用，点击发推按钮。你会看到一个完整的 Twitter 界面，而你无需编写任何代码，而且从中发推功能确实可以正常工作！

如你所见，iOS 5 内置的 Twitter 支持极大简化了与 Twitter 协作的应用开发。下一章，我们将进一步探讨 blocks，以及它们如何与 Twitter 集成等现代 API 协同工作，并逐步改进我们这个小型的 Twitter 客户端。

## 总结

本章我们深入探讨了 iOS 上的网络相关话题，涵盖将 XML 和 JSON 解析为有意义的模型对象、创建及使用 URL 连接、以及异步使用这些连接。此外，我们还研究了从远程源下载图像、以及向网络服务发送数据。我们还创建了一个简单的 Twitter 客户端，随着本书内容的深入，我们将持续为其添加新功能。

[www.it-ebooks.info](http://www.it-ebooks.info/)

