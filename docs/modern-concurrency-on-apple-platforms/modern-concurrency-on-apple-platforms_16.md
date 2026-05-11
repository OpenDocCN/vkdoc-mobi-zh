# 10. `@TaskLocal`

Swift 作为一种语言，拥有丰富的特性。语言本身通过引入许多功能来支持现代并发系统的存在，使得在编译时而非运行时捕获许多常见的并发问题成为可能。这使得即使应用在后台大量使用并发，也能轻松发布非常健壮的应用。

Swift 有一个略微晦涩的功能——属性包装器。它们在 SwiftUI 的许多地方被使用（`@State`、`@StateObject`、`@EnvironmentObject` 等），但除此之外，许多开发者并不知道可以有与 SwiftUI 无关的属性包装器。他们甚至可以创建自己的属性包装器。你可以几乎立即识别出属性包装器，因为它们以 `@` 符号开头，尽管它们可能与其他 Swift 功能（例如属性或全局 actor）混淆。

Swift 团队引入了一个有趣的属性包装器，用于与新的并发系统一起使用。我们说的是 `@TaskLocal`，它可以用于在任务树中的其他任务之间共享数据。正如我们所了解的，任务树决定了任务如何继承某些属性，包括它们运行的 actor、优先级、取消状态和*任务局部变量*。我们已经了解了之前所有继承上下文的工作原理，但尚未学习任务局部变量。

## `@TaskLocal` 属性包装器介绍

`@TaskLocal` 值可以在任务的上下文中读取和写入。该值是隐式共享的，并且任务本身创建的任何子任务（无论是 `async let` 还是组任务）都可以访问它。



### 使用 `@TaskLocal`

要使用这个属性包装器，标记为 `@TaskLocal` 的属性必须是 `static`。它们可以是可选类型，也可以有默认值。

读取它们的值时，你不需要做任何特殊处理。你可以像使用任何属性一样，在任何地方尝试使用该值，但如果该值未被父任务预先设置，它将是 `nil` 或你为其分配的默认值。

代码清单 10-1 展示了我们如何声明 `@TaskLocal` 变量。

```
class ViewController: UIViewController {
@TaskLocal static var currentVideogame: Videogame?
// ...
}
代码清单 10-1
声明 @TaskLocal 变量
```

`@TaskLocal` 变量必须是 `static`。在这个例子中，我们使用了在第 8 章中创建的 `Videogame` 结构体。现在我们可以使用这个属性在任务树中向下共享一个 `Videogame`。

现在，代码清单 10-2 展示了读取这个变量是多么简单。

```
// 在 ViewController 外部
func expensiveVidegameOperation() async {
if let vg = await ViewController.currentVideogame {
print("我们正在处理 \(vg.title)")
}
}
代码清单 10-2
读取 @TaskLocal 变量
```

这段假设的代码位于视图控制器外部的某个位置。我想强调的是，`@TaskLocal` 属性不受任何作用域的限制。它们仅受限于任务本身，而这个任务可以存在于许多不同的地方。

细心的读者可能已经注意到，访问这个变量是一个 `await` 操作。`TaskLocal` 属性会在任务内部同步对其的访问，以防止出现数据竞争。

写入 `@TaskLocal` 属性则不是那么直接。如果你尝试使用 `=` 运算符进行简单的赋值，编译器会阻止你，因为它是只读属性。

你不能进行简单的赋值，而是需要将变量绑定到任务树中。为此，你需要在变量名前使用 `$` 符号，并调用其上的 `withValue` 方法。为避免混淆，代码清单 10-3 展示了如何将变量绑定到任务。

```
override func viewDidLoad() {
super.viewDidLoad()
// 加载视图后进行任何额外的设置。
let vg = Videogame(title: "塞尔达传说：时之笛", year: 1998)
Self.$currentVideogame.withValue(vg) {
// 我们可以在这里启动一些使用 LocalValue 的异步任务
}
}
代码清单 10-3
绑定一个后续可由任务使用的值
```

如果你对 Swift 的属性包装器不熟悉，我建议你阅读我写的一篇关于此主题的专门博文。^(²) 简而言之，属性包装器可以增强普通属性的能力。通过使用美元符号 `$`，我们可以访问属性包装器的内部能力。我们的 `Videogame` 结构体没有提供 `withValue` 方法，但包装它的属性包装器提供了这个方法。

在代码清单 10-3 中，我们将一个新的 `vg` 变量绑定到我们的 `currentVideogame` 任务值。从这里开始生成的所有任务，只要它们是任务树的一部分，就可以访问它。

代码清单 10-4 将创建一个更复杂的层次结构，其中共享了一个 `@TaskLocal` 值。

```
class ViewController: UIViewController {
@TaskLocal static var currentVideogame: Videogame?
override func viewDidLoad() {
super.viewDidLoad()
// 加载视图后进行任何额外的设置。
let vg = Videogame(title: "塞尔达传说：时之笛", year: 1998)
Self.$currentVideogame.withValue(vg) {
Task {
await expensiveVidegameOperation()
Task {
await expensiveVidegameOperation()
Task.detached {
await expensiveVidegameOperation()
}
}
}
}
}
}
代码清单 10-4
在更复杂的层次结构中向下共享 @TaskLocal 变量
```

我们从绑定 `Videogame` 任务局部变量后启动一些任务开始。我们启动一个 `Task`，在其中调用 `expensiveVideogameOperation`，这将打印“我们正在处理 塞尔达传说：时之笛”。在它 `await` 之后，我们启动另一个 `Task`，它是当前 `Task` 的子任务。调用 `expensiveVideogameOperation` 也将打印“我们正在处理 塞尔达传说：时之笛”，因为这个子任务可以访问相同的父任务。

当我们启动一个分离任务时，情况就更有趣了。当我们启动分离任务时，我们也调用了 `expensiveVideogameOperation`，但这次它打印的是“在任务层次结构中未找到游戏！”正如我们在第 5 章中讨论的，分离任务完全独立于启动它们的任何任务，并且它们实际上没有父任务可言，尽管它们可以是其他任务的父任务（只要这些任务不是作为分离任务启动的）。因此，在代码清单 10-4 中，我们的分离任务没有 `currentVideogame`。它不属于之前子任务所处的同一任务树。

你可以在分离任务内将不同的 `Videogame` 绑定到 `@TaskLocal` 变量，并且分离任务的任何子任务都可以访问它——再次记住，分离任务实际上并不是它的子任务。代码清单 10-5 展示了这一能力。

```
Task.detached {
await expensiveVidegameOperation()
let anotherVg = Videogame(title: "深渊传说", year: 2005)
await Self.$currentVideogame.withValue(anotherVg) {
await expensiveVidegameOperation()
}
}
代码清单 10-5
为在分离任务内使用而分配 @TaskLocal 变量
```

## 总结

你可能会遇到需要将某个值共享给任务层次结构中所有子任务的情况。`@TaskLocal` 属性包装器非常适合这种情况。值将被共享给一个任务的所有子任务以及同一任务树内的所有任务，但任何分离任务由于它们的分离特性，将无法访问上层任务局部变量。



