# 4. 结构化并发

到目前为止，我们已经详细讨论了`async/await`是什么以及它是如何工作的。我们已经看到这个新工具如何帮助我们编写类似于过程式编程的结构化代码，因为我们可以摆脱其他类型的回调（如闭包和委托），这些回调破坏了代码的线性可读性。但我们已经看到，所有这些代码实际上并没有同时、并发或以多线程方式运行。

考虑我们在第 2 章（清单 4-1）中编写的社交媒体应用中的`authenticateAndFetchProfile`方法的异步版本。

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
*清单 4-1 `authenticateAndFetchProfile`的异步版本*

这个方法有三个异步调用，但并非所有调用同时执行。事实上，我们*需要*生物识别操作完成，才能下载任何要显示的信息。下载个人资料信息和关注者*依赖于*身份验证成功。目前，该函数经过三个基本步骤：

1.  使用生物识别进行身份验证。

2.  下载个人资料信息。

3.  下载关注者和关注用户信息。

这两个下载操作都依赖于身份验证，但除此之外，它们彼此完全独立，如果愿意，我们可以同时执行它们。在本章中，我们将开始同时执行多个任务。

注意

在第 1 章中，我们将并发定义为“一个线程（或你的程序）同时处理多件事情的能力。”这严格来说并不意味着同时做多件事，通常，当人们想到并发时，他们真正想到的是多线程或并行。然而，为了与社区赋予该词的含义保持一致，从这一点开始，当我们谈论并发时，我们指的是同时执行多个任务。



## 理解结构化并发

结构化并发遵循与结构化编程相同的理念。其核心是按照代码预期执行的顺序来编写代码，并看到可预测（或至少半可预测）的结果。与基于闭包的代码不同，变量具有明确定义的作用域，你可以期望代码不会跳转到其他破坏代码阅读流程的地方。这也让你无需操心其他细节，比如内存管理（闭包中捕获的变量会发生什么？闭包能否被持有？`weak self`？嗯？）。

而以上这些还仅仅是思考那些非并发的基于闭包的调用时的情况。你能想象你的代码中包含多个基于闭包的并发调用，其中大量独立代码同时运行的情形吗？

有了结构化并发，我们可以让多个代码路径同时运行，并得到可预测的结果，而无需考虑那些在多个基于闭包的调用之间跳转的迷宫般的代码。

新的 `async/await` 系统提供了两种实现结构化并发的方式：`async let` 构造体和任务组。

### `async let` 构造体

也称为 *async let 绑定*。用这段代码创建你的第一个并发片段简单得令人难以置信。如果你知道如何用 `let` 创建一个常量，那么你就能使用 `async let`。要并发运行代码，你只需要在 `let` 声明前加上 `async` 关键字。声明的其余部分看起来就像普通的 Swift 变量初始化。请注意，要执行此操作，你必须处于异步上下文中——要么在 `Task` 内部，要么在标记为 `async` 的函数内。当你的代码在 `async let` 声明中运行时，赋值之后的所有内容都将在其他地方执行（被启动的函数和方法可能会在不同的线程中运行），并且这些任务将并发运行。`async let` 任务作为创建它们的任务的子任务被创建。

说到这里，我们可以对代码清单 4-1 做一些修改。我们之前提到过，可以同时获取个人资料信息和关注者及关注用户的数据，因为它们彼此完全独立。我们将创建一个不同的函数来说明 `async let` 是如何工作的。代码清单 4-2 中的函数创建了一个新函数 `fetchData`，它将并发执行两次网络调用来填充 `UserProfileView` 屏幕。

```swift
func fetchData() async throws -> (userInfo: UserInfo, followingFollowersInfo: FollowerFollowingInfo) {
    let userApi = UserAPI()
    async let userInfo = userApi.fetchUserInfo()
    async let followers = userApi.fetchFollowingAndFollowers()
    return (try await userInfo, try await followers)
}
```

*代码清单 4-2 — 使用 `async let` 并发运行代码*

如果你在 `async let` 调用之间插入打印语句，你会看到它们被立即调用。`userInfo` 和 `followers` 会同时运行，但代码在即将返回之前会暂停。回想一下，`await` 关键字是一个挂起点，这意味着我们的函数将暂停，直到 `fetchUserInfo` 和 `fetchFollowingAndFollowers` 都返回或其中任何一个抛出错误。

你可能还记得，在第 2 章中，我们说过每个 `async` 调用都必须配对一个 `await`。但我们从未说过 `await` 必须在同一行。使用 `async let` 时，当你需要作为 `async let` 一部分赋值的变量的值时，才需要使用 `await` 关键字。因此，如果你的 `async let` 调用返回一个值供另一个函数使用，你就在调用点处 `await`。如果你需要在函数内部并发获取多个值，你可以在 `return` 语句处 `await`。`async` 调用必须与 `await` 关键字配对的规则并没有被违反。

使用 `async let` 非常有用，你可以在任何地方使用 `await` 关键字，无论是在单个变量声明中，在等待数组元素时，在返回元组之前（就像我们在代码清单 4-2 中所做的那样），等等。

如果你愿意，可以将函数最后一行代码 `return (try await userInfo, try await followers)` 改写为 `return try await (userInfo, followers)`，这样可以少写一些代码。选择哪种方式纯粹是风格问题。

代码清单 4-3 展示了使用了新的 `fetchData()` 方法的 `authenticateAndFetchProfile()` 方法所做的修改：

```swift
func authenticateAndFetchProfile() async {
    isAuthenticated = await authenticateWithBiometrics()
    if isAuthenticated {
        do {
            loadingStatus = .loading
            let data = try await fetchData()
            userInfo = data.userInfo
            followingFollowersInfo = data.followingFollowersInfo
            self.loadingStatus = .idle
        } catch {
            loadingStatus = .error(error)
        }
    }
}
```

*代码清单 4-3 — 对 `authenticateAndFetchProfile()` 的修改*

现在我们调用了 `fetchData` 方法，它将返回一个元组。请注意，我们仍然需要使用 `await` 来调用它，因为即使其内部的逻辑可能包含 `async let` 调用，这个方法本身的签名仍然是 `async` 的。这个方法只会在其内部的两个 `async let` 任务都完成后才返回。也就是说，你仍然可以用 `async let` 来调用 `fetchData`，以使其与其他任务并发运行。

> **注意**
> 虽然我们使用 `async let` 构造体来明确声明我们并发运行任务的意图，但最终决定是否真正并发运行你的代码，或者为多个调用重用另一个线程，仍然取决于系统。也就是说，系统可能会决定将 `fetchUserInfo` 和 `fetchFollowingAndFollowers` 都运行在一个不同但共享的线程中，本质上使它们串行运行在非主线程上。

如果你不想自己动手修改，可以下载“第 2 章 - 社交媒体应用 (Async-Await)(`async let`)”示例项目来查看实际运行的代码。

### 任务组

当你明确知道某项任务涉及的并发数量时，`async let` 构造体是理想的选择。在我们的社交媒体应用中，我们知道需要获取个人资料信息和关注者信息——恰好是两个可以同时运行的任务。但有时你可能不知道完成应用的某个特定功能所需的并发数量。想象一下，你有一个允许用户下载动态数量图片的应用，或者你想同时对可变数量的图片应用滤镜。在这些情况下，我们可能需要一个任务、两个任务、300 个任务，甚至更多。针对这些情况，`async/await` 系统为我们提供了任务组。

任务组是一种结构化并发工具，它允许我们同时运行未知数量（可能）的并发任务，前提是这些任务是相关的。如果你的任务太多，Swift 不会同时运行所有任务，它会优先处理任务，并尽可能多地并发运行它们。



#### 任务组实战

要创建任务组，你可以使用 `withTaskGroup` 和 `withThrowingTaskGroup` 函数。与创建显式续体时类似，当你的组可以无异常完成时使用前者，而当组可能抛出错误时则使用后者。

我准备了一个简单的 SwiftUI 项目，它将执行以下任务：

1.  从服务器获取包含图片列表的 JSON 文件。
2.  在任务组中下载每个 URL，将图片存储到本地，然后渲染一个显示这些图片的 SwiftUI 视图。

由于我们在获取文件列表之前不知道需要下载多少张图片，因此可以使用任务组来尽可能多地并发下载。

**注**：计算机系统的线程数量并非无限。任务组会尽力并发下载所有图片，但也会进行优化和任务优先级排序。如果你同时处理大量并发任务，请注意任务组可能会将部分任务排队延后，它们不一定会同时执行所有任务。

##### 图片下载器项目

这个项目是一个 SwiftUI 应用，它从互联网下载图片列表，然后下载该列表中的所有图片。你可以下载“Chapter 4 - ImageDownloader”项目来查看。我们只会讨论源代码的关键部分，因为把所有代码都放在这里会占用大量篇幅。

运行后，应用会有两个界面。第一个界面如图 4-1 所示。

![](img/532302_1_En_4_Fig1_HTML.jpg)

截图展示了下载器应用的主界面，其中包含无线网络和满格电池图标，时间为 8:16，以及“无内容显示”文字和下方的“加载图片”按钮。

**图 4-1** – ImageDownloader 应用的主界面

点击“加载图片”按钮后，应用将开始下载图片。稍等片刻，所有图片都会显示在界面中。图 4-2 展示了应用加载部分内容后的界面截图。

![](img/532302_1_En_4_Fig2_HTML.jpg)

截图展示了三张猫咪照片，截图顶部显示有无线网络和电池图标。

**图 4-2** – 下载图片后的用户界面

**注**：希望你喜欢猫咪。

我们的 JSON 数据返回一个 `imageName-url` 配对数组。每张图片都有对应的名称和 URL。文件 `ServerImage.swift`（如代码清单 4-4 所示）展示了其内容：

```swift
import Foundation
struct ServerImage: Codable {
    let imageName: String
    let url: URL
}
```

**代码清单 4-4** – `ServerImage` 对象

`ImageManager.swift` 文件中的 `ImageManager` 类包含了所有核心逻辑。该类有两个重要的属性，如代码清单 4-5 所示。

```swift
private let imageListURL = URL(string: "https://www.andyibanez.com/fairesepages.github.io/books/async-await/taskgroups/imagelist_tif.json")!
private let urlSession = URLSession.shared
```

**代码清单 4-5** – `ImageDownloader` 类的主要属性

`imageListURL` 变量用于存储稍后下载图片列表的 URL。我们通过 `urlSession` 变量复用了相同的 URL 会话进行所有下载。

**注**：我故意创建了文件体积较大的图片（每张约 25 MB）。这是为了让你有足够的时间感受此项目的异步并发特性。如果你的网络不设限或速度较慢，可以将 `imageListURL` 变量中存储的 URL 中的“tif”部分替换为“jpg”。这会使程序使用体积更小的 JPEG 图片（约 3 MB）。

然后，`ImageManager` 类包含三个方法。代码清单 4-6 展示了用于下载并解析列表本身的函数。

```swift
func fetchImageList() async throws -> [ServerImage] {
    let (data, _) = try await urlSession.data(from: imageListURL)
    let urls = try jsonDecoder.decode([ServerImage].self, from: data)
    return urls
}
```

**代码清单 4-6** – `fetchImageList` 方法

我们再次体会到 `async/await` 的简洁性。请想象一下，如果使用基于闭包的调用，这个方法会是什么样子。

下载单张图片的方法更有趣一些，如代码清单 4-7 所示。

```swift
/// 返回本地图片路径。
func download(_ serverImage: ServerImage) async throws -> URL {
    let fileManager = FileManager.default
    let tmpDir = fileManager.temporaryDirectory
    let (downloadedImageUrl, _) = try await urlSession.download(from: serverImage.url)
    let destinationUrl = tmpDir.appendingPathComponent(serverImage.imageName)
    // 如果目标路径已存在，则删除它。
    try? fileManager.removeItem(at: destinationUrl)
    try fileManager.moveItem(at: downloadedImageUrl, to: destinationUrl)
    return destinationUrl
}
```

**代码清单 4-7** – 下载单张图片



首先，我们使用`urlSession`的`download`方法而非`data`方法，因为这些都是潜在的大文件。我们会将它们存储在路径（`destinationUrl`）中，并且在存储新文件之前，我们会移除该路径下的任何旧文件，以防止可能的干扰错误。在本地存储图片后，我们会将本地 URL 返回给调用者。

最后一个方法比较有趣。该方法会下载类型为`ServerImage`的对象数组中的所有图片，详见 4-8。

```
/// Downloads all the images in the array and returns an array of URLs to the local files
func download(serverImages: [ServerImage]) async throws -> [URL] {
    var urls: [URL] = []
    try await withThrowingTaskGroup(of: URL.self) { group in
        for image in serverImages {
            group.addTask(priority: .userInitiated) {
                let imageUrl = try await self.download(image)
                return imageUrl
            }
        }
        for try await imageUrl in group {
            urls.append(imageUrl)
        }
    }
    return urls
}
```
*代码清单 4-8 使用任务组同时下载多个图片的实现*

从高层次来看，该函数首先声明了一个 URL 变量。在这个变量中，我们将存储所有下载的图片的本地 URL。然后，我们创建一个任务组，这本身是一个可等待的操作，最后，我们返回这些 URL。再次强调，我们可以体会到`async/await`提供的线性特性。

当你使用`withTaskGroup`或`withThrowingTaskGroup`创建任务组时，需要指定该组将产生的数据类型。在本例中，我们的任务组产生 URL，因为`download(serverImages:)`方法会返回 URL。这两个任务组方法的闭包会为我们提供任务组本身——这里我们将其命名为`group`。

我们通过`addTask`方法向该组提交任务，一旦任务被提交，它们就会开始执行。在我们的`download(serverImages:)`函数中，我们遍历所有服务器图片，并在每次迭代中创建一个下载图片的任务。这会使任务组*在最佳情况下*同时执行所有任务。就像`async/await`系统中的其他工具一样，系统会对任务进行优先级排序，如果所有资源都可用，它会尝试并发执行所有任务，但如果你提交了大量任务，也不必对任务组未能同时执行所有任务感到惊讶。我们可以选择性地指定优先级。我们的方法使用了`.userInitiated`优先级，这是可用的最高优先级。我们将在下一章详细讨论优先级。

需要记住的一点是，添加到组中的任务可能不会按照提交的顺序执行。如果你想使用任务组，那么设计代码时务必使执行顺序无关紧要。

最后，在`withThrowingTaskGroup`内部，我们有另一个循环——一个可等待的`for`循环。这个循环将在下载过程中陆续接收所有 URL。它会逐个接收每个 URL，这样我们就不会使`URLs`变量处于无效状态。再次强调，不要期望按特定顺序获取 URL。`group`变量本身是一个集合，因此我们不仅可以用`for-in`遍历它，还可以使用高阶函数（如`filter`、`map`、`reduce`）来处理结果。是的，`for`循环可以使用`await`关键字来处理那些随时间发送值的代码。

请注意，如果任何图片下载任务发生错误，整个任务组都会抛出错误。请记住这一点，因为它可能导致意料之外的行为。如果你认为图片下载可能会失败，在这个特定项目中，为下载任务提供一个默认图片可能是个好主意。

## 总结

在本章中，我们学习了什么是结构化并发。我们了解到结构化并发借鉴了结构化编程的思想，这使得编写和理解并发代码变得非常有用。

我们探讨了两种创建结构化并发的方式：首先，`async let`绑定，当你确切知道需要多少并发时，这种方法非常有用。使用`async let`时，我们需要在需要变量值的时候使用`await`关键字。另一个结构化并发工具是任务组。当我们想同时运行多个任务，但不确定具体的并发数量时（例如同时从网络下载多张图片），或者当并发来自动态源（如用户输入）时，我们会使用任务组。

## 练习题

优化应用

下载“Chapter 4 Exercise – ForumApp”项目。该应用会消费几个 Web 服务并渲染以下界面：

![](img/532302_1_En_4_Figa_HTML.jpg) 一张截图显示了三张照片，前两张显示“加载图片错误，重试”，最后一张是一条粗直线，屏幕顶部是电池和 Wi-Fi 图标。 ![](img/532302_1_En_4_Figb_HTML.jpg) 一张截图显示了四张照片，其中`AndylbanezK`位于总帖子数、主题数和新帖子数下方，以及在线好友列表，如姓名和加入日期。

根据给定的源代码，执行以下操作：

1. 目前，该应用使用了`async/await`，但并未使用并发。请识别可以并发运行的代码并优化应用。

2. 获取“在线好友”的代码在`ForumAPI.swift`文件的`fetchProfiles(for:)`方法中硬编码为最多 3 个好友。请修复代码，使其使用任务组来并发获取所有用户资料，并且不再硬编码最大的资料获取数量。

你可以下载“Chapter 4 Exercise – Forum App (Solved)”来查看解决方案。

