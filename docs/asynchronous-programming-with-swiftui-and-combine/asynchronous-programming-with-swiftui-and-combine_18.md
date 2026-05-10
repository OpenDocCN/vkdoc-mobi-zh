# 13. Combine 调度器与 SwiftUI

默认情况下，SwiftUI 中响应 UI 事件运行的任何代码都会在主线程上执行。由于我们的大部分代码都涉及响应某些用户交互来更新其他 UI 元素，因此在大多数情况下这没有问题。例如，你可能需要验证用户的输入，以确保他们填写了多步骤表单中的所有必填字段。这是一个内存密集型过程，其运行速度足够在主线中执行，而不会引起任何问题。

然而，一旦你想执行更复杂的计算，或者需要访问本地存储、网络或任何访问延迟高于本地内存的 API，如果你在主线程上执行这些代码，就可能阻塞 UI。

阻塞 UI 会导致各种问题：应用的 UI 变得无响应，动画开始出现卡顿，最终用户会感到不满，开始在 App Store 上留下负面评论，或在 Twitter 上抱怨。

这就是为什么你应该将任何长时间运行的代码卸载到后台线程。当你的代码在后台运行时，主线程可以自由处理 UI 事件。用户可以继续使用应用，直到后台进程最终完成。某些后台进程可能会产生需要在 UI 上显示的结果。然而，由于 UI 更新必须在主线程上进行，因此你需要先切换回主线程，才能用后台线程接收到的结果更新 UI。

Combine 提供了一种优雅且声明式的机制，用于控制管线的各个部分在何处运行。这种机制基于调度器（Scheduler）的概念构建，帮助我们推理代码应该在何处执行，而无需直接处理线程的复杂性。

在本章中，你将学习如何有效使用这种机制，帮助你构建能更好利用系统资源的应用，从而保持应用 UI 的响应性。



## 什么是调度器

Combine 使用 `Scheduler` 作为一种抽象机制，让我们可以指定代码运行的*时机*和*位置*，从而无需直接与线程打交道。根据苹果官方文档，`Scheduler` 是*一个定义何时以及如何执行闭包的协议*^(⁹⁹)。

`Scheduler` 协议本身定义了一系列方法，允许调用者立即或在未来的某个日期和时间执行代码：

```
public protocol Scheduler {
    /// 为该调度器描述一个时间点。
    associatedtype SchedulerTimeType : Strideable where
        Self.SchedulerTimeType.Stride : SchedulerTimeIntervalConvertible
    /// 一种定义调度器所接受选项的类型。
    ///
    /// 该类型可由每个 `Scheduler` 自由定义。
    /// 通常，接受 `Scheduler` 参数的操作
    /// 也会接受 `SchedulerOptions`。
    associatedtype SchedulerOptions
    /// 该调度器对当前时刻的定义。
    var now: Self.SchedulerTimeType { get }
    /// 调度器允许的最小容差。
    var minimumTolerance: Self.SchedulerTimeType.Stride { get }
    /// 在下一个可能的机会执行该操作。
    func schedule(options: Self.SchedulerOptions?,
                  _ action: @escaping () -> Void)
    /// 在指定日期之后某个时间执行该操作。
    func schedule(after date: Self.SchedulerTimeType,
                  tolerance: Self.SchedulerTimeType.Stride,
                  options: Self.SchedulerOptions?,
                  _ action: @escaping () -> Void)
    /// 在指定日期之后，以指定频率执行该操作，
    /// 如果可能的话，可以选择考虑容差。
    func schedule(after date: Self.SchedulerTimeType,
                  interval: Self.SchedulerTimeType.Stride,
                  tolerance: Self.SchedulerTimeType.Stride,
                  options: Self.SchedulerOptions?,
                  _ action: @escaping () -> Void) -> Cancellable
}
```

不同的调度器通过使用关联类型 `SchedulerTimeType` 来实现这种计时方面的功能。该关联类型需要遵循 `SchedulerTimeIntervalConvertible`，这是一种表示相对时间的方式。

让我们看看在使用 Combine 时会遇到的一些调度器，并讨论何时使用它们。

### 调度器类型

以下是在使用 SwiftUI 时最相关的调度器：

*   `ImmediateScheduler` 是默认调度器，如果你没有显式指定其他调度器，就会使用它。它会在触发管道的同一线程上立即执行代码。我们将在下一节更详细地讨论这个调度器。

*   `RunLoop` 是一个你会经常看到的调度器。它在特定的运行循环上执行工作。

*   `DispatchQueue` 允许我们在特定的调度队列上执行代码。最常用的是主调度队列和后台调度队列（你可以指定不同的服务质量等级，从 `background` 到 `userInteractive`），但你也可以创建自己的调度队列，并根据需要进行配置。

那么，你应该使用哪个 `Scheduler` 呢？

在 SwiftUI 上下文中，最常用的调度器是 `RunLoop` 和 `DispatchQueue`。尽管它们看起来非常相似（参见 Philippe Hausler 在 Swift 论坛上的回答^(¹⁰⁰)），但在任何需要调度运行在主线程上的代码以访问 UI 的 Combine 管道中，有一个区别是相关的。

正如这个 StackOverflow 回答^(¹⁰¹)中所解释的，当使用 `RunLoop` 时，如果用户在拖动或触摸 UI，Combine 管道将不会传递事件。然而，当使用 `DispatchQueue` 作为调度器时，管道会继续传递事件。

因此，如果你希望确保即使在与应用 UI 交互（例如滚动列表、点击按钮或拖动屏幕上的元素）时，Combine 管道仍能继续传递事件，那么你应该使用 `DispatchQueue`。

### 默认行为

如果你没有明确告诉 Combine 如何调度你的代码，它将默认在与触发管道的事件相同的线程上运行你的代码。在 SwiftUI 中使用 Combine 时，大多数管道会订阅某个视图模型上的已发布属性。SwiftUI 运行在主线程上，因此任何源自 UI 的事件都将从主线程发送。

在下面的示例中，我们通过响应按钮点击来更改已发布属性的值。处理按钮点击的闭包运行在主线程上，因此视图模型中的 Combine 管道也将在主线程上运行。

```
class ViewModel: ObservableObject {
    @Published var demo = false
    private var cancellables = Set<AnyCancellable>()

    init() {
        $demo
            .sink { value in
                print("Main thread: \(Thread.isMainThread)")
            }
            .store(in: &cancellables)
    }
}

struct DemoView: View {
    @StateObject var viewModel = ViewModel()

    var body: some View {
        Button("Toggle from main thread") {
            viewModel.demo.toggle()
        }
        .buttonStyle(.action)
    }
}
```

当你运行这段代码^(¹⁰²)并点击按钮时，你会看到以下输出，这证实了 SwiftUI 在主线程上发送事件：

```
Main thread: true
```

这种调度行为是由 Combine 的默认调度器 `ImmediateScheduler` 引起的。它会在当前线程上立即执行代码。因此，如果你从后台线程发送事件，管道也会在该特定后台线程上运行。让我们对我们的示例稍作修改，将更改已发布属性的代码包装在 `DispatchQueue.global().async { }` 调用中。结果，Combine 管道将在同一个后台线程上运行。

```
class ViewModel: ObservableObject {
    @Published var demo = false
    private var cancellables = Set<AnyCancellable>()

    init() {
        $demo
            .sink { value in
                print("Main thread: \(Thread.isMainThread)")
            }
            .store(in: &cancellables)
    }
}

struct DemoView: View {
    @StateObject var viewModel = ViewModel()

    var body: some View {
        Button("Toggle from main thread") {
            DispatchQueue.global().async {
                viewModel.demo.toggle()
            }
        }
        .buttonStyle(.action)
    }
}
```

运行这段代码并点击按钮，会得到以下输出：

```
2022-05-14 12:19:32.093826+0200 SwiftUICombineSchedulers[41912:2513626] [SwiftUI] Publishing changes from background threads is not allowed; make sure to publish values from the main thread (via operators like receive(on:)) on model updates.
Main thread: false
```

这种默认行为在大多数情况下都适用。例如，如果你想验证用户输入，这通常可以在主线程上运行，因为你将把多个 UI 状态组合到输入表单的 `isValid` 状态中。

然而，一旦你需要访问网络（或任何其他异步数据源），情况就变得复杂了，你会希望在回到主线程更新 UI 之前，先在后台线程上运行管道的部分内容。

一种切换线程的方法是显式使用 `DispatchQueue` 及其方法，在主队列（`DispatchQueue.main`）和全局后台队列之一（`DispatchQueue.global()`）或你自己创建和管理的队列之间进行切换。



## 切换调度器

显式调用方法切换到最合适的`DispatchQueue`虽然可行，但这种方法会导致代码过于冗长。

如果能有一种声明式的方法，确保管道的各个部分在适当的线程上运行，那岂不是更优雅？

Combine 提供了多个操作符，允许我们通过声明要使用的调度器来实现线程切换。

其中最重要的是`receive(on:)`，你会发现自己经常使用它，尤其是在访问网络时：它允许我们告诉 Combine 在接收订阅者（例如`sink`或`assign`）发送的事件时使用哪个调度器。

另一个关键的调度操作符是`subscribe(on:)`——我们可以用它来指定 Combine 在订阅上游发布者时应使用的调度器。

影响管道中调度器使用的其他操作符还包括`debounce`、`throttle`和`delay`。

在接下来的章节中，我们将探讨这些操作符如何影响一个接收 SwiftUI 事件处理器事件的 Combine 管道的运行过程。我们将使用以下发布者来模拟一段执行长时间计算的代码^(¹⁰³)：

```swift
func performWork() -> AnyPublisher {
    print("[performWork:start] isMainThread: \(Thread.isMainThread)")
    return Deferred {
        Future { promise in
            print("[performWork:Future:start] isMainThread: \(Thread.isMainThread)")
            sleep(5)
            print("[performWork:Future:finished] isMainThread: \(Thread.isMainThread)")
            promise(.success(true))
        }
    }
    .eraseToAnyPublisher()
}
```

由于`Future`会在创建后立即执行其闭包，而不会等待订阅者连接，因此我们需要将其包装在`Deferred`发布者中。这样做可以确保闭包中的代码仅在连接订阅者后才会执行，从而使我们能够影响该发布者使用的调度器。

### 使用`subscribe(on:)`控制上游发布者

通过使用`subscribe(on:)`操作符，你可以控制上游发布者运行在哪个调度队列上。

当你希望确保发布者在后台线程上运行时，这非常有用。与其将代码包裹在`DispatchQueue.global().async { }`调用中，不如添加一个`receive(on:)`调用。这种声明式方法将使你的代码更易于阅读和理解。

`subscribe(on:)`操作符指定了用于执行上游发布者的`subscribe`、`cancel`和`request`操作的调度器。

在以下代码片段中^(¹⁰⁴)，我们通过在管道中添加`subscribe(on: DispatchQueue.global(qos: .background)`调用，确保`performWork()`中的发布者在后台线程上运行：

```swift
func start() {
    print("[start:at beginning] isMainThread: \(Thread.isMainThread)")
    self.performWork()
        .handleEvents(receiveSubscription: { sub in
            print("[receiveSubscription] isMainThread: \(Thread.isMainThread)")
        }, receiveOutput: { value in
            print("[receiveOutput] isMainThread: \(Thread.isMainThread)")
        }, receiveCompletion: { completion in
            print("[receiveCompletion] isMainThread: \(Thread.isMainThread)")
        }, receiveCancel: {
            print("[receiveCancel] isMainThread: \(Thread.isMainThread)")
        }, receiveRequest: { demand in
            print("[receiveRequest] isMainThread: \(Thread.isMainThread)")
        })
        .map { value -> Bool in
            print("[map 1] isMainThread: \(Thread.isMainThread)")
            return value
        }
        .subscribe(on: DispatchQueue.global(qos: .background))
        .map { value -> Int in
            print("[map 2] isMainThread: \(Thread.isMainThread)")
            return self.times + 1
        }
        .sink { value in
            print("[sink] isMainThread: \(Thread.isMainThread)")
            self.times = value
        }
        .store(in: &self.cancellables)
    print("[start:at end] isMainThread: \(Thread.isMainThread)")
}
```

当从主线程调用此代码时（例如，在`Button`的动作处理程序中），你将在控制台上看到以下输出：

```
[start:at beginning] isMainThread: true
[performWork:start] isMainThread: true
[start:at end] isMainThread: true
[performWork:Future:start] isMainThread: false

[performWork:Future:finished] isMainThread: false
[receiveSubscription] isMainThread: false
[receiveRequest] isMainThread: false
[receiveOutput] isMainThread: false
[map 1] isMainThread: false
[map 2] isMainThread: false
[sink] isMainThread: false
2022-05-10 09:59:07.514607+0200 SwiftUICombineSchedulers[80945:27603444] [SwiftUI] Publishing changes from background threads is not allowed; make sure to publish values from the main thread (via operators like receive(on:)) on model updates.
[receiveCompletion] isMainThread: false
```

如你所见，调用始于主线程，但随后执行切换到后台线程。因此，发布者将在后台线程上执行，从而释放主线程用于其他与 UI 相关的工作。

如前所述，调用`subscribe(on:)`会影响上游发布者。然而，管道的其余部分*也*会使用你指定的调度器执行，这就是为什么 SwiftUI 会发出运行时警告，说我们不应从后台线程更新 UI。请记住，所有 UI 更新都应从主线程执行。

### 使用`receive(on:)`控制下游订阅者

通过使用`receive(on:)`操作符，你可以影响 Combine 将为所有下游操作符和订阅者使用哪个调度器。

这对于确保 Combine 管道的订阅者在主线程上运行非常有用——例如，当将值赋给与 SwiftUI 视图关联的已发布属性时：对该属性进行的任何更改都将导致 UI 更新，因此必须在主线程上进行。

让我们更新之前的代码片段，在`sink`操作符之前添加`.receive(on: DispatchQueue.main)`调用：

```swift
override func start() {
    print("[start:at beginning] isMainThread: \(Thread.isMainThread)")
    self.performWork()
        .handleEvents(receiveSubscription: { sub in
            print("[receiveSubscription] isMainThread: \(Thread.isMainThread)")
        }, receiveOutput: { value in
            print("[receiveOutput] isMainThread: \(Thread.isMainThread)")
        }, receiveCompletion: { completion in
            print("[receiveCompletion] isMainThread: \(Thread.isMainThread)")
        }, receiveCancel: {
            print("[receiveCancel] isMainThread: \(Thread.isMainThread)")
        }, receiveRequest: { demand in
            print("[receiveRequest] isMainThread: \(Thread.isMainThread)")
        })
        .map { value -> Bool in
            print("[map 1] isMainThread: \(Thread.isMainThread)")
            return value
        }
        .subscribe(on: DispatchQueue.global(qos: .background))
        .map { value -> Int in
            print("[map 2] isMainThread: \(Thread.isMainThread)")
            return self.times + 1
        }
        .receive(on: DispatchQueue.main)
        .sink { value in
            print("[sink] isMainThread: \(Thread.isMainThread)")
            self.times = value
        }
        .store(in: &self.cancellables)
    print("[start:at end] isMainThread: \(Thread.isMainThread)")
}
```

这将告诉 Combine 为任何下游操作符和订阅者使用`main`调度队列。在我们的例子中，这意味着`sink`订阅者将在主线程上执行，如下面的控制台输出所示。你还会注意到 SwiftUI 不再发出关于从后台发布模型更改的警告：

```
[start:at beginning] isMainThread: true
[performWork:start] isMainThread: true
[start:at end] isMainThread: true
[performWork:Future:start] isMainThread: false

[performWork:Future:finished] isMainThread: false
[receiveSubscription] isMainThread: false
[receiveRequest] isMainThread: false
[receiveOutput] isMainThread: false
[map 1] isMainThread: false
[map 2] isMainThread: false
[receiveCompletion] isMainThread: false
[sink] isMainThread: true
```



### 影响调度编排的其他操作符

Combine 中包含多个会影响事件向下游管道传递时序的操作符：

- `debounce` 仅在事件之间的指定时间间隔过后才发布元素。
- `throttle` 在指定时间间隔内发布上游发布者发布的最近元素或第一个元素。
- `delay` 在特定调度器上将所有输出延迟指定的时间量后，再传递给下游接收者。

所有这些操作符都接受一个时间间隔参数和一个调度器参数，用于确定操作符在何时传递其输出元素。让我们通过一个快速示例来理解其中的含义。

在 SwiftUI 中，一个常用的时序操作符是 `debounce`——它允许我们指定一个时间间隔，该间隔必须存在于操作符将发送给其下游订阅者的两个事件之间。这对于调用远程 API 根据用户输入执行搜索的搜索对话框特别有用。为了避免向远程 API 发送过多请求而造成过载，我们通常会在保存搜索词条的已发布属性上安装一个 `debounce` 操作符。

让我们来看一下前面关于优化网络层的章节中使用的一段代码：

```
$input
.debounce(for: 0.8, scheduler: DispatchQueue.main)
.handleEvents { subscription in
self.logEvent(tag: "handleEvents")
} receiveOutput: { value in
self.logEvent(tag: "receiveOutput - {\(value)}")
} receiveCompletion: { completion in
self.logEvent(tag: "receiveCompletion")
} receiveCancel: {
self.logEvent(tag: "receiveCancel")
} receiveRequest: { demand in
self.logEvent(tag: "receiveRequest")
}
.sink { value in
self.logEvent(tag: "sink - {\(value)}")
print("Value: \(value)")
self.output = value
}
.store(in: &cancellables)
```

这段代码获取用户在文本输入字段中输入的内容，然后使用 `debounce` 操作符来减少传递给下游订阅者的事件数量。这意味着下游订阅者只有在用户停止输入 0.8 秒后，才会接收到 `input` 属性的当前值。

如果改用 `DispatchQueue.global(qos: .background)`，则所有事件都会在后台线程上到达。

这意味着为某个调度操作符提供一个调度器，相当于添加了一个对 `subscribe(on:)` 的调用。

## 执行异步工作

在主线程上执行计算密集型工作并非良策——正如我们在前面的示例中看到的，在主线程上运行此类代码可能会导致 UI 卡顿，甚至完全阻塞 UI。

就像访问异步 API（例如网络、像 Firebase 这样的云服务，甚至是异步处理事件的本地 API）一样，您应通过在后端调度器上订阅相应的发布者（或操作符）来将任何此类代码卸载到后台线程。当后台进程完成并且发布者发出事件时，您最终需要切换到主线程来更新 UI。

以下是使用的一般模式：

```
publisher
.subscribe(on: DispatchQueue.global())
.receive(on: DispatchQueue.main)
.sink { }
```

您可以使用 `DispatchQueue.global(qos:)` 的重载版本来指定您希望用于后台运行代码的服务质量：

- `background`：后台任务在所有任务中优先级最低。将此类别分配给您的应用在后台运行时用于执行工作的任务或调度队列。
- `utility`：实用工具任务的优先级低于 default、userInitiated 和 userInteractive 任务，但高于 background 任务。将此服务质量类别分配给不会阻止用户继续使用您的应用的任务。例如，您可以将其分配给用户不会主动跟踪其进度的长时间运行任务。
- `default`：默认任务的优先级低于 userInitiated 和 userInteractive 任务，但高于 utility 和 background 任务。将此类别分配给您的应用代表用户启动或用于执行主动工作的任务或队列。
- `userInitiated`：用户发起任务在系统中的优先级仅次于用户交互任务。将此类别分配给为用户正在执行的操作提供即时结果，或阻止用户使用您的应用的任务。例如，您可以使用此服务质量类别来加载您想要显示给用户的电子邮件内容。
- `userInteractive`：用户交互任务在系统中具有最高优先级。将此类别用于与用户交互或主动更新您的应用用户界面的任务或队列。例如，将其用于动画或交互式跟踪事件。

要查看实际运行的示例，请参阅本章示例项目中的 `SchedulerDemoView.swift`。

## 与其他 API 集成

我们通常可以完全控制自己编写的代码，因此可以应用上一节概述的技术来控制管道的调度。但在使用他人编写的代码时，我们可能并不总能拥有这种便利。在本节中，我们将查看上游发布者控制调度的几个示例——以及我们如何影响管道其余部分的调度方式——这在将管道的结果分配给可能与 UI 连接的已发布属性时尤其重要。

### URLSession

首先看一下网络访问。我们在第 9 章中详细讨论了 `URLSession` 的使用，您可能还记得我们遇到了一些调度问题。下面是一个典型的代码片段，它从 URL 获取数据，然后将其分配给一个与 SwiftUI 视图连接的已发布属性：

```
URLSession.shared.dataTaskPublisher(for: url)
.map(\.data)
.decode(type: UserNameAvailableMessage.self,
decoder: JSONDecoder())
.map(\.isAvailable)
.replaceError(with: false)
.assign(to: &$isUsernameAvailable)
```

运行此代码将产生一个警告：

```
[SwiftUI] Publishing changes from background threads is not allowed; make sure to publish values from the main thread (via operators like receive(on:)) on model updates.
```

此警告是由于 `URLSession` 在后台线程上执行，因此管道的其余部分（包括 `assign` 操作符）也将在同一线程上执行造成的。这意味着 UI 将从后台线程更新，从而触发了该警告。

为避免这种情况，我们需要做的就是在 `assign` 操作符之前添加一个 `receive(on:)` 操作符，以确保我们从主线程访问 UI：

```
URLSession.shared.dataTaskPublisher(for: url)
.map(\.data)
.decode(type: UserNameAvailableMessage.self,
decoder: JSONDecoder())
.map(\.isAvailable)
.replaceError(with: false)
.receive(on: DispatchQueue.main)
.assign(to: &$isUsernameAvailable)
```



### Firebase

另一个能够自主管理调度的 API 示例是 Firebase，这是 Google 的应用开发平台。Firebase 的大部分服务（如 Cloud Firestore、Cloud Storage、Firebase Authentication 等）都是异步的，这意味着对 Firebase 的所有调用都应在后台线程上执行。我们来看一个示例，并了解 Firestore 是如何管理这一点的。

以下代码片段展示了如何从 Firestore 获取单个文档：

```swift
let docRef = Firestore.firestore()
.collection("books")
.document(documentId)
docRef.getDocument(as: Book.self) { result in
switch result {
case .success(let book):
self.book = book
self.errorMessage = nil
case .failure(let error):
self.errorMessage = "\(error.localizedDescription)"
}
}
```

Firestore 会创建一个串行调度队列（参见 `executor_libdispatch.mm`，第 362 行^(¹⁰⁵)），并使用它来执行所有向 Cloud Firestore 后端发起远程调用的操作：

```cpp
std::unique_ptr Executor::CreateSerial(const char* label) {
dispatch_queue_t queue =
dispatch_queue_create(label,
DISPATCH_QUEUE_SERIAL);
return absl::make_unique(queue);
}
```

一旦此调用完成，Firestore 将使用主调度队列来调用完成处理程序。如果你希望使用不同的调度队列来返回结果，可以通过 `FirestoreSettings.dispatchQueue`^(¹⁰⁶) 来设置自定义调度队列：

```swift
let settings = Firestore.firestore().settings
settings.dispatchQueue = DispatchQueue.global(qos: .background)
Firestore.firestore().settings = settings
```

当你需要在后台线程上执行多个相互依赖的操作，然后再更新 UI 时，这会很有用。

在前面的代码片段中，我们使用完成处理程序来处理对 `getDocument` 调用的结果。Firebase 也支持 Combine。以下是使用 Combine 时，之前代码片段的改写形式：

```swift
db.collection("books").document("hitchhiker").getDocument()
.tryMap { documentSnapshot in
try documentSnapshot.data(as: Book.self)
}
.replaceError(with: Book.empty)
.assign(to: &$book)
```

由于 Firestore 负责将所有操作调度到最合适的调度队列（一个用于获取数据的串行后台队列，以及一个用于返回结果的主调度队列），因此通常无需切换调度队列。

## 总结

传统上，开发者必须手动处理多线程，经常需要使用 `DispatchQueue` 和其他类似机制来切换线程。这不仅需要深入理解线程，还不可避免地导致代码冗长、难以阅读和维护。

Combine 将调度器作为声明式替代方案，帮助开发者避免手动将代码包装在 `DispatchQueue.main.async { }` 等调用中。相反，我们可以使用 `subscribe(on:)` 和 `receive(on:)` 等 Combine 操作符来声明 Combine 应使用的调度策略。

本章的关键要点如下：

1.  如果你未指定调度器，Combine 将在你发起调用的同一线程上运行代码。
2.  对于 SwiftUI，这很可能是主线程。
3.  然而，`URLSession` 等异步 API 可能会切换到后台线程。
4.  在这种情况下，你应该在更新 UI 之前使用 `receive(on:)` 切换回主线程。
5.  另一方面，某些 API（如 Firebase）可能会在返回结果之前切换回主线程。你需要意识到这一点，以避免不必要的线程跳转。
6.  如果你想将长时间运行的任务卸载到后台，可以使用 `subscribe(on:)` 操作符。

总的来说，Combine 的调度器让处理异步代码变得更加容易且不易出错。在编写 SwiftUI 代码时，Xcode 的紫色警告信息特别有用。

脚注 1 2 3 4 5 6 7 8

