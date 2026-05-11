# 15. 使用假件保持测试隔离，使用傀儡保持测试清晰

*当被测系统依赖全局状态时，如何保持测试隔离？*

*通过将依赖项转移到内存中，使用假件测试双打。*

*如何清晰区分哪些依赖影响被测试的行为，哪些不影响？*

*通过使用一个不做任何事的傀儡测试双打来替换那些不影响行为的依赖项。*

我们已经了解了测试双打，即被测系统依赖项的“测试专用等价物”，以及如何使用其中两种：存根控制被测系统的输入（第 8 章和第 10 章），间谍记录其对其他对象产生的影响效果（第 12 章）。

你可能偶尔还会用到另外两种测试双打。*假件* 用一个更简单的实现替换依赖项，并复现其行为。*傀儡* 提供一个不做任何事的实现来填充依赖项。在本章中，我们将学习如何使用它们以及何时使用。



## 假对象（Fake）：如何绕过缓慢或有状态的依赖

假设你想要让订单在应用重启后依然保持，这样用户切换到其他应用且被系统回收时就不会丢失订单。为了保持示例简洁，我们使用 `UserDefaults` 作为存储层。

第一步是为 `Order` 存储功能定义一个抽象，并让 `UserDefaults` 遵循该协议：

```
// OrderStoring.swift
protocol OrderStoring {
func getOrder() -> Order
func updateOrder(_ order: Order)
}
```

`UserDefaults` 如何实现 `OrderStoring` 协议的细节与我们此处要探讨的内容无关。你可以在本章的源码中找到可运行的版本。

下一步是重构 `OrderController`，让它使用遵循 `OrderStoring` 协议的类型来读取和更新订单：

```
// OrderController.swift
import Combine
import Foundation
class OrderController: ObservableObject {
@Published private(set) var order: Order
private let orderStoring: OrderStoring
init(orderStoring: OrderStoring = UserDefaults.standard) {
self.orderStoring = orderStoring
order = orderStoring.getOrder()
}
func isItemInOrder(_ item: MenuItem) -> Bool {
return order.items.contains { $0 == item }
}
func addToOrder(item: MenuItem) {
updateOrder(with: Order(items: order.items + [item]))
}
func removeFromOrder(item: MenuItem) {
let items = order.items
guard let indexToRemove = items.firstIndex(where: { $0.name == item.name }) else { return }
let newItems = items.enumerated().compactMap { (index, element) -> MenuItem? in
guard index == indexToRemove else { return element }
return .none
}
updateOrder(with: Order(items: newItems))
}
func resetOrder() {
updateOrder(with: Order(items: []))
}
private func updateOrder(with newOrder: Order) {
orderStoring.updateOrder(newOrder)
order = newOrder
}
}
```

重构之后，你预期测试能够通过，但实际情况并非如此。不仅测试会失败，而且多次运行时会出现不同的失败结果。

这些测试会执行生产代码，由于 `OrderController init` 中的默认值，生产代码使用了 `UserDefaults.standard`。

`UserDefaults` 是一个存储组件：它是有状态的。当测试运行时，它们通过存储值来改变 `UserDefaults` 的状态，并且这些更改会在测试和应用程序启动之间持续存在。

测试还从 `UserDefaults.standard` 读取数据，这意味着它们依赖于一个有状态的组件，并且不再孤立。而**假对象（Fake Test Double）**可以让我们打破这种依赖关系。

我们可以为 `OrderStoring` 构建一个 Fake，它的行为类似于 `UserDefaults`，但不会在单个测试执行或应用程序启动之间持久化其更改。这样，我们就可以让测试再次变得孤立且无状态。让我们用一个 Fake 来初始化 `OrderDetail.ViewModel`：

```
// OrderStoringFake.swift
@testable import Albertos
class OrderStoringFake: OrderStoring {
private var order: Order = Order(items: [])
func getOrder() -> Order {
return order
}
func updateOrder(_ order: Order) {
self.order = order
}
}
// OrderDetail.ViewModelTests.swift
// ...
class OrderDetailViewModelTests: XCTestCase {
func testWhenCheckoutButtonTappedStartsPaymentProcessingFlow() {
let orderController = OrderController(orderStoring: OrderStoringFake())
// ...
```

得益于 `OrderStoringFake`，每个测试现在都使用一个新的 `OrderStoring` 实例运行。测试是隔离的：一个测试对它的 `OrderStoringFake` 所做的更改不会影响其他测试。

我们可以通过在 `setUp` 和 `tearDown` 方法中重置其内容来解决 `UserDefaults` 的有状态问题，但这样做需要更多的工作，并且会使测试用例更长。此外，我们还需要在使用 `OrderDetail.ViewModel` 的每个测试用例中重复编写 `setUp` 和 `tearDown` 框架。总的来说，使用 Fake 需要更少的代码和维护量。

当依赖项速度缓慢时，Fake 也很有用。在第 10 章，我们使用了一个 Stub 来用恒定的 0.1 秒延迟替换不可预测的网络响应时间，并控制其发送的响应。类似地，我们的生产系统可能依赖缓慢或异步的计算，我们可以在测试中用更快的版本替换它们。

## 哑对象（Dummy）：如何提供测试所需但与行为无关的依赖

ViewModel 这样的对象通常需要`协调`与其视图交互的不同组件。例如，`OrderDetail.ViewModel`负责定义视图要显示的内容、启动支付流程并对其结果做出反应。

针对 `OrderDetail.ViewModel` 展示逻辑的测试需要一个遵循 `PaymentProcessing` 协议的实例来实例化 SUT，即使它们测试的输出不依赖于支付结果。

使用我们到目前为止看到的任何 Test Double 都能让测试编译通过，但也可能误导读者认为该依赖与测试相关。使用 Spy 会造成混淆，因为我们设置了它却从未读取它的任何属性。使用 Fake 或 Stub 则可能暗示该依赖提供了影响测试结果的间接输入：

```
// OrderDetail.ViewModelTests.swift
func testWhenOrderIsEmptyShouldNotShowTotalAmount() {
let viewModel = OrderDetail.ViewModel(
orderController: OrderController(
orderStoring: OrderStoringFake()
),
paymentProcessor: ???
onAlertDismiss: {}
)
XCTAssertNil(viewModel.totalText)
}
```

我们如何能清楚地表明 `PaymentProcessing` 实例不会影响这些测试？使用一个什么都不做的 Test Double——即 **Dummy（哑对象）**：

```
// PaymentProcessingDummy .swift
@testable import Albertos
import Combine
class PaymentProcessingDummy: PaymentProcessing {
func process(order: Order) -> AnyPublisher<Void, Error> {
return Result.success(())
.publisher
.eraseToAnyPublisher()
}
}
```

注意，这个 Dummy 的 `process(order:)` 实现中发布了一个 `Void` 值。我将 Dummy 定义为什么都不做的替身，但我们仍然需要编写它们使其能编译通过。在实现必须返回结果的 Dummy 时，选择你能想到的最简单的代码。Dummy 仅作为占位符使用，因此它们提供什么输出并不重要。

现在我们已经为 `PaymentProcessing` 构建了一个 Dummy，我们可以更新那些不直接依赖支付处理的测试，让它们使用它，例如：

```
// OrderDetail.ViewModelTests.swift
func testWhenOrderIsEmptyShouldNotShowTotalAmount() {
let viewModel = OrderDetail.ViewModel(
orderController: OrderController(
orderStoring: OrderStoringFake()
),
paymentProcessor: PaymentProcessingDummy(),
onAlertDismiss: {}
)
XCTAssertNil(viewModel.totalText)
}
```

正如 Gerard Meszaros 在 [*xUnit Test Patterns*](http://xunitpatterns.com/) 中解释的那样，Dummy 通过“省略构建真实对象所必需的不相关代码，并明确指出哪些对象和值不被 SUT 使用”来改进我们的测试。正如我们在第 4 章关于测试固件的讨论中提到的，一个测试越容易阅读和理解，就越容易使用。

我们同样可以将 Dummy 的思想应用于 `onAlertDismiss` 值，以表明它也不会影响测试：

```
// OrderDetail.ViewModelTests .swift
// ...
class OrderDetailViewModelTests: XCTestCase {
let alertDismissDummy: () -> Void = {}
// ...
func testWhenOrderIsEmptyShouldNotShowTotalAmount() {
let viewModel = OrderDetail.ViewModel(
orderController: OrderController(
orderStoring: OrderStoringFake()
),
paymentProcessor: PaymentProcessingDummy(),
onAlertDismiss: alertDismissDummy
)
XCTAssertNil(viewModel.totalText)
}
}
```

将 Fake 和 Dummy 与 Stub 和 Spy 一起添加到你的 Test Double 工具箱中，以便为任何类型的行为编写测试。使用 Fake 来避免依赖全局状态和缓慢的计算，并使用 Dummy 来明确那些是必需的但与测试无关的组件。



