# 登出

当用户希望停止将 Facebook 与您的应用集成时，您可以调用 `logout` 方法清除所有应用状态，并向服务器发送请求以撤销当前的访问令牌，如下所示：

`[facebook logout:self];`

请注意，登出不会撤销您应用的权限，而只是清除应用的访问令牌。如果之前已从您的应用登出的用户再次返回，他只会看到一条正在登录您应用的通知，而不会看到授权权限的提示。要修改或撤销应用的权限，用户必须访问其 Facebook 隐私设置面板中的“应用、游戏和网站”标签页。

## Twitter 的次优（但仍很棒！）工具

Twitter 并未为 iOS 构建特定的 SDK，但仍有一些捷径可让开发更轻松。热门 Twitter 客户端 Twitterific 的创建者开发了 `MGTwitterEngine`，这是一个提供多种方法的类库，可让开发者更轻松地使用 Twitter API。`MGTwitterEngine` 完全支持 Twitter API，因此我们将在本书中全程使用它。

不过，自己动手实现也很容易，因为 Twitter 允许你选择以 XML 或 JSON 格式获取动态内容。这意味着你可以在不带来太多麻烦的情况下，将 Twitter 集成到你的应用中。

### 使用 MGTwitterEngine

`MGTwitterEngine` API 让你可以轻松从应用内部发布内容到 Twitter。首先实例化 `MGTwitterEngine` 对象：

`MGTwitterEngine *engine = [[MGTwitterEngine alloc] initWithDelegate:self];`

#### 进行 API 调用

`MGTwitterEngine` API 让使用 Twitter 完成任务变得简单。

然后你可以向 `MGTwitterEngine` 发起请求，例如获取该用户在 Twitter 上关注的人的最新动态：

`NSString *connectionID = [twitterEngine getFollowedTimelineFor:nil since:nil startingAtPage:0];`

创建 `MGTwitterEngine` 对象的类需要实现 `MGTwitterEngineDelegate` 协议来处理请求响应。

一个成功的请求会回调你的对象中的 `MGTwitterEngineDelegate 的 requestSucceeded:` 方法。之后，根据请求的性质，会执行三个回调函数之一（你将在本书后续的第 6 章中了解更多信息）。

高级应用可能希望根据其具体需求，提供自定义的解析和/或错误处理。

#### 错误处理

错误通过 `MGTwitterEngineDelegate` 接口进行处理。应用对象可以实现此接口，并在需要时将自己指定为委托来处理任何错误。

#### 使用 ShareKit

ShareKit 是另一个为 iOS 提供的工具，它让你从应用内部发布内容到 Twitter 变得轻而易举。我们也鼓励你探索 ShareKit 能为你的应用做些什么。

### 总结

本书的其余部分将致力于使用 Twitter 和 Facebook 来编码和设计应用。我们会尽量公平地讨论两者，但我们现在就提醒你，Facebook API（一般来讲）更容易使用、更全面且更新更及时。在应用中集成 Twitter 功能比较繁琐（有时也令人恼火）；然而，由于 Twitter API 项目在 App Store 上通常比 Facebook API 项目更成功，我们认为多花些精力可能是值得的。

