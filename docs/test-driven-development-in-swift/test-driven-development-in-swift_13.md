# 10. 测试网络代码

*如何为与网络交互的代码编写测试？*

*通过使用`Stub`规避网络交互，来模拟不同行为并避免不稳定的测试。*

测试网络代码时常见的问题是网络既不可预测又缓慢，这会导致非确定性的测试——测试可能这次通过，下次就失败。

在本章中，我们将学习如何运用*依赖反转原则*和*桩测试替身*技术，将代码与网络的现实限制解耦。

远程菜单加载功能已接近完成。我们构建了一个能够在远程 API 数据传入时更新的视图，并使领域模型`Decodable`。现在是实现从网络加载数据的逻辑的时候了。我们将负责此功能的对象称为`MenuFetcher`。

Apple 在 Foundation 框架中通过`URLSession`提供了一个先进且通用的网络调用系统，我们将用它来实现网络功能。我们还知道`MenuFetcher`必须遵循`MenuFetching`，这是我们在第 7 章定义的抽象。该`protocol`是 ViewModel 与底层组件之间关于如何从远程资源获取菜单的契约。

`MenuFetching`定义了我们需要实现的行为。当请求成功时，它应返回接收到的`Data`并转换为`[MenuItem]`。当请求失败时，它应传递接收到的错误。让我们根据这个规范为`MenuFetcher`编写测试列表：

```
// MenuFetcherTests.swift
@testable import Albertos
import XCTest
class MenuFetcherTests: XCTestCase {
func testWhenRequestSucceedsPublishesDecodedMenuItems() {}
func testWhenRequestFailsPublishesReceivedError() {}
}
```

这里一个自然的起点是使用`URLSession`直接调用远程 API，并断言结果符合我们的预期。

在单元测试中直接访问网络是不可取的。这会导致测试缓慢且不稳定，并且难以覆盖所有不同场景。让我们看看原因以及应对方法。

## 为什么不应在单元测试中发起网络请求

为了理解测试直接网络调用的局限性，让我们自己编写这样一个测试。

到目前为止，我们在本书中对于大多数新代码都使用了愿望驱动编程。当你还不确定代码应采用何种形态时，愿望驱动编程有助于开始编写代码，但我们现在对 Combine 已有足够了解，可以清晰地知道应使用的结构。让我们从`MenuFetcher`的不完整实现开始，这样我们就可以编写一个没有编译错误的完整测试：

```
// MenuFetcher.swift
import Combine
class MenuFetcher: MenuFetching {
func fetchMenu() -> AnyPublisher {
return Future { $0(.success([])) }.eraseToAnyPublisher()
}
}
```

获取请求返回一个 `AnyPublisher<[MenuItem], Error>`，它会发送异步更新。我们在第 7 章已经看到，可以通过使用`sink`订阅它，并使用`XCTestExpectation`等待新值来测试这类代码：

```
// MenuFetcherTests.swift
// ...
func testWhenRequestSucceedsPublishesDecodeMenuItems() {
let menuFetcher = MenuFetcher()
let expectation = XCTestExpectation(description: "Publishes decoded [MenuItem]")
menuFetcher.fetchMenu()
.sink(
receiveCompletion: { _ in },
receiveValue: { items in
// 如何测试 items 的值是否正确？
expectation.fulfill()
}
)
.store(in: &cancellables)
wait(for: [expectation], timeout: 1)
}
```

编写这个测试引出一个问题：我们如何断言`MenuFetcher`发布了正确的值？

由于数据来自远程 API，唯一知道期望值的方法就是查看远程数据。一旦我们知道后端保存了哪些值，就可以使用一些粗略的断言，例如，检查数量是否相同，以及第一个和最后一个元素是否匹配。

你可以在[`https://github.com/mokagio/tddinswift_fake_api/`](https://github.com/mokagio/tddinswift_fake_api/)找到一个假 API 版本。为了遵循小步前进、只编写必要代码的原则，这个假 API 实际上只是一个 JSON 文件的容器。菜单列表的端点不过是假响应静态 JSON 的 URL。

从远程资源获取静态 JSON 是模拟与完整 API 后端交互的真实行为的绝佳方法：

```
// MenuFetcherTests.swift
// ...
menuFetcher.fetchMenu()
.sink(
receiveCompletion: { _ in },
receiveValue: { items in
XCTAssertEqual(items.count, 42)
XCTAssertEqual(items.first?.name, "spaghetti carbonara")
XCTAssertEqual(items.last?.name, "pasta all'arrabbiata")            expectation.fulfill()
}
)
```

在这种情况下使用较宽松的断言是可以的，因为我们在前一章已经为解码编写了全面的测试。如果不是这种情况，并且 JSON 解码逻辑并不简单，我会鼓励你至少检查数组中的一个元素是否具有其对应 JSON 中的所有预期属性。

使用`Cmd U`键盘快捷键运行测试将显示失败。这并不意外：我们的初始实现返回一个硬编码的空数组。

让我们使用`URLSession`和 Combine 实现一个真正的网络调用：

```
// MenuFetcher.swift
import Combine
import Foundation
class MenuFetcher: MenuFetching {
func fetchMenu() -> AnyPublisher {
let url = URL(string:
"https://raw.githubusercontent.com/mokagio/tddinswift_fake_api/trunk/menu_response.json")!
URLSession.shared
.dataTaskPublisher(for: URLRequest(url: url))
.map { $0.data }
.decode(type: [MenuItem].self, decoder: JSONDecoder())
.eraseToAnyPublisher()
}
}
```

现在测试应该通过了。我之所以说*应该*，是因为有几个因素可能导致它失败。

首先，如果后端菜单发生变化，测试将会失败。例如，如果 Alberto 添加了一道新菜，那么关于数量的预期就会失败。



## 根据变量值进行测试的局限

**根据变量值进行测试会使你的单元测试变得不可预测，并且需要持续更新测试套件。** 为菜单设置专用 API 的全部意义在于简化更新过程；我们可以预期菜单在未来会多次变更。每次远程菜单发生变化时，测试都可能会失败。你可能正在开发一个新功能，却意外发现这个测试突然失败了。

另一个导致此测试不可预测的因素是它对网络的依赖。API 可能因为连接质量差而响应缓慢，或者在没有互联网连接时完全不响应。如果发生这种情况，测试会失败，但原因是外部因素而非你的源代码。

取决于你的连接质量，在`wait(for:, timeout:)`中使用的 1 秒可能不够，测试将会超时。你可能需要使用更长的时间间隔来使测试对慢速网络更健壮，但这会以测试套件运行缓慢为代价。

即使使用更长的超时时间，你仍然容易受到其他网络故障的影响。要验证这一点，请从模拟器中删除应用程序以清除任何缓存值，通过关闭 Wi-Fi 或拔下以太网电缆断开 Mac 的互联网连接，然后重新运行测试。测试将失败并显示：

*   异步等待失败：超出 1 秒超时，存在未满足的期望：“发布已解码的 [MenuItem]。”

**与网络交互会因响应时间的开销而拖慢你的单元测试**。测试驱动开发的优势之一是其快速的反馈循环，但从测试中执行真实的网络请求会降低其速度，使整个过程效率降低。

即使你愿意忽略这些限制，当 API 请求失败时，你又该如何测试代码的行为呢？

解决之道是应用*依赖反转原则*。

## 强制解包可选值

你可能已经注意到，代码通过调用`URL(string:)`并传入一个硬编码值来获取 URL。`URL(string:)`返回一个`Optional`值，我们使用`!`后缀来[强制解包](https://docs.swift.org/swift-book/LanguageGuide/TheBasics.html%2523ID332)它。

强制解包一个`Optional`是告诉 Swift 编译器我们相信其值在运行时永远不会是`none`的方式，从而避免使用`if let`或`guard let`手动解包的需求。

通常最好避免强制解包：如果由于某种原因，该值在运行时为`none`，应用程序将会崩溃。最好多写几行代码来显式解包`Optional`，并处理它们没有值的情况。

虽然避免强制解包是明智的做法，但这个`URL(string:)`调用只有在编写硬编码值的开发者犯错时才会返回`none`。正如 Chris Eidhof、Daniel Eggert 和 Florian Kugler[论证](https://www.objc.io/blog/2018/03/27/unwrapping-optionals/)的那样，这种强制解包的使用是一种通过让应用程序尽早崩溃来暴露程序员错误的有用方式。由于我们实践 TDD，并且大部分代码都被测试覆盖，像这样的程序员错误导致崩溃而不被发现的可能性很小，因此我们可以在这些特定情况下放心使用强制解包。

## 如何将单元测试与网络解耦

`MenuFetcher`依赖于`URLSession`来访问网络。正如我们在第 7 章中所见，高层组件不应依赖于低层组件；它们都应该依赖于抽象。这就是*依赖反转原则*。

网络只是我们系统的另一个依赖。我们可以在`MenuFetcher`和`URLSession`之间放置一个抽象层，并构建一个*Stub 测试替身*来模拟网络提供给 SUT 的间接输入。

我们使用了`URLSession dataTaskPublisher(for:)`方法来执行网络请求，该方法返回一个`AnyPublisher<(Data, URLResponse), URLError>`。我们将要定义的抽象应该具有类似的签名，区别在于我们的业务逻辑不需要读取`URLResponse`：

```
// NetworkFetching.swift
import Combine
import Foundation
protocol NetworkFetching {
func load(_ request: URLRequest) -> AnyPublisher
}
```

我们可以通过一个简洁的`extension`使`URLSession`符合`NetworkFetching`协议：

```
// URLSession+NetworkFetching.swift
import Combine
import Foundation
extension URLSession: NetworkFetching {
func load(_ request: URLRequest) -> AnyPublisher {
return dataTaskPublisher(for: request)
.map { $0.data }
.eraseToAnyPublisher()
}
}
```

保持符合`protocol`的代码尽可能简单且不含自定义逻辑至关重要，因为我们不会对其进行测试。通过这样的实现，所有代码都不在我们的控制之下，因为它是 Apple 的代码。测试`URLSession`不是我们的责任，我们可以相对确信它会始终按预期运行。¹

让我们应用 DIP 并重构`MenuFetcher`，使其依赖于`NetworkFetching`而不是直接调用`URLSession`：

```
// MenuFetcher.swift
import Combine
import Foundation
class MenuFetcher: MenuFetching {
let networkFetching: NetworkFetching
init(networkFetching: NetworkFetching = URLSession.shared) {
self.networkFetching = networkFetching
}
func fetchMenu() -> AnyPublisher {
let url = URL(string:
"https://raw.githubusercontent.com/mokagio/tddinswift_fake_api/trunk/menu_response.json")!
networkFetching
.load(URLRequest(url: url))
.decode(type: [MenuItem].self, decoder: JSONDecoder())
.eraseToAnyPublisher()
}
}
```

测试仍然通过了，但存在前面讨论过的注意事项和局限性。现在是时候为`NetworkFetching`构建一个*Stub*，并用它来模拟我们希望测试的不同行为了。



## 使用桩模拟网络请求

为了编写测试，我们需要一种方法为测试提供预定义的输入以供校验：要么是用于解码的 `Data`，要么是一个 `URLError` 值。为此，我们可以使用在第 8 章学到的相同技术构建一个桩测试替身：

```
// NetworkFetchingStub.swift
@testable import Albertos
import Combine
import Foundation
class NetworkFetchingStub: NetworkFetching {
private let result: Result
init(returning result: Result) {
self.result = result
}
func load(_ request: URLRequest) -> AnyPublisher {
return result.publisher
// 添加延迟以模拟真实世界的异步行为
.delay(for: 0.01, scheduler: RunLoop.main)
.eraseToAnyPublisher()
}
}
```

我们可以使用这个桩来解除与网络非确定性特性的耦合，从而更好地控制测试的输入：

```
// MenuFetcherTests.swift
// ...
func testWhenRequestSucceedsPublishesDecodedMenuItems() throws {
let json = """
[
{ "name": "a name", "category": "a category", "spicy": true },
{ "name": "another name", "category": "a category", "spicy": true }
]
"""
let data = try XCTUnwrap(json.data(using: .utf8))
let menuFetcher = MenuFetcher(networkFetching: NetworkFetchingStub(returning: .success(data)))
let expectation = XCTestExpectation(description: "发布已解码的 [MenuItem]")
menuFetcher.fetchMenu()
.sink(
receiveCompletion: { _ in },
receiveValue: { items in
XCTAssertEqual(items.count, 2)
XCTAssertEqual(items.first?.name, "a name")
XCTAssertEqual(items.last?.name, "another name")
expectation.fulfill()
}
)
.store(in: &cancellables)
wait(for: [expectation], timeout: 1)
}
```

使用桩消除了对网络的依赖，使测试运行更快、更健壮。如果你现在尝试离线运行测试，会发现它们都能通过。

在网络测试中使用桩也有助于明确断言中使用的值来自何处：它们现在已是测试本身的一部分。

接下来，我们来看失败处理的测试：

```
// MenuFetcherTests.swift
// ...
func testWhenRequestFailsPublishesReceivedError() {
let expectedError = URLError(.badServerResponse)
let menuFetcher = MenuFetcher(networkFetching: NetworkFetchingStub(returning: .failure(expectedError)))
let expectation = XCTestExpectation(description: "发布接收到的 URLError")
menuFetcher.fetchMenu()
.sink(
receiveCompletion: { completion in
guard case .failure(let error) = completion else { return }
XCTAssertEqual(error as? URLError, expectedError)
expectation.fulfill()
},
receiveValue: { items in
XCTFail("期望失败，但成功获取了 \(items)")
}
)
.store(in: &cancellables)
wait(for: [expectation], timeout: 1)
}
```

这个测试已经能通过。原因是我们测试的是 Combine 对 `URLSession` 扩展的默认行为。

尽管有人可能认为这个测试是多余的，但我建议你保留它。确保代码能够妥善处理失败，是构建优秀用户体验的健壮软件的关键。为此编写明确的测试，表明我们认真对待错误处理。错误处理全部由 Combine 在底层完成这一事实是具体的实现细节，未来可能会发生变化。一个专门的测试能确保一旦错误处理逻辑改变，你能立即知晓。

### 第三方替代方案

本书仅涉及原生 `XCTest`，但在测试网络代码方面，有一个值得提及的第三方库：[OHHTTPStubs](https://github.com/AliSoftware/OHHTTPStubs)。该库允许你直接在 `URLSession` 层面桩化网络请求，而无需创建专门的抽象层，这使其成为为大型遗留代码库添加测试的绝佳选择——这类代码库在重构前往往难以修改。

通过将测试与网络本身隔离，你可以专注于测试应用如何发起请求并处理其结果，从而测试网络逻辑。

通过定义一个抽象来隐藏 `URLSession` 的内部细节，你可以模拟来自网络的任何响应，并使用桩测试替身编写全面的测试，同时消除真实 HTTP 请求带来的固有非确定性影响。

请尽量保持遵循该抽象层的代码量最少，最好只使用 Apple 提供的方法。即使没有为它编写测试，你也能对其正确行为充满信心。

## 练习时间

假设 API 有另一个返回单个条目的“今日推荐菜”端点。你将如何运用测试驱动开发来实现从该端点读取数据？你会将功能添加到 `MenuFetching` 中，还是创建一个新的专用抽象？`NetworkFetchingStub` 是否需要变更，还是它已准备好支持这个测试？

你可以在 [`https://raw.githubusercontent.com/mokagio/tddinswift_fake_api/trunk/dish_of_the_day.json`](https://raw.githubusercontent.com/mokagio/tddinswift_fake_api/trunk/dish_of_the_day.json) 找到该端点的响应数据。

这里还有一个练习给你。目前，`MenuFetcher` 使用一个硬编码值作为 API 端点。硬编码值有助于快速入门，但它们不够灵活。例如，你无法通过硬编码的 URL 让一个版本的应用连接预发布环境、另一个版本连接生产环境的后端。

更好的方法是使用基础 URL 参数初始化 `MenuFetcher`，使其不关心 API 部署在何处。在内部，`MenuFetcher` 可以通过将适当路径附加到基础值来构造端点 URL。你可以编写哪些测试来实现这一改进？

**提示**

修改桩，使其期望一个 `URLRequest` 和 `Result` 配对，并仅当传入 `load(_:)` 的 `URLRequest` 与给定的相匹配时，才返回给定的 `Result`。

## 关键要点

-   **不要在单元测试中发起网络请求**。访问网络会因真实世界的物理限制使测试产生非确定性并变慢。

-   **定义一个抽象层，将源代码和测试代码与底层的网络实现解耦**。通过应用依赖倒置原则，你将构建出更模块化的软件，并能够使用测试替身。

-   **围绕 `URLSession` 使用尽可能薄的封装**。由于这部分代码无法直接测试，它应该尽可能精简和简单。

-   **使用桩测试替身来模拟来自网络的成功和失败响应**。借助桩，你可以为网络组件提供任何类型的输入。

## 尾注

1.  如果你想更进一步，对 `URLSession` 的行为进行安全检查，可以编写一个集成测试来验证 `URLSession` 上的 `NetworkFetching` 方法实现。正如我们所见，与真实网络交互会使测试产生非确定性并变慢。该测试应放在一个专用的测试目标中，不与单元测试一起运行。当你升级到新版本的 Xcode 时，可以将其作为安全检查来使用。



