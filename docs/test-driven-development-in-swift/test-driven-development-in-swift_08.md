# 5. 使用 Fixtures 变更测试

*如何在修改生产代码的类型签名时，无需更新使用这些类型的测试？*

*通过将测试与生产对象的初始化代码解耦。*

测试驱动开发承诺帮助开发者以稳定的节奏工作。为了实现这一点，仅先编写测试是不够的；我们需要像维护生产代码那样维护测试代码，使其保持灵活和易于操作。正如 Robert C. Martin 在 *Clean Code: 敏捷软件工艺手册*中所说：

- 测试代码和生产代码同等重要。它不是二等公民。它需要思考、设计和关怀。它必须像生产代码一样保持整洁。

本章介绍一种技术，它允许你更新生产类型的结构，而无需不断地返回并更新你的测试：*fixture*。

我们将了解什么是 fixture，如何为我们的类型定义它们，以及这种方法如何帮助我们编写不仅更易于维护，而且更清晰的测试。

## 源码变更的隐藏成本

Alberto 很高兴收到我们在前一章中构建的应用早期版本，并给出了一些反馈。对于他的菜单来说，一项重要的信息是显示哪些菜品是辣的。他不希望他的顾客因为不喜欢辣食而点了一道最终却无法享受的菜。¹

Alberto 希望在每个辛辣菜品旁边有一个视觉提示，就像他的纸质菜单一样。你同意从使用一个辣椒表情符号开始，![../images/501287_1_En_5_Chapter/501287_1_En_5_Figa_HTML.gif](img/501287_1_En_5_Figa_HTML.gif)。（当你无法接触到设计师或没有时间绘制图标时，表情符号是增添色彩和模拟图标的好方法。）

为了做出这一更改，你需要更新数据模型来跟踪`MenuItem`是否辛辣，并更新 UI 层为那些辛辣的菜品添加视觉提示。让我们先看看数据模型。我们将在下一章讨论视图。

我们可以像这样更新`MenuItem`以添加辛辣信息：

```
// MenuItem.swift
struct MenuItem {
    let name: String
    let category: String
    let spicy: Bool
}
```

如果进行此更改，我们将遇到一堆编译失败，并显示如下错误：

```
MenuItem(category: "pastas", name: "name")
// 编译器说：调用中缺少参数 'spicy'
```

`MenuItem`中的新属性意味着编译器将生成一个新的初始化器，这使得我们之前使用的初始化器变得无效。

要恢复到绿色状态，我们需要更新所有实例化`MenuItem`的调用点。这样做的工作量与实例化次数成正比：实例化越多，更新它们的工作量就越大。在生产代码中你可能没有很多更新要做，因为一个类型通常只被实例化几次，但在测试中，我们可能对`init`方法有数十次调用。

单元测试旨在节省开发者的时间；如果维护测试代码变成一项耗时的任务，测试驱动开发就会从提高生产力的最佳盟友变成最差的敌人。

我们需要一种方法，将测试中创建值的方式与其实际的`init`解耦，这样我们就能在更新`init`时，无需总是修改测试中的所有初始化代码。



## 固定装置（Fixtures）

为了降低更新 `MenuItem.init()` 的成本，我们可以让测试在一个集中的位置初始化它，这样每当它的结构发生变化时，我们只需更新一处代码。

我们可以在测试目标中扩展 `MenuItem`，提供一个静态方法供测试使用，以获取该类型的一个实例。这种方法被称为固定装置（fixture），由于我们在扩展中定义固定装置，这种技术被称为 *固定装置扩展（Fixture Extension）*：

```
// MenuItem+Fixture.swift
@testable import Albertos
extension MenuItem {
static func fixture(
category: String = "category",
name: String = "name"
) -> MenuItem {
MenuItem(category: category, name: name)
}
}
```

所有输入参数的默认值是降低测试套件维护成本的关键。

当你添加一个新属性时，只需更新固定装置并提供一个默认值即可。你无需更新任何现有测试，并且在编写需要该属性的新测试时，你可以直接使用包含新属性的固定装置。当你删除一个属性时，唯一需要更新的测试是那些在调用固定装置时传入了该属性自定义值的测试。

我们可以像下面这样更新测试以使用固定装置：

```
// MenuGroupingTests.swift
func testMenuWithOneCategoryReturnsOneSection() throws {
let menu = [
MenuItem.fixture(category: "pastas", name: "name"),
MenuItem.fixture(category: "pastas", name: "other name"),
]
let sections = groupMenuByCategories(menu)
XCTAssertEqual(sections.count, 1)
let section = try XCTUnwrap(sections.first)
XCTAssertEqual(section.items.count, 2)
XCTAssertEqual(section.items.first,  "name")
XCTAssertEqual(section.items.last, "other name")
}
```

现在，要添加 `spicy` 属性，唯一需要修改的代码就在固定装置的定义中：

```
// MenuItem.swift
struct MenuItem {
let name: String
let category: String
let spicy: Bool
}
// MenuItem+Fixture.swift
extension MenuItem {
static func fixture(
category: String = "category",
name: String = "name",
spicy: Bool = false,
) -> MenuItem {
MenuItem(
category: category,
name: name,
spicy: spicy
)
}
}
```

## 固定装置（Fixtures）与便利初始化器（Convenience Initializers）

为什么要定义固定装置而不是带有默认值的便利初始化器？因为清晰性。

当阅读 `let item = MenuItem.fixture(name: "a name")` 时，这个实例携带了专为测试目的选择的默认值，这比阅读 `let item = MenuItem(name: "a name")` 要清晰得多。调用 `MenuItem(...)` 看起来像生产代码；而 `MenuItem.fixture(...)` 则是属于测试套件的内容。

让你的代码清晰易读具有巨大的投资回报：你只编写一次代码，但你和你的队友会阅读它很多次。这对于测试代码和生产代码同样适用。

## 固定装置（Fixtures）使测试的行动者（Actors）更明确

一旦你建立了固定装置，就可以通过只关注那些真正影响被测行为的输入元素来编写更清晰的测试。

让我们看看上一章中这个没有使用固定装置的测试：

```
func testMenuWithManyCategoriesReturnsOneSectionPerCategoryInReverseAlphabeticalOrder() {
let menu = [
MenuItem(            category: "pastas", name: "a pasta", spicy: false        ),
MenuItem(            category: "drinks", name: "a drink", spicy: false        ),
MenuItem(            category: "pastas", name: "another pasta", spicy: false        ),
MenuItem(            category: "desserts", name: "a dessert", spicy: false        ),
]
let sections = groupMenuByCategories(menu)
XCTAssertEqual(sections.count, 3)
XCTAssertEqual(sections[0].category,  "pastas")
XCTAssertEqual(sections[1].category,  "drinks")
XCTAssertEqual(sections[2].category, "desserts")
}
```

我们正在测试生成的分类（sections）是否与输入的类别（categories）匹配，并且是否按字母逆序排列。在输入的所有属性中，唯一影响输出的是 `category`。然而，我们准备输入代码时，却不得不为所有其他无关属性添加值。

使用固定装置，我们可以去除这些噪声，使输入只关注真正重要的内容：

```
let menu = [
MenuItem.fixture(category: "pastas"),
MenuItem.fixture(category: "drinks"),
MenuItem.fixture(category: "pastas"),
MenuItem.fixture(category: "dessert")
]
```

**额外提示**

你可以通过定义数组类型，然后在调用函数时省略 `MenuItem` 来使代码更紧凑；Swift 编译器会自行推断。

```
let menu: [MenuItem] = [
.fixture(category: "pastas"),
.fixture(category: "drinks"),
.fixture(category: "pastas"),
.fixture(category: "dessert")
]
```

除了使你的测试对读者更清晰，并消除修改源代码时的摩擦之外，固定装置还能在你编写测试本身时节省时间。你无需考虑与被测行为无关的属性的赋值，也无需输入它们。

## 固定装置（Fixtures）是可组合的

你可以使用固定装置来定义新的固定装置。例如，下面展示了如何为 `MenuSection` 定义一个固定装置：

```
// MenuSection+Fixture.swift
@testable import Albertos
extension MenuSection {
static func fixture(
category: String = "a category",
items: [MenuItem] = [.fixture()]
) -> MenuSection {
return MenuSection(category: category, items: items)
}
}
```

## 尽早引入固定装置（Fixtures）

引入固定装置就像为退休储蓄；由于复利效应，你开始得越早，获得的价值就越大。

为一个类型定义固定装置并将其应用于现有测试，短期内会拖慢你的速度，但当需要更改该类型的结构时，你很快就能弥补回来。

*三次法则* 是决定何时引入固定装置的一个良好启发式方法。如果你已经在两个测试中调用了某个类型的 `init`，那么在调用第三个之前，就为它定义一个固定装置。

## 练习时间

为了亲身体验固定装置的价值，请为 `MenuItem` 添加新的属性。例如，在后续开发中，我们可能需要一个表示菜肴价格的属性。尝试添加它，并看看你需要在生产和测试代码中修改哪些部分：

```
// MenuItem.swift
struct MenuItem {
let name: String
let category: String
let spicy: Bool
let price: Double
}
```

## 要点总结

*   **使用固定装置扩展（Fixture Extension）来消除更新生产代码类型结构时的摩擦**。通过在测试中创建值的方式与其 `init` 之间增加一层间接性，当你编辑、添加或删除类型的属性时，将无需更新每一个测试调用点。
*   **固定装置帮助你将测试聚焦于关键的行动者（Actors）**。得益于其默认值，固定装置允许你省略那些不影响被测行为的 `init` 参数。
*   **固定装置是可组合的**。你可以将一个固定装置用作另一个固定装置某个属性的默认值。
*   **尽早引入固定装置**。固定装置节省时间的效果会随着时间的推移而累积；你可以采用“三次法则”这个启发式方法来决定何时添加它们。

## 尾注

1.  有人可能会争辩说，在开发的这个阶段，为辛辣食物添加 UI 提示并非最高优先级，我们应该专注于构建应用核心功能的骨架，例如网络 API 和点单功能。绝对正确！然而，从学习如何通过测试驱动开发 iOS 应用程序的角度来看，在继续探讨更高级的主题之前，学习为生成 UI 数据的逻辑编写单元测试是非常有用的。



