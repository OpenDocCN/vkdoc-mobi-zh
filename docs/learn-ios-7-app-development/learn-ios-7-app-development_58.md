# 其他社交网络互动

在 `ColorModel` 中，我们只探索了社交网络的分享功能。如果你希望你的应用从用户的社交网络中获取信息，那完全是另一回事。其他类型的互动，比如获取用户 Facebook 好友的联系信息，则由 `SLRequest` 类处理。

`SLRequest` 的工作方式与 `NSURLRequest` 非常相似。你在第 3 章中使用 `NSURLRequest` 对象向 X.co 网址缩短服务发送了请求。要使用社交网络系统，你需要以几乎相同的方式准备一个 `SLRequest` 对象，提供服务的 URL、方法（`POST` 或 `GET`）以及任何必需的参数。你发送请求，并提供一个用于处理响应的代码块。

`SLRequest` 与 `NSURLRequest` 最大的区别在于 `account` 属性。该属性存储了一个 `ACAccount` 对象，用于描述用户在特定社交网络服务上的账户。这个属性使得 `SLRequest` 能够处理将你的请求发送到服务器所需的所有认证和加密工作。如果你曾经编写过任何 OAuth 处理代码，你会感激 `SLRequest` 为你承担的繁重工作。

因此，要使用其他社交网络功能，你必须准备以下内容：

*   服务类型
*   服务 URL
*   请求方法（`POST`、`GET`、`DELETE`）
*   请求参数字典
*   用户的 `ACAccount` 对象

服务类型是 `SLServiceTypeFacebook`、`SLServiceTypeSinaWeibo`、`SLServiceTencentWeibo` 或 `SLServiceTypeTwitter` 之一。URL、方法和参数字典取决于你发出的请求类型。有关这些细节，请查阅特定服务的开发者文档。表 13-1 列出了一些可供开始阅读的网址。

**表 13-1.** 社交服务开发者文档

| 社交服务 | URL |
| --- | --- |
| Facebook | [`https://developers.facebook.com/docs/`](https://developers.facebook.com/docs/) |
| 新浪微博 | [`http://open.weibo.com/wiki/`](http://open.weibo.com/wiki/%20) |
| 腾讯微博 | [`http://dev.t.qq.com/`](http://dev.t.qq.com/) |
| Twitter | [`https://dev.twitter.com/docs`](https://dev.twitter.com/docs) |

最后，你需要用户的 `ACAccount` 对象。账户和登录信息由 iOS 为你的应用维护，因此你的应用只需请求即可。用户是否希望授权你的应用使用他们的账户，或者他们是否需要登录，这一切都由系统为你处理。

获取账户对象的基本步骤如下：

1.  创建一个 `ACAccountStore` 对象的实例。
2.  向账户存储发送 `-accountTypeWithAccountTypeIdentifier:` 消息，以获取你感兴趣服务的 `ACAccountType` 对象。`ACAccountType` 对象是获取用户在特定服务上账户的关键。
3.  最后，向账户存储发送 `-requestAccessToAccountsWithType:` 消息。如果成功（且被允许），你的应用将收到该用户的一个 `ACAccount` 对象数组。

像 Facebook 这样的服务允许 iOS 用户一次只能登录一个账户。而 Twitter 则允许用户同时连接多个账户。你的应用需要决定是使用所有账户对象、选定的对象，还是仅使用一个。一旦你有了 `ACAccount` 对象，用它来设置 `SLRequest` 的 `account` 属性，你就准备好进行社交互动了！

## 总结

你已经学会了如何为你的应用添加另一个炫酷的功能，让用户能够与世界各地的朋友和家人联系并分享内容——而且只需要少量代码就能实现。你还学会了如何针对特定服务定制内容，或排除某些服务。如果你想更精细地控制应用提供哪些服务，你学会了如何使用 `SLComposeViewController` 创建特定的分享界面，以及如何使用 `SLRequest` 类，它为无限的社交网络集成提供了通道。

在此过程中，你还获得了一些实际经验：在屏幕外的图形上下文中进行绘制，以及使用 Xcode 的重构工具。重构命令包含一套强大的代码维护工具。如果你计划重命名或移动某个方法或属性，你应该与重构工具以及其他全局编辑命令成为朋友。要了解更多信息，请在 Xcode 的“文档和 API 参考”窗口中搜索“Make Projectwide Changes”。

向朋友和同事分享内容并非 iOS 应用通信的唯一方式。在第 3 章中，你编写了一个使用互联网网址缩短服务的应用。在下一章中，你将编写一个通过 Wi-Fi 或蓝牙直接与其他 iOS 设备通信的应用。



