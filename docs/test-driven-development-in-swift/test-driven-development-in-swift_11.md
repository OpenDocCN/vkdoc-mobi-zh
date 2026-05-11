# 8. 测试基于间接输入的代码

*如何隔离测试依赖于间接输入的行为？*

*通过为依赖项构建一个测试专用的替代品，你可以用它来控制依赖项提供给被测系统的间接输入。*

我们正在构建从远程 API 获取新菜单功能的 UI 组件。我们从 UI 开始，以拥有动态更新的*最早可测试*版本，但这意味着我们还没有真实的网络组件。

在上一章中，我们应用了*依赖注入原则*，并为 ViewModel 所依赖的 `MenuFetching` 定义了一个抽象，这使我们能够使用网络对象的占位实现，并专注于当前任务。这让我们能够编写测试，驱动 `MenuList.ViewModel` 在收到菜单数据后更新视图，但这些测试并不完整：我们还没有测试错误处理，并且成功的获取测试中存在一个“神秘嘉宾”。

测试 `MenuList.ViewModel` 行为的挑战在于，它依赖于 `MenuFetching` 提供的间接输入。到目前为止，我们测试的组件都是通过方法接收输入并返回输出，但 `MenuList.ViewModel` 的输入来自于它在内部调用 `MenuFetching.fetchMenu()` 时获得的返回值。

在本章中，我们将学习如何使用*桩测试替身*来解决这个限制，从而对 `MenuFetching` 发送给 `MenuList.ViewModel` 的间接输入进行细粒度控制。

## 桩测试替身

在上一章中，我们为成功的菜单获取场景编写了以下测试：

```
// MenuList.ViewModelTests.swift
func testWhenFecthingSucceedsPublishesSectionsBuiltFromReceivedMenuAndGivenGroupingClosure() {
    var receivedMenu: [MenuItem]?
    let expectedSections = [MenuSection.fixture()]
    let spyClosure: ([MenuItem]) -> [MenuSection] = { items in
        receivedMenu = items
        return expectedSections
    }
    let viewModel = MenuList.ViewModel(menuFetching: MenuFetchingPlaceholder(), menuGrouping: spyClosure)
    let expectation = XCTestExpectation(
        description: "Publishes sections built from received menu and given grouping closure"
    )
    viewModel
        .$sections
        .dropFirst()
        .sink { value in
            // 确保分组闭包被调用时传入的是接收到的菜单
            XCTAssertEqual(receivedMenu, menu)
            // 确保发布的值是分组闭包的结果
            XCTAssertEqual(value, expectedSections)
            expectation.fulfill()
        }
        .store(in: &cancellables)
    wait(for: [expectation], timeout: 1)
}
```

该测试使用全局的 `menu` 常量来断言 ViewModel 正确调用其分组闭包。`menu` 是一个*神秘嘉宾*：除非你最近看过 `MenuFetchingPlaceholder` 的实现，否则你不会知道这就是它返回的值。

难以理解的测试是一种负担：当它们失败时，弄清楚原因以及修复它们会花费更长的时间。一个匆忙或不懂得测试价值的开发者可能会注释掉测试“稍后修复”，但我们都知道“稍后”永远不会到来。

我们如何让间接输入与结果行为之间的关系变得清晰？有没有一种方法可以在“安排”阶段，像我们之前处理直接输入一样，以同样直接的方式配置间接输入？是的，有办法。

因为 `MenuList.ViewModel` 依赖于抽象而非具体类型，我们可以构建另一个 `MenuFetching` 实现，在其中配置它提供的间接输入，并在测试中使用它，这毫无阻碍。

在 *xUnit 测试模式* 中，Gerard Meszaros 将被测系统依赖的这种“测试专用等价物”称为*测试替身*。

根据依赖项在测试行为中所扮演的角色，我们可以使用不同类型的测试替身。我们即将构建的是*桩*。

桩是一种测试替身，它提供对被测系统间接输入的控制：

```
// MenuFetchingStub.swift
@testable import Albertos
import Combine
import Foundation

class MenuFetchingStub: MenuFetching {
    let result: Result<[MenuItem], Error>

    init(returning result: Result<[MenuItem], Error>) {
        self.result = result
    }

    func fetchMenu() -> AnyPublisher<[MenuItem], Error> {
        return result.publisher
            // 使用延迟来模拟现实世界的异步行为
            .delay(for: 0.1, scheduler: RunLoop.main)
            .eraseToAnyPublisher()
    }
}
```

桩的工作方式取决于它所替代的依赖项类型，但指导原则始终是提供一种配置所需输入的方法，并在桩的实现中使用它。

在这个例子中，我们用响应的 `Result` 初始化桩，然后将其转换为 `AnyPublisher` 作为 `fetchMenu()` 的返回值。

另外，注意用于模拟异步行为的延迟。有时，通过提供立即返回所需值的测试替身来从测试中移除异步行为是有用的。然而，在这个例子中，异步行为正是我们要实现的。移除延迟会使测试脱离真实行为，并可能导致误报。

现在，我们可以通过用 `MenuFetchingStub` 替换 `MenuFetchingPlaceholder` 来驱逐神秘的 `menu` 访客：

```
func testWhenFecthingSucceedsPublishesSectionsBuiltFromReceivedMenuAndGivenGroupingClosure() {
    var receivedMenu: [MenuItem]?
    let expectedSections = [MenuSection.fixture()]
    let spyClosure: ([MenuItem]) -> [MenuSection] = { items in
        receivedMenu = items
        return expectedSections
    }
    let expectedMenu = [MenuItem.fixture()]
    let menuFetchingStub = MenuFetchingStub(returning: .success(expectedMenu))
    let viewModel = MenuList.ViewModel(menuFetching: menuFetchingStub, menuGrouping: spyClosure)
    let expectation = XCTestExpectation(
        description: "Publishes sections built from received menu and given grouping closure"
    )
    viewModel
        .$sections
        .dropFirst()
        .sink { value in
            // 确保分组闭包被调用时传入的是接收到的菜单
            XCTAssertEqual(receivedMenu, expectedMenu)
            // 确保发布的值是分组闭包的结果
            XCTAssertEqual(value, expectedSections)
            expectation.fulfill()
        }
        .store(in: &cancellables)
    wait(for: [expectation], timeout: 1)
}
```

测试仍然通过。对于 `MenuList.ViewModel` 来说，没有任何变化。ViewModel 不知道也不关心我们给它的是哪个具体的 `MenuFetching` 实现——无论是占位实现、测试替身还是真实的实现。

我们现在对被测系统的间接输入与其输出之间有了清晰的因果关系。接下来让我们转向列表中最后一个同样重要的测试：错误处理的测试。



### 使用 `Result` 显式处理错误

目前，`sections` 属性的类型是 `[MenuSection]`，但 `MenuFetching` 也可能抛出错误，而视图需要能显示这个错误。

为了处理这两种情况，我们可以使用 `Result` 类型。`Sections` 可以变成 `Result<[MenuSection], Error>`，我们可以更新 `MenuList`，让它根据结果是 `success` 还是 `failure` 来渲染不同的内容。

像 `Result` 这样的枚举的妙处在于，它能在类型系统层面明确地表达成功和失败情况之间的互斥关系。视图要么处于成功状态，要么处于失败状态，而 `Result` 实例要么是 `.success` 情况，要么是 `.failure` 情况。

让我们利用编译器错误作为快速反馈机制来进行这项修改：

```
// MenuList.ViewModel.swift
// ...
class ViewModel: ObservableObject {
@Published private var sections: Result = .success([])
init(
menuFetching: MenuFetching,
menuGrouping: @escaping ([MenuItem]) -> [MenuSection] = groupMenuByCategory
) {
menuFetching
.fetchMenu()
.sink(
receiveCompletion: { _ in },
receiveValue: { [weak self] value in
self?.sections = .success(menuGrouping(value))
}
)
.store(in: &cancellables)
```

在我们更新 `MenuList.ViewModel` 后，由于类型变化，它的视图无法构建：

```
// MenuList.swift
// ...
var body: some View {
List {
ForEach(viewModel.sections) { section in
// 编译器提示：无法将类型 'Result' 的值
// 转换为预期的参数类型 'Range'
//
// 后续访问 sections 时还会出现更多错误...
```

我们需要更新 `MenuList` 来处理 `Result` 类型的 `success` 和 `failure` 两种情况：

```
// MenuList.swift
// ...
var body: some View {
switch viewModel.sections {
case .success(let sections):
List {
ForEach(sections) { section in
Section(header: Text(section.category)) {
ForEach(section.items) { item in
MenuRow(viewModel: .init(item: item))
}
}
}
}
case .failure(let error):
Text("发生了一个错误：")
Text(error.localizedDescription).italic()
}
}
```

更新 `MenuList` 后，源代码构建成功，但测试无法通过。正如我们在前一章中讨论过的，我们现在才看到这个失败，是因为 Xcode 在构建生产目标之前不会构建测试目标。

编译器高亮显示了测试中需要更新的代码，伴随的失败提示如下：

```
// MenuList.ViewModelTests.swift
// ...
XCTAssertTrue(viewModel.sections.isEmpty)
// 编译器提示：无法将类型 'Result' 的值
// 转换为预期的参数类型 'Range'
```

我们先来更新默认值行为的测试：

```
// MenuList.ViewModelTests.swift
// ...
func testWhenFetchingStartsPublishesEmptyMenu() throws {
let viewModel = MenuList.ViewModel(menuFetching: MenuFetchingStub(returning: .success([.fixture()])))
let sections = try viewModel.sections.get()
XCTAssertTrue(sections.isEmpty)
}
```

`.get()` 是一个方便的 `Result` 方法，它会返回成功实例的关联值，否则会抛出异常。

确保 `Result` 值为 `success` 的另一种方法是使用 `guard case` 语句。下面是在成功场景测试中的应用示例：

```
// MenuList.ViewModelTests.swift
// ...
func testWhenFetchingSucceedsPublishesSectionsBuiltFromReceivedMenuAndGivenGroupingClosure() {
// ...
viewModel
.$sections
.dropFirst()
.sink { value in
guard case .success(let sections) = value else {
return XCTFail("期望一个成功的 Result，得到：\(value)")
}
// 确保分组闭包是用接收到的菜单调用的
XCTAssertEqual(receivedMenu, expectedMenu)
// 确保发布的值是分组闭包的结果
XCTAssertEqual(sections, expectedSections)
expectation.fulfill()
}
.store(in: &cancellables)
// ...
}
```

现在代码可以编译，测试也仍然是绿色的。我们终于可以继续处理失败行为的测试了。我们已经学习了如何为 `@Published` 属性和 `Result` 类型编写测试；现在只需要使用不同的状态设置和预期即可。

为了给我们的 SUT 提供来自 `MenuFetching` 的失败结果，我们需要用一个 `failure` 值来初始化 `MenuFetchingStub`。我们应该用什么作为错误呢？

`sections` 类型在其定义中使用了泛型 `Error`，但测试中需要一个具体的错误类型。我们可以为测试定义一个专用的错误类型：

```
// TestError.swift
struct TestError: Equatable, Error {
let id: Int
}
```

通过让 `TestError` 遵循 `Equatable` 协议，我们可以将其与 `XCTAssertEqual` 一起使用，使测试变得直接明了。

让我们配置 Stub 让它返回一个已知的测试错误，并确保我们的代码将其传递到 `sections`：

```
func testWhenFetchingFailsPublishesAnError() {
let expectedError = TestError(id: 123)
let menuFetchingStub = MenuFetchingStub(returning: .failure(expectedError))
let viewModel = MenuList.ViewModel(menuFetching: menuFetchingStub, menuGrouping: { _ in [] })
let expectation = XCTestExpectation(description: "发布一个错误")
viewModel
.$sections
.dropFirst()
.sink { value in
guard case .failure(let error) = value else {
return XCTFail("期望一个失败的 Result，得到：\(value)")
}
XCTAssertEqual(error as? TestError, expectedError)
expectation.fulfill()
}
.store(in: &cancellables)
wait(for: [expectation], timeout: 1)
}
```

测试因超时而失败。为了让它通过，我们需要在 ViewModel 中处理 `MenuFetching` 可能发布的 `.failure` 情况：

```
// MenuList.ViewModel.swift
// ...
menuFetching
.fetchMenu()
.sink(
receiveCompletion: { [weak self] completion in
guard case .failure(let error) = completion else { return }
self?.sections = .failure(error)
},
receiveValue: { [weak self] value in
self?.sections = .success(menuGrouping(value))
}
)
.store(in: &cancellables)
```

现在我们已经处于绿态，功能也完成了，是时候问一下我们是否可以改进到目前为止编写的代码了。

一个不错的改进是将从 `[MenuItem]` 到 `[MenuSection]` 的转换提取为一个专门的步骤：

```
menuFetching
.fetchMenu()
.map(menuGrouping)
.sink(
receiveCompletion: { [weak self] completion in
guard case .failure(let error) = completion else { return }
self?.sections = .failure(error)
},
receiveValue: { [weak self] value in
self?.sections = .success(value)
}
)
.store(in: &cancellables)
```

这种对输入的处理与对 `@Published` 属性的赋值之间的分离，使数据流更加清晰。

*依赖倒置原则*指出，高层模块不应依赖低层模块，而应依赖抽象。结合本章和前一章，我们看到了应用这一原则所带来的实际好处。

我们已经构建了一个功能完整的动态更新 UI，用于显示从远程 API 获取的菜单列表，尽管我们还没有网络组件。我们确信我们的实现现在可以正常工作，并且在接入真实 API 获取时也能工作，因为指导编写它的测试使用了一个 *Stub* 来模拟 `MenuFetching` 遵循类型可能产生的各种输入。

现在，靠近视图层的代码已经就位，下一步是向下移动一层，将 JSON 数据转换为 `MenuItem`。

## 练习时间

你决定通过添加重试功能来增强菜单 UX 的弹性。失败视图应该有一个按钮，能让 ViewModel 重新开始获取菜单。

你如何测试这个新行为？

**提示**

一种可行的方法就是让 Stub 发布不同的值，例如先发布一个错误，再发布一个成功，然后编写一个测试来执行重试方法并验证发布值的序列。



## 关键要点

*   **应用依赖倒置原则可以让你使用测试替身编写测试**。测试替身是受测系统依赖项的“测试专用等价物”，可用于保持测试隔离并模拟不同的行为。
*   **用桩对象替换依赖项，以控制其向受测系统提供的间接输入**。使用桩对象，你可以测试许多不同的间接输入场景，始终让输入与输出之间的因果关系清晰明确。
*   **使用** `enum` **来描述 SwiftUI 视图可能处于的互斥状态**。例如，使用 `Result` 来表示由成功或失败操作生成的内容。

