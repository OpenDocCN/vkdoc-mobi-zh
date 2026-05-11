# 异步处理

本章介绍如何在不中断应用程序主线程的情况下，向应用中添加耗时任务。Objective-C 提供了多种解决此问题的方案，本章将重点讲解其中最重要的三种：`NSThread`、Grand Central Dispatch 和 `NSOperationQueue`。

本章的实践指南将向你展示如何：

-   为后台处理创建新线程
-   向主线程发送消息以更新用户界面
-   锁定线程以保证数据结构同步
-   使用 Grand Central Dispatch（GCD）实现异步处理
-   使用操作队列，以更面向对象的方式实现异步处理
-   使用串行队列保护数据结构，而无需锁定线程，从而提高多线程性能

**注意：** 本章的主题可能较为复杂，因为多线程对软件开发人员而言是一个难题。支撑多线程的底层技术在过去几年中发展迅速，因此你将看到几种可用的替代方案，用以解决异步处理所涉及的问题。

## 6.1 在新线程中运行进程

### 问题

你的应用需要执行一个会耗费大量时间的任务，但你希望用户界面在此期间保持响应，且不受新操作的影响。

### 解决方案

将耗时任务放入一个方法中，然后使用 `NSThread` 创建一个与主线程分离的新线程，让新操作在其中执行。

### 工作原理

我们将正在执行的可执行程序（如应用程序）称为*进程*，此时它们由操作系统（此处指 iOS 或 OSX）执行。进程由多个线程构成，这些线程可同时执行操作。这些操作可能在不同的处理器上同时发生，也可能使用分时策略（每个线程轮流使用计算机处理器）在同一个处理器上发生。

所有程序都至少有一个主线程，称为*主线程*。应用使用主线程来管理用户界面，但也可能存在其他线程同时执行任务，这些任务与用户界面无直接关系，或者通常不属于主线程的一部分。

**注意：** 要跟随本实践指南进行操作，你需要一个包含按钮和活动指示器的“单视图 iPhone 应用”。关于如何使用 iPhone 应用和用户控件的一般信息，请参见实践指南 1.12 和实践指南 1.13。有关设置用户界面的详细信息，请参见此处完整的代码清单。

要在 Objective-C 中创建新线程，你可以使用 `NSThread` 类，但首先你需要将所有要在独立线程上运行的代码放入一个方法中。

```
-(void) bigTask{
    for(int i=0;i<40000;i++){
        NSString *newString = [NSString stringWithFormat:@"i = %i", i];
        NSLog(@"%@", newString);
    }
    [self.myActivityIndicator stopAnimating];
}
```

这个名为 `bigTask` 的方法会循环 40000 次。每次循环中，都会构建一个新字符串并写入日志。完成所有循环后，它会向活动指示器发送消息，使其停止旋转，表明任务已结束。

#### 自动释放池

在继续之前，你还需要对 `bigTask` 做一件事。这涉及内存管理，第 8 章会更详细地介绍，并且它在处理线程时非常重要。你需要将 `bigTask` 中的代码放入一个自动释放池中。自动释放池允许 Objective-C 使用内存资源，并在需要时释放这些资源。每个线程都需要一个 `autoreleasepool`，否则你的应用会出现内存泄漏。

要往 `bigTask` 中添加 `autoreleasepool`，请将 `bigTask` 的所有代码包含在一个以 `@autorelease` 关键字开头的代码块中。

```
-(void) bigTask{
@autoreleasepool {
        for(int i=0;i<40000;i++){
            NSString *newString = [NSString stringWithFormat:@"i = %i", i];
            NSLog(@"%@", newString);
        }
        [self.myActivityIndicator stopAnimating];
}
}
```

你可以简单地将 `bigTask` 指定为某个按钮触控事件的动作，从而直接执行这个方法。但是，如果这样做，你的用户界面将在任务完成前完全无响应。

执行 `bigTask` 的一个更好的方法是创建一个新方法，用于设置用户界面，然后再执行 `bigTask`。你最终会将此方法分配给一个按钮动作。将其命名为 `bitTaskAction`，并像这样开始方法：

```
-(void)bigTaskAction{
    [self.myActivityIndicator startAnimating];
}
```

到目前为止，`bigTaskAction` 只是通过向活动指示器发送消息使其开始旋转，从而设置好用户界面。要执行这个大任务，请使用 `NSThread` 类方法 `detachNewThreadSelector:toTarget:withObject:`。

```
-(void)bigTaskAction{
    [self.myActivityIndicator startAnimating];

    [NSThread detachNewThreadSelector:@selector(bigTask)
                             toTarget:self
                           withObject:nil];

}
```

这将创建一个新线程，并执行你通过 `@selector` 关键字指定的方法中的代码。你还可以指定目标对象，该对象必须是第一个参数中方法所在的对象。如果该方法接受一个参数，那么你可以通过该方法的最后一个参数传递一个对象。你的方法不需要参数，因此你只需传递 `nil`。

要使其在应用中生效，请将创建线程的方法作为一个动作分配给一个用户控件。如果你将其添加到 iPhone 应用中，请确保将 `bigTaskAction` 添加到 `action` 方法中。

```
[self.myButton addTarget:self
                   action:@selector(bigTaskAction)
         forControlEvents:UIControlEventTouchUpInside];
```

当用户像这样触碰应用中的按钮时，大任务将在其自身的线程中执行，而不会中断用户界面。相关代码请参见代码清单 6-1 至 6-4。



### 代码

**列表 6-1.** `AppDelegate.h`

```objc
#import <UIKit/UIKit.h>

@class ViewController;

@interface AppDelegate : UIResponder <UIApplicationDelegate>

@property (strong, nonatomic) UIWindow *window;
@property (strong, nonatomic) ViewController *viewController;

@end
```

**列表 6-2.** `AppDelegate.m`

```objc
#import "AppDelegate.h"
#import "ViewController.h"

@implementation AppDelegate

@synthesize window = _window;
@synthesize viewController = _viewController;

- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:  ![Image](img/U001.jpg)
(NSDictionary *)launchOptions{
    self.window = [[UIWindow alloc] initWithFrame:[[UIScreen mainScreen] bounds]];
    // 应用程序启动后的自定义覆盖点。
    self.viewController = [[ViewController alloc] initWithNibName:@"ViewController"
                                                           bundle:nil];
    self.window.rootViewController = self.viewController;
    [self.window makeKeyAndVisible];

    return YES;
}

@end
```

**列表 6-3.** `ViewController.h`

```objc
#import <UIKit/UIKit.h>

@interface ViewController : UIViewController

@property(strong) UIButton *myButton;
@property(strong) UIActivityIndicatorView *myActivityIndicator;

@end
```

**列表 6-4.** `ViewController.m`

```objc
#import "ViewController.h"

@implementation ViewController
@synthesize myButton, myActivityIndicator;

-(void) bigTask{
    @autoreleasepool {
        for(int i=0;i<40000;i++){
            NSString *newString = [NSString stringWithFormat:@"i = %i", i];
            NSLog(@"%@", newString);
        }
        [self.myActivityIndicator stopAnimating];
    }
}
/*
 //不使用新线程执行任务（观察 UI 以了解其工作方式）
 -(void)bigTaskAction{
 [self.myActivityIndicator startAnimating];
 [self bigTask];
 }
 */

//通过分离新线程执行任务（观察 UI 以了解其工作方式）
-(void)bigTaskAction{
    [self.myActivityIndicator startAnimating];

    [NSThread detachNewThreadSelector:@selector(bigTask)
                             toTarget:self
                           withObject:nil];

}

- (void)viewDidLoad{
    [super viewDidLoad];

    //创建按钮
    self.myButton = [UIButton buttonWithType:UIButtonTypeRoundedRect];
    self.myButton.frame = CGRectMake(20, 403, 280, 37);
    [self.myButton addTarget:self
                      action:@selector(bigTaskAction)
            forControlEvents:UIControlEventTouchUpInside];
    [self.myButton setTitle:@"执行长任务"
                   forState:UIControlStateNormal];

    [self.view addSubview:self.myButton];

    //创建活动指示器
    self.myActivityIndicator = [[UIActivityIndicatorView alloc] init];
    self.myActivityIndicator.frame = CGRectMake(142, 211, 37, 37);
self.myActivityIndicator.activityIndicatorViewStyle =  ![Image](img/U001.jpg)
UIActivityIndicatorViewStyleWhiteLarge;
    self.myActivityIndicator.hidesWhenStopped = NO;

    [self.view addSubview:self.myActivityIndicator];

}

@end
```

### 使用说明

要使用此代码，首先在 Xcode 中创建一个基于单视图（Single View）的应用程序，就像你在食谱 1.12 中所做的那样。然后，将列表 6-4 中的代码添加到你自己的 `ViewController` 类中。

运行应用程序。在 iOS 模拟器中，你应该会看到一个应用程序，其中包含一个按钮和一个位于视图中央的白色活动指示器。触摸按钮并检查日志。你应该会看到 `for` 循环正在执行，并且活动指示器在 iOS 应用视图中旋转。当 `bigTask` 完成后，活动指示器将停止旋转。

为了对比不使用新线程时会发生什么，请注释掉列表 6-4 中的 `bigTaskAction` 方法，然后取消注释备选的 `bigTask` 方法。如果你不确定选择哪个方法，请参阅列表 6-4 中的注释。

使用备选的 `bigTaskAction` 运行应用程序，并观察主线程如何被锁定。你将无法使用界面，但 `for` 循环会继续执行，并且你会在日志中看到结果。

### 6.2 在主线程和后台线程之间通信

### 问题

当你尝试从后台线程更新用户界面时，只有在后台线程处理完成后才会发生更改，这使得进度条等组件变得毫无用处。你希望向用户更新后台任务的进度。

### 解决方案

使用 `NSObject` 方法 `performSelectorOnMainThread:withObject:waitUntilDone:` 在主线程上执行方法。你需要将更新用户界面（或以其他方式与主线程交互）的代码放在其自己的方法中。


### 工作原理

本方法延续了你在方案 6.1 中开始的工作。不过，你将在应用程序中添加一个 `UIProgressView` 对象属性。

`UIProgressView` 是 `UIKit` 类，可用于创建从 0% 到 100% 显示任务进度的用户界面元素，外观呈现为一条在屏幕上移动的蓝色进度条。你将使用这个进度视图向用户展示 `bigTask` 的完成进度。

以下是你在 `ViewController` 类中放置 `UIProgressView` 对象的位置。该属性需放在头文件中。

```
#import <UIKit/UIKit.h>

@interface ViewController : UIViewController

@property(strong) UIButton *myButton;
@property(strong) UIActivityIndicatorView *myActivityIndicator;
@property(strong) UIProgressView *myProgressView;

@end
```

`myProgressView` 属性也必须添加到 `ViewController` 实现文件中的 `@synthesize` 语句里。

```
#import "ViewController.h"

@implementation ViewController
@synthesize myButton, myActivityIndicator, myProgressView;

...

@end
```

请注意，此处未完整重现整个 `ViewController` 实现。请查看列表 6-7 以了解 `ViewController` 中代码的完整上下文。

现在，创建一个更新进度视图的方法。该方法必须与你放置后台线程代码的方法（在本示例应用程序中为 `bigTask`）分开。

将你的方法命名为 `updateProgressViewWithPercentage:`。参数是一个 `NSNumber` 对象，表示任务完成的百分比。

```
-(void)updateProgressViewWithPercentage:(NSNumber *)percentDone{

}
```

确保将此方法添加到 `ViewController` 实现中，但在 `bigTask` 方法之前。或者，如果你更愿意将实际方法放在 `bigTask` 之后，也可以将 `updateProgressViewWithPercentage:` 作为前向声明添加到 `ViewController` 头文件中。

接下来，将更新进度视图的代码添加到新方法中。你只需向进度视图发送一条包含更新信息的消息。

```
-(void)updateProgressViewWithPercentage:(NSNumber *)percentDone{
    [self.myProgressView setProgress:[percentDone floatValue]
                            animated:YES];
}
```

如你所见，你将进度视图的状态设置为从 `percentDone` 参数获取的百分比。`setProgress` 实际上期望的是一个基本的 `float` 类型，这意味着你必须使用 `NSNumber floatValue` 方法来返回 `NSNumber` 对象的 `float` 值版本。第二个参数允许你控制进度视图是否以动画方式更新。

这是你将从后台线程调用的方法。与方案 6.1 一样，你有一个名为 `bigTask` 的方法，其中包含后台线程代码。这段代码有一个细微的变化：计数的最大值从 40,000 减少到 10,000，因为在测试这段代码时，计数到 40,000 耗时过长。以下是更新后的 `bigTask`：

```
-(void) bigTask{
    @autoreleasepool {
        for(int i=0;i<10000;i++){
            NSString *newString = [NSString stringWithFormat:@"i = %i", i];
            NSLog(@"%@", newString);
        }
        [self.myActivityIndicator stopAnimating];
    }
}
```

同样，`bigTask` 的代码位于 `ViewController` 实现中。在你指示用户界面在主线程上更新之前，需要先准备一些条件。由于你不希望界面在线程中的每次操作时都更新，以下是一些决定何时更新界面的规则。

你希望每当任务总完成进度达到 10% 时更新界面。由于你的计数范围是 10,000，因此可以简单地在每计数 1,000 时更新界面。你需要一个整数来帮助跟踪这一点，现在添加它，并将初始值赋为 `1000`。

```
-(void) bigTask{
    @autoreleasepool {
        int updateUIWhen = 1000;
        for(int i=0;i<10000;i++){
            NSString *newString = [NSString stringWithFormat:@"i = %i", i];
            NSLog(@"%@", newString);
        }
        [self.myActivityIndicator stopAnimating];
    }
}
```

接下来，你需要检测计数何时达到 `updateUIWhen` 中的值。当达到 1,000 时，你还需要增加 `updateUIWhen`。以下是一种实现方式：

```
-(void) bigTask{
    @autoreleasepool {
        int updateUIWhen = 1000;
        for(int i=0;i<10000;i++){
            NSString *newString = [NSString stringWithFormat:@"i = %i", i];
            NSLog(@"%@", newString);
            if(i == updateUIWhen){
                updateUIWhen = updateUIWhen + 1000;
            }
        }
        [self.myActivityIndicator stopAnimating];
    }
}
```

现在，你可以通过将 `i` 除以 10,000 来计算完成百分比。将该行代码放在 `if` 语句左花括号后的第一个位置。

```
-(void) bigTask{
    @autoreleasepool {
        int updateUIWhen = 1000;
        for(int i=0;i<10000;i++){
            NSString *newString = [NSString stringWithFormat:@"i = %i", i];
            NSLog(@"%@", newString);
            if(i == updateUIWhen){
                float f = (float)i/10000;

                updateUIWhen = updateUIWhen + 1000;
            }
        }
        [self.myActivityIndicator stopAnimating];
    }
}
```

如果你仔细查看这行新代码，会发现 `i` 前面有 `(float)`。这被称为*类型转换*。在这种情况下，你将 `i`（一个整数）视为 `float` 类型，以便将除法结果赋值给 `float` 类型。你需要 `float` 类型，因为你的进度视图需要 0 到 1 之间的值。

接下来，创建一个 `NSNumber` 对象，因为将在主线程上执行的方法需要它作为参数。

```
-(void) bigTask{
    @autoreleasepool {
        int updateUIWhen = 1000;
        for(int i=0;i<10000;i++){
            NSString *newString = [NSString stringWithFormat:@"i = %i", i];
            NSLog(@"%@", newString);
            if(i == updateUIWhen){
                float f = (float)i/10000;
                NSNumber *percentDone = [NSNumber numberWithFloat:f];

                updateUIWhen = updateUIWhen + 1000;
            }
        }
        [self.myActivityIndicator stopAnimating];
    }
}
```

现在你拥有了在主线程上向用户界面发送消息所需的一切。要向主线程发送消息，你可以使用 `NSObject` 方法 `performSelectorOnMainThread:withObject:waitUntilDone:`。你需要传递一个方法（使用 `@selector` 关键字）、一个作为方法参数的对象（你刚刚创建的 `NSNumber`）以及一个 `BOOL` 值，指示是否要阻塞当前线程直到方法完成。

以下代码将发送该消息：

```
-(void) bigTask{
    @autoreleasepool {
        int updateUIWhen = 1000;
        for(int i=0;i<10000;i++){
            NSString *newString = [NSString stringWithFormat:@"i = %i", i];
            NSLog(@"%@", newString);
            if(i == updateUIWhen){
                float f = (float)i/10000;
                NSNumber *percentDone = [NSNumber numberWithFloat:f];
                [self performSelectorOnMainThread:
                              @selector(updateProgressViewWithPercentage:)
                                      withObject:percentDone
                                   waitUntilDone:YES];
                updateUIWhen = updateUIWhen + 1000;
            }
        }
        [self.myActivityIndicator stopAnimating];
    }
}
```


这里你正在向执行用户界面的主线程发送一条消息，该消息带有一个参数，其中包含任务完成进度的信息。所有用于更新进度视图的代码都位于 `updateProgressViewWithPercentage:` 方法中。

接下来，在任务完成后，你需要再次向主线程发送一条消息，以确保进度视图在你完成时被完全填满。这条消息与你刚刚所做的类似。

```objective-c
-(void) bigTask{
    @autoreleasepool {
        int updateUIWhen = 1000;
        for(int i=0;i<10000;i++){
            NSString *newString = [NSString stringWithFormat:@"i = %i", i];
            NSLog(@"%@", newString);
            if(i == updateUIWhen){
                float f = (float)i/10000;
                NSNumber *percentDone = [NSNumber numberWithFloat:f];
                [self performSelectorOnMainThread:
                              @selector(updateProgressViewWithPercentage:)
                                      withObject:percentDone
                                   waitUntilDone:YES];
                updateUIWhen = updateUIWhen + 1000;
            }
        }
        [self performSelectorOnMainThread:@selector(updateProgressViewWithPercentage:)
                               withObject:[NSNumber numberWithFloat:1.0]
                            waitUntilDone:YES];
        [self.myActivityIndicator stopAnimating];
    }
}
```

相关代码请参见列表 6-5 到 6-8。

## 代码

**列表 6-5.** *AppDelegate.h*

```objective-c
#import <UIKit/UIKit.h>

@class ViewController;

@interface AppDelegate : UIResponder <UIApplicationDelegate>

@property (strong, nonatomic) UIWindow *window;

@property (strong, nonatomic) ViewController *viewController;

@end
```

**列表 6-6.** *AppDelegate.m*

```objective-c
#import "AppDelegate.h"
#import "ViewController.h"

@implementation AppDelegate

@synthesize window = _window;
@synthesize viewController = _viewController;

- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:
(NSDictionary *)launchOptions{
    self.window = [[UIWindow alloc] initWithFrame:[[UIScreen mainScreen] bounds]];
    // 应用程序启动后的自定义覆盖点。
    self.viewController = [[ViewController alloc] initWithNibName:@"ViewController"
                                                           bundle:nil];
    self.window.rootViewController = self.viewController;
    [self.window makeKeyAndVisible];

    return YES;
}

@end
```

**列表 6-7.** *ViewController.h*

```objective-c
#import <UIKit/UIKit.h>

@interface ViewController : UIViewController

@property(strong) UIButton *myButton;
@property(strong) UIActivityIndicatorView *myActivityIndicator;
@property(strong) UIProgressView *myProgressView;

@end
```

**列表 6-8.** *ViewController.m*

```objective-c
#import "ViewController.h"

@implementation ViewController
@synthesize myButton, myActivityIndicator, myProgressView;

-(void)updateProgressViewWithPercentage:(NSNumber *)percentDone{
       [self.myProgressView setProgress:[percentDone floatValue]
                               animated:YES];
}

-(void) bigTask{
    @autoreleasepool {
        int updateUIWhen = 1000;
        for(int i=0;i<10000;i++){
            NSString *newString = [NSString stringWithFormat:@"i = %i", i];
            NSLog(@"%@", newString);
            if(i == updateUIWhen){
                float f = (float)i/10000;
                NSNumber *percentDone = [NSNumber numberWithFloat:f];
                [self performSelectorOnMainThread:
                      @selector(updateProgressViewWithPercentage:)
                                      withObject:percentDone
                                  waitUntilDone:YES];
                updateUIWhen = updateUIWhen + 1000;
            }
        }
        [self performSelectorOnMainThread:@selector(updateProgressViewWithPercentage:)
                               withObject:[NSNumber numberWithFloat:1.0]
                            waitUntilDone:YES];
        [self.myActivityIndicator stopAnimating];
    }
}

// 通过分离新线程执行任务（观察 UI 以了解其工作原理）
-(void)bigTaskAction{
    [self.myActivityIndicator startAnimating];

    [NSThread detachNewThreadSelector:@selector(bigTask)
                             toTarget:self
                           withObject:nil];

}

- (void)viewDidLoad{
    [super viewDidLoad];

        //创建按钮
        self.myButton = [UIButton buttonWithType:UIButtonTypeRoundedRect];
        self.myButton.frame = CGRectMake(20, 403, 280, 37);
        [self.myButton addTarget:self
                          action:@selector(bigTaskAction)
                forControlEvents:UIControlEventTouchUpInside];
        [self.myButton setTitle:@"执行长任务"
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

## 用法

要使用此代码，首先在 Xcode 中创建一个基于 Single View 的应用程序，就像你在方案 1.12 中所做的那样。接下来，将列表 6-8 中的代码添加到你自己的 `ViewController` 类中。

运行应用程序。在 iOS 模拟器中，你应该会看到一个带有一个按钮和一个空白进度视图的应用。你还会在视图中间看到一个白色的活动指示器。触摸按钮以在后台线程中启动 `bigTask` 并检查日志。随着 `bigTask` 运行，你应该会看到进度视图以 10% 的增量填充，直到任务完成。

## 6.3 使用 NSLock 锁定线程

### 问题

你的应用程序使用了多个线程，但有时你需要确保两个线程不会试图同时使用同一块代码。你的应用程序可能引发冲突，导致用户困惑或文件被访问次数过多。

例如，尝试运行方案 6.2 中的应用程序，但在第一个任务开始填充进度视图后第二次触摸按钮。如果仔细观察，你会看到进度视图开始前后跳动，因为每个线程都会将进度视图的值更改为该特定线程的当前百分比。

### 解决方案

使用 `NSLock` 让其他线程等待，直到当前线程处理完关键代码块。



### 工作原理

在此示例中，假设你正从食谱 6.2 中的应用程序开始，并且你的应用行为与“问题”部分所述一致。你需要确保`bigTask`一次只在一个线程中执行。这将使每个线程排队等待执行。

你需要做的第一件事是向视图控制器添加一个`NSLock`对象。以下是将其设置为属性的示例：

```
#import <UIKit/UIKit.h>

@interface ViewController : UIViewController

@property(strong) UIButton *myButton;
@property(strong) UIActivityIndicatorView *myActivityIndicator;
@property(strong) UIProgressView *myProgressView;
@property(strong) NSLock *threadLock;

@end
```

此属性也必须包含在视图控制器实现中的`@synthesize`语句中。

```
#import "ViewController.h"

@implementation ViewController
@synthesize myButton, myActivityIndicator, myProgressView, threadLock;

@end
```

**注意：** 将此类对象作为属性包含并非绝对必要。你也可以直接将它们添加为视图控制器实现中的局部实例。这是你必须做出的设计决策。我这样包含`NSLock`是为了尽可能与之前的食谱保持一致。

在使用`NSLock`之前，你需要实例化一个对象并将其分配给你已有的属性。在视图控制器中，最佳位置是在`viewDidLoad`方法中。

```
#import "ViewController.h"

@implementation ViewController
@synthesize myButton, myActivityIndicator, myProgressView, threadLock;

- (void)viewDidLoad{
    [super viewDidLoad];

    //创建 NSLock 对象
    self.threadLock = [[NSLock alloc] init];
}

@end
```

此处未列出视图控制器的所有代码，但你可以在代码清单 6-11 中查看。

现在你已经有了一个`NSLock`对象，只需锁定你的线程即可。为此，在线程代码的开头向`NSLock`对象发送`lock`消息，并在线程代码的末尾发送`unlock`消息。

以下代码位于你视图控制器实现中的`bigTask`方法内。

```
-(void) bigTask{
    [self.threadLock lock];
    @autoreleasepool {
        int updateUIWhen = 1000;
        for(int i=0;i<10000;i++){
            NSString *newString = [NSString stringWithFormat:@"i = %i", i];
            NSLog(@"%@", newString);
            if(i == updateUIWhen){
                float f = (float)i/10000;
                NSNumber *percentDone = [NSNumber numberWithFloat:f];
                [self performSelectorOnMainThread:
                      @selector(updateProgressViewWithPercentage:)
                                      withObject:percentDone
                                  waitUntilDone:YES];
                updateUIWhen = updateUIWhen + 1000;
            }
        }
        [self performSelectorOnMainThread:@selector(updateProgressViewWithPercentage:)
                               withObject:[NSNumber numberWithFloat:1.0]
                            waitUntilDone:YES];
        [self.myActivityIndicator stopAnimating];
    }
    [self.threadLock unlock];
}
```

现在你可以确保该线程将被锁定，直到线程执行完毕。请参阅代码清单 6-9 至 6-12 获取代码。

#### 代码

**代码清单 6-9.** *AppDelegate.h*

```
#import <UIKit/UIKit.h>

@class ViewController;

@interface AppDelegate : UIResponder <UIApplicationDelegate>

@property (strong, nonatomic) UIWindow *window;

@property (strong, nonatomic) ViewController *viewController;

@end
```

**代码清单 6-10.** *AppDelegate.m*

```
#import "AppDelegate.h"

#import "ViewController.h"

@implementation AppDelegate

@synthesize window = _window;
@synthesize viewController = _viewController;

- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:
(NSDictionary *)launchOptions{
    self.window = [[UIWindow alloc] initWithFrame:[[UIScreen mainScreen] bounds]];
    //应用启动后的自定义覆盖点。
    self.viewController = [[ViewController alloc] initWithNibName:@"ViewController"
                                                           bundle:nil];
    self.window.rootViewController = self.viewController;
    [self.window makeKeyAndVisible];

    return YES;
}

@end
```

**代码清单 6-11.** *ViewController.h*

```
#import <UIKit/UIKit.h>

@interface ViewController : UIViewController

@property(strong) UIButton *myButton;
@property(strong) UIActivityIndicatorView *myActivityIndicator;
@property(strong) UIProgressView *myProgressView;
@property(strong) NSLock *threadLock;

@end
```

**代码清单 6-12.** *ViewController.m*

```
#import "ViewController.h"

@implementation ViewController
@synthesize myButton, myActivityIndicator, myProgressView, threadLock;

-(void)updateProgressViewWithPercentage:(NSNumber *)percentDone{
       [self.myProgressView setProgress:[percentDone floatValue]
                               animated:YES];
}

-(void) bigTask{
    [self.threadLock lock];
    @autoreleasepool {
        int updateUIWhen = 1000;
        for(int i=0;i<10000;i++){
            NSString *newString = [NSString stringWithFormat:@"i = %i", i];
            NSLog(@"%@", newString);
            if(i == updateUIWhen){
                float f = (float)i/10000;
                NSNumber *percentDone = [NSNumber numberWithFloat:f];
                [self performSelectorOnMainThread:
                      @selector(updateProgressViewWithPercentage:)
                                      withObject:percentDone
                                  waitUntilDone:YES];
                updateUIWhen = updateUIWhen + 1000;
            }
        }
        [self performSelectorOnMainThread:@selector(updateProgressViewWithPercentage:)
                               withObject:[NSNumber numberWithFloat:1.0]
                            waitUntilDone:YES];
        [self.myActivityIndicator stopAnimating];
    }
    [self.threadLock unlock];

}

//通过分离新线程执行任务（观察 UI 了解其工作原理）
-(void)bigTaskAction{
    [self.myActivityIndicator startAnimating];

    [NSThread detachNewThreadSelector:@selector(bigTask)
                             toTarget:self
                           withObject:nil];

}

- (void)viewDidLoad{
    [super viewDidLoad];

    //创建 NSLock 对象
    self.threadLock = [[NSLock alloc] init];

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

    //创建进度条
    self.myProgressView = [[UIProgressView alloc] init];
    self.myProgressView.frame = CGRectMake(20, 20, 280, 9);
    [self.view addSubview:self.myProgressView];

}

@end
```



### 使用说明

要测试此代码，请先在 Xcode 中创建一个基于单视图（Single View）的应用程序，就像你在方案 1.12 中所做的那样。接下来，将代码清单 6-12 中的代码添加到你自己的 `ViewController` 类中。

运行该应用程序。在 iOS 模拟器中，你会看到一个包含一个按钮和一个空进度视图的应用程序。你还会在视图中央看到一个白色的活动指示器。触摸按钮两次，即可在两个后台线程中启动 `bigTask` 任务。

当 `bigTask` 运行时，你应该会看到进度视图以 10% 的增量逐步填满，直至任务完成。观察进度视图，它会填满到 100%，然后重置回 0%，再继续增长到 100%。这正是 `NSLock` 的预期行为。

### 6.4 使用 @synchronized 锁定线程

### 问题

你的应用程序使用了多线程，但有时你需要确保两个线程不会同时尝试使用同一块代码，并且你希望有一种不同于 `NSLock` 的解决方案。

**注意：** `@synchronized` 和 `NSLock` 解决的是相同的线程问题，因此本方案看起来与方案 6.3 非常相似。这两种方法虽然相似，但实现方式不同，`@synchronized` 能够处理异常。这同时也导致 `@synchronized` 比 `NSLock` 有更大的性能开销。

以方案 6.2 中的应用程序为起始点。方案 6.2 中的应用程序，每次你触摸按钮时都会执行该线程一次，这会导致进度视图的行为变得不可预期。

### 解决方案

为了确保一次只有一个线程可以使用一段代码，请将整个代码块包含在一对花括号内，并在其前面加上 `@synchronized` 指令。

### 工作原理

对于此示例，我们假设你从方案 6.2 的应用程序开始，并且你的应用程序表现如“问题”部分所述。你想要确保 `bigTask` 一次只在一个线程中执行。这将使每个线程依次等待。

视图控制器的所有代码并未在此列出，但你可以在代码清单 6-15 中查看。

与方案 6.3 不同，你不需要添加任何属性代码。你需要做的只是将 `bigTask` 中的代码用花括号括起来，并加上 `@synchronized` 指令。

```
-(void) bigTask{
    @synchronized(self){
        @autoreleasepool {
            int updateUIWhen = 1000;
            for(int i=0;i<10000;i++){
                NSString *newString = [NSString stringWithFormat:@"i = %i", i];
                NSLog(@"%@", newString);
                if(i == updateUIWhen){
                    float f = (float)i/10000;
                    NSNumber *percentDone = [NSNumber numberWithFloat:f];
                    self performSelectorOnMainThread:  `![Image
                          @selector(updateProgressViewWithPercentage:)
                                           withObject:percentDone
                                        waitUntilDone:YES];
                    updateUIWhen = updateUIWhen + 1000;
                }
            }
            self performSelectorOnMainThread:  `![Image
                  @selector(updateProgressViewWithPercentage:)
                                   withObject:[NSNumber numberWithFloat:1.0]
                                waitUntilDone:YES];
            [self.myActivityIndicator stopAnimating];
        }
    }
}
```

完整代码请参见代码清单 6-13 至 6-16。

#### 代码

**代码清单 6-13.** *AppDelegate.h*

```
#import <UIKit/UIKit.h>

@class ViewController;

@interface AppDelegate : UIResponder <UIApplicationDelegate>

@property (strong, nonatomic) UIWindow *window;

@property (strong, nonatomic) ViewController *viewController;

@end
```

**代码清单 6-14.** *AppDelegate.m*

```
#import "AppDelegate.h"

#import "ViewController.h"

@implementation AppDelegate

@synthesize window = _window;
@synthesize viewController = _viewController;

- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:  `![Image](img/U001.jpg)
(NSDictionary *)launchOptions{
    self.window = [[UIWindow alloc] initWithFrame:[[UIScreen mainScreen] bounds]];
    // 覆写点，用于应用程序启动后的自定义。
    self.viewController = [[ViewController alloc] initWithNibName:@"ViewController"
                                                           bundle:nil];
    self.window.rootViewController = self.viewController;
    [self.window makeKeyAndVisible];

    return YES;
}

@end
```

**代码清单 6-15.** *ViewController.h*

```
#import <UIKit/UIKit.h>

@interface ViewController : UIViewController

@property(strong) UIButton *myButton;
@property(strong) UIActivityIndicatorView *myActivityIndicator;
@property(strong) UIProgressView *myProgressView;

@end
```

**代码清单 6-16.** *ViewController.m*

```
#import "ViewController.h"

@implementation ViewController
@synthesize myButton, myActivityIndicator, myProgressView;

-(void)updateProgressViewWithPercentage:(NSNumber *)percentDone{
       [self.myProgressView setProgress:[percentDone floatValue]
                               animated:YES];
}

-(void) bigTask{
    @synchronized(self){
        @autoreleasepool {
            int updateUIWhen = 1000;
            for(int i=0;i<10000;i++){
                NSString *newString = [NSString stringWithFormat:@"i = %i", i];
                NSLog(@"%@", newString);
                if(i == updateUIWhen){
                    float f = (float)i/10000;
                    NSNumber *percentDone = [NSNumber numberWithFloat:f];
                    self performSelectorOnMainThread:  `![Image
                          @selector(updateProgressViewWithPercentage:)
                                           withObject:percentDone
                                        waitUntilDone:YES];
                    updateUIWhen = updateUIWhen + 1000;
                }
            }
            self performSelectorOnMainThread:  `![Image
                  @selector(updateProgressViewWithPercentage:)
                                   withObject:[NSNumber numberWithFloat:1.0]
                                waitUntilDone:YES];
            [self.myActivityIndicator stopAnimating];
        }
    }
}

// 通过分离一个新线程来执行任务（观察 UI 以了解其工作方式）
-(void)bigTaskAction{
    [self.myActivityIndicator startAnimating];

    [NSThread detachNewThreadSelector:@selector(bigTask)
                             toTarget:self
                           withObject:nil];

}

- (void)viewDidLoad{
    [super viewDidLoad];

    // 创建按钮
    self.myButton = [UIButton buttonWithType:UIButtonTypeRoundedRect];
    self.myButton.frame = CGRectMake(20, 403, 280, 37);
    [self.myButton addTarget:self
                      action:@selector(bigTaskAction)
            forControlEvents:UIControlEventTouchUpInside];
    [self.myButton setTitle:@"执行耗时任务"
                   forState:UIControlStateNormal];
    [self.view addSubview:self.myButton];

    // 创建活动指示器
    self.myActivityIndicator = [[UIActivityIndicatorView alloc] init];
    self.myActivityIndicator.frame = CGRectMake(142, 211, 37, 37);
self.myActivityIndicator.activityIndicatorViewStyle = `![Image](img/U001.jpg)
UIActivityIndicatorViewStyleWhiteLarge;
    self.myActivityIndicator.hidesWhenStopped = NO;
    [self.view addSubview:self.myActivityIndicator];

    // 创建进度条
    self.myProgressView = [[UIProgressView alloc] init];
    self.myProgressView.frame = CGRectMake(20, 20, 280, 9);
    [self.view addSubview:self.myProgressView];

}

@end
```



#### 用法

要测试这段代码，首先像食谱 1.12 中那样在 Xcode 中创建一个基于单视图的应用程序。然后，将代码清单 6-16 中的代码添加到您自己的 `ViewController` 类中。

运行该应用程序。在 iOS 模拟器中，您应该会看到一个带有按钮的应用程序，并且在视图顶部有一个空的进度视图。您还会在视图中间看到一个白色的活动指示器。触摸按钮两次，即可在两个后台线程中启动 `bigTask`。

随着 `bigTask` 运行，您应该会看到进度视图以 10% 的增量逐渐填满，直到任务完成。观察发现，进度视图会先填满到 100%，然后重置回 0%，并再次逐步增加到 100%。`@synchronized` 发挥了其应有的作用。

### 6.5 使用 Grand Central Dispatch (GCD) 进行异步处理

### 问题

您希望在应用程序中实现异步处理，计划支持较新的 OSX 和 iOS 系统，并且不希望使用 `NSThread` 以及为使应用程序线程安全所需的各种锁定机制。

**注意：** 使用多线程的应用程序可能会变得更复杂，有时甚至运行更慢，因为开发者需要担心代码区域、资源或数据结构可能同时被多个线程访问的情况。将线程短时间锁定（如食谱 6.3 和 6.4 所示）有助于使代码线程安全（可供多个线程安全使用）。然而，这可能会阻止应用程序充分利用可用资源。

### 解决方案

如果您知道用户已更新系统（或者您选择仅支持更新的系统），请考虑使用 Grand Central Dispatch (GCD) 作为 `NSThread` 的替代方案。GCD 解决了与 `NSThread` 相同的问题，并遵循执行异步代码相同的基本思路。GCD 是一项较新的技术，在拥有多个处理器的计算机上效率更高。GCD 在 OSX 10.6 版本和 iOS 4 版本中引入。

如果您使用的是支持 GCD 的 OSX 版本进行开发，则无需执行任何特殊操作即可为您的应用程序添加 GCD 支持。GCD 确实需要使用一种称为 *blocks* 的编程技术，这可能需要一些时间来适应。

Blocks 是被视为对象的代码区域。这意味着您可以将代码行放在花括号之间，然后将它们视为一个对象。通常，您会看到 blocks 作为方法的参数使用，这也是 blocks 在 GCD 中的使用方式。您将在下一节中了解 blocks 如何与 GCD 一起使用。

### 工作原理

对于此食谱，您将解决与食谱 6.1、6.2 和 6.3 中相同的问题。本质上，您有一个带有按钮的 iOS 应用程序，该按钮会触发一个长时间运行的任务，您希望保持用户界面的响应性，并让进度视图在任务执行时逐渐填充。这次，您将使用 GCD 来解决此问题。

GCD 使用 blocks 而不是方法（使用 `@selector` 指令）。这意味着您无需将所有要执行的代码放入一个新方法中。相反，您可以直接在 action 方法 `bigTaskAction` 中将代码作为参数传递给 GCD 函数。使用 GCD 函数 `dispatch_async` 来实现这一点。

```
-(void)bigTaskAction{
    [self.myActivityIndicator startAnimating];

    dispatch_async(dispatch_get_global_queue(DISPATCH_QUEUE_PRIORITY_DEFAULT, 0), ^{

        int updateUIWhen = 1000;
        for(int i=0;i<10000;i++){
            NSString *newString = [NSString stringWithFormat:@"i = %i", i];
            NSLog(@"%@", newString);
            if(i == updateUIWhen){
                float f = (float)i/10000;
                NSNumber *percentDone = [NSNumber numberWithFloat:f];

                updateUIWhen = updateUIWhen + 1000;
            }
        }

    });
}
```

让我们看一下带有该 GCD 函数的第一行代码。

```
dispatch_async(dispatch_get_global_queue(DISPATCH_QUEUE_PRIORITY_DEFAULT, 0), ^{
```

第一部分是函数名 `dispatch_async`，这是一个异步执行代码的 GCD 函数。还有一个类似的函数 `dispatch_sync`，它同步执行代码。该函数需要的第一个参数是一个调度队列。

```
dispatch_async(dispatch_get_global_queue(DISPATCH_QUEUE_PRIORITY_DEFAULT, 0), ^{
```

您提供了另一个函数，该函数反过来返回此应用程序的默认调度队列。

**注意：**  GCD 有一个代码队列的概念，这些队列被安排在下个可用的处理器上运行。当您使用 GCD 时，需要指定您希望将代码放入哪个队列。此处您使用的是默认队列，您也可以将其用于后台处理。您还可以使用主队列，它类似于用户界面的主线程。

此函数的下一个参数是代码块。您知道正在处理的是一个代码块，因为它以 `^` 符号开头，并有一个左花括号。

```
dispatch_async(dispatch_get_global_queue(DISPATCH_QUEUE_PRIORITY_DEFAULT, 0), ^{
```

之后的所有代码行都是 block 参数的一部分。此代码被安排在下个处理器可用时执行。整个 GCD 函数以代码 `);` 结束。

到目前为止，您所做的只是将这个耗时任务安排在后台执行。但您仍然希望在任务进行时更新用户界面。您可以使用另一个 GCD 函数在主线程上更新用户界面，而不是在主线程上执行一个选择器。此任务应同步完成，因此将 GCD 函数与主调度队列结合使用。

```
dispatch_sync(dispatch_get_main_queue(), ^{
    [self.myProgressView setProgress:[percentDone floatValue]
                            animated:YES];

});
```

这取代了您之前必须编写的方法。您可以直接使用手头的变量，而无需担心传递参数，当您将这个 GCD 调用放入整个代码块的上下文中时就能看到这一点。

```
-(void)bigTaskAction{
    [self.myActivityIndicator startAnimating];

    dispatch_async(dispatch_get_global_queue(DISPATCH_QUEUE_PRIORITY_DEFAULT, 0), ^{
```


```objectivec
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
```

**注意：** 使用 GCD 调度队列时，当你使用 `dispatch_async` 时，无法确切知道代码块的执行顺序。系统会选择最高效的方式。因此，如果顺序很重要（比如更新界面时），请使用 `dispatch_sync`。

最后，为了完整起见，你需要在任务完成时填满进度视图并停止活动指示器。再次使用 GCD，在代码块末尾为主队列调度另一个任务来实现。

```objectivec
-(void)bigTaskAction{
    [self.myActivityIndicator startAnimating];

    dispatch_async(dispatch_get_global_queue(DISPATCH_QUEUE_PRIORITY_DEFAULT, 0), ^{

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
```

代码参见列表 6-17 至 6-20。

#### 代码

**列表 6-17.** *AppDelegate.h*

```objectivec
#import <UIKit/UIKit.h>

@class ViewController;

@interface AppDelegate : UIResponder <UIApplicationDelegate>

@property (strong, nonatomic) UIWindow *window;

@property (strong, nonatomic) ViewController *viewController;

@end
```

**列表 6-18.** *AppDelegate.m*

```objectivec
#import "AppDelegate.h"
#import "ViewController.h"

@implementation AppDelegate

@synthesize window = _window;
@synthesize viewController = _viewController;

- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:  
(NSDictionary *)launchOptions{
    self.window = [[UIWindow alloc] initWithFrame:[[UIScreen mainScreen] bounds]];
    // 覆写点：应用程序启动后的自定义设置。
    self.viewController = [[ViewController alloc] initWithNibName:@"ViewController"
                                                           bundle:nil];
    self.window.rootViewController = self.viewController;
    [self.window makeKeyAndVisible];

    return YES;
}

@end
```

**列表 6-19.** *ViewController.h*

```objectivec
#import <UIKit/UIKit.h>

@interface ViewController : UIViewController

@property(strong) UIButton *myButton;
@property(strong) UIActivityIndicatorView *myActivityIndicator;
@property(strong) UIProgressView *myProgressView;

@end
```

**列表 6-20.** *ViewController.m*

```objectivec
#import "ViewController.h"

@implementation ViewController
@synthesize myButton, myActivityIndicator, myProgressView;

-(void)bigTaskAction{
    [self.myActivityIndicator startAnimating];

    dispatch_async(dispatch_get_global_queue(DISPATCH_QUEUE_PRIORITY_DEFAULT, 0), ^{

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

    // 创建按钮
    self.myButton = [UIButton buttonWithType:UIButtonTypeRoundedRect];
    self.myButton.frame = CGRectMake(20, 403, 280, 37);
    [self.myButton addTarget:self
                      action:@selector(bigTaskAction)
            forControlEvents:UIControlEventTouchUpInside];
    [self.myButton setTitle:@"执行长任务"
                   forState:UIControlStateNormal];
    [self.view addSubview:self.myButton];

    // 创建活动指示器
    self.myActivityIndicator = [[UIActivityIndicatorView alloc] init];
    self.myActivityIndicator.frame = CGRectMake(142, 211, 37, 37);
    self.myActivityIndicator.activityIndicatorViewStyle = 
    UIActivityIndicatorViewStyleWhiteLarge;
    self.myActivityIndicator.hidesWhenStopped = NO;
    [self.view addSubview:self.myActivityIndicator];

    // 创建标签
    self.myProgressView = [[UIProgressView alloc] init];
    self.myProgressView.frame = CGRectMake(20, 20, 280, 9);
    [self.view addSubview:self.myProgressView];

}

@end
```

#### 用法

要测试此代码，首先按照 Recipe 1.12 中的方法，在 Xcode 中创建一个基于 Single View 的应用程序。然后，将列表 6-20 中的代码添加到你自己的 `ViewController` 类中。

运行应用程序。在 iOS 模拟器中，你应该会看到一个带有一个按钮和一个空进度视图的应用程序。你还会在视图中间看到一个白色的活动指示器。触摸按钮启动 `bigTask` 运行。随着 `bigTask` 的运行，你应该会看到进度视图以 10% 的增量逐渐填满，直到任务完成。

一般来说，GCD 是进行后台处理的首选方式。如果目标系统较新，在决定使用哪种技术时，GCD 应是首选。GCD 已针对多核应用程序进行了优化，因此在多核 Mac 上使用 GCD 时，你会看到应用程序性能的大幅提升。

与 `NSThread` 相比，GCD 更易于使用，因为无需额外的对象，也无需像 `NSThread` 那样编写额外的方法。不过，你会看到很多使用 `NSThread` 进行后台处理的示例，而且该选项也是可用的。

## 6.6 在 GCD 中使用串行队列

### 问题

你使用 GCD 执行异步处理，但遇到了一种情况：需要代码块按照它们在代码中出现的顺序逐一执行。例如，在 Recipe 6.5 中，当用户在长任务运行期间触摸按钮时（进度视图来回跳动），你会遇到与本章前面相同的问题。

此前，你使用 `NSLock` 或 `@synchronized()` 解决了这个问题，但这些方法会带来一定成本，这反而削弱了使用 GCD 本身的部分优势。


### 解决方案

与其锁定代码，不如使用 GCD 串行队列，按代码块放入队列的顺序加载并执行它们。你可以使用 GCD 函数 `dispatch_queue_create(DISPATCH_QUEUE_SERIAL, 0)` 来创建一个串行队列。确保该串行队列在其所服务对象的生命周期内始终处于作用域范围内。

### 工作原理

在本节中，你将修改方案 6.5，使用串行队列来解决遇到的问题。首先，你需要为串行队列创建一个属性。

```objc
#import <UIKit/UIKit.h>

@interface ViewController : UIViewController

@property(strong) UIButton *myButton;
@property(strong) UIActivityIndicatorView *myActivityIndicator;
@property(strong) UIProgressView *myProgressView;
@property(assign) dispatch_queue_t serialQueue;

@end
```

只要队列在所需时间内始终处于作用域内，这也可以是视图控制器中的一个局部实例变量。

你还需要确保在视图控制器的 `@synthesize` 语句中也实现了串行队列。

```objc
#import "ViewController.h"

@implementation ViewController
@synthesize myButton, myActivityIndicator, myProgressView, serialQueue;

@end
```

`viewDidLoad` 视图控制器方法是放置创建串行队列所需代码的最佳位置。

```objc
#import "ViewController.h"

@implementation ViewController
@synthesize myButton, myActivityIndicator, myProgressView, serialQueue;

...

- (void)viewDidLoad{
    [super viewDidLoad];

    self.serialQueue = dispatch_queue_create(DISPATCH_QUEUE_SERIAL, 0);

}

...

@end
```

该函数需要一个参数来指定要创建的队列类型。这里使用 `DISPATCH_QUEUE_SERIAL`，是因为你需要一个串行队列，确保每次仅按代码块放入队列的顺序执行一个代码块。部分视图控制器代码已省略；完整代码请参见代码清单 6-23。

接下来需要对方案 6.5 代码所做的修改是，将之前使用的默认队列替换为你刚刚创建的串行队列。这发生在 `bigTaskAction` 方法中。

```objc
#import "ViewController.h"

@implementation ViewController
@synthesize myButton, myActivityIndicator, myProgressView, serialQueue;

...

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

...

@end
```

如你所见，我们将开始动画活动指示器的消息移到了此操作的主代码块内。我们还将其放入了主队列，因为这涉及更新用户界面。这样做的理由是，每次此类代码块在串行队列中执行时，你希望活动指示器开始旋转。代码请参见代码清单 6-21 至 6-24。

