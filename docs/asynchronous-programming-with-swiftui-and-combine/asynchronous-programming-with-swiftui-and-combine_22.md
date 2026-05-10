# 排版后的文档

```swift
@MainActor
func refresh() async {
    print("\(#function) is on main thread BEFORE await: \(Thread.isMainThread)")
    let result = await fetchRandomWord()
    randomWord = result.word
    print("\(#function) is on main thread AFTER await: \(Thread.isMainThread)")
}
```

这将解决从后台线程访问 UI 的所有问题。一旦我们在视图模型中添加更多访问 UI 的函数，将整个类标注为 `@MainActor` 可能会更高效，如下所示：

```swift
@MainActor
class LibraryViewModel: ObservableObject {
    ...
}
```

## 总结

在本章中，你学习了如何在 SwiftUI 中使用 Swift 5.5 新增的结构化并发模型。SwiftUI 提供了设计周密的机制，使从 UI 调用异步代码变得尽可能自然。

例如，你可以使用 `.task` 视图修饰符在视图出现时运行异步代码。使用 `.task` 而非 `.onAppear` 时，SwiftUI 会在视图消失时自动处理任务的取消。

搜索和刷新数据是另一种常见场景，通常需要运行异步代码。`.refreshable` 和 `.searchable` 视图修饰符为其闭包创建了一个异步上下文，因此你可以轻松地在其中调用异步代码。

如果你需要从非异步上下文（例如 `Button` 的动作处理器）调用异步代码，你可以通过将代码包装在 `Task { }` 块中，轻松地自行创建一个异步上下文。

你还看到，有时使用 Combine 驱动部分 UI 比使用 `async/await` 更合适。`.searchable()` 视图修饰符就是一个非常适合与 Combine 配合使用的 API 的绝佳示例。

许多示例都使用了 `List` 视图。第 5 章将更详细地介绍如何构建简单和高级的 `List` 视图。

脚注 1

