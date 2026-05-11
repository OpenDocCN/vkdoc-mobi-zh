# 3. 测试驱动开发入门

在前面的章节中，我们讨论了使用测试来驱动代码设计的优势，以及如何使用 Apple 的 `XCTest` 框架编写测试。现在是时候看看测试驱动开发的具体例子了。

我们将学习如何通过编写测试然后使其通过的迭代过程来实现一段逻辑。我们将看到 TDD 如何让我们首先专注于让代码正确，然后再使其整洁。最后，我们将发现在 Swift 这样的语言中，编译器如何补充驱动软件实现的反馈循环，以及如何利用这一点。

测试驱动开发利用测试的反馈来指导生产代码的实现。

如何开始这个过程？如何从测试开始，使用 `XCTest` 编写 Swift 代码？第一步是理解你需要实现的行为。

让我们来看看来自 [exercism.io](https://exercism.io/) 的这个问题，这是一个磨练你软件技能的好地方：

*   编写一个函数，给定一个代表年份的整数，判断该年份是否为闰年。
*   在公历中，闰年发生在所有能被 4 整除的年份，除了所有能被 100 整除的年份，除非该年份同时也能被 400 整除。
*   例如，2021 年不是闰年，但 2020 年是。1900 年不是闰年，但 2000 年是。

以下是该函数的一个虚拟版本，它可以编译但不包含任何必要的逻辑：

```
func isLeap(_ year: Int) -> Bool {
    return false
}
```

我们可以编写什么样的测试来验证我们的代码实现了问题描述中的行为呢？

## 测试列表

描述本身已经突出了行为细节，甚至提供了输入和输出的示例来验证：

*   能被 4 整除的年份是闰年，例如 2020 年。
*   能被 100 整除的年份不是闰年，例如 2100 年。
*   能被 400 整除的年份是闰年，例如 2000 年。
*   任何其他年份都不是闰年，例如 2021 年。

我们可以为每一项编写一个测试：

```
import XCTest
class LeapYearTests: XCTestCase {
    func testEvenlyDivisibleBy4IsLeap() {}
    func testEvenlyDivisibleBy100IsNotLeap() {}
    func testEvenlyDivisibleBy400IsLeap() {}
    func testNotEvenlyDivisibleBy4Or400IsNotLeap() {}
}
```

注意，我只写了测试函数的名字，内部没有代码。这就是一个*测试列表*。

编写测试列表是开始理解你正在处理的代码需要完成哪些不同细节的一种方式。

定义测试列表后的下一步是编写一个（且仅一个）测试。在 TDD 中，我们采用小步、增量式的方式进行，一次解决一个问题。这也适用于选择编写哪些测试以及如何编写它们。你希望尽快获得反馈。编写一个测试比编写所有测试要快。



## 伪装……直到成功

你该从哪个测试开始？选择一个你认为能轻松通过的测试是个好主意。从最简单的测试入手，是另一种让反馈时间尽可能短的方法。

在本例中，所有测试在我看来难度相当，所以我会从第一个开始：

```
func testEvenlyDivisibleBy4IsLeap() {
XCTAssertTrue(isLeap(2020))
}
XCTAssertTrue failed
```

我们能让这个测试通过的最简单方法是什么？只需返回一个能让测试通过的值，稍后再处理实际实现：

```
func isLeap(_ year: Int) -> Bool {
return true
}
```

这种返回硬编码值以使测试通过的技术被称为“伪装”。“伪装”让你在不确定实现细节时能轻松开始编写代码。先使用一个伪造的实现，这样你可以先运行一些测试，并从它们中学习。

对于`leapYear`这样实现简单的场景，“伪装”可能有些大材小用。如果脑中已经浮现出一个能让测试通过的明显实现方案，尽管直接写，无需先伪装。不过你可能会惊讶，“伪装”在很多情况下正是你突破困局、开始解决问题的关键。

借助这个伪造的实现，我们的测试现在变成了绿色。下一步是什么？

既然我们已经通过绿色测试建立了基准，就可以着手实际实现了，只要保持测试通过，代码的正确性就有保障。另一方面，我们还有更多测试要写，所以在这种情况下，我会继续写下一个测试。

下一个值得编写的测试是针对不能被 4 整除的输入，也就是刚才那个测试的反面：

```
func testNotEvenlyDivisibleBy4Or400IsNotLeap() {
XCTAssertFalse(isLeap(2021))
}
XCTAssertFalse failed
```

我们现在已经到达无法再继续“伪装”实现的地步。再使用另一个硬编码值将无法同时通过两个测试：

```
func isLeap(_ year: Int) -> Bool {
return year % 4 == 0
}
```

让我们继续下一个测试：

```
func testEvenlyDivisibleBy100IsNotLeap() {
XCTAssertFalse(isLeap(2100))
}
XCTAssertFalse failed
```

我能想到让这个测试通过的最简单方法是添加一个守卫语句：

```
func isLeap(_ year: Int) -> Bool {
guard year % 100 != 0 else { return false }
return year % 4 == 0
}
```

我们只剩一个测试要写了：

```
func testEvenlyDivisibleBy400IsLeap() {
XCTAssertTrue(isLeap(2000))
}
XCTAssertTrue failed
```

和前文一样，我能想到的最简单方法还是一个守卫：

```
func isLeap(_ year: Int) -> Bool {
guard year % 400 != 0 else { return true }
guard year % 100 != 0 else { return false }
return year % 4 == 0
}
```

我们所有的测试都通过了。现在有了一个完整的函数，用于判断给定年份是否为闰年。

我们通过以下步骤达到了这个结果：编写一个测试，观察它失败，利用失败信息编写刚好能让测试通过的代码，然后对下一个测试重复这个过程。红色，绿色，红色，绿色，红色，绿色。

我们并没有给自己设定一步到位写出可用实现的高远目标。相反，我们利用测试的反馈，几乎一点一点地构建它，每次都编写尽可能简单的代码。

通常，为了让测试通过，你编写的代码会相当丑陋。这没关系。记住：小步增量。先让代码工作，再让它变得整洁。

拥有测试，并且知道它们覆盖了代码的行为——因为你亲眼看到它们从红色变为绿色——能给你重构的自信。重构意味着在不影响行为的前提下改变代码的外观。重构是测试驱动开发（TDD）的核心部分。

在探讨重构如何融入工作流程之前，我先就测试的结构说几句。

## 安排、执行、断言

我们用于驱动闰年实现的测试都是一行代码。被测对象、其输入和输出，以及针对预期行为的断言，都整齐地放在一行中：

```
func testEvenlyDivisibleBy4IsLeap() {
XCTAssertTrue(isLeap(2020))
}
```

当被测行为不像一个接收一个输入并返回`Bool`的方法那么简单时，通过将测试显式分为三个阶段来组织测试会很有帮助：

* **安排**：准备输入。
* **执行**：执行被测行为。
* **断言**：验证输出是否符合预期。

让我们按照“安排、执行、断言”重写上面的测试：

```
func testEvenlyDivisibleBy4IsLeap() {
let year = 2020         // 安排
let leap = isLeap(year) // 执行
XCTAssertTrue(leap)     // 断言
}
```

清晰且一致地组织你的测试将有助于你浏览它们，因为所有测试都会呈现相同的结构。无论是寻找输入、被测试的方法，还是断言，都会更容易，因为它们总是位于测试中的相同位置。“万物各得其所”这句谚语不仅适用于你的家，也适用于你的代码库。

这三个阶段的另一种表述是“给定、当、则”。给定一个能被 4 整除的年份，当调用`isLeap(_:)`时，则结果为`true`。

“给定、当、则”更适合高层次测试，比如那些着眼于完整用户旅程的 UI 测试：给定一个已认证用户，当加载设置页面时，则显示认证提示。由于本书专注于单元测试，我们将使用“安排、执行、断言”。

现在我们已经学会了如何组织测试，让我们看看重构在测试驱动开发流程中的位置。



## 先让测试通过；再让代码整洁

测试驱动开发（TDD）让你摆脱既要写出能工作的代码、又要写出整洁代码的双重心理负担。首先，测试会引导你写出能工作的代码；随后，它们又赋予你信心，让你能专注于让代码变得整洁。

假设你定义了一个如下的 `Product` 值类型：

```
struct Product {
let category: String
let price: Double
}
```

你需要编写一个函数，该函数接收一个已售 `Product` 数组和一个类别值，并返回该类别的总销售额：

```
func sumOf(_ products: [Product], withCategory category: String) -> Double {
return 0.0
}
```

我们先把测试清单列出来：

```
import XCTest
class SumOfProductsTests: XCTestCase {
func testSumOfEmptyArrayIsZero() {}
func testSumOfOneItemIsItemPrice() {}
func testSumIsSumOfItemsPricesForGivenCategory() {}
}
```

让我们从第一个测试开始：空数组的产品类别总和应该为零。像这样的行为*边界*案例通常更容易实现。在这种情况下，由于输入数组为空，我们知道实现过程涉及的逻辑会非常少，这给了我们一个机会来搭建行为框架，后续我们就能在此基础上构建完整的行为：

```
func testSumOfEmptyArrayIsZero() {
let category = "books"
let products = [Product]()
let sum = sumOf(products, withCategory: category)
XCTAssertEqual(sum, 0 )
}
```

因为函数的实现很简单，返回了 `0`，所以测试已经通过了。我觉得没问题。我知道随着我们添加更多测试，它们会要求实现逻辑变得正确。

让我们通过处理另一个行为边界案例来逐步构建完整的实现：

```
func testSumOfOneItemIsItemPrice {
let category = "books"
let products = [Product(category: category, price: 3)]
let sum = sumOf(products, withCategory: category)
XCTAssertEqual(sum, 3)
}
XCTAssertEqual failed: ("0.0") is not equal to ("3.0")
```

这是我想到的第一个实现：

```
var sum = 0.0
for product in products {
sum += product.price
}
return sum
```

现在测试通过了。是时候问一个问题了：有没有更好的方式来写这段代码？

我更喜欢集合操作，而不是 `for` 循环。这样看起来如何？

```
return products.reduce(0.0) { $0 + $1.price }
```

测试仍然通过，并且我们不再需要定义一个 `var` 来在遍历数组时累加总和。

测试帮助我们确保代码实现上的改动没有影响其行为。这正是重构的定义：改变代码的编写方式，但不改变其行为。

现在只剩下一个测试需要编写了，即针对函数主要用例的测试：

```
func testSumIsSumOfItemsPricesForGivenCategory() {
let category = "books"
let products = [
Product(category: category, price: 3),
Product(category: "movies", price: 2),
Product(category: category, price: 1)
]
let sum = sumOf(products, withCategory: category)
XCTAssertEqual(sum, 4)
}
XCTAssertEqual failed: ("6.0") is not equal to ("4.0")
```

这个测试没有通过，因为我们没有逻辑来只选择与给定类别匹配的 `Product`：

```
return products.reduce(0.0) {
guard $1.category == category else { return $0 }
return $0 + $1.price
}
```

使用这个更新后的实现，测试通过了；因此，实现是正确的。再次，让我们问一下：我们能改进它吗？

我喜欢代码只遍历数组一次的做法，但我更希望将过滤操作与求和操作分开：

```
return products
.filter { $0.category == category }
.reduce(0.0) { $0 + $1.price }
```

我认为这样可读性更好；它清晰地表明：“给定一些商品，取出匹配某个类别的商品，然后合计它们的价格。”

我们又一次从红色状态（测试失败）过渡到了绿色状态（测试通过），然后重构了代码。

**红色、绿色、重构。这就是 TDD 的咒语**。

强迫自己写出能工作、优雅且没有重复的代码，比单独解决这些问题需要更多的精力。测试驱动开发为你提供了一种过程，让你能一次只全神贯注于一个问题。

这并非是说，如果你已经想到了更好的解决方案，却还要故意写出肮脏或次优的代码只是为了通过测试。写出你脑海中浮现的第一个实现，然后，一旦让测试变绿，问问自己是否还能改进代码。你会惊讶且高兴地发现，很多时候答案是肯定的。

我们已经看到，一个失败的测试既是对代码正确性的反馈机制，也是对下一步该实现什么的建议。在 Swift 中，我们可以利用另一个与失败测试同样强大的反馈机制：编译器。

## 编译器是 TDD 过程的一部分

2014 年 Swift 的推出是苹果开发生态系统的一次重大革命。凭借其强类型系统，它背离了 Objective-C 的动态本质。

强类型系统消除了整类运行时类型不匹配的错误。如果你将一个函数修改为返回 `Array<String>` 而不是 `String`，它的所有调用者将无法编译，并报错“无法将类型 ‘`Array<String>`’ 的值转换为指定类型 ‘`String`’”。在动态语言中，应用程序仍会运行，直到它尝试对返回的数组值调用 `String` 方法时才会失败。

强类型还能帮助你使不一致的状态*无法被表示*。我最喜欢的例子是使用 `enum` 来表示相互排斥的状态，而不是为每个可能的值设置一组可为空的属性：

```
let responseValue: Value?
let responseError: Error?
// 对比
let response: Result
```

使用 `responseValue` 和 `responseError` 这对属性，你需要编写一个测试来确保如果一个为 `nil`，另一个有值，并且它们不同时为 `nil`。使用 `Result` 枚举则将这个测试转移到了类型系统层面。编译器强制了这种互斥关系；你无需为其编写测试。

当你通过测试的视角来看待代码时，拥有一个强类型系统会让一整类测试变得多余。

在强类型语言中，TDD 既是测试驱动开发，也是*类型驱动*开发。编译器错误就像测试失败：它们针对代码的正确性给你反馈，并且你可以将测试编码进你的对象和函数的类型签名中。

让我们看看如何利用编译器来了解下一步该写什么代码，就像我们从阅读测试失败信息中得出的结论一样。



## 理想化编码（Wishful Coding）

在`leapYear`练习中，我们在编写测试之前从一个虚拟函数实现开始。另一种不同的方法是仅在编译器错误提示我们需要某个函数时才编写它：

```
import XCTest
class LeapYearTests: XCTestCase {
func testEvenlyDivisibleBy4IsLeap() {
XCTAssertTrue(isLeap(4))
// Compiler says: Cannot find 'isLeap' in scope
}
}
```

编译器找不到与我们编写的签名匹配的函数。我们处于红色状态，只不过这次不是测试失败，而是编译器错误。与测试失败类似，编译器错误会提示接下来应该做什么才能进入绿色状态。如果编译器找不到名为`isLeap`的函数，那么我们就为它定义一个：

```
func isLeap(_ year: Int) {}
```

现在编译器有了一个新的错误：

```
XCTAssertTrue(isLeap(4)) // Compiler says: Cannot convert value
// of type '()' to expected argument
// type 'Bool`
```

`XCTAssertTrue`期望一个`Bool`值进行检查，但我们的`isLeap`函数当前没有返回任何内容，更准确地说，它返回`()`，也就是`Void`：

```
func isLeap(_ year: Int) -> Bool {
return true
}
```

现在代码可以编译了，我们可以运行测试来验证实现是否正常工作。

我们在这里所做的是，在定义代码之前，先编写我们期望存在的代码，然后使用编译器错误作为指导来得出函数定义。这种技术称为**Wishful Coding（理想化编码）**。

Wishful Coding 可能看起来是一个过于漫长的过程，尤其是对于像`leapYear`函数这样直接的代码。我是在倡导你应该*始终*使用这种方法吗？不，但我希望你了解这种技术。

下次当你为事先确定函数签名而感到困扰时，不妨在测试断言中编写你期望存在的代码，然后观察编译如何失败，并从这个错误开始着手解决。

我鼓励你尝试这种方法，即使你感觉可能会慢一点。在拳击电影《洛奇 3》中，阿波罗·克里德将悲伤而失落的洛奇带到游泳池，让他“锻炼并使用那些他从未意识到的肌肉”。洛奇一开始笨拙而缓慢，但最终在鼓舞人心的配乐中一圈又一圈地游泳，并最终夺回了他的冠军头衔。

像 Wishful Coding 和 Fake It 这样的技术就像是你未曾意识到的肌肉。你需要锻炼并强化它们，这样当需要使用它们的时候，它们就能随时投入行动。

既然我们已经了解了测试驱动开发的核心要素，现在是时候将它们应用到真实世界中了。在下一章中，我们将开始一个全新应用的测试驱动开发。

## 关键要点

*   **测试列表**：开始列出你可能需要的所有测试，以验证你需要实现的代码的行为。

*   **红、绿、重构**。一次只处理一个测试，编写测试，观察它因为该行为没有实现而失败，然后将失败作为指导，让测试通过。一旦测试通过，问问自己代码是否可以改进。

*   **TDD 让你先专注于让代码工作，然后再让其变得整洁**。分别处理每个任务比同时尝试两者更容易。测试给了你迭代实现的信心。

*   **将测试分为三个阶段：安排、执行、断言**。清晰且一致地分离输入、被测行为以及对其输出的期望，可以减少阅读测试的认知负担。

*   **编译器错误也是一种反馈来源**。

*   **Fake It（假实现）**。如果你不确定如何实现某些功能，先硬编码能让测试通过的值。一旦测试通过，你就可以有信心地处理真正的实现。

*   **Wishful Coding（理想化编码）**：如果你不确定如何定义函数或类型，先在测试中编写它的用法，然后将编译器的失败消息作为定义的起点。

