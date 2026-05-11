# 第 6 章：集成网络与 Web 服务

```
@interface LCTTwitterController : NSObject

+ (id)sharedInstance;

- (void)authorizeAccount;

- (void)getTweetsInUserTimelineWithCompletionHandler:(void(^)(NSArray
*tweets))handler;

@end
```

我们将使用`sharedInstance`方法来获取指向单例对象的指针。`authorizeAccount`方法将确保我们有一个有效的账户，这对于另一个方法`getTweetsInUserTimelineWithCompletionHandler:`是必需的，该方法将返回一个用户可阅读的推文列表。你可能会对其声明中的这个奇怪语法感到困惑：

```
(void(^)(NSArray *tweets))handler
```

这是一个 block：一段封装在对象中的代码，你可以将其传递给一个方法。这个特定的 block 返回 void，并有一个参数，即一个名为`tweets`的`NSArray`。我们将在下一章重点讨论 blocks。现在，只需从书中复制这个语法。

现在让我们为这些方法添加实现。在 Xcode 中，切换到 Twitter 控制器的实现文件（`LCTTwitterController.m`），并添加加粗显示的行（这是一大段文本，但我们稍后会回来讨论它）：

```
#import "LCTTwitterController.h"

#import <Accounts/Accounts.h>

#import <Twitter/Twitter.h>

static NSString * const kSavedTwitterAccountKey = @"SavedTwitterAccount";

@implementation LCTTwitterController {
    ACAccountStore *_accountStore;
    ACAccount *_twitterAccount;
}

+ (id)sharedInstance
{
    static id _sharedInstance = nil;
    if (_sharedInstance == nil) {
        _sharedInstance = [[self alloc] init];
    }
    return _sharedInstance;
}
```

[www.it-ebooks.info](http://www.it-ebooks.info/)

