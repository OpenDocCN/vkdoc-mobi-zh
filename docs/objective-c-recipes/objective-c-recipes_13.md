# 代码

**代码清单 6-21.** *AppDelegate.h*

```objc
#import <UIKit/UIKit.h>

@class ViewController;

@interface AppDelegate : UIResponder <UIApplicationDelegate>

@property (strong, nonatomic) UIWindow *window;

@property (strong, nonatomic) ViewController *viewController;

@end
```

**代码清单 6-22.** *AppDelegate.m*

```objc
#import "AppDelegate.h"

#import "ViewController.h"

@implementation AppDelegate

@synthesize window = _window;
@synthesize viewController = _viewController;

- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:
(NSDictionary *)launchOptions{
    self.window = [[UIWindow alloc] initWithFrame:[[UIScreen mainScreen] bounds]];
    // Override point for customization after application launch.
    self.viewController = [[ViewController alloc] initWithNibName:@"ViewController"
                                                           bundle:nil];
    self.window.rootViewController = self.viewController;
    [self.window makeKeyAndVisible];

    return YES;
}

@end
```

**代码清单 6-23.** *ViewController.h*

```objc
#import <UIKit/UIKit.h>

@interface ViewController : UIViewController

@property(strong) UIButton *myButton;
@property(strong) UIActivityIndicatorView *myActivityIndicator;
@property(strong) UIProgressView *myProgressView;
@property(assign) dispatch_queue_t serialQueue;

@end
```

**代码清单 6-24.** *ViewController.m*

```objc
#import "ViewController.h"

@implementation ViewController
@synthesize myButton, myActivityIndicator, myProgressView, serialQueue;

-(void)bigTaskAction{

    dispatch_async(self.serialQueue, ^{

        dispatch_sync(dispatch_get_main_queue(), ^{
            [self.myActivityIndicator startAnimating];
        });

        int updateUIWhen = 1000;
        for(int i=0;i<10000;i++){
            NSString *newString = [NSString stringWithFormat:@"i = %i", i];
            NSLog(@"%@", newString);
            if(i == updateUIWhen){
                float f = (float)i/10000;
                NSNumber *percentDone = [NSNumber numberWithFloat:f];

                dispatch_sync(dispatch_get_main_queue(), ^{
                    [self.myProgressView setProgress:[percentDone floatValue]
                                            animated:YES];

                });

                updateUIWhen = updateUIWhen + 1000;
            }
        }

        dispatch_sync(dispatch_get_main_queue(), ^{

            [self.myProgressView setProgress:1.0
                                    animated:YES];
            [self.myActivityIndicator stopAnimating];

        });
    });

}

- (void)viewDidLoad{
    [super viewDidLoad];

    self.serialQueue = dispatch_queue_create(DISPATCH_QUEUE_SERIAL, 0);

    //创建按钮
    self.myButton = [UIButton buttonWithType:UIButtonTypeRoundedRect];
    self.myButton.frame = CGRectMake(20, 403, 280, 37);
    [self.myButton addTarget:self
                      action:@selector(bigTaskAction)
            forControlEvents:UIControlEventTouchUpInside];
    [self.myButton setTitle:@"执行长时间任务"
                   forState:UIControlStateNormal];
    [self.view addSubview:self.myButton];

    //创建活动指示器
    self.myActivityIndicator = [[UIActivityIndicatorView alloc] init];
    self.myActivityIndicator.frame = CGRectMake(142, 211, 37, 37);
    self.myActivityIndicator.activityIndicatorViewStyle =
UIActivityIndicatorViewStyleWhiteLarge;
    self.myActivityIndicator.hidesWhenStopped = NO;
    [self.view addSubview:self.myActivityIndicator];

    //创建标签
    self.myProgressView = [[UIProgressView alloc] init];
    self.myProgressView.frame = CGRectMake(20, 20, 280, 9);
    [self.view addSubview:self.myProgressView];

}

@end
```



### 用法

要测试这段代码，首先按食谱 6.5 的方式设置一个应用程序。运行该应用后，在 iOS 模拟器中你会看到一个包含一个按钮和空白进度视图的界面。视图中央还会有一个白色活动指示器。点击按钮两次，即可在后台线程中启动 `bigTask` 任务。随着 `bigTask` 的运行，你会看到进度视图以 10% 的增量逐渐填满，直至任务完成。每次点击按钮都会重复此过程。进度视图不应频繁来回跳动。

## 6.7 使用 NSOperationQueue 实现异步处理

### 问题

你希望为应用添加异步处理能力，但更倾向于使用比 GCD 方法更具面向对象特性的方案。

### 解决方案

如果你希望使用 GCD 但不想直接调用 GCD 库，请使用 `NSOperationQueue`。

**注意：** `NSOperationQueue` 适用于 iOS 2 及以上版本和 OSX 10.5 及以上版本。这使得 `NSOperationQueue` 在需要支持旧系统运行的应用时成为理想选择，同时避免使用带线程锁的 `NSThread`。使用 `NSOperationQueue` 时，实现的细节对你来说是透明的。旧系统会通过线程支持 `NSOperationQueue`，而新系统则会使用 GCD。

`NSOperationQueue` 代表一个将要执行的代码队列。你可以使用 `NSOperationQueue` 在后台运行代码，或在主队列中处理用户界面操作。

`NSOperationQueue` 支持多种添加代码的方式。如果操作系统支持 blocks（iOS 4 及以上版本和 OSX 10.6 及以上版本），你可以直接使用 `addOperationWithBlock:` 方法将代码添加到队列中。

如果不支持，则必须将要执行的代码封装为独立的 `NSOperation` 子类。`NSOperation` 的子类会像一个 block 一样工作，将数据和要在队列中执行的代码封装在一起。

#### 实现原理

在本食谱中，你将解决食谱 6.3 中相同的难题。但与使用必须加锁的线程不同，你将使用操作队列和主队列来异步分发代码。再次以食谱 6.2 为基础模板，将其修改为使用操作队列（完整代码上下文请参见清单 6-25 至 6-28）。

首先，在视图控制器的实现中直接添加主队列和串行队列的本地实例。

```
#import "ViewController.h"

@implementation ViewController
@synthesize myButton, myActivityIndicator, myProgressView;
NSOperationQueue *serialQueue;
NSOperationQueue *mainQueue;

@end
```

主队列负责向用户界面发送指令。串行队列则会按照接收顺序逐一执行代码块，其行为与食谱 6.6 中的 GCD 串行队列一致。

`viewDidLoad` 方法是实例化这两个队列对象的理想位置。

```
#import "ViewController.h"

@implementation ViewController
@synthesize myButton, myActivityIndicator, myProgressView;
NSOperationQueue *serialQueue;
NSOperationQueue *mainQueue;

- (void)viewDidLoad{
    [super viewDidLoad];

    //创建操作队列
    mainQueue = [NSOperationQueue mainQueue];

    serialQueue = [[NSOperationQueue alloc] init];
    serialQueue.maxConcurrentOperationCount = 1;

}

@end
```

你可以通过 `[NSOperationQueue mainQueue]` 方法直接获取主队列的引用。这是一个单例，始终返回主队列的实例。串行队列则通过 `alloc` 和 `init` 构造器创建。将 `maxConcurrentOperationCount` 设为 1，即可使其成为串行队列，因为这样它每次只能执行一个操作。

队列设置完成后，即可在 `bigTaskAction` 方法中使用它们来调度 block。

```
-(void)bigTaskAction{

    [serialQueue addOperationWithBlock: ^{

        [mainQueue addOperationWithBlock: ^{
            [self.myActivityIndicator startAnimating];
        }];

        int updateUIWhen = 1000;
        for(int i=0;i<10000;i++){
            NSString *newString = [NSString stringWithFormat:@"i = %i", i];
            NSLog(@"%@", newString);
            if(i == updateUIWhen){
                float f = (float)i/10000;
                NSNumber *percentDone = [NSNumber numberWithFloat:f];

                [mainQueue addOperationWithBlock: ^{
                    [self.myProgressView setProgress:[percentDone floatValue]
                                            animated:YES];
                }];

                updateUIWhen = updateUIWhen + 1000;
            }
        }

        [mainQueue addOperationWithBlock: ^{
            [self.myProgressView setProgress:1.0
                                    animated:YES];
            [self.myActivityIndicator stopAnimating];
        }];
    }];
}
```

如果你已经阅读过食谱 6.6，可以发现这里基本遵循了与 GCD 相同的模式。请参见清单 6-25 至 6-28 获取完整代码。



#### 代码

**清单 6-25.** *AppDelegate.h*

```objective-c
#import <UIKit/UIKit.h>

@class ViewController;

@interface AppDelegate : UIResponder <UIApplicationDelegate>

@property (strong, nonatomic) UIWindow *window;
@property (strong, nonatomic) ViewController *viewController;

@end
```

**清单 6-26.** *AppDelegate.m*

```objective-c
#import "AppDelegate.h"
#import "ViewController.h"

@implementation AppDelegate

@synthesize window = _window;
@synthesize viewController = _viewController;

- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions{
    self.window = [[UIWindow alloc] initWithFrame:[[UIScreen mainScreen] bounds]];
    // 应用启动后的自定义覆盖点。
    self.viewController = [[ViewController alloc] initWithNibName:@"ViewController"
                                                           bundle:nil];
    self.window.rootViewController = self.viewController;
    [self.window makeKeyAndVisible];

    return YES;
}

@end
```

**清单 6-27.** *ViewController.h*

```objective-c
#import <UIKit/UIKit.h>

@interface ViewController : UIViewController

@property(strong) UIButton *myButton;
@property(strong) UIActivityIndicatorView *myActivityIndicator;
@property(strong) UIProgressView *myProgressView;

@end
```

**清单 6-28.** *ViewController.m*

```objective-c
#import "ViewController.h"

@implementation ViewController
@synthesize myButton, myActivityIndicator, myProgressView;
NSOperationQueue *serialQueue;
NSOperationQueue *mainQueue;

-(void)bigTaskAction{
    [serialQueue addOperationWithBlock: ^{
        [mainQueue addOperationWithBlock: ^{
            [self.myActivityIndicator startAnimating];
        }];
        int updateUIWhen = 1000;
        for(int i=0;i<10000;i++){
            NSString *newString = [NSString stringWithFormat:@"i = %i", i];
            NSLog(@"%@", newString);
            if(i == updateUIWhen){
                float f = (float)i/10000;
                NSNumber *percentDone = [NSNumber numberWithFloat:f];

                [mainQueue addOperationWithBlock: ^{
                    [self.myProgressView setProgress:[percentDone floatValue]
                                            animated:YES];
                }];

                updateUIWhen = updateUIWhen + 1000;
            }
        }

        [mainQueue addOperationWithBlock: ^{
            [self.myProgressView setProgress:1.0
                                    animated:YES];
            [self.myActivityIndicator stopAnimating];
        }];
    }];
}

- (void)viewDidLoad{
    [super viewDidLoad];

    // 创建操作队列
    mainQueue = [NSOperationQueue mainQueue];
    serialQueue = [[NSOperationQueue alloc] init];
    serialQueue.maxConcurrentOperationCount = 1;

    // 创建按钮
    self.myButton = [UIButton buttonWithType:UIButtonTypeRoundedRect];
    self.myButton.frame = CGRectMake(20, 403, 280, 37);
    [self.myButton addTarget:self
                      action:@selector(bigTaskAction)
            forControlEvents:UIControlEventTouchUpInside];
    [self.myButton setTitle:@"执行长时间任务"
                   forState:UIControlStateNormal];
    [self.view addSubview:self.myButton];

    // 创建活动指示器
    self.myActivityIndicator = [[UIActivityIndicatorView alloc] init];
    self.myActivityIndicator.frame = CGRectMake(142, 211, 37, 37);
    self.myActivityIndicator.activityIndicatorViewStyle = UIActivityIndicatorViewStyleWhiteLarge;
    self.myActivityIndicator.hidesWhenStopped = NO;
    [self.view addSubview:self.myActivityIndicator];

    // 创建进度条
    self.myProgressView = [[UIProgressView alloc] init];
    self.myProgressView.frame = CGRectMake(20, 20, 280, 9);
    [self.view addSubview:self.myProgressView];
}

@end
```

#### 使用方式

运行该应用程序。在 iOS 模拟器中，您会看到一个应用，其中包含一个按钮和一个空的进度视图。视图中央还会看到一个白色的活动指示器。触摸按钮两次，即可在两个后台线程中启动 `bigTask` 运行。

随着 `bigTask` 的运行，您应该会看到进度视图以 10% 的增量逐渐填满，直到任务完成。此过程将根据您触摸按钮的次数重复进行。进度视图不应来回反复跳动。

## 第 7 章

### 使用 Web 内容

本章介绍如何使用 Objective-C 处理 Web 内容。

本章的实践指南将向您展示如何：

*   使用 `NSURL` 下载文件
*   使用 XML 和 JSON 的 Web 服务
*   解析 XML 和 JSON 数据
*   使用 `NSURLConnection` 异步使用 Web 内容

## 7.1 下载文件

### 问题

您希望从互联网下载一个文件。

### 解决方案

使用 `NSURL` 指定文件的 URL，然后使用 `NSData` 将该文件的内容下载到您的文件系统中。

**注意：** URL 代表统一资源定位符。URL 是一个字符串，用于指定互联网资源的位置。`NSURL` 是 Foundation 框架中的一个类，可让您在 Objective-C 中使用 URL。

### 工作原理

要实现此解决方案，您必须有一个可供下载的互联网文件。我在我的博客上发布了一个文本文件用于此示例。该文件的 URL 是：

`http://howtomakeiphoneapps.com/wp-content/uploads/2012/03/objective-c-recipes-example-file.txt`

此过程的第一步要求您使用要下载的资源的 URL 创建一个新的 `NSURL` 对象。使用刚刚提供的 URL，如下所示：

```objective-c
NSURL *remoteTextFileURL = [NSURL URLWithString:@"http://howtomakeiphoneapps.com/wp-content/uploads/2012/03/objective-c-recipes-example-file.txt"];
```

接下来，创建一个新的 `NSData` 对象，并使用 `NSURL` 对象的内容初始化它。

```objective-c
NSData *remoteTextFileData = [NSData dataWithContentsOfURL:remoteTextFileURL];
```

您可以直接在 Objective-C 程序中使用 `NSData` 对象（有关使用 `NSData` 的示例，请参阅实践指南 4.11），或者像这样将其保存到文件系统中：

```objective-c
[remoteTextFileData writeToFile:@"/Users/Shared/objective-c-recipes-example-file.txt"
                     atomically:YES];
```

代码请参见清单 7-1。

#### 代码

**清单 7-1.** *main.m*

```objective-c
#import <Foundation/Foundation.h>
int main (int argc, const char * argv[]){
    @autoreleasepool {
        NSURL *remoteTextFileURL = [NSURL URLWithString:@"http://howtomakeiphoneapps.com/wp-content/uploads/2012/03/objective-c-recipes-example-file.txt"];
        NSData *remoteTextFileData = [NSData dataWithContentsOfURL:remoteTextFileURL];
        [remoteTextFileData writeToFile:@"/Users/Shared/objective-c-recipes-example-file.txt"
                             atomically:YES];
    }
    return 0;
}
```

#### 使用方式

要尝试本实践指南，请设置一个 Mac 命令行 Xcode 项目，并将 `main.m` 文件中的代码修改为清单 7-1 中的代码。构建并运行此命令行应用，将文件下载到您 Mac 上的以下位置：

`/Users/Shared/objective-c-recipes-example-file.txt`

找到并打开此文件，查看下载是否成功。

### 7.2 使用 XML 的 Web 服务



### 问题

你希望向应用程序中添加使用 XML 数据的网络服务。

**注意：** 互联网公司发布网络服务，以便开发者能够将这些服务集成到自己的应用程序中。网络服务的工作方式类似于网页浏览器：在浏览器中，你输入网址（即请求）、敲击回车键，然后等待互联网上远程计算机的响应。当响应返回时，浏览器会根据响应中的规则和内容为你呈现一个网页。网络服务的工作方式与此相同，区别在于由应用程序发送请求并获取响应。

互联网公司会尽力使用标准格式来制定网络服务的请求和响应，从而使应用程序更容易使用它们的服务。网络请求是字符串（如网址），而网络响应则是格式化为 XML 或 JSON 的字符串。有关 XML 和 JSON 的详细内容，将在后续章节讨论。

### 解决方案

根据网络服务发布者提供的文档，构造一个请求字符串。基于该请求字符串创建一个 `NSURL` 对象，并使用 `NSData` 从网络服务下载响应。然后使用 `NSXMLParser` 解析返回的 XML 文档。

### 工作原理

在本教程中，你将学习如何使用一家名为 bitly 的公司提供的网络服务。该公司发布了一项用于缩短长 URL 的网络服务。你只需按照指定格式向 bitly 网络服务发送包含长 URL 及你的 bitly 凭证的请求，bitly 便会返回一个包含缩短后 URL 的 XML 文件。

**注意：** 要跟随本教程操作，你需要先在 bitly 上创建一个（免费）账户，并获取自己的 API 密钥和 API 用户名。请访问 [`https://bit.ly`](https://bit.ly) 注册账户。

我将使用 Xcode 中基于命令行的 Mac 应用程序来演示本教程，但你也可以使用任何你喜欢的项目类型。由于 `NSXMLParser` 采用委托设计模式，你需要将代码放置在能够采纳协议并支持委托的类中。通过依次选择 **文件** ![Image](img/U002.jpg) **新建文件** ![Image](img/U002.jpg) **Objective-C 类**，为你的项目添加一个新类。将该类命名为 `LinkShortener`。

`LinkShortener` 的接口需要包含一个名为 `recorderString` 的 `NSMutableString` 的前向声明，该变量用于记录从网络服务获取的数据。此外，`LinkShortener` 还需要一个字符串变量来跟踪 XML 解析器当前在 XML 文件中的位置。将这个变量命名为 `currentElement`，并将 `currentElement` 和 `recorderString` 都设为私有属性。同时，你需要为当对象需要缩短 URL 时调用的函数声明一个前向声明；将该函数命名为 `getTheShortUrlVersionOfThisLongURL`。`LinkShortener` 的接口应如下所示：

```
#import <Foundation/Foundation.h>

@interface LinkShortener : NSObject{
    @private
    NSMutableString *recorderString;
    NSString *currentElement;
}

-(NSString *)getTheShortURLVersionOfThisLongURL:(NSString *)longURL;

@end
```



### XML 解析的工作原理

在继续之前，我们先讨论一下什么是 XML，以及 `NSXMLParser` 如何读取 XML 文档。XML 代表可扩展标记语言，用于存储和传输数据。XML 的工作原理是将数据包裹在开始标签和结束标签之间。开始标签是由字符 `<` 和 `>` 包围的字符。结束标签是由字符 `</` 和 `>` 包围的字符。标签和数据合在一起被称为一个元素。

例如，如果我有一个人物的 XML 数据类型，我可能会使用一个开始标签，比如 `<Person>`。结束标签看起来像 `</Person>`。中间的字符是数据。整体看起来是这样的：

`<Person>Matthew J. Campbell</Person>`

XML 标签旨在具有描述性，因此标签的含义显而易见。整个文档会有许多包含数据的标签，并且可以按层级结构排列。因此，你可能会有嵌套在其他标签数据中的标签数据，如下所示：

`<Person>`
`        <Name>Matthew J. Campbell</Name>`
`        <Gender>Male</Gender>`
`</Person>`

`NSXMLParser` 从头开始逐元素读取 XML 文档，直到到达文档末尾。如果 `NSXMLParser` 正在读取上面的 XML，它会从 `Person` 元素开始，然后移动到 `Name` 元素，接着是 `Gender` 元素。这种解析 XML 的方法称为 XML 简单 API (SAX)。

你将使用委托来解析从 bitly 获取的 XML 数据。当解析器查看文档中的每个元素时，它会向委托 `LinkShortener` 对象发送一条消息，让你有机会从每个元素中提取数据。

因此，`LinkShortener` 需要能够充当 `NSXMLParser` 的委托，这意味着你需要让 `LinkShortener` 遵循 `NSXMLParserDelegate` 协议。通过在 `NSObject` 父类之后直接包含协议名称来实现这一点。

```
#import <Foundation/Foundation.h>

@interface LinkShortener : NSObject <NSXMLParserDelegate>{
    @private
    NSMutableString *recorderString;
    NSString *currentElement;
}

-(NSString *)getTheShortURLVersionOfThisLongURL:(NSString *)longURL;

@end
```

既然你已经采用了这个协议，你需要至少实现这两个委托方法：`parser:didStartElement:namespaceURI:qualifiedName:attributes:` 和 `parser:foundCharacters:`。

由于你的应用程序只需要 XML 文件中一个元素的数据，因此仅使用这两个委托方法就够了。但是，如果你希望在解析器遇到 XML 数据中的结束标签时得到通知，你也可以视需要实现 `parser:didEndElement:namespaceURI:qualifiedName:` 方法。

打开 `LinkShortener.m` 来实现第一个委托方法。

```
- (void) parser:(NSXMLParser *)parser
didStartElement:(NSString *)elementName
   namespaceURI:(NSString *)namespaceURI
  qualifiedName:(NSString *)qName
     attributes:(NSDictionary *)attributeDict{

        currentElement = [elementName copy];
        if ([elementName isEqualToString:@"shortUrl"]) {
                recorderString = [[NSMutableString alloc] init];
        }
}
```

请记住，每次到达一个新的 XML 元素时，这个委托方法都会执行（大多数 XML 文件都包含大量元素）。因此，需要复制参数 `elementName` 并将其放入 `currentElement` 中。这样，当其他委托方法执行时，你就能跟踪当前正在处理的元素。

这段代码的另一个重要部分是 `if` 语句，它用于检查当前是否处于与 `shortUrl` 对应的元素中。bitly 正是在这个元素中放置缩短后的 URL 字符串。如果确实遇到了 `shortUrl` 元素，你将创建并初始化一个新的 `NSMutableString`，以便稍后记录在该元素中找到的内容。

现在你可以实现下一个委托方法。每当在文件的 XML 元素中遇到字符时，这个委托方法就会执行。你可以测试 XML 解析器是否处于 `shortUrl` 元素中。如果是，则将找到的字符追加到 `recorderString NSMutableString` 中。

```
- (void)parser:(NSXMLParser *)parser foundCharacters:(NSString *)string{
        if ([currentElement isEqualToString:@"shortUrl"])
                [recorderString appendString:string];

}
```

所有这些工作都是为了准备让 `LinkShortener` 接收 XML 数据。现在你可以准备请求并将其发送到网络服务器以下载响应。所有这些都发生在函数 `getTheShortUrlVersionOfThisLongURL:` 中，你可以开始在 `LinkShortener` 的实现文件中编写代码。

```
-(NSString *)getTheShortUrlVersionOfThisLongURL:(NSString *)longURL{

}
```

在这个函数中，你要做的第一件事是构造请求字符串。为此，你需要根据 bitly 要求的请求字符串，结合 `longURL` 参数以及你自己的 API 密钥和 API 登录名来构建一个字符串。

```
#warning 请从 https://bitly.com/a/your_api_key 获取你的 API 登录名，并在运行前将其放在此处 ![Image](img/U001.jpg)
NSString *APILogin = @"[你的 API 登录名]";
#warning 请从 https://bitly.com/a/your_api_key 获取你的 API 密钥，并在运行前将其放在此处 ![Image](img/U001.jpg)
NSString *APIKey = @"[你的 API 密钥]";

NSString *requestString = [[NSString alloc] initWithFormat: ![Image](img/U001.jpg)
@"http://api.bit.ly/shorten?version=2.0.1&longUrl=%@&login=%@&apiKey=%@&format=xml", ![Image](img/U001.jpg)
 longURL, APILogin, APIKey];
```

我在示例代码中包含了警告，以便你记住在此处填入你自己的凭证。此外，如果你仔细查看请求字符串的第一部分，你会看到一个格式参数，其返回值为 xml (`format=xml`)。这就是你告诉 bitly 你希望响应以 XML 格式返回的方式。

接下来，你需要一个 `NSURL` 对象，你可以基于请求字符串来创建它。

```
NSURL *requestURL = [NSURL URLWithString:requestString];
```

要下载数据，请像在食谱 7.1 中那样使用 `NSData`。

```
recorderString = nil;
NSData *responseData = [NSData dataWithContentsOfURL:requestURL];
```

这里是一个将 `recorderString` 设置为 nil 的好地方，以防此函数在该对象的生命周期内已被使用过。现在你已经下载了数据，你可以使用 `NSXMLParser` 来遍历 XML 并提取 `shortUrl` 元素中包含的内容。

为此，使用下载的 `NSData` 对象实例化一个新的 `NSXMLParser`，将此对象的委托设置为 `self`，然后向 `NSXMLParser` 对象发送解析消息。

```
NSXMLParser *xmlParser = [[NSXMLParser alloc] initWithData:responseData];
xmlParser.delegate = self;
[xmlParser parse];
```

此时，XML 解析器会查看数据，并使用之前编码的委托方法提取有意义的内容。XML 解析器找到的所有内容都会被记录在 `recorderString` 中。一旦 XML 解析器完成工作，你就可以将结果返回给调用者。

```
if(recorderString)
     return [recorderString copy];
else
     return nil;
```

你可以在此处使用 `if` 语句：如果找到了任何数据，则发送 `recorderString` 的一个副本。

最后，要从程序的其他部分使用该函数，你需要导入 `LinkShortener` 头文件，实例化一个 `LinkShortener` 对象，然后使用该函数并传入一个长 URL。以下是在 `main.m` 中实现的方法：

```
#import <Foundation/Foundation.h>
#import "LinkShortener.h"

int main(int argc, const char * argv[]){
    @autoreleasepool {

        NSString *longURL = @"http://howtomakeiphoneapps.com/how-to- ![Image](img/U001.jpg)
asynchronously-add-web-content-to-uitableview-in-ios/1732/";

        LinkShortener *linkShortener = [[LinkShortener alloc] init];

        NSString *shortURL = [linkShortener getTheShortURLVersionOfThisLongURL:longURL];

        NSLog(@"shortURL = %@", shortURL);

    }
    return 0;
}
```

代码请参见列表 7-2 到 7-4。



### 代码

**列表 7-2** *LinkShortener.h*

```objectivec
#import <Foundation/Foundation.h>

@interface LinkShortener : NSObject<NSXMLParserDelegate>{
    @private
    NSMutableString *recorderString;
    NSString *currentElement;
}

-(NSString *)getTheShortURLVersionOfThisLongURL:(NSString *)longURL;

@end
```

**列表 7-3** *LinkShortener.m*

```objectivec
#import "LinkShortener.h"

@implementation LinkShortener

-(NSString *)getTheShortURLVersionOfThisLongURL:(NSString *)longURL{

    #warning 请从 https://bitly.com/ 获取你的 API 登录名并在运行前填入此处
    NSString *APILogin = @"[您的 API 登录名]";
    #warning 请从 https://bitly.com/ 获取你的 API 密钥并在运行前填入此处
    NSString *APIKey = @"[您的 API 密钥]";

    NSString *requestString = [[NSString alloc] initWithFormat:
@"http://api.bit.ly/shorten?version=2.0.1
&longUrl=%@&login=%@&apiKey=%@&format=xml",
longURL, APILogin, APIKey];

    NSURL *requestURL = [NSURL URLWithString:requestString];
    recorderString = nil;
    NSData *responseData = [NSData dataWithContentsOfURL:requestURL];
    NSXMLParser *xmlParser = [[NSXMLParser alloc] initWithData:responseData];
    xmlParser.delegate = self;
    [xmlParser parse];

    if(recorderString)
        return [recorderString copy];
    else
        return nil;;
}

- (void) parser:(NSXMLParser *)parser
didStartElement:(NSString *)elementName
   namespaceURI:(NSString *)namespaceURI
  qualifiedName:(NSString *)qName
     attributes:(NSDictionary *)attributeDict{

        currentElement = [elementName copy];
        if ([elementName isEqualToString:@"shortUrl"]) {
                recorderString = [[NSMutableString alloc] init];
        }
}

- (void)parser:(NSXMLParser *)parser foundCharacters:(NSString *)string{
        if ([currentElement isEqualToString:@"shortUrl"])
                [recorderString appendString:string];

}

@end
```

**列表 7-4** *main.m*

```objectivec
#import <Foundation/Foundation.h>
#import "LinkShortener.h"

int main(int argc, const char * argv[]){
    @autoreleasepool {

        NSString *longURL = @"http://howtomakeiphoneapps.com/
how-to-asynchronously-add-web-content-to-uitableview-in-ios/1732/";

        LinkShortener *linkShortener = [[LinkShortener alloc] init];

        NSString *shortURL = [linkShortener getTheShortURLVersionOfThisLongURL:longURL];

        NSLog(@"shortURL = %@", shortURL);

    }
    return 0;
}
```

### 用法

要使用 URL 缩短功能，请将头文件导入到需要使用该功能的类中。然后，实例化一个来自`LinkShortener`类的`LinkShortener`对象。最后，要使用该功能，请向`LinkShortener`对象发送消息`getTheShortURLVersionOfThisLongURL:`，并将长 URL 作为参数传入。如果网络请求成功，该函数将返回缩短后的 URL；如果请求不成功，则返回`nil`。以下是你应该会在控制台日志中看到的内容：

`shortURL = http://bit.ly/yFmJFh`

**注意：** 你从 bitly 收到的缩短 URL 可能与我测试此代码时收到的并不完全一样。

## 7.3 使用 JSON 消费 Web 服务

### 问题

你希望将使用 JSON 数据的 Web 服务添加到你的应用中。

**注意：** JSON 是 XML 的一种替代方案，许多互联网公司在实现 Web 服务时都使用它。JSON 代表 JavaScript 对象表示法，用于数据存储和传输。实现为 REST（表述性状态转移）Web 服务的 Web 服务会同时提供 XML 和 JSON 响应数据。其他类型的 Web 服务可能只提供其中一种。

### 解决方案

与配方 7.2 类似，根据 Web 服务发布者提供的文档构造一个请求字符串。基于请求字符串创建一个`NSURL`对象，并使用`NSData`下载来自 Web 服务的响应。使用`NSJSONSerialization`来解析你收到的 JSON 数据。

**注意：** `NSJSONSerialization`从 Mac OSX 10.7 和 iOS 5.0 开始可用。

### 工作原理

本配方使用与配方 7.2 相同的 bitly Web 服务，并且请求和下载结果的过程相同。然而，`NSJSONSerialization`不使用委托，因此你不需要添加新文件或类来使用`NSJSONSerialization`进行 JSON 解析。

由于你不需要为此创建一个单独的类，你可以将构造请求字符串所需的代码放在应用中需要使用该 Web 服务的任何地方。如果你继续使用命令行 Mac 应用，这段代码可以直接放入`main.m`文件中。以下是构造请求字符串的方法：

```objectivec
NSString *longURL = @"http://howtomakeiphoneapps.com/
how-to-asynchronously-add-web-content-to-uitableview-in-ios/1732/";

#warning 请从 https://bitly.com/ 获取你的 API 登录名并在运行前填入此处
NSString *APILogin = @"[您的 API 登录名]";
#warning 请从 https://bitly.com/ 获取你的 API 密钥并在运行前填入此处
NSString *APIKey = @"[您的 API 密钥]";

NSString *requestString = [[NSString alloc] initWithFormat:
@"http://api.bit.ly/shorten?version=2.0.1
&longUrl=%@&login=%@&apiKey=%@&format=json", longURL, APILogin, APIKey];
```

然后，你可以使用`NSURL`和`NSData`基于此请求字符串获取响应。

```objectivec
NSURL *requestURL = [NSURL URLWithString:requestString];

NSData *responseData = [NSData dataWithContentsOfURL:requestURL];
```

#### JSON 解析

接下来你将看到如何解析 JSON。与 XML 使用的标记数据方案不同，JSON 基于两种结构组织内容：名称-值对的集合和值的有序列表。JSON 的名称-值对集合遵循与 Objective-C `NSDictionary`相同的模式，而 JSON 值的有序列表则对应于 Objective-C `NSArray`。

如果你查看一个 JSON 文件，你会看到这些类型的结构由花括号组织，而不是 XML 文件中的标记数据。例如，配方 7.2 中描述的`Person`元素的 JSON 版本如下所示：

`{"Person":"Matthew J. Campbell","Gender":"Male"}`

由于 JSON 数据是以这种方式键控的，并且字典和数组结构与编程语言的映射非常好，因此 JSON 通常更容易解析。稍后你将看到，你有一个 Foundation 函数可用，它只需将你的 JSON 响应转换为一个`NSDictionary`，里面包含可直接使用的 JSON 内容。

你首先需要一个`NSError`对象，你将其与必须发送给`NSJSONSerialization`的`JSONObjectWithData:options:error:`消息一起传递。

```objectivec
NSError *error = nil;
NSDictionary *bitlyJSON = [NSJSONSerialization JSONObjectWithData:responseData
                                                          options:0
                                                            error:&error];
```

此函数的结果被赋值给一个`NSDictionary`，该字典保存了 JSON 数据的内容。要获取你需要的内容，只需访问字典中的各种对象。这通常需要你引用嵌套的字典、数组和对象。你需要检查响应数据以明确具体需要什么。以下是从响应数据中获取 bitly 短 URL 的方法：

```objectivec
if(!error){
    NSDictionary *results = [bitlyJSON objectForKey:@"results"];
    NSDictionary *resultsForLongURL = [results objectForKey:longURL];
    NSString *shortURL = [resultsForLongURL objectForKey:@"shortUrl"];
    NSLog(@"shortURL = %@", shortURL);
}
else{
    NSLog(@"解析 JSON 时出现错误");
}
```

相关代码请参见列表 7-5。



### 代码

**代码清单 7-5.** *main.m*

```
#import <Foundation/Foundation.h>

int main(int argc, const char * argv[]){
    @autoreleasepool {

        NSString *longURL = @"http://howtomakeiphoneapps.com/how-to-asynchronously-add-web-content-to-uitableview-in-ios/1732/";

#warning Get your API Login from https://bitly.com/ and put it here before running
         NSString *APILogin = @"[YOUR API LOGIN]";
#warning Get your API key from https://bitly.com/ and put it here before running
         NSString *APIKey = @"[YOUR API KEY]";

        NSString *requestString = [[NSString alloc] initWithFormat:
@"http://api.bit.ly/shorten?version=2.0.1&longUrl=%@&login=%@&apiKey=%@&format=json",
longURL, APILogin, APIKey];

        NSURL *requestURL = [NSURL URLWithString:requestString];
        NSData *responseData = [NSData dataWithContentsOfURL:requestURL];

        NSError *error = nil;
        NSDictionary *bitlyJSON = [NSJSONSerialization JSONObjectWithData:responseData
                                                                  options:0
                                                                    error:&error];
        if(!error){
            NSDictionary *results = [bitlyJSON objectForKey:@"results"];
            NSDictionary *resultsForLongURL = [results objectForKey:longURL];
            NSString *shortURL = [resultsForLongURL objectForKey:@"shortUrl"];
            NSLog(@"shortURL = %@", shortURL);
        }
        else{
            NSLog(@"There was an error parsing the JSON");
        }

    }
    return 0;
}
```

### 使用方法

你可以在应用程序的任何区域使用这段代码。如果你是在 Mac 命令行应用程序中测试，可以直接将代码包含在 `main.m` 文件中，但需要先从 bitly 获取 API 登录名和 API 密钥。查看控制台日志窗口以查看 Web 服务请求的结果。你应该会看到类似这样的输出：

```
shortURL = http://bit.ly/yFmJFh
```

**注意：** JSON 所需的步骤远比 XML 少，当你在处理 Web 服务（且可用时）时，它很可能是你的首选。但请注意，JSON 可能并非始终可用，并且你必须使用 Mac OSX 10.7 或 iOS 5 或更高版本才能使用 JSON。

## 7.4 异步消费 Web 内容

### 问题

你希望以后台进程的方式消费 Web 内容，这样网络活动不会影响你的用户界面。

### 解决方案

当你希望异步处理网络请求，或者需要对网络连接和 Web 请求过程进行更多控制时，请使用 `NSURLConnection` 和 `NSURLRequest`。

### 工作原理

首先，你需要一个发送到 Web 服务器的请求字符串。你可以使用 Web 服务的请求字符串（如你在配方 7.2 和 7.3 中所做的那样），甚至可以输入一个网页 URL。在本例中，我将使用我博客的 RSS feed，因为我知道它提供了一些可下载的 XML 数据。

**注意：** RSS 代表真正简易聚合（Really Simple Syndication）。RSS 用于发布博客文章和播客等内容。RSS 文件通常是基于 XML 的大型文件，但也遵循有助于内容发布的其他规范。由于大多数博客软件都内置了 RSS 功能，因此为你的应用程序添加 RSS feed 是向用户发布信息的一种简单方法。

在本配方中，我将使用一个 Mac Cocoa 应用程序。你也可以在此处使用 iOS 应用程序，但如果尝试使用 Mac 命令行应用程序，将会遇到问题。这是因为与 `NSURLConnection` 一起使用的异步方法的执行时间可能长于 Mac 命令行应用程序的生命周期，因此你可能永远无法在控制台日志中看到结果。

代码位于 Mac Cocoa 应用程序的 `AppDelegate.m` 文件中，具体在 `applicationDidFinishLaunching:` 委托方法内。首先你需要请求字符串（本例中是我博客的 RSS feed）。

```
NSString *requestString = @"http://www.howtomakeiphoneapps.com/feed/";
```

然后，你可以基于该请求字符串构建一个 `NSURL` 对象。

```
NSURL *requestURL = [NSURL URLWithString:requestString];
```

使用 `requestURL` 对象实例化一个新的 `NSURLRequest`。

```
NSURLRequest *request = [[NSURLRequest alloc] initWithURL:requestURL
                                              cachePolicy:NSURLRequestReloadIgnoringLocalCacheData
                                          timeoutInterval:10];
```

你还可以指定请求如何处理缓存以及超时间隔。接下来，你需要设置一个 `NSOperationQueue`，它将与 `NSURLConnection` 一起用于执行 Web 请求。

```
NSOperationQueue *backgroundQueue =[[NSOperationQueue alloc] init];
```

最后，使用一个类方法来异步执行你的 Web 请求。你需要三个参数：`NSURLRequest` 对象、`NSOperationQueue` 对象和一个代码块。该代码块让你有机会告诉 `NSURLConnection` 在检索到数据后要执行什么代码。

```
[NSURLConnection sendAsynchronousRequest:request
                                   queue:backgroundQueue
                       completionHandler:^(NSURLResponse *response, NSData *data, NSError *error) {

    if(!error){
         NSString *requestResults = [[NSString alloc] initWithData:data
                                                          encoding:NSStringEncodingConversionAllowLossy];

         NSLog(@"requestResults=%@", requestResults);
    }
    else
         NSLog(@"error=%@", error);

                }];
```

**注意：** 此功能仅适用于 Mac OSX 10.7 和 iOS 5.0 及更高版本。

仔细查看 `completionHandler` 块，了解如何处理返回的 `NSData` 对象。通常在处理数据之前，你会先测试 `NSError` 对象以确认一切正常。这里我们只是将整个 RSS feed 输出到控制台日志，但如果你想要处理 RSS feed，可以设置一个 XML 解析器，如配方 7.2 中所述。相关代码请参见代码清单 7-6 至 7-8。



#### 代码

**清单 7-6.** *main.m*

```
#import <Cocoa/Cocoa.h>
int main(int argc, char *argv[]) {
    return NSApplicationMain(argc, (const char **)argv);
}
```

**清单 7-7.** *AppDelegate.h*

```
#import <Cocoa/Cocoa.h>
@interface AppDelegate : NSObject <NSApplicationDelegate>
@property (assign) IBOutlet NSWindow *window;
@end
```

**清单 7-8.** *AppDelegate.m*

```
#import "AppDelegate.h"
@implementation AppDelegate
@synthesize window = _window;
- (void)applicationDidFinishLaunching:(NSNotification *)aNotification {
    NSString *requestString = @"http://www.howtomakeiphoneapps.com/feed/";
    NSURL *requestURL = [NSURL URLWithString:requestString];
    NSURLRequest *request = [[NSURLRequest alloc] initWithURL:requestURL cachePolicy:NSURLRequestReloadIgnoringLocalCacheData timeoutInterval:10];
    NSOperationQueue *backgroundQueue = [[NSOperationQueue alloc] init];
    [NSURLConnection sendAsynchronousRequest:request queue:backgroundQueue completionHandler:^(NSURLResponse *response, NSData *data, NSError *error) {
        if (!error) {
            NSString *requestResults = [[NSString alloc] initWithData:data encoding:NSStringEncodingConversionAllowLossy];
            NSLog(@"requestResults=%@", requestResults);
        } else {
            NSLog(@"error=%@", error);
        }
    }];
}
@end
```

#### 用法

要尝试本实例，请在 Mac 或 iOS 应用程序的应用委托中包含此代码。检查日志以查看从网络下载的数据。要测试错误处理，请断开 Mac 与网络的连接，并检查日志以查看错误对象报告的内容。如果网络请求成功，您应该在控制台日志中看到类似以下内容：

```
requestResults=<?xml version="1.0" encoding="UTF-8"?>
<rss version="2.0" xmlns:atom="http://www.w3.org/2005/Atom">
<channel>
    <title>How to Make iPhone Apps</title>
    <atom:link href="http://howtomakeiphoneapps.com/feed/" rel="self" type="application/rss+xml" />
...
```

这段代码可以放在应用程序中任何需要消费网络内容的地方，但请注意不要干扰或阻塞其他进程，例如用户界面。

