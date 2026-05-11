# 排版后的文本

为了保持本书篇幅简短，我们会采取一些捷径，例如强制解包可选值。请记住，始终要安全且负责任地处理可选值。

现在，我们想要下载这个用户信息并用它来更新一个变量。打开`UserProfileViewModel.swift`文件并找到`fetchUserInfo()`函数。列表 2-3 展示了调用点的样子。

```swift
private func fetchUserInfo() {
  let userApi = UserAPI()
  print("Beginning data download")
  print("Downloading User info")
  self.loadingStatus = .loading
  userApi.fetchUserInfo { userInfo, error in
    DispatchQueue.main.async {
      print("User info downloaded")
      if let error = error {
        self.loadingStatus = .error(error)
      } else if let userInfo = userInfo {
        self.loadingStatus = .idle
        self.userInfo = userInfo
      }
    }
  }
  print("Ending function")
}
```
*列表 2-3 调用 fetchUserInfo 方法*

上述代码将调用我们`API`对象中的`fetchUserInfo(_)`函数。如果成功，它会从视图模型中设置一个`userInfo`变量，否则设置一个错误。

运行应用并检查打印语句。

新开发者可能期望控制台打印如下内容：

```
Beginning data download
Downloading User info
User info downloaded
Ending function
```

但实际情况并非如此，你会看到如下输出：

```
Beginning data download
Downloading User info
Ending function
User info downloaded
```

这是因为当并发任务启动时，线程仍在执行你的代码。它会在`fetchUserInfo`调用之前开始打印语句，并且由于数据下载是一个*并发*调用，`dataTask.resume`的执行被挂起，因此线程会在下载本身完成之前执行函数调用*之后*的内容。当下载完成时，`dataTask`中的内容会继续执行，在所有其他内容之后打印`User info downloaded`。假设你的数据下载速度快于线程其余部分的执行时间——这是不可能的，因为你期望网络调用比本地 CPU 调用更快——那么它有可能按预期顺序打印语句，但即便如此，也不能保证一定会这样。

现在，假设你想只在用户通过生物识别认证后才下载用户信息。你的函数将类似于列表 2-4。

```swift
func authenticateAndFetchProfile() {
  authenticateWithBiometrics { success in
    if success {
      self.isAuthenticated = true
      let userApi = UserAPI()
      userApi.fetchUserInfo { userInfo, error in
        DispatchQueue.main.async {
          if let error = error {
            self.loadingStatus = .error(error)
          } else if let userInfo = userInfo {
            self.loadingStatus = .idle
            self.userInfo = userInfo
          }
        }
      }
    }
  }
}
```
*列表 2-4 回调地狱*

由于我们依赖生物识别读取状态来决定是否下载个人资料，我们需要等待它响应，然后才能进行用户个人资料调用。一旦我们收到响应，我们的闭包就会执行，用户调用就会生效。

更糟糕的是，我们甚至还没有完成端点的消费。我们还需要消费下载关注者和粉丝数据的方法。`UserAPI.swift`文件中有一个方法`fetchFollowingAndFollowers(_)`，我们可以用它来实现这一点。列表 2-5 修改了`authenticateAndFetchProfile()`方法，以便在获取主个人资料后下载粉丝和关注用户。

```swift
func authenticateAndFetchProfile() {
  authenticateWithBiometrics { success in
    if success {
      self.isAuthenticated = true
      let userApi = UserAPI()
      userApi.fetchUserInfo { userInfo, error in
        DispatchQueue.main.async {
          if let error = error {
            self.loadingStatus = .error(error)
          } else if let userInfo = userInfo {
            self.userInfo = userInfo
            userApi.fetchFollowingAndFollowers { followingFollowers, error in
              if let error = error {
                self.loadingStatus = .error(error)
              } else {
                self.followersFollowing = followingFollowers
              }
            }
          }
        }
      }
    }
  }
}
```
*列表 2-5 更大的回调地狱*

你可能认为列表 2-5 中的代码是夸张的，但确实存在这样的代码库。有些开发者通过使用信号量的技巧来强制 API 调用一个接一个地被调用。其他开发者可能会选择使用`NSOperationQueue`来实现。有些人会使用 GCD。还有的人可能会觉得将其拆分为多个函数调用更实用，但如果某些操作依赖于其他操作，拆分为不同的函数调用会使代码难以追踪。

有了新的`async/await`系统，你将能够完全摒弃闭包来实现这个特定目的，并且能够编写更接近过程式编程的代码。它还将帮助你和你团队的其他成员为函数签名定义指导方针，从而避免像列表 2-1 和列表 2-2 的函数签名之间出现的不匹配问题。

## 开始使用 async/await

回想一下，在过程式编程中，代码是按从上到下的逻辑顺序执行的。如果你回到列表 2-3，你会意识到由于`print`语句被调用的顺序，这里的情况并非如此。

如果我们不是通过闭包传递数据，而是通过函数的标准`return`语句来传递数据，那岂不是很好？这正是新系统允许我们做的。通过摒弃闭包，你可以编写自然的代码，从而更容易地表达你的意图，使未来的开发者（包括未来的你自己）能够更清晰地理解代码库的功能。

以过程式思维来考虑，我们可以将社交媒体应用的任务总结为一个三步程序：

1.  `首先使用生物识别认证用户。`
2.  `然后获取他们的用户个人资料。`
3.  `最后，获取用户的粉丝和关注用户。`

使用闭包，这是意大利面条式代码。使用`async/await`，我们可以几乎原样地编写这三个语句。我们将把基于闭包的代码转换为`async/await`，但在开始之前，让我们先学习一些理论知识。



### `async` 关键字

该系统的核心是 `async` 和 `await` 关键字。理解如何使用它们对于使用系统的其余部分至关重要。

可以异步运行，或者可以与其他函数同时运行的函数，在其返回类型前有 `async` 关键字。这将告诉编译器和其他程序员你的函数是异步的。代码清单 2-6 展示了 `async` 声明的样子。

```
func fetchUserInfo() async throws -> UserInfo { /*...*/ }
代码清单 2-6
一个异步函数声明
```

代码清单 2-6 中的声明将是我们将基于闭包的代码迁移到 `async/await` 的基础。我们甚至还没写多少代码，就已经能看到一些好处了。完成处理程序完全消失了。如果你的函数可能抛出错误，就像我们最初的 `fetchUserInfo` 方法那样，你可以将 `async` 函数标记为 `throws`，这将使错误处理更加自然。最后，这个函数将返回一个 `UserInfo`，它不是可选项，这样你就不必解包该对象以及进行其他可能的检查。

要调用 `async` 函数，你需要位于并发上下文中。有几种创建此类上下文的方法：

-   `async` 函数的函数体本身就是一个并发上下文。如果一个函数没有被标记为 `async`，它就不能直接调用 `async` 函数。代码清单 2-7 将无法编译。

```
func authenticateAndFetchProfile() {
//...
let userProfile = try await fetchUserInfo()
//...
}
代码清单 2-7
尝试在异步上下文之外调用异步函数
```

将该函数标记为 `async` 将使其能够编译。代码清单 2-8 通过修复代码使其能够编译。

```
func authenticateAndFetchProfile() async {
//...
let userProfile = try await fetchUserInfo()
//...
}
代码清单 2-8
使用 async 关键字创建异步上下文
```

但如果我们没有并发上下文会怎样？在你的调用层级中的某个位置，你可能会发现自己处于完全没有并发上下文来运行代码的情况。幸运的是，另一种创建异步上下文的方法将帮助我们处理这个问题。

-   通过创建一个 `Task` 对象。`Task` 是一个非常强大的对象，它允许我们在后面进行多线程操作。我们还能对它们进行一些控制，例如取消它们。我们将在后面的章节中探讨这些特性。现在，我们只需使用 `Task` 来创建一个并发上下文。`Task` 有一个带有闭包的初始化器，而该闭包本身就是一个并发上下文。代码清单 2-9 展示了如何使用 `Task`。

```
func authenticateAndFetchProfile() {
Task {
//...
let userProfile = try await fetchUserInfo()
//...
}
}
代码清单 2-9
使用 Task 创建异步上下文
```

即使 `authenticateAndFetchProfile()` 没有被标记为 `async`，在其中创建一个 `Task` 也能允许我们运行异步函数。

### `await` 关键字

每次调用异步函数时，都必须在某个点上配对一个 `await` 关键字。`await` 本身具有特殊的语义，它不仅仅是调用 `async` 函数的语法。

如果在代码执行过程中遇到 `await` 关键字，你的程序可以选择挂起当前正在执行的函数。因此，`await` 关键字通常也被称为**挂起点**。这是系统会做出的决定，你无法控制。当我们说它可能会挂起时，意味着当前执行将会暂停，而该 `async` 函数将在其他地方继续执行。如果你的当前函数被挂起，其中的代码将不会被执行。

为了更好地理解挂起，代码清单 2-10 将修改代码清单 2-8，在被等待的调用前后添加一些 `print` 语句。

```
func authenticateAndFetchProfile() async {
//...
print("正在获取用户信息")
let userProfile = try await fetchUserInfo()
print("用户信息已获取")
//...
}
代码清单 2-10
添加 print 语句将使其按顺序打印内容
```

代码清单 2-10 将打印 `正在获取用户信息`，过一会儿后，它将打印 `用户信息已获取`。这实际上就是这些语句编写的顺序，并且你无需涉足任何闭包或其他东西就能得到该输出。太好了！

在发生挂起的 `await` 调用期间发生的情况是，你将控制权交还给系统，因此你无法控制何时能收回控制权。调用 `fetchUserInfo()` 的同一个线程将可以自由地去做其他工作，并且这些其他工作与你无关，因为它们由系统处理。它可能是你应用中的另一个任务，也可能超出你的领域。在控制权返回给你之前，你无法继续执行语句。因此，代码清单 2-10 中的代码将按照编写的顺序打印这些语句，但是打印 `用户信息已获取` 可能需要一点时间，因为你已将线程交由系统处理，而下载用户信息是一项耗时的任务。当用户信息下载完成后，`fetchUserInfo` 调用中会执行一个 `return` 语句，正是这个语句会将控制权返回给你。请注意，由系统决定何时将控制权交还给你，并且这不会在遇到 `return` 语句后立即发生。系统可能正在执行其他并发任务，在回到你的任务之前，它需要先处理好优先级。

总之，在遇到挂起点（`await` 关键字）后，如果系统选择挂起，那么在控制权交还给你之前，其后的任何代码都不会被执行。在挂起发生期间，系统会利用该线程去做其他工作。

在 `await` 调用之后发生的任何事情都是**延续**。我们需要继续执行在被挂起之前正在做的工作。这很重要，因为当我们收回控制权时，`await` 之后的代码中的语句可能会在不同的线程上运行。在代码清单 2-10 中，这意味着第一个 `print` 语句可能在一个线程上运行，而第二个 `print` 语句可能在一个完全不同的线程上运行。使用 `async/await` 并不能免除你在主线程上执行 UI 工作的责任，因此如果你需要更新 UI，你需要将该工作延期到主线程。在实践中，这是通过一个名为 `@MainActor` 的全局参与者来完成的。我们将在后面的章节中详细讨论全局参与者。

有了所有这些理论基础，我们现在可以动手实践了。我们将重写基于闭包的代码，改用 `async/await`。



### 使用 `async/await`

苹果所有基于闭包的并发 API 都提供了 `async/await` 版本，可在 iOS 15 及更高版本中使用。虽然使用 Xcode 13.3 可以在低至 iOS 13 的系统上使用 `async/await`，但苹果原生支持 `async/await` 的 API 仅适用于 iOS 15 及以上版本。

现在我们将开始把所有基于闭包的代码迁移到 `async/await`。你可以从“第 2 章 - 社交媒体应用 (Async-Await)”下载已完成的项目。

让我们从 `Shared ➤ API ➤ UserAPI.swift` 文件开始。

首先迁移 `fetchUserInfo(completionHandler:)`。因此，使用代码清单 2-11 中的签名创建新函数。

```
func fetchUserInfo() async throws -> UserInfo {
}
代码清单 2-11
fetchUserInfo 的 async/await 版本的基本签名
```

代码清单 2-11 中的函数可以标记为 `throws`。通常，如果你要建模可以抛出错误或返回有效值的代码，只需将方法标记为 `throws`，然后将结果类型分配为调用成功时返回的对象。本质上，原完成处理程序闭包的内容已被重新组织到函数签名中。

你可以立即看出 `async/await` 帮助我们编写更具语义性的代码。在 Swift 中，我们可以从函数中抛出错误，但令人惊讶的是，许多开发者并不这样编写代码。这是因为大多数非琐碎的应用程序本质上是并发的（例如，使用网络调用），而且由于我们无法以惯用方式处理闭包中的错误，唯一的解决方案是将错误作为闭包的一部分返回。有了 `async/await`，我们的函数签名更具可读性，因此也更具自文档性。

现在，我们将使用苹果在 iOS 15 中提供的第一个 `async/await` API。

所有返回数据的 `URLSession` 方法都有 `async/await` 变体。在代码清单 2-12 中，我们将实现返回用户信息 JSON 为 `Data` 的逻辑。

```
func fetchUserInfo() async throws -> UserInfo {
let url = URL(string: "https://www.andyibanez.com/fairesepages.github.io/books/async-await/user_profile.json")!
let session = URLSession.shared
let (userInfoData, response) = try await session.data(from: url)
}
代码清单 2-12
使用 async/await 进行网络调用
```

目前一切顺利，但这段代码还不能编译，因为我们没有返回任何内容。我希望你注意到，调用 `session.data(from:)` 根本不需要闭包。你也不再需要调用 `resume()`——一旦你调用 `data(from:)` 的异步版本，任务就会开始。

现在我们需要将 JSON 解析为 API 消费者可以使用的对象。代码清单 2-13 将完成该方法，包括 JSON 解析并返回值。

```
func fetchUserInfo() async throws -> UserInfo {
let url = URL(string: "https://www.andyibanez.com/fairesepages.github.io/books/async-await/user_profile.json")!
let session = URLSession.shared
let (userInfoData, response) = try await session.data(from: url)
let userInfo = try JSONDecoder().decode(UserInfo.self, from: userInfoData)
return userInfo
}
代码清单 2-13
发起网络请求、解析结果 JSON 并通过 async/await 代码按顺序返回对象
```

就是这样。这就是 `fetchUserInfo` 完整的 `async` 版本。我们将一个 15 行的代码块缩减为仅 5 行（尽管在书中很难体会到！）。感谢代码清单 2-13，我们才能真正体会到 `async/await` 的优雅。

让我们借此机会，通过这个示例来总结一下目前所学的关于 `async/await` 的所有内容。

首先，我们将 `fetchUserInfo` 方法声明为 `async`。请注意，由于 Swift 支持方法重载，`fetchUserInfo()` 和 `fetchUserInfo(completionHandler:)` 可以共存。通过将函数标记为 `async`，我们创建了一个并发上下文，在此上下文中我们可以调用其他 `async` 函数，而调用我们函数的调用者本身也需要处于并发上下文中（要么他们自己也标记为 `async`，要么在 `Task {}` 调用中）。

当执行到达 `let (userInfoData, response) = try await session.data(from: url)` 这一行时，我们的代码被挂起，它正在执行的线程可以自由地去做其他工作——可能与我们的应用程序无关。得益于这种挂起，后续将数据转换为对象的行将不会执行，因为我们依赖于上一行的数据。当 `session.data(from:)` 完成下载内容后，它会通知系统准备好返回给调用者。系统最终会通过返回并赋值 `(userInfoData, response)` 变量来通知你的应用程序。系统返回并且这些变量有了值之后，代码将按照编写的顺序继续执行：解析 JSON，然后将该对象返回给调用者。这是最纯粹的过程式编程。

当然，代码也可能抛出错误。设备可能没有网络连接，或者服务器可能宕机。如果发生错误，你可以直观地预见到会发生什么，因为这与代码不是异步的情况完全相同：`session.data(from:)` 会抛出一个错误（符合 `Error` 的对象，因此我们将函数标记为 `throws`），并且所有后续行都不会执行。因此，在此示例中，调用者必须使用 `try`、`try?` 或 `try!` 调用 `fetchUserInfo()`（在此情况下，强烈不推荐使用 `try!`）。

如果你理解了以上所有内容，请继续阅读。理解 `async/await` 在最基本层面上的工作原理对于充分利用其所有特性至关重要，因此如果需要，请随意休息一下。

你可能注意到代码此时在以下行有一个警告：

```
let (userInfoData, response) = try await session.data(from: url)
```

`session.data(from:)` 返回一个元组，因此我们可以将其解构为两个不同的变量。由于你没有使用 `response` 变量，你可以将其替换为下划线（`_`），但我将其放在这里是为了展示，如果 API 有其他要求（例如需要检查标头，或以不同方式处理 HTTP 状态码），你可以使用这个类型为 `URLResponse` 的变量。你需要将其转换为 `HTTPURLResponse` 才能获得任何有意义的信息。

接下来，代码清单 2-14 是获取关注者/关注信息的方法的 `async/await` 版本。

```
func fetchFollowingAndFollowers() async throws -> FollowerFollowingInfo {
let url = URL(string: "https://www.andyibanez.com/fairesepages.github.io/books/async-await/user_followers.json")!
let session = URLSession.shared
let (data, _) = try await session.data(from: url)
let followingFollowers = try JSONDecoder().decode(FollowerFollowingInfo.self, from: data)
return followingFollowers
}
代码清单 2-14
获取关注者和正在关注用户的 async/await 版本方法
```

**注意**

有很好的方法可以抽象这些调用并避免代码复用，但这超出了本书的范围。

现在，让我们转到 `Shared ➤ Views ➤ UserProfileView ➤ UserProfileViewModel.swift`。

代码清单 2-15 首先将 `@MainActor` 属性添加到类中。

```
@MainActor
class UserProfileViewModel: ObservableObject {/*..*/}
代码清单 2-15
将 @MainActor 全局 Actor 添加到类声明中
```


好的，作为一名高级文档工程师和翻译员，我将严格按照注意事项和示例格式，将给定的英文文本翻译成中文。


我们之前简要讨论过全局 Actor，清单 2-15 首次在实际场景中使用了`@MainActor`全局 Actor。这将确保每当属性更新时，都会在主线程上执行。这一点很重要，因为 SwiftUI 使用 Observable 对象来更新 UI，因此更新它们需要在主线程上完成。

最简单的迁移方法是`authenticateWithBiometrics(completionHandler:)`，因此我们从这个方法开始。Apple 也提供了这些 API 的`async`版本。清单 2-16 展示了如何使用`authenticateWithBiometrics`的`async`版本。

```swift
private func authenticateWithBiometrics() async -> Bool {
    let context = LAContext()
    do {
        let success = try await context.evaluatePolicy(.deviceOwnerAuthenticationWithBiometrics, localizedReason: "To fetch your profile")
        return success
    } catch {
        return false
    }
}
```

这个调用可能会抛出错误，但对于我们 API 的使用者来说，可能没有必要关心这些细节。在这种情况下，如果发生任何错误，将在内部处理，并向调用者返回`false`。因此，我们不会将该方法标记为`throws`。

清单 2-17 将创建`fetchUserInfo`的`async`版本。

```swift
private func fetchUserInfo() async {
    do {
        let userApi = UserAPI()
        self.loadingStatus = .loading
        print("Beginning data download")
        print("Downloading User info")
        self.userInfo = try await userApi.fetchUserInfo()
        print("User info downloaded")
        self.loadingStatus = .idle
    } catch {
        self.loadingStatus = .error(error)
    }
    print("Ending function")
}
```

清单 2-17 是一个非常有趣的代码片段。我在这里保留 print 语句只是为了表明它们会按照你编写的顺序打印出来。再次向过程式编程致以微小的敬意。

同样值得注意的是，我们不需要做任何特殊操作就能回到主线程。`self.userInfo`和`self.loadingStatus`的赋值在主线程上运行非常重要，因为它们直接与 UI 相关联。因为我们将类标记为`@MainActor`，所以所有对属性的更新都将在主线程上运行。如果你没有将整个类标记为`@MainActor`，也可以仅将此方法标记为`@MainActor`。

让我们继续往下进行。我们现在将编写`fetchFollowingFollowers()`的`async`版本。清单 2-18 提供了这样的实现。

```swift
private func fetchFollowingFollowers() async {
    do {
        let userApi = UserAPI()
        self.loadingStatus = .loading
        self.followingFollowersInfo = try await userApi.fetchFollowingAndFollowers()
        self.loadingStatus = .idle
    } catch {
        self.loadingStatus = .error(error)
    }
}
```

这段代码与`fetchUserInfo`非常相似，因此这里不应该有什么新内容。

最后，我们将创建`authenticateAndFetchProfile()`的`async`版本，它为我们一步步完成所有操作：进行身份验证，如果成功，则获取用户配置文件，然后获取关注者和正在关注的信息。清单 2-19 提供了其初始实现。

```swift
func authenticateAndFetchProfile() async {
    isAuthenticated = await authenticateWithBiometrics()
    if isAuthenticated {
        await fetchUserInfo()
        await fetchFollowingFollowers()
    }
}
```

这个新方法比原始的`authenticateAndFetchProfile(completionHandler:)`方法短得多。你不仅能欣赏到它的简洁，而且缺少 completion handler 再次使我们的代码非常易于阅读。我们也不再需要那些烦人的`DispatchQueue.main.async`调用了，这些调用曾使我们的代码嵌套更深，因为`@MainActor`将确保触发 UI 更新的属性更改在主线程上运行。

我们的方法现在是`async`的，但这意味着在 SwiftUI 中，当按下“Authenticate”按钮时，我们将需要使用`Task`。打开`Shared ➤ Views ➤ UserProfileView ➤ UserProfileView.swift`，找到“Authenticate”按钮（第 52 行），并将对`viewModel.authenticateAndFetchProfile()`的调用包装在一个`Task`中。清单 2-20 展示了如何实现。

```swift
Button {
    Task {
        await viewModel.authenticateAndFetchProfile()
    }
} label: {
    if viewModel.availableBiometricType == .none {
        Text("Authenticate")
    } else {
        Label {
            Text("Authenticate")
        } icon: {
            Image(systemName: imageNameForBiometricType(viewModel.availableBiometricType))
        }
    }
}
.buttonStyle(.borderedProminent)
```

由于 SwiftUI 本身不在异步上下文中运行，因此为每个需要异步功能的地方创建一个异步上下文非常重要。在这种情况下，`authenticateAndFetchProfile()`是一个异步任务。

我们快完成了，但在结束之前，你可能已经注意到我们的`fetchUserProfile()`和`fetchFollowingFollowers()`的`async`版本非常相似。在这种特定情况下，我们可以通过完全删除这两个函数并将其内容移动到`authenticateAndFetchProfile()`中来进一步减少代码。清单 2-21 为`authenticateAndFetchProfile()`提供了一个新版本，该版本会比原始版本稍长，但对我们来说更有意义，因为它消除了两个多余的方法。

```swift
func authenticateAndFetchProfile() async {
    isAuthenticated = await authenticateWithBiometrics()
    if isAuthenticated {
        do {
            let userApi = UserAPI()
            loadingStatus = .loading
            userInfo = try await userApi.fetchUserInfo()
            followingFollowersInfo = try await userApi.fetchFollowingAndFollowers()
            self.loadingStatus = .idle
        } catch {
            loadingStatus = .error(error)
        }
    }
}
```

缺少 completion handler 使我们能够用一行代码获取用户配置文件以及关注者和正在关注的数据。正因为如此，我们可以删除为此目的而创建的函数。

我们完成了！我们已经将所有基于 completion handler 的代码转换为`async/await`。请记住，在本例中，这样做非常容易，因为我们的应用很小。有一些技术可以用于加速更大项目中的迁移，我们将在下一章讨论*Continuations*时探讨这些技术。

### async get 属性

`async/await`不仅限于函数调用。你还可以在只读计算属性中使用它。清单 2-22 展示了如何实现，但我们不会在我们的社交媒体应用中使用它。

```swift
var userInfo: UserInfo {
    get async throws {
        let url = URL(string: "https://www.andyibanez.com/fairesepages.github.io/books/async-await/user_profile.json")!
        let session = URLSession.shared
        let (userInfoData, _) = try await session.data(from: url)
        let userInfo = try JSONDecoder().decode(UserInfo.self, from: userInfoData)
        return userInfo
    }
}
```

在清单 2-22 中，我们声明了一个既可以是`async`又可以是`throws`的属性。通常，我不建议你使用这个特性，除非你实现某种缓存机制。否则，你 API 的使用者访问该属性的频率可能会远高于预期，这将导致他们的应用出现显著的延迟。在某些情况下，避免使用缓存机制可能是合理的，但你需要在具体情况下进行分析。



### iOS 13 和 iOS 14 中的 async/await

此前，我们提到虽然 `async/await` 机制在 iOS 13 和 iOS 14 上能够运行，但苹果并没有提供任何以此标识的 API。这意味着，仔细想想，在这些版本中你其实无法对任何东西使用 `async/await`。你可能会疑惑，既然苹果在这些版本中根本没有提供任何 `async/await` 的 API，那么支持这个功能又有什么意义呢？如果你需要支持这些版本，我将在下一章讨论*续体*时，教你一个小技巧，让你能够获得那些方法的 `async/await` 版本。

### 本章小结

在本章中，你学习了如何使用 `async/await` 机制的基本构建块，也就是该机制名称所源自的那些关键字。为了充分运用这个新系统的所有特性，理解 `async` 和 `await` 如何协同工作至关重要。

我们还解释了基于闭包的并发与 `async/await` 之间的主要区别。我们展示了这个机制如何与挂起操作配合，以及这些挂起操作的行为与我们通常在完成处理器或闭包中看到的有何不同。

## 练习

**编程练习**

下载“Chapter 2 Exercise”项目。这是一个 HealthKit 项目，它从“健康”App 读取数据并显示在应用中。该应用目前使用的是基于闭包的调用。完成此练习无需深入了解 HealthKit。

1.  重构 `HealthManager` 类（位于 `HealthManager.swift` 文件中），使其使用 `async/await` 替代基于闭包的调用。完成后删除基于闭包的方法。提示：iOS 15 提供了一个新的 `HKSampleQueryDescriptor` 对象，它支持 Swift 并发。同时，你需要使用一个新的 `HKSamplePredicate<HKSample>` 对象来实现检索逻辑。

2.  重构 `ContentViewViewModel`（位于 `ContentViewViewModel.swift` 文件中），使其使用你的 `async/await` 方法替代基于闭包的调用。

    “Chapter 2 Exercise - Solved async-await”项目中包含了答案。

**注意**
我建议你在真机上运行此练习。虽然在模拟器上也能运行，但你需要在模拟器的“健康”App 中手动添加数据。而你的 iPhone 很可能已经存有这些信息。

