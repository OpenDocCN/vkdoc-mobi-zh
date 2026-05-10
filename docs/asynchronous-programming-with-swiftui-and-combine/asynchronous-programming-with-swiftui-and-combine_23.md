# 16. 融会贯通：SwiftUI、async/await 和 Combine

移动应用程序必须处理持续不断的事件流：用户输入、网络流量以及操作系统的回调都在争夺应用的注意力。构建感觉流畅的应用是一项具有挑战性的任务，因为你需要高效地处理所有这些事件。

Combine 和 `async/await` 是旨在简化这一过程的框架和语言特性集合中较新的成员。

在本章中，我们将探讨 Combine 和 `async/await` 的共性与差异，我将向你展示如何高效地同时使用两者在 SwiftUI 应用中调用异步 API。

为了更好地理解各自的特性，我们将查看一些代码片段，这些片段取自一个允许用户按书名搜索图书的 SwiftUI 屏幕。你可以在本书的 GitHub 仓库中找到这个示例应用的源代码。(124)

## 使用 Combine 获取数据

Apple 的许多 API 都支持 Combine，`URLSession` 就是其中之一。为了从 URL 获取数据，我们可以调用 `dataTaskPublisher`，然后使用 Combine 的一些运算符来处理响应，并将其转换为应用可以使用的数据模型。以下代码片段展示了一个典型的 Combine 管道，用于从远程 API 获取数据、映射结果、提取所需信息并处理错误。

> *此代码片段中的错误处理相当基础。关于 Combine 中错误处理以及如何在 SwiftUI 应用中以有意义的方式向用户显示错误消息的更深入讨论，请查看第 10 章，我们在该章中更详细地探讨了这个重要主题。*

```swift
private func searchBooks(matching searchTerm: String) -> AnyPublisher<[Book], Never> {
    let escapedSearchTerm = searchTerm.addingPercentEncoding(withAllowedCharacters: .urlHostAllowed) ?? ""
    let url = URL(string: "https://openlibrary.org/search.json?q=\(escapedSearchTerm)")!
    return URLSession.shared.dataTaskPublisher(for: url)
        .map(\.data)
        .decode(type: OpenLibrarySearchResult.self, decoder: JSONDecoder())
        .map(\.books)
        .compactMap { openLibraryBooks in
            openLibraryBooks?.map { Book(from: $0) }
        }
        .replaceError(with: [Book]())
        .eraseToAnyPublisher()
}
```

对于不熟悉 Combine 的人来说，这段代码的工作原理可能并不一目了然，更不用说能组合出这样的管道了。养成函数式响应式思维模式可能是学习 Combine 时最大的障碍之一。

## 使用 async/await 获取数据

现在让我们看看如何使用 `async/await` 实现相同的方法。Apple 已确保最重要的异步 API 可以使用 `async/await` 调用。为了从 URL 获取数据，我们可以异步调用 `await URLSession.shared.data(from: url)`。通过将此调用包装在 `try catch` 块中，我们可以添加与先前代码片段中相同类型的错误处理，并在发生错误时返回一个空数组。

为了方便他们（以及 Firebase 等其他 SDK 提供商），Apple 实现了与 Objective-C 的并发互操作性。(125) 简而言之，这确保了 Swift 编译器为每个具有完成块的 Objective-C 方法生成一个 async 版本。要详细了解其工作原理，请观看我的视频“在 Firebase 中使用 async/await”，(126) 我在其中对此进行了更详细的解释。

```swift
private func searchBooks(matching searchTerm: String) async -> [Book] {
    let escapedSearchTerm = searchTerm.addingPercentEncoding(withAllowedCharacters: .urlHostAllowed) ?? ""
    let url = URL(string: "https://openlibrary.org/search.json?q=\(escapedSearchTerm)")!
    do {
        let (data, _) = try await URLSession.shared.data(from: url)
        let searchResult = try OpenLibrarySearchResult.init(data: data)
        guard let libraryBooks = searchResult.books else {
            return []
        }
        return libraryBooks.compactMap { Book(from: $0) }
    } catch {
        return []
    }
}
```

如果你有一些编写和阅读 Swift 代码的经验，你会理解这段代码的功能——即使你之前没有使用 `async/await` 的经验：所有与 `async/await` 相关的关键字都与代码的其余部分融为一体，使其阅读和理解起来相当自然。这很大程度上归功于 Swift 语言团队将 Swift 的并发特性建模为类似于使用 `try/catch` 进行错误处理的方式。

当然，要*编写*这样的代码，你需要对 Swift 的并发特性有基本的了解，因此学习曲线肯定是存在的。

## 这是 Combine 的终结吗？

对比这两个代码片段，你可能会认为使用 `async/await` 的那一个对于可能既不熟悉 Combine 也不熟悉 `async/await` 的开发者来说更容易理解，这主要是因为你能够以线性的方式从上到下阅读。

相反，要理解 Combine 版本的代码，你必须知道什么是 publisher，为什么某些操作是嵌套的（例如，`compactMap`/`map` 结构内部用于映射图书的代码），以及为什么需要调用 `eraseToAnyPublisher`。如果你刚接触 Combine，这些可能会看起来非常令人困惑。

再加上 WWDC 2021 上缺少关于 Combine 的讲座——Apple 似乎真的对函数式响应式编程失去了热情。

那么，既然两个代码片段似乎做的是同样的事情，这是否意味着 Combine 的终结呢？

嗯，我不这么认为，这要归因于 SwiftUI 与 Combine 的紧密集成。事实上，Combine 使得 SwiftUI 中的许多事情变得异常简单，只需极少的代码。



### 连接 UI…

为了更好地理解这一点，我们来看看如何从 SwiftUI 中调用之前的代码片段。以下代码展示了一种实现搜索屏幕的典型方式——我们使用了一个 `List` 视图来显示结果，并通过 `.searchable` 视图修饰符来设置搜索字段，并将其连接到视图模型上的 `searchTerm` 已发布属性：

```
struct BookSearchCombineView: View {
    @StateObject var viewModel = ViewModel()
    var body: some View {
        List(viewModel.result) { book in
            BookSearchRowView(book: book)
        }
        .searchable(text: $viewModel.searchTerm)
    }
}
```

### …到 Combine 管道

通过将 `searchTerm` 设为视图模型上的一个已发布属性，它就变成了一个 Combine 发布者，使我们能够将其用作 Combine 管道的起点。视图模型的初始化器是设置此管道的好地方：

```
fileprivate class ViewModel: ObservableObject {
    @Published var searchTerm: String = ""
    @Published private(set) var result: [Book] = []
    @Published var isSearching = false
    private var cancellables = Set<AnyCancellable>()

    init() {
        $searchTerm
            .debounce(for: 0.8, scheduler: DispatchQueue.main) // (1)
            .map { searchTerm -> AnyPublisher<[Book], Never> in // (2)
                self.isSearching = true
                return self.searchBooks(matching: searchTerm)
            }
            .switchToLatest() // (3)
            .receive(on: DispatchQueue.main) // (4)
            .sink(receiveValue: { books in // (5)
                self.result = books
                self.isSearching = false
            })
            .store(in: &cancellables) // (6)
    }

    private func searchBooks(matching searchTerm: String) -> AnyPublisher<[Book], Never> {
        // ...
    }
}
```

在这里，我们订阅 `searchTerm` 发布者，然后使用几个 Combine 操作符来获取用户输入、调用远程 API、接收结果，并将它们分配给一个连接到 UI 的已发布属性：

1.  `debounce` 操作符只会将事件在两次事件之间间隔 0.8 秒后才传递。这样，我们只有在用户输入完毕或短暂停顿时才会调用远程 API。

2.  我们使用 `map` 操作符来调用 `searchBooks` 管道（其本身也是一个发布者），并将其结果返回到管道中。

3.  尽管我们使用了 `debounce` 操作符来减少事件数量，但我们仍可能遇到多个网络请求同时进行的情况。这会导致网络响应可能乱序到达。为了防止这种情况，我们使用 `switchToLatest()`——这将切换到来自上游发布者的最新输出，并丢弃所有其他先前的事件。

4.  为了确保我们只从主线程对 UI 进行更改，我们调用 `receive(on: DispatchQueue.main)`。

5.  要将管道的结果（我们从 `searchBooks` 接收到的 `Book` 实例数组）分配给已发布属性 `result`，我们通常会使用 `assign(to:)` 订阅者，但由于我们还想要将 `isSearching` 属性设置为 `false`（以关闭我们 UI 上的进度视图），我们需要使用 `sink` 订阅者，因为这将允许我们执行多个指令。

6.  使用 `sink` 订阅者也通常意味着我们需要将订阅存储在一个 `Cancellable` 或一个 `AnyCancellables` 的 `Set` 中。

请注意，处理诸如丢弃乱序事件或仅在用户停止输入时才发送请求以减少发送请求数量等具有挑战性的任务是多么容易。正如你稍后将会看到的，当使用 `async/await` 时，这会稍微复杂一些。

### …到 async/await 方法

当使用 `async/await` 时，同样的代码会是什么样子呢？

要调用基于 `async/await` 的 `searchBooks` 版本，我们需要选择一种略有不同的方法。我们不直接订阅 `$searchTerm` 发布者，而是创建一个名为 `executeQuery` 的 `async` 方法，并创建一个调用 `searchBooks` 的 `Task`：

```
fileprivate class ViewModel: ObservableObject {
    @Published var searchTerm: String = ""
    @Published private(set) var result: [Book] = []
    @Published private(set) var isSearching = false
    private var searchTask: Task<Void, Never>? // (1)

    @MainActor // (7)
    func executeQuery() async {
        searchTask?.cancel() // (2)
        let currentSearchTerm = searchTerm.trimmingCharacters(in: .whitespaces)
        if currentSearchTerm.isEmpty {
            result = []
            isSearching = false
        }
        else {
            searchTask = Task { // (3)
                isSearching = true // (4)
                result = await searchBooks(matching: searchTerm) // (5)
                if !Task.isCancelled {
                    isSearching = false // (6)
                }
            }
        }
    }

    private func searchBooks(matching searchTerm: String) async -> [Book] {
        // ...
    }
}
```

在 `Task` 内部，我们还通过根据进程的当前状态更新视图模型的 `isSearching` 已发布属性来处理进度视图的状态。

在应用的此部分基于 Combine 的版本中，我们使用了 `map` 和 `switchToLatest` 的组合，以确保我们只接收最新用户输入的结果。这对于网络请求尤其重要，因为它们可能会乱序返回。

为了使用 `async/await` 实现相同的功能，我们需要使用协作式任务取消^(¹²⁷)：我们将对任务的引用保存在 `searchTask` (1) 中，并在启动新任务 (3) 之前取消任何可能正在运行的任务 (2)。

由于 `searchBooks` 被标记为 `async`，Swift 运行时可以决定在非主线程上执行它。然而，在 `executeQuery` 中，我们希望通过设置已发布的属性 `result` (5) 和 `isSearching` (4, 6) 来更新 UI。为了确保它运行在主线程上，我们必须使用 `@MainActor` 属性 (7) 来标记它。

作为最后一步，我们需要对 UI 进行一个微小但重要的更改：由于我们无法将一个异步方法订阅到一个已发布的属性，我们需要找到另一种方法，以便在用户在搜索字段中输入每个字符时调用 `executeQuery`。

事实证明，Apple 在最最新版本的 SwiftUI 中添加了一个合适的视图修饰符——`onReceive(_ publisher:)`。这个视图修饰符允许我们注册一个闭包，当给定的发布者发出事件时，该闭包将被调用：

```
List(viewModel.result) { book in
    BookSearchRowView(book: book)
}
.searchable(text: $viewModel.searchTerm)
.onReceive(viewModel.$searchTerm) { searchTerm in
    Task {
        await viewModel.executeQuery()
    }
}
```

总的来说，使用 `async/await` 需要我们做更多的工作，而且很容易出错，比如搞错协作式任务取消，或者忘记一个重要步骤，比如取消任何可能仍在运行的任务。就开发者体验而言，Combine 比 `async/await` 遵循更声明式的方法：你告诉框架要 *做什么*，而不是 *怎么做*。



### 从 Combine 调用异步代码

在上一节中，我曾断言无法使用 `async/await` 订阅 Combine 发布者。但事实果真如此吗？让我们看看能否实现一种巧妙的方式，将 `async/await` 与 Combine 结合起来。

以下代码片段展示了一个视图模型，它使用 Combine 管道来调用异步版本的 `searchBooks` 方法：

```
fileprivate class ViewModel: ObservableObject {
    // MARK: - 输入
    @Published var searchTerm: String = ""
    // MARK: - 输出
    @Published private(set) var result: [Book] = []
    @Published var isSearching = false
    
    init() {
        $searchTerm
            .debounce(for: 0.8, scheduler: DispatchQueue.main) // (1)
            .removeDuplicates()                                // (2)
            .handleEvents(receiveOutput: { output in           // (3)
                self.isSearching = true
            })
            .flatMap { value in
                Future { promise in
                    Task {
                        let result = await self.searchBooks(matching: value)
                        promise(.success(result))
                    }
                }
            }
            .receive(on: DispatchQueue.main)
            .eraseToAnyPublisher()
            .handleEvents(receiveOutput: { output in           // (4)
                self.isSearching = false
            })
            .assign(to: &$result)                              // (5)
    }
    
    private func searchBooks(matching searchTerm: String) async -> [Book] {
        let escapedSearchTerm = searchTerm
            .addingPercentEncoding(withAllowedCharacters: .urlHostAllowed) ?? ""
        let url = 
            URL(string: "https://openlibrary.org/search.json?q=\(escapedSearchTerm)")!
        do {
            let (data, _) = 
                try await URLSession.shared.data(from: url)
            let searchResult = 
                try OpenLibrarySearchResult.init(data: data)
            guard let libraryBooks = searchResult.books else {
                return []
            }
            return libraryBooks.compactMap { Book(from: $0) }
        } catch {
            return []
        }
    }
}
```

这种方法使我们能够利用 Combine 的强大功能，仅用几行代码就能改善用户体验：

- 通过使用 `debounce` 操作符 (1)，我们可以推迟通过网络发送搜索请求，直到用户停止输入约一秒钟。这意味着我们将消耗更少的带宽（对用户有利），并减少 API 调用次数（对我们有利，尤其是在调用可能按次计费的 API 时）。
- 我们还可以通过使用 `removeDuplicates` 操作符 (2) 移除任何重复的 API 调用来进一步减少请求数量。

在代码层面也有一些优势：

- 通过使用 `handleEvents` 操作符 (3, 4)，我们可以将处理进度视图的代码从 `map` 和 `sink` 操作符中抽取出来。这也允许我们用一个更简单易用的 `assign` 订阅者替换 `sink`/`store` 组合。
- 只有一个地方 (5) 将管道的结果赋值给 `result` 属性，从而降低了引入细微编程错误的机会。

与此同时，在编写网络访问代码时，我们可以利用 `async/await` 的优势：能够以线性方式自上而下地阅读代码，这比使用回调或嵌套闭包的代码更容易理解。

让我们仔细看看允许我们从 Combine 管道调用异步方法的代码：

```
somePublisher
    .flatMap { value in
        Future { promise in
            Task {
                let result = await self.searchBooks(matching: value)
                promise(.success(result))
            }
        }
    }
```

要调用异步版本的 `searchBooks`，我们需要建立一个异步上下文。这就是我们将调用包装在 `Task` 中的原因。一旦 `searchBook` 返回，我们就通过将结果作为 `.success` 枚举值发送来兑现 promise。

我们可以通过将相关部分抽取到 `Publisher` 的扩展中来简化这段代码：

```
extension Publisher {
    /// 执行异步调用并将其结果返回给下游订阅者。
    ///
    /// - Parameter transform: 一个闭包，它将一个元素作为参数，并返回一个产生该类型元素的发布者。
    /// - Returns: 一个发布者，它将来自上游发布者的元素转换为该元素类型的发布者。
    func `await`(_ transform: @escaping (Output) async -> T) -> AnyPublisher {
        flatMap { value -> Future in
            Future { promise in
                Task {
                    let result = await transform(value)
                    promise(.success(result))
                }
            }
        }
        .eraseToAnyPublisher()
    }
}
```

这允许我们使用以下代码调用异步方法：

```
somePublisher
    .await { searchTerm in
        await self.searchBooks(matching: searchTerm)
    }
```

## 总结

苹果在 WWDC 2021 上似乎对 Combine 缺乏关注，这导致社区中出现了很多困惑和不确定性——考虑到苹果对 `async/await` 的重视程度，你是否还应该投入精力学习 Combine？

要回答这个问题，我们需要退一步思考，理解 Combine 和 `async/await` 的价值主张。

粗略一看，它们似乎解决的是同一个用例：异步调用 API。然而，仔细观察就会发现，它们实际上截然不同：

Combine 是一个响应式框架，其核心概念是事件流，你可以通过操作符对其进行转换，然后使用订阅者进行消费。这种无副作用的编程方式更容易确保你的应用始终处于一致的状态。实际上，SwiftUI 的状态管理系统大量使用了 Combine——每个 `@Published` 属性，顾名思义，都是一个发布者，这使连接 Combine 管道变得很容易。

另一方面，`Async/await` 旨在使异步编程和处理并发更容易实现和推理。虽然这使创建线性控制流变得更容易，但它不能像 Combine 那样对状态提供相同的保证。

我的建议是，在任何给定情况下，使用两者中更合理的那一个。对于任何与 UI 相关的任务，我个人更喜欢使用 Combine，因为它在实现诸如用户输入防抖、将多个输入流合并为一个，以及高效处理网络请求无序执行等原本难以实现的方面，提供了前所未有的能力和灵活性。

Combine 和 `async/await` 是我们工具箱中两种不同的工具，我们有责任明智地、按照它们预期的用途来使用它们。

我的建议是，在应用中处理来自多个来源输入的 UI 相关功能时使用 Combine。一些示例如下：

- 输入验证：验证多个输入字段，确保用户填写了所有必填字段，处理字段间依赖关系（例如，选择信用卡支付需要填写卡号，而选择发票支付则需要提供额外的发票地址）
- 搜索和过滤：确保基于用户在搜索栏中输入的最新值以及他们选择的任何过滤条件进行搜索和过滤
- 在用户输入到达数据层之前对其进行清理：例如，确保不要因为用户输入的每一个字符都命中 API 端点，或者移除重复查询，以使网络层更高效（并消耗更少的带宽）

另一方面，你可能会发现，在调用异步 API、发出网络请求或与 Firebase 等 BaaS 平台交互时，使用 `async/await` 比使用 Combine 更容易。

最后，正如你在本章中所见，将 `async/await` 和 Combine 结合起来是可行的，这允许你混合并匹配这两种方法的最佳特性。




脚注 1 2 3 4

## 索引

### A
- `Add Modifiers`
- `AppKit`
- `Apple App Store`
- `Aspect Ratio` 修饰器
- `Async/await` API
- 异步特性
- 代码层级与组合
- 基于 Combine 的版本
- Combine 的操作符
- 并发模型
- 协作任务取消
- 错误处理与 JSON 解析
- 框架与语言参数
- 管道示例应用（`searchBooks`）
- 搜索与刷新数据
- `searchTerm` 属性
- `URLSession`
- 异步算法
- API
- 手工三明治的制作过程
- `greet` 函数
- 方法
- 网络
- 同步版本
- 任务
- 世界
- 实际应用中的异步代码 API
- 编译时错误
- 获取数据
- `SwiftUI` 任务
- `Xcode 13`
- 异步 `executeQuery` 方法
- 异步编程 API
- 基于 `async/await` 的行为
- 概念
- 并发模型
- 定义
- 函数
- 基于处理器的版本
- 改进
- 语言层级
- `makeSandwich`
- `slice` 函数
- `toastBread` 函数与 `Slice`
- `Attributes inspector`
- `Autocapitalization`

### B
- 后端即服务（`BaaS`）
- `@Binding`
- `BookRowView`
- `BookShelfApp.swift`
- 构建技术，可复用的 `SwiftUI` 组件

### C
- `剑桥词典`
- `子视图`
- `Cloud Firestore`
- 代码重构
- 代码可复用
- 组合
- `Combine`
    - `addSnapshotListener` 方法
    - 按钮点击
    - 回调驱动的代码
    - `CollectionReference`
    - 概念
    - 创建发布者
    - `CurrentValueSubject`
    - 演示服务器
    - 优雅且声明性的机制
    - `eraseToAnyPublisher` 操作符
    - 获取数据
    - `flatMap` 操作符
    - Futures
    - `handleEvents` 操作符
    - `isFormValidPublisher`
    - `isUsernameAvailablePublisher`
    - 懒计算属性
    - 映射数据操作符
    - 内置操作符实现
    - `retry` 操作符
    - `PassthroughSubject`
    - 已发布的属性发布者
    - `Publishers.CombineLatest()`
    - 重构
    - 重试
    - `send(:)` 方法
    - 服务器控制台输出
    - 注册表单
    - `snapshotPublisher`
    - 主题（Subjects）
    - 订阅者
    - 元组，键路径
    - 更新
    - 用户名发布者
    - `UserNameValid` 枚举
    - 用户输入
    - 视图模型
    - 封装 API
- `Combine` 框架特性
    - `ObservableObject`
    - `@Published` 转化 `SwiftUI` 的关系
- `CombineLatest`
- Combine 管道
- Combine 特有的——`filter`
- 复杂列表行
- 复杂视图
- 并发模型
- 面向消费者的平台
- 容器视图
    - 类别
    - 复杂 UI 分组
    - 返回类型
- `ContentView`
- `ContentView.swift`
- 跨平台 UI
- 自定义列表行
- `CustomRowView`

### D
- 数据绑定
- 开发者
- 设备/网络离线错误
    - `APIError`
    - 连接性
    - `isUsernameAvailablePublisher`
    - `mapError`
    - 移动设备
    - 网络错误
    - `usernameMessage` 视图模型
- `DispatchQueue.global(qos:)`
- 显示书籍详情，视图
- 领域特定语言（`DSL`）
- 下钻导航
    - `App` 结构体
    - `BookDetailsView`
    - `BookEditView`
    - `BookRowView`
    - `Book` 结构体
    - `通讯录` 应用
    - `编辑` 按钮
    - 初始化器注入
    - 列表绑定
    - `NavigationLink`
    - `ObservableObject`
    - 根视图
    - `@StateObject`
    - 静态书籍列表
    - 更新
    - 视图模型
- 动态列表
    - `Apple`
    - 异步数据获取
    - 绑定
    - 显示元素
    - 下拉刷新
    - 搜索

### E
- 编辑器占位符
- 枚举
- `@EnvironmentObject`
- 错误处理
    - `checkUserNameAvailablePublisher`
    - `Chrome Dino` 游戏
    - 错误条件
    - 错误消息
    - 扩展
    - 忽略错误的 `flatMap`
    - 内联错误
    - `isUsernameAvailablePublisher`
    - 网络 API
        - `APIError`
        - `checkUserNamerAvailablePublisher`
        - 关注点
        - 网络错误
    - 注册表单需求，解决方案
    - 重试
    - 类型别名
    - 视图模型
- 扩展
- `ExtractedView`
- `提取子视图`

### F
- Firebase
    - 异步
    - `BookListViewModel`
    - `Combine`
    - 管道定义
    - 已发布的属性
    - 服务
    - 视图模型
- Firestore
    - Cloud Firestore
    - 集合/查询组合数据
    - 调度队列
    - 文档获取数据
    - `Future`
        - `DocumentReference`
        - `DocumentSnapshot`
        - `getDocument` 方法
        - promise 参数
        - 属性
        - 单次触发的 Combine 发布者
        - 快照/错误
    - 实时同步
    - `SDK`
    - 快照监听器
    - 更新
- 平面列表
- `FocusableListView` 视图
- `FocusedReminder` 属性
- `@FocusState`
- 表单
    - `BookDetailsView`
    - 布尔属性
    - 按钮
    - 数据模型
    - 显示数据
    - 动态数据
    - 标题文本
    - `Hello World`
    - 图片
    - 输入元素
    - 标签
    - `NumberFormatter`
    - 预览提供者
    - 注册表单
    - JSON 文档用户名
    - 静态数据
    - 静态表单
    - `TextField`
    - 主题
    - 切换 UI 元素
    - `UITableView` 版本
- `Frame` 修饰器
- 完全滑动
- 函数式响应式框架
- 函数式响应式编程

### G
- `GitHub` 仓库

### H
- 页眉和页脚
- 层级列表
- 水平堆栈与间隔器
- `HStack`
- 拥抱
- 拥抱

### I, J, K
- `Image` 和 `Text` 视图
- 命令式 UI 工具包
- `IndexSet`
- 输入表单
- 输入验证
    - 绑定
    - `BookEditViewModel`
    - `Book` 属性
    - `BookShelfApp`
    - `布尔` 值
    - 组合管道
    - 完成处理器
    - 通讯录应用
    - 取消动作
    - 事件
    - 信息流
    - 表单验证
    - 功能性绑定
    - 初始化器
    - `ISBN`
    - `isISBNValid`
    - `isValid` 属性
    - `map` 闭包
    - `@ObservableObject`
    - 密码
    - `Publishers.CombineLatest`
    - `保存` 函数
    - 注册表单
    - 社交功能
    - 数据源
    - 步骤
    - `SwiftUI`
    - 用户账户
    - 用户名
    - 用户数据
    - 验证消息
    - 视图模型
    - `wrappedValue`
- Interface Builder
- 内部服务器错误
    - `APIError` 枚举
    - `dataTaskPublisher`
    - 演示服务器
    - 错误消息
    - HTTP 状态码实现
    - 压力
    - `retry` 操作符
    - 声音断点
    - `tryCatch` 操作符
    - 用户名验证错误
- 国际标准书号（`ISBN`）
- 互联网
- iPad 模拟器
- iPhone

### L
- 布局行为
    - 扩展
    - 拥抱
    - `SwiftUI` 布局过程
- 库
- `List` 单元格
    - `.listSectionSeparator()`
    - `.listSectionSeparatorTint()`
    - `.listStyle` 视图修饰器
- 列表视图
    - 复杂列表行
    - 自定义列表行
    - 动态
    - 代码行
    - 静态文本
    - `SwiftUI` 视图创建
    - 列表行内的 `SwiftUI` 视图
    - UI 结构

### M
- `@MainActor`
- 管理焦点
    - Apple 文档
    - 编辑
    - 消除空元素
    - 更快的导航
    - `@FocusState`
    - 在 `List` 视图中处理回车键
    - MVVM 方法
    - `SwiftUI`
- 标记为已读/未读操作
- 内存密集型进程
- 移动应用
- 模型视图控制器（`MVC`）
- 模型视图展示器（`MVP`）
- 模型、视图、视图模型（`MVVM`）
- 多重滑动操作
- 多线程

### N
- 嵌套列表
- 网络访问
    - API 端点
    - Apple 文档
    - CPU 周期
    - `debounce` 操作符
    - 边缘连接
    - `removeDuplicates`
    - 根本原因
    - `share()` 操作符
    - 事件
    - `print()` 操作符
    - 订阅者
    - 订阅
    - `$username` 发布者
    - 订阅者
    - 测试服务器
- 创建新 `SwiftUI` 应用
    - `画布`
    - 选择 iOS 区域
    - 为项目命名
    - 双向工具
    - `Xcode`

### O
- Objective-C 方法
- `ObservableObject`
    - `@ObservedObject`
    - `@ObservedObject` 视图模型
- `onDelete` 修饰器
    - `onDelete` 的闭包
    - `onDelete` 视图修饰器
- `onMove` 视图修饰器
- `.onSubmit` 视图修饰器
- 不透明返回类型
- 不透明类型
- 操作符
    - `.addTopping` 组合
    - `CompactMap`
    - `filter` 操作符
    - `map` 名称
    - 参数
    - 管道
    - `.prefix` 操作符
    - 发布者
    - 配料

### P, Q
- 父视图
- 占位符
- 预览画布
- 预览配置
- `PreviewProvider`
- 属性封装器
    - `@Publishedturns`
    - `Publishers.CombineLatest`
    - 创建错误
    - 事件
    - 长时间运行的计算
    - 披萨配送服务地址
    - `.eraseToAnyPublisher()` 操作符
    - 订单状态管道
    - 披萨订购应用
    - 披萨配料
    - `下单` 按钮
    - `.print()` 操作符
    - `subscribe(on:)` 操作符
    - 类型
    - 值
- `Publishers.CombineLatest`
- 下拉刷新
- 厄运金字塔

### R
- `RandomAccessCollection`
- 响应式编程
- `ReactiveX`
- 渲染过程
- 响应解析错误
    - 弹窗
    - 数据解码操作符
    - 解码错误
    - 错误消息
    - 事件
    - 指导
    - `isUsernameAvailablePublisher`
    - 网络请求
    - 情况
    - 视图模型
- 重试操作符
    - 代码
    - 指数退避
    - 扩展实现
    - 参数
    - `Publishers.TryCatch`
- `RxSwift`

### S
- `SampleBooks`
- 制作三明治的过程
- 调度器
    - 访问网络
    - 组合管道
    - 默认行为
    - 定义
    - `ImmediateScheduler`
    - 输出协议
    - `receive(on)`
    - `SchedulerTimeType`
    - `subscribe(on)`
    - 线程
    - 类型
- 滚动视图
- `SearchableBooksListView`
- `SearchTerm` 发布者
- `分隔线`
- 单一视图
- 类似 Smalltalk 的调用语义
- 数据源
- `Spacer`
- `Spacer` 视图
- `@State`
    - `@StateObject`
- `StateStepper`
- 字符串插值
- 结构体
- 样式设置
    - 页眉和页脚
    - 列表单元格
    - 列表样式
    - 分隔线
- 订阅者
    - `receive(on:)` 操作符
- Swift 并发宣言
- Swift 语言特性团队
- Swift 包索引
- Swift Playgrounds
- Swift 编程语言
- Swift 的新并发模型
- SwiftUI 应用
    - Apple 的投资
    - `async/await`
    - 属性
    - 错误
    - 回调
    - 协作，UI 设计师与开发者
    - Combine
    - 跨平台 UI 工具包
    - 声明式语法
    - 灵活的基于堆栈的布局系统
    - 输入元素
    - 检查器
    - 交互性
    - 关键组件
    - 网络驱动的 Combine 管道
    - 属性封装器
    - 反应式状态管理系统
    - 可复用的 UI 组件
    - 软件开发
    - 文本与图片
    - 传统应用
    - 教程
    - UI
    - 用户界面元素
    - “一次编写，到处运行” 范式
    - Xcode 的预览画布
- SwiftUI 属性
    - 组合优于继承
    - 声明式 *vs.* 命令式
    - 状态函数
    - 状态管理视图
- SwiftUI 的状态管理
    - 应用
    - 数据模型
    - 绑定对象
    - 绑定值类型
    - `@EnvironmentObject`
    - `实时预览` 按钮
    - `ObservableObject` 协议
    - 属性封装器
    - `@StateObject`
    - 用户问候
    - 视图与数据模型同步
- `SwiftUI View` 文件代码编辑器
    - 复杂 UI
    - 容器组件
    - 图片缩放预览
    - 源码更新
    - 文本视图
    - `VStack` 和 `Image` 视图
    - `VStack` 容器
    - `Xcode` 工具
- `swipeActions` 修饰器
- 滑动操作
    - 添加
    - Apple 的邮件应用
    - 控件
    - 易用的 UI 启示
    - 特性
    - 完全滑动列表行修饰器
    - 移动与删除
    - `onDelete` 样式
    - 滑动删除尾部边缘
    - `UIKit`
- 滑动删除
- 同步编程
    - `makeSandwich`
    - 制作三明治算法
    - `toastBread` 函数

### T
- `TextField` 视图
- `文本输入框`
- `ToastBread` 函数

### U
- `UIKit`
- UI 相关任务
- `UITextField`
- UI 视图
    - 控件
    - 指示器
    - 元素
    - 图片
    - 形状
- `URLSession`
    - 获取数据
    - 问题
    - 发布
    - `receive(on:)` 操作符
    - 线程
- 用户界面（`UI`）
    - 方面
    - 阻塞
    - 元素
    - 事件
    - 框架更新
- 用户

### V
- 验证错误
    - 后端实现
    - `URLErrors`
    - 用户名视图模型
- 值
- 视图组合
- 视图、交互器、展示器、实体与路由（`VIPER`）
- 视图修饰器
    - 应用定制
    - 子视图
    - 配置视图
    - 注册动作处理器
    - Swift 方法
- 视图修饰器
- 视图协议
- 视图
    - 组合 `ContentView`
    - 自定义类型
    - 分解
    - 层级
    - 仅描述
    - `PreviewProvider`
    - 原语
    - 结构体，`View` 协议结构
    - Swift 包
    - `SwiftUI` 文档
    - `SwiftUI` 的构建块
    - UI
- 视图库
- `VStack`

### W
- 全球开发者大会（`WWDC`）

### X, Y, Z
- `Xcode` 编辑器
    - 图形化工具
    - 库
    - `新建文件` 对话框
    - 预览画布
    - 重构工具
    - 源码编辑器
    - 双向工具
