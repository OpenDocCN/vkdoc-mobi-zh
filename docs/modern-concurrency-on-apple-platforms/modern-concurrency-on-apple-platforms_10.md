# 5. 非结构化并发

在上一章中，我们详细讨论了结构化并发。我们了解到结构化并发遵循与结构化编程相同的理念。与结构化并发自然对应的是非结构化并发。

非结构化并发以牺牲少量易用性为代价，提供了更多的灵活性。在使用非结构化并发时，你的代码不一定会遵循我们喜欢的自上而下的结构。幸运的是，使用非结构化并发仍然非常容易，其灵活性也不会显著增加程序的复杂性。

为了使用非结构化并发，我们使用一个我们已经了解的对象：`Task`。

## 深入理解 Task

`Task`是一个有用的对象，它允许我们执行两项重要操作：

1.  使用其初始化器创建任务的那一刻，它会异步启动闭包中的代码。
2.  它为我们创建一个并发上下文，使我们能够以调用`async`方法的方式使用并发。



### 创建任务

`Task` 是一个可以存储值、错误、两者皆存储或两者皆不存储的对象。要创建一个异步、无结构化任务，我们调用 `Task` 并使用一个闭包（即我们想要异步运行的代码）对其进行初始化。我们在本书中一直在持续这样做。例如，代码清单 5-1 是来自第 2 章的一个小片段，它使用 `Task` 调用某个 `async` 方法。

```
func authenticateAndFetchProfile() {
    Task {
        //...
        let userProfile = try await fetchUserInfo()
        //...
    }
}
代码清单 5-1
使用 Task 调用异步代码
```

在代码清单 5-1 中，`fetchUserInfo()` 方法在 `authenticateAndFetchProfile()` 方法被调用时立即执行。你无法决定 `Task` 内部代码何时执行。它总是在初始化后立即执行。

当你想使用 `Task` 进行并发操作时，你拥有的第一个灵活性是指定优先级。优先级类型为 `Task.Priority`，与你在使用任务组时可以使用的类型相同。代码清单 5-2 展示了在使用新的并发系统时你可以使用的优先级选项。

```
static let background: TaskPriority
static let high: TaskPriority
static let low: TaskPriority
static var medium: TaskPriority
static let userInitiated: TaskPriority
static let utility: TaskPriority
代码清单 5-2
可用的优先级选项
```

`Task` 是一个对象，因此你可以将其存储在一个变量中。这也是“无结构化并发”这一名称的部分由来。你可以有多个 `Task` 对象，将它们存储在变量中，甚至存储在集合中。无结构化并发能做到而结构化并发做不到的一件事就是取消任务。任务取消是一个重要主题，本章在讨论任务树时会进一步探讨。现在，请记住你可以通过调用 `cancel()` 方法来取消任务。

代码清单 5-3 展示了如何声明一个任务、将其存入变量并在后续取消它。

```
func authenticate() {
    let authenticationTask = Task {
        let context = LAContext()
        let policy = LAPolicy.deviceOwnerAuthenticationWithBiometrics
        return try await context.evaluatePolicy(policy, localizedReason: "To log in")
    }
    authenticationTask.cancel()
}
代码清单 5-3
创建一个 Task，并在之后显式取消它
```

我们创建了一个类型为 `Task<Bool, Error>` 的变量。这是因为 `context.evaluatePolicy(_:localizedReason:)` 返回一个 `Bool` 值，并且可能抛出错误。如果我们需要继续执行某些工作，可以对 `Task` 的 value 进行 await，如代码清单 5-4 所示。

```
Task {
    let authenticationSuccessful = try await authenticationTask.value
    if authenticationSuccessful {
        // 身份验证成功
    }
}
代码清单 5-4
等待 Task 的值
```

我们需要使用 `try await`，因为我们的任务可能返回一个值并抛出错误。注意这个新创建的 `Task` 中缺少了 `do-catch` 语句。这段代码会隐式创建一个类型为 `Task<(), Error>` 的任务，因为它不返回任何内容，但可能抛出错误（由 `context.evaluatePolicy` 抛出的错误）。任务不需要显式的 `do-catch` 代码块，因为错误会被闭包自身捕获，但如果在你的应用程序上下文中这样做有意义，也可以添加这些语句。

当你使用 `Task {}` 创建一个新任务时，它会继承生成它的任务的上下文信息。继承的信息包括任务运行的参与者（如果你在 `@MainActor` 上运行的内容中生成一个 `Task`，那么该任务也将在 `@MainActor` 上运行）以及优先级。如果你新生成的 `Task` 是一个根任务（即它创建了一个异步上下文，但本身并非在异步上下文中运行），它将继承其所在任何线程的参与者。

### 无结构化并发实战

既然你对无结构化并发有了更好的理解，并且终于了解了 `Task` 对象是什么，我们可以开始编写一些示例代码，以便更好地理解这一切是如何工作的。

我们将处理并修改上一章创建的项目——ImageDownloader 项目。该项目运行得很好，但有两个主要问题我想解决：

1. 当应用启动时，它会在屏幕中央显示一个 `ProgressView`，直到所有图像下载完成。这意味着如果我们有很多图像，或者它们**非常大**，`ProgressView` 将会显示很长时间。一旦图像下载完成，视图会刷新并一次性显示所有图像。

2. 无法保证图像的显示顺序。图像会按照下载完成的先后顺序添加到数组中。这本身无害，但如果你有一个按时间顺序显示元素的应用程序，这就很重要了。

我们将修改原始项目并解决这两个问题。如果你想直接下载修改后的版本，可以自由下载“第 4 章 - ImageDownloaderUnstructured”项目。


好的，作为一名高级文档工程师和翻译员，我将严格遵循您的注意事项和示例，将给定的英文文本翻译成中文。


#### 修改 `ImageManager` 类

首先，将代码清单 5-5 中的代码添加到文件顶部。

```
enum ImageDownloadStatus {
case downloading(_ task: Task)
case error(_ error: Error)
case downloaded(_ localImageURL: URL)
}
struct ImageDownload: Identifiable {
let id: UUID = UUID()
let status: ImageDownloadStatus
}
代码清单 5-5
附加对象
```

目前，`ContentView` 从 `ContentViewViewModel` 读取 URL 数组，并将其显示在 UI 中。我们将对此进行更改，让 `ImageManager` 成为图像的实际数据源。我们还将让视图读取 `ImageDownload` 对象，而不是 URL 对象。这将帮助我们根据图像的下载状态（`ImageDownloadStatus` 枚举）显示不同的 UI。我们需要让 `ImageDownload` 遵循 `Identifiable` 协议，以便它能与 SwiftUI 良好配合。

此外，由于 `ImageManager` 类现在将成为 SwiftUI 的数据源，因此需要将其标记为 `@MainActor`，如代码清单 5-6 所示。

```
@MainActor
class ImageManager: ObservableObject {
代码清单 5-6
将 ImageManager 标记为 @MainActor
```

现在，转到 `TaskGroupsApp.swift` 文件，并修改其中 `ImageManager` 类变量的声明，以添加 `@MainActor` 属性，如代码清单 5-7 所示。

```
@MainActor
fileprivate let imageManager = ImageManager()
代码清单 5-7
将 imageManager 变量标记为 @MainActor
```

回到 `ImageManager.swift` 文件，这次将 `images` 变量的类型修改为 `[ImageDownload]`。同时，添加 `@Published` 属性。这两个更改如代码清单 5-8 所示。

```
@Published private(set) var images: [ImageDownload] = []
代码清单 5-8
为远程图像创建可发布的属性
```

因为 SwiftUI 会将其用作数据源，所以只要这个数组发生变化，我们的 UI 就会更新。

最后，我们将根据代码清单 5-9 修改 `download(serverImages:)` 函数。

```
func download(serverImages: [ServerImage]) {
for imageIndex in (0..<serverImages.count) {
let downloadTask = Task {
do {
let imageUrl = try await download(serverImages[imageIndex])
self.images[imageIndex] = ImageDownload(status: .downloaded(imageUrl))
} catch {
self.images[imageIndex] = ImageDownload(status: .error(error))
}
}
images.append(ImageDownload(status: .downloading(downloadTask)))
}
}
代码清单 5-9
新版本的 download(serverImages:)
```

这就是实现部分魔法的地方。我们将首先遍历所有已下载的 `ServerImage`。对于每个服务器图像，我们将创建一个显式的 `Task`。我们还会立即将此任务以 `.downloading(_)` 状态添加到 `images` 数组中。该枚举跟踪任务本身，以便我们稍后可以根据需要取消它。

任务主体将尝试下载图像。如果下载失败，我们将使用 `.error(_)` 值替换其在 `images` 数组中的条目。如果成功，我们将使用 `.downloaded(url)` 值替换它。简而言之，任务的主体将根据下载是否成功，用不同的枚举值替换其在数组中的位置。并且由于这是一个 `@Published` 属性，SwiftUI 可以观察其更改，并实时获知该数组发生的任何情况。

#### 修改 `ContentViewViewModel` 类

`ContentViewViewModel.swift` 文件中的主要修改是 `downloadImages()` 函数，如代码清单 5-10 所示。我们还从这个文件中移除了 `imageUrls` 数组，因为该工作已委托给 `ImageManager` 类。

```
func downloadImages() {
Task {
guard let manager = imageManager else { return }
do {
let imageList = try await manager.fetchImageList()
manager.download(serverImages: imageList)
} catch {
print(error)
}
}
}
代码清单 5-10
新的 downloadImages() 方法
```

视图模型现在是一个非常轻量的类，只包含这个方法。我们可以将所有事情委托给 `ImageManager` 类，但我更倾向于将其保留为一个独立的逻辑类。

#### 修改 `ContentView` 对象

`body` 属性按照代码清单 5-11 进行修改。

```
var body: some View {
ScrollView {
LazyVStack {
if let manager = viewModel.imageManager {
ForEach(manager.images) { imageDownload in
switch imageDownload.status {
case .error(_): imageErrorView()
case .downloading(_): imageDownloadView()
case .downloaded(let url): imageView(for: url)
}
}
}
}
}
.onAppear {
viewModel.imageManager = imageManager
viewModel.downloadImages()
}
}
代码清单 5-11
新的 body 属性
```

我们将根据 `ImageDownload` 对象的 `ImageDownloadStatus` 属性来显示不同的视图。如果出现错误，我们将显示一个“x”标记。如果正在下载，我们将显示一个图片占位符。如果图片已下载完成，我们将显示图片本身。代码清单 5-12 展示了这些额外方法的实现。

```
@ViewBuilder
func imageDownloadView() -> some View {
VStack {
Image(systemName: "photo")
.resizable()
.frame(width: 300, height: 300)
.foregroundColor(.gray)
}
}
@ViewBuilder
func imageErrorView() -> some View {
VStack {
Image(systemName: "xmark")
.resizable()
.frame(width: 300, height: 300)
}
}
@ViewBuilder
func imageView(for url: URL) -> some View {
Image(uiImage: UIImage(data: viewModel.data(for: url))!)
.resizable()
.frame(width: 300, height: 300)
}
代码清单 5-12
用于创建视图的辅助属性
```

就这样！如果你现在运行应用，将会看到一个带有图片占位符的 UI。图 5-1 展示我们的基本 UI。

![](img/532302_1_En_5_Fig1_HTML.jpg)

这张截图显示了一个手机屏幕，上面有三张即将加载的图片，从上到下排列，屏幕顶部有时间、蜂窝网络和电池图标。

图 5-1

显示将要下载的图片的占位符

随着图片的加载，图 5-2 展示了占位符如何被实际图片替换。

![](img/532302_1_En_5_Fig2_HTML.jpg)

这张截图展示了一个手机屏幕，上面有三张猫的图片，从上到下排列，屏幕顶部有时间、蜂窝网络和电池图标。

图 5-2

图片在下载过程中逐个替换占位符

这个改进版的 `ImageDownloader` 项目在下载图片时会显示占位符，而不是在等待所有图片下载完成时显示一个 `ProgressView`，并且图片将始终以相同的顺序显示。很整洁！


  
