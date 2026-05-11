# 图 6-2. 调试区域中打开的 Xcode 控制台

我的 Xcode 编辑器窗口使用深色背景；你的窗口（如果未修改过）将使用白色背景。

如果网络连接成功，你现在应该在控制台中看到响应数据。这是包含密歇根州底特律天气预报的 XML 格式数据。下一步是获取这些 XML 数据，将其转换为可用的格式，并展示给用户。但在那之前，我们当前的做法存在一个问题：通过使用 `NSURLConnection` 的 `sendSynchronousRequest:returningResponse:error:` 方法，我们阻止了应用在收到服务器响应前完成启动。这不仅给用户带来糟糕的体验，更重要的是，如果应用启动时间过长，它将被操作系统终止。让我们在处理数据之前先解决这个问题。

## 异步操作

我们之前使用的方法是一个典型的同步方法，它返回三个对象：数据、响应和错误（如果遇到的话）。`NSURLConnection` 还支持使用委托来接收这些对象，这样你可以在收到响应之前继续执行其他操作。让我们修改代码使其异步运行。打开应用委托的实现文件（我的是 `LCTAppDelegate.m`），通过删除带有删除线的行并添加粗体显示的行来修改 `application:didFinishLaunchingWithOptions:` 方法：

```
- (BOOL)application:(UIApplication *)application
didFinishLaunchingWithOptions:(NSDictionary *)launchOptions
{
    self.window = [[UIWindow alloc] initWithFrame:[[UIScreen mainScreen]
bounds]];
    // 应用启动后的自定义覆盖点
    self.window.backgroundColor = [UIColor whiteColor];
    [self.window makeKeyAndVisible];

    NSURL *weatherURL =
        [NSURL URLWithString:@"http://weather.yahooapis.com/forecastrss?p=48226"];
    NSURLRequest *urlRequest = [NSURLRequest requestWithURL:weatherURL];
    NSURLResponse *urlResponse = nil;
    NSError *error = nil;
    NSData *receievedData = [NSURLConnection sendSynchronousRequest:urlRequest
                                               returningResponse:&urlResponse
                                                           error:&error];
    NSString *receivedText = [[NSString alloc] initWithData:receievedData
                                                   encoding:NSUTF8StringEncoding];
    NSLog(@"%@", receivedText);

    NSURLConnection *connection = [[NSURLConnection alloc]
                                      initWithRequest:urlRequest
                                             delegate:self];
    [connection start];
    return YES;
}
```

## URL 连接委托方法

你可能已经注意到我们从未打开应用委托的头文件来声明我们遵守某个协议。虽然在 Cocoa Touch 中有一个名为 `NSURLConnectionDelegate` 的委托协议，但 `NSURLConnection` 的 delegate 属性并未声明为遵守该协议。从历史上看，URL 连接委托方法一直是一个非正式协议，并且重要的方法至今仍然如此。每次使用 `NSURLConnection` 时，你几乎都需要实现以下四个方法：

- `connection:didReceiveResponse:`
- `connection:didReceiveData:`
- `connection:didFailWithError:`
- `connectionDidFinishLoading:`

与 `UITableView` 的委托和数据源方法类似，所有这些方法的第一个参数都是指向 URL 连接的指针。之前通过单个同步方法返回的对象——响应正文中的数据、响应本身以及可能存在的错误——现在分别在不同的方法中返回，并在连接完成时额外调用一个方法。这种分离带来的一个后果是，我们需要在连接完成加载之前找一个地方来存储这些值。使用粗体行修改应用委托的实现文件（`LCTAppDelegate.m`），为这些值添加私有实例变量：

```
#import "LCTAppDelegate.h"

@implementation LCTAppDelegate {
    NSMutableData *_receivedData;
}
```


`NSURLResponse *_receivedResponse;`

`NSError *_connectionError;`

}

`@synthesize window = _window;`

…

您可能会注意到，我们使用的不再是`NSData`对象，而是`NSMutableData`对象。这是因为 URL 连接会多次调用`connection:didReceiveData:`方法，因此我们需要在其整个生命周期内持续跟踪接收到的所有数据。基于这一考虑，请在应用代理实现文件（`LCTAppDelegate.m`）中，用粗体显示的代码行进行修改：

```
- (BOOL)application:(UIApplication *)application
didFinishLaunchingWithOptions:(NSDictionary *)launchOptions
{
    self.window = [[UIWindow alloc] initWithFrame:[[UIScreen mainScreen]
bounds]];
    // 重写点为应用启动后的自定义设置
    self.window.backgroundColor = [UIColor whiteColor];
    [self.window makeKeyAndVisible];
    NSURL *weatherURL =
    [NSURL URLWithString:@"http://weather.yahooapis.com/forecastrss?p=48226"];
    NSURLRequest *urlRequest = [NSURLRequest requestWithURL:weatherURL];
    NSURLConnection *connection = [[NSURLConnection alloc]
initWithRequest:urlRequest
delegate:self];
    _receivedData = [[NSMutableData alloc] init];
    [connection start];
    return YES;
}
```

```
- (void)connection:(NSURLConnection *)connection
didReceiveResponse:(NSURLResponse *)response
{
    _receivedResponse = response;
}
```

```
- (void)connection:(NSURLConnection *)connection didReceiveData:(NSData
*)data
{
    [_receivedData appendData:data];
}
```

```
- (void)connection:(NSURLConnection *)connection didFailWithError:(NSError
*)error
{
    _connectionError = error;
}
```

```
- (void)connectionDidFinishLoading:(NSURLConnection *)connection
{
    NSLog(@"Response: %@", _receivedResponse);
    NSString *receivedString = [[NSString alloc] initWithData:_receivedData encoding:NSUTF8StringEncoding];
    NSLog(@"Body: %@", receivedString);
}
```

运行您的应用，您应该会看到与之前类似的控制台输出。不同之处在于，在连接完成前，应用的其余部分可以继续执行。如果连接加载需要 30 秒，用户将会感激不需要为此等待。

## 异步网络请求的注意事项

在继续之前，您需要了解一些异步使用`NSURLConnection`的注意事项。首先，`NSURLConnection`操作没有内置的排队机制。如果您创建了 50 个对象，每个对象都异步触发一个`NSURLConnection`，您会很快发现应用尝试同时打开 50 个连接时出现性能问题。虽然从硬件角度看，iOS 设备完全有能力维持多个并发连接，但超过一定数量就可能成为问题。一个常用的经验法则是并发连接数不超过三个，尤其是在用户连接到蜂窝网络时。

异步网络请求的第二个问题是，每个活动连接被赋予相同的优先级。如果您的应用中有一次极其关键的数据上传，它可能会与其他正在下载图片或内容的代码竞争资源，甚至可能与设备后台运行的邮件应用竞争。虽然这通常不算严重问题，但在设计应用时仍需注意。

最后，协调多个 URL 连接的代理消息比较困难。当然这也是可行的：每个代理方法的第一个参数都是指向 URL 连接的指针，因此您可以先引用该值再继续处理，但这很快就会变得繁琐，并且管理这些连接的状态也会颇具挑战。

幸运的是，我并非只带来坏消息。其他开发者早已遇到过这个问题，并为此创建了开源解决方案。其中大多数方案都使用了内置的排队机制。



`NSOperationQueue`的机制，我们将在后续章节中讨论。它们各自有其用途和优势，因此我不会只推荐某一个库，而是列举三个我认为很有用的库：`ASIHTTPRequest`、`AFNetworking`和`MKNetworkKit`。当你在构建真实应用时，请在网上搜索这些解决方案，并考虑利用它们来辅助你的异步网络处理。

回到我们的天气解析示例应用，我们已经以合适的异步方式从服务器接收了数据，现在让我们着手将数据解析为有用的形式。

[www.it-ebooks.info](http://www.it-ebooks.info/)

