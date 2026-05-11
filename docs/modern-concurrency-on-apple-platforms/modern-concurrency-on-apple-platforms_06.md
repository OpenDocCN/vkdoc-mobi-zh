# 2. 初识 async/await

上一章我们探讨了编写并发与多线程代码时常遇到的问题，也了解了苹果为开发者提供的多种方案——从难以正确使用的底层替代方案，到更易用但灵活性稍低的高层替代方案。

本书剩余部分将聚焦于新的并发体系，我们将其简称为 `async/await`。本章将探索这套新体系的基础构建块——`async` 和 `await` 关键字。目前我们暂不深入多线程细节。当理解如何运用这些关键字，以及它们与你已熟悉的 SDK 中 API 的交互方式后，你便掌握了学习这套新体系所有功能的正确工具。但在继续之前，让我们先多聊聊闭包与回调（在 Objective-C 中称为 blocks）。

## 闭包在并发与多线程中的定位

在学习本章时，我建议你获取示例项目“第 2 章 - 社交媒体应用”。首先，我们将讨论应用中那些基于闭包并发的现有代码，后续再将其改造为使用 `async/await`。

示例应用包含两个不同的视图。

第一个视图显示一个简单的按钮，点击后可通过生物识别进行身份验证，如图 2-1 所示。

![](img/532302_1_En_2_Fig1_HTML.jpg)

手机截图中显示时间、WiFi 信号、满电电池以及“Authenticate”（验证）字样。

**图 2-1** 社交媒体应用的入口点

第二个界面（图 2-2）在成功验证后显示个人资料视图：

![](img/532302_1_En_2_Fig2_HTML.jpg)

手机截图中显示时间、WiFi 信号、满电电池，以及一位名为 Andybanez 的男士头像，其关注者与被关注数均为零。

**图 2-2** 社交媒体应用的主个人资料视图

验证成功后，应用将调用两个不同的网络服务：一个用于获取用户资料，另一个用于获取其关注者与被关注者数量。

> **注意**：若在模拟器中运行此应用，需先注册 Face ID。操作方式：打开模拟器，进入“功能”菜单 ➤ FaceID，确保已勾选“已注册”选项，然后重新运行应用。

在撰写本文时，大多数涉及并发的应用都采用基于闭包的并发方式。打开 `UserAPI.swift` 文件，你将看到清单 2-1 中这再熟悉不过的代码。

```
class UserAPI {
    func fetchUserInfo(
        completionHandler: @escaping (_ userInfo: UserInfo?, _ error: Error?) -> Void
    ) {
        let url = URL(string: "https://www.andyibanez.com/fairesepages.github.io/books/async-await/user_profile.json")!
        let session = URLSession.shared
        let dataTask = session.dataTask(with: url) { data, response, error in
            if let error = error {
                completionHandler(nil, error)
            } else if let data = data {
                do {
                    let userInfo = try JSONDecoder().decode(UserInfo.self, from: data)
                    completionHandler(userInfo, nil)
                } catch {
                    completionHandler(nil, error)
                }
            }
        }
        dataTask.resume()
    }
    //...
}
```

**清单 2-1** 从服务器下载数据并解析以展示给用户

这段代码对新手来说可能令人望而生畏，但有经验的开发者能一眼看穿其逻辑。上述代码创建了一个 `URLSessionDataTask`，这是 Foundation 框架中用于通过 HTTP 从网络下载数据的对象。我们需要在新建的 `dataTask` 上调用 `resume()` 才能真正执行下载。任务完成后，我们检查是否有错误：若有，则调用函数的 `completionHandler`，不传递 `userInfo` 而仅传递一个 `Error`；若有数据，则尝试将其解析为 `UserInfo` 对象。若解析出错，同样向处理器传递错误；若成功，则获得所需结果，并用新下载的对象调用处理器。

你可能见过该函数的变体。例如，有些开发者会选择将此函数定义为接收两个闭包：一个仅接收 `userInfo` 对象，另一个仅接收错误，这样不同情况调用不同的闭包。这能消除可选类型，但如果存在无论成功失败都需要执行的公共代码，代码会变得更长。清单 2-2 展示了这种实现方式。

```
func fetchUserInfo(
    success: @escaping (_ userInfo: UserInfo) -> Void,
    failure: @escaping (_ error: Error) -> Void
) {
    //...
}
```

**清单 2-2** 处理完成处理器的另一种方式



