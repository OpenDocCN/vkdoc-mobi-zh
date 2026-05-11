# 选择你的武器！

Facebook 和 Twitter 都有多种用途，且功能多有重叠。本章的任务就是弄清楚应该集成哪个服务（或者先集成哪一个）。让我们深入探究，看看 Facebook 和 Twitter 能为我们提供哪些支持。

阅读本章后，你应该了解以下内容：

*   利用 Facebook 的 iOS SDK 及其移动网页 SDK 可以做什么。
*   如何更轻松地在 iOS 中集成 Twitter 的 API。

## 它们各有什么优势？

哪个集成方案是你的首要选择，这主要取决于你的具体应用，而非其他因素。然而，在决定将精力集中在哪里时，还是有一些普遍性的考虑因素。你对 Facebook 和 Twitter 了解得越多，就越能判断出哪个更适合你的应用（或者——天哪！——是否需要两者都集成）。

#### Facebook

Facebook 拥有超过 5 亿注册用户，其中 1 亿用户通过移动设备访问 Facebook。这是一个非常庞大的用户群体。如果你的应用打算依赖某个平台来实现普及性，那么 Facebook 因其惊人的国际流行度，实际上已成为首选。

话虽如此，从数据上看，Facebook 的内容主要是私人照片。Facebook Photos 是该平台最受欢迎的功能，而 iOS 上支持此功能的部分代码是开源的。Facebook 状态主要涉及个人想法，其消息系统主要用于成员之间的个人通信。品牌和企业虽然存在，但主要以粉丝专页的形式出现，这些页面主要通过“赞”按钮获得关注。

#### Twitter

Twitter 与 Facebook 截然不同。它已成为突发新闻最重要的传播渠道，Twitter 上的大部分内容都旨在尽可能快地分享。这几乎与 Facebook 的生态系统相反——在 Facebook 上，精细的隐私设置可以防止内容以不受控制的方式泄露（至少在原则上是这样）。Twitter 每天 6500 万条推文中，绝大多数是公开的，而非私密的，并且它每天产生如此多的内容，以至于没有空间存档所有通过其系统的推文。（相比之下，Facebook 即使在用户删除文件和个人资料后，也会保存它们。）在撰写本文时，每月约有 1.9 亿人使用 Twitter。

**注意：** 初创公司喜欢抛出数千万的“用户”统计数据，但这些数字到底意味着什么？我们先从 Facebook 说起。除非你注册并登录，否则 Facebook 几乎毫无用处。因此，当 Facebook 声称拥有 5 亿用户（并且还在增长）时，它指的是已经注册并在系统中输入了个人信息的人数。相比之下，Twitter 的数百万阅读者都是*潜水者*，或者说是没有个人资料的人。在撰写本文时，ComScore 估计 Twitter 全球每月有 8360 万独立访客，美国约有 2400 万，这些数字比 Twitter 报告的要少。同样值得一提的是，在这 6500 万条每日推文中，有多少是自动机器人或垃圾邮件发送者，尚不可知。不管你怎么看，Facebook 都是一个规模大得多的服务，但 Twitter 包含了更多可公开访问（且具有公共价值）的信息。

## 开始使用 Facebook 强大的开发者工具

Facebook 有一个专门的 iOS SDK 来帮助简化集成。Facebook 喜欢强调其 SDK 能轻松实现单点登录，这样用户就不必每次打开你的应用都登录。但其功能远不止于此。使用 Facebook 的 iOS SDK，你可以轻松完成以下操作：

*   提示用户登录 Facebook，并授予你的应用访问权限。
*   向 Graph API 和较旧的 REST API 发出请求。
*   向用户显示常见的 Facebook 对话框，用于创建涂鸦墙帖等。
*   在运行 4.x 版本 iOS 并支持多任务处理的 iOS 设备上，你可以利用 Facebook 的单点登录功能。此功能允许多个应用程序共享用户的 Facebook 登录状态。换句话说，如果用户已经从 Facebook iOS 应用或另一个使用 Facebook iOS SDK 的应用中登录过 Facebook，那么只要你的应用也使用了 Facebook iOS SDK，就不会再提示用户登录。你将在第 5 章中了解更多相关信息。
*   Facebook 的 iOS SDK 由该公司最初的移动开发者乔·休伊特创建。他很慷慨地将其大部分工作开源，代码可在 GitHub 上获取，地址为 [`https://github.com/facebook/facebook-ios-sdk`](https://github.com/facebook/facebook-ios-sdk)。Facebook 的开发工具包预装了一些示例项目，但本书将提供更多可供在线下载的项目。

在接下来的章节中，我们将更深入地讨论如何在 Xcode 中设置你的 iOS 项目以使用 Facebook 和 Twitter 的 API；不过，让我们先快速浏览一下如何在实际代码中使用 Facebook 和 Twitter 的 API。

### 使用 Facebook 的 API

现在让我们看看如何使用 Facebook 的 API。首先实例化 `Facebook` 对象：

```
Facebook* facebook = [[Facebook alloc] init];
```

使用 iOS SDK，你可以做三件主要的事情：

*   **处理身份验证和授权：** 提示用户登录 Facebook 并向你的应用授予权限。
*   **进行 API 调用：** 获取用户个人资料数据，以及用户好友的信息。
*   **显示对话框：** 通过 `UIWebView` 与用户交互——这对于实现快速的 Facebook 交互（例如，发布到用户的动态）非常有用，无需预先获取权限或实现原生用户界面。

#### 进行 API 调用

Facebook Graph API 提供了一个简单、一致的视图来展示 Facebook 社交图谱，统一表示了图谱中的对象（例如，人、照片、事件和粉丝专页）以及它们之间的联系（例如，好友关系、共享内容和照片标签）。

你可以通过将 Graph 路径传递给 `request()` 方法来访问 Graph API。

例如，以下代码使你能够访问当前登录用户的信息：

```
[facebook requestWithGraphPath:@"me" andDelegate:self];
```

以下代码使你能够获取当前登录用户的 `friends` 信息：

```
[facebook requestWithGraphPath:@"me/friends" andDelegate:self];
```

你的委托对象应实现 `FBRequestDelegate` 接口来处理你的请求响应。成功的请求会回调委托中的 `FBRequestDelegate` 接口的 `request:didLoad:` 方法。传递给委托的结果可以是 `NSArray`、`NSString`、`NSDictionary` 或 `NSNumber`，具体取决于你请求的信息及其响应的格式。

高级应用可能希望根据自身需求提供自定义的解析和/或错误处理。

#### 显示对话框

此 SDK 提供了一种弹出 Facebook 对话框的方法。目前支持的对话框包括授权流程中使用的登录和权限对话框，以及用于发布帖子到用户动态的对话框。

使用以下代码调用对话框以发布消息到用户动态：

```
[facebook dialog:@"feed" andParams:nil andDelegate:self];
```

上述代码允许你通过一行代码在应用中提供基本的 Facebook 功能——无需构建原生对话框、进行 API 调用或处理响应。更多示例，请参考附带的示例应用。

#### 错误处理

错误由 `FBRequestDelegate` 和 `FBDialogDelegate` 协议处理。应用可以实现这些协议，并根据需要指定行为来处理任何错误。



