# `switch` 语句

Swift 的 `switch` 语句是一个功能强大的工具集，其能力远超同类语言中的 `switch` 语句。让我们从一个简单的例子开始，你对此应该很熟悉。

```
let number = 9
switch number {
    case 1:
        println("最孤独的数字")
    case 2:
        println("足够跳探戈")
    case 3, 5, 7:
        println("\(number) 是质数")
    case 4, 6, 8:
        println("\(number) 是偶数")
    case 13:
        println("被认为不吉利")
    default:
        println("\(number) 是我并不熟悉的数字")
}
```

你应该了解其许多特性：

*   你可以对大多数 Swift 类型（整数、字符串、枚举、元组以及任何遵循 `Equatable` 协议的对象或结构体）进行 `switch`。
*   多个匹配条件可以在单个 `case` 语句中列出，用逗号分隔。
*   没有“贯穿”机制。每个 `case` 代码块执行完毕后，语句的执行就结束了。如果你希望一个块贯穿到下一个，请以特殊的 `fallthrough` 语句结束该块。
*   因为没有“贯穿”，所以不需要 `break` 语句，但你仍然可以使用它来提前退出一个块。如果你的某个 `case` 没有代码，请使用一个单独的 `break` 语句；每个 `case` 必须有一个代码块，而 `break` 满足该要求。
*   所有 `switch` 语句必须是穷尽的，这意味着它们必须覆盖每一种可能的情况。对于枚举，这意味着你必须为每个枚举值提供一个 `case`。如果你的 `case` 不是穷尽的（或者 Swift 无法确定它们是穷尽的），你必须包含一个 `default` 情况。

使 `switch` 语句如此强大的原因是它的 `case` 表达式。一个 `case` 表达式实际上是一个模式匹配机制。它可以是一个常量（如前所示）、一个范围、一个任意表达式，或一个包含多个模式的元组。你可以将元组值绑定到新变量，或者用任意的布尔条件来限定 `case` 表达式。让我们逐一看看这些变体。

### 范围案例

一个 `case` 也可以是一个范围，如下例所示：

```
let testScore = 91
switch testScore {
    case 94...100:
        println("优秀！")
    case 86...93:
        println("非常体面")
    case 75...85:
        println("相当不错")
    case 60...74:
        println("还行")
    default:
        println("不及格")
}
```

整数 `testScore` 会与每个闭区间进行比较。你可能会问：“范围可以重叠吗？”，“它们可以包含变量或表达式吗？”。是的，可以。看看下面的 `switch` 语句：

```
let passingScore = 55
switch testScore {
    case 94...100:
        println("优秀！")
    case (100+passingScore)/2...100:
        println("非常体面")
    case passingScore...100:
        println("足够好")
    default:
        println("不及格")
}
```

其中一些范围由变量决定，另一些则包含表达式。这些范围也存在重叠。这个 `case` 语句之所以能工作，是因为以下规则：

*   案例是按顺序评估的。
*   执行第一个匹配该值的案例。

如果你有两个重叠的案例，请对它们进行排序，使优先级较高的案例在 `case` 列表中靠前。

### 切换元组

现在来看看对元组进行 `switch` 操作，如下例所示：

```
var sandwich = ("pastrami", "swiss")
var name = ""
switch sandwich {
    case ("tuna", "cheddar"):
        name = "金枪鱼三明治"
    case ("ground beef", "cheddar"):
        name = "芝士汉堡"
    case ("ground beef", _):
        name = "汉堡"
    case ("steak", "cheddar"):
        name = "芝士牛排三明治"
    case ("ham", "american"):
        name = "火腿芝士三明治"
    case ("ham", "gruyère"):
        name = "法式火腿干酪三明治"
    case ("ham", "swiss"):
        name = "古巴三明治"
    default:
        name = "\(sandwich.0) 配 \(sandwich.1)"
}
```

每个 `case` 中的模式都应用于 `switch` 元组中对应的值。如果全部匹配，则执行该 `case`。在这个例子中，大多数模式都是常量。但它们中的任何一个都可以是变量、表达式或范围，就像非元组的 `switch` 语句一样。

你也可以使用一个*通配符*，它总是匹配元组中的一个值。如果你想忽略该值，请将元组模式替换为 `_`（下划线），如前一个例子中的 `("ground beef", _)` 情况所示。如果你想匹配所有值，并在代码中检查该值，请使用 `let` 语句绑定该值，如下例所示：



```swift
sandwich = ("ground beef", "blue cheese")
switch sandwich {
    case ("tuna", "cheddar"):
        name = "Tuna melt"
    case ("ground beef", let cheese):
        name = "Hamburger with \(cheese)"
    default:
        name = "Today's Speical"
}
```

`let cheese`模式匹配`sandwich.1`的每一个可能值，然后将该值绑定到`cheese`变量。现在在该`case`的代码中，你可以互换使用`cheese`和`sandwich.1`。

回到“枚举”章节，`switch`语句也被用于访问枚举的关联值。凭借你对`switch`语句的新认识，你可能想再看一下那个示例。

如果这还不够灵活，你还可以用任意条件限定任何`case`——比如附加一个额外的`if`语句。你可以在`case`语句后面跟一个`where`表达式。如果`case`模式匹配，则会计算`where`条件。如果条件为真，则执行`case`块。如果不为真，则该`case`不匹配，并考虑下一个`case`。以下示例演示了这一点：

```swift
let now = NSDate()
let cal = NSCalendar.currentCalendar()
let when = cal.components(.HourCalendarUnit | .WeekdayCalendarUnit, fromDate: now)
var special = ""
switch when.weekday {
    case 1, 7 where when.hour<13:         // 周末（周日或周六）下午 1 点前
        special = "Eggs Benedict"
    case 1, 7:                            // 周末，当天剩余时间
        special = "Cobb Salad"
    case 2 where when.hour<8:             // 周一，早上 8 点前
        special = "Ham with Red-eye Gravy"
    case 3, 5 where when.hour<10:         // 周二或周四，上午 10 点前
        special = "Fried Egg Sandwich"
    case 4:                               // 周三，全天
        special = "Ruben"
    case 5, 6:                            // 周四（上午 10 点后）或周五任何时间
        special = "Egg Salad Sandwich"
    default:                              // 其他任何时间
        special = "Cheeseburger"
}
```

`when`变量包含当前日期和时间的日历组件。日历组件是可理解的时间单位，例如星期几、月份名称等。这个`case`语句结合使用了模式来匹配星期几，并结合布尔表达式来限定一天中的时间。这些表达式可以是任何内容，也可能考虑是否是假日或黑色星期五。

## 可选值

半个多世纪以来，程序员一直在一个问题上挣扎：值什么时候不是值？经常有这样的情况：一个变量（比如日期）可能有时包含一个有效的日期对象，有时却不包含。很久以前，程序员约定用存储一个零值作为指针或对象引用的值，来表示它不引用有效的对象。他们称这个值为`NULL`或`nil`。

**注意** 这种做法变得非常普遍，以至于现代计算机内存硬件现在被设计为在地址`0x00000000`处或附近永远不会有任何有效内存。这可以防止空指针引用与有效数据混淆。（这也是一种让程序快速崩溃的方法。）

早期的编程语言允许任何指针或对象引用被设置为`nil`。这意味着任何对象引用（在任何变量、参数以及任何函数中）都可能是`nil`。你要么必须测试每一个传入的值是否为`nil`，要么只能希望调用者永远不会在你预期之外向你发送`nil`引用。这个难题无疑是导致 iOS 及大多数其他平台上程序崩溃的最常见单一原因。

对于整数这样的值，情况变得更糟。你必须保留一个值来表示该数字无效。有时这可以简单地是`0`或`-1`，但如果这些值也是有用的数字，你可能不得不选择一个像`-999`或`9223372036854775807`这样的数字。而且对此没有约定，所以如果有一天`-999`变成了一个可用的数字，所有相关代码都得改变。对于更小的类型，情况会更糟。考虑一个布尔值。你会选择`true`还是`false`来表示它既不是`true`也不是`false`？

Swift 通过可选值解决了这个问题以及许多其他问题。*可选*类型是一种可以持有值或根本没有值的类型。每种 Swift 类型都有一个可选类型，通过在类型名称后跟一个问号（`?`）来表示。`Int`类型总是存储一个整数值。`Int?`类型可以存储任意整数值，或者根本没有值。

可选类型让程序员能够做他们一直需要做的事情：声明一个可能有值也可能没有值的变量。但 Swift 以一种非常清晰且深思熟虑的方式做到了这一点，并且没有预留任何值。

更重要的是，当你没有使用可选值时，Swift 保证该变量将包含一个有效值。始终如此。毫无疑问。如果你有一个`NSDate`参数，该参数将*始终*包含一个有效的日期对象。你永远不必测试它是否为`nil`，你的程序也永远不会因为忘记测试`nil`而崩溃。

## 使用可选值

通过在任意类型后面加上`?`来声明可选值，如下面的示例所示：

```swift
var sometimesAnObject: NSDate? = NSDate()
var sometimesAString: String? = nil
var sometimesAnInt: Int?
var sometimesAnAnswer: Bool?
```

在第一个例子中，`sometimesAnObject`可以存储一个`NSDate`对象或特殊值`nil`。在 Swift 中，`nil`不代表零；它是一个保留的特殊值，意思是“没有值”，并且不等同于任何其他值。`sometimesAString`变量存储一个可选的字符串，预设为“没有值”。当你声明一个可选值时，你不必提供默认值；Swift 会自动将其设置为`nil`。`sometimesAnInt`和`sometimeAnAnswer`都被预初始化为`nil`。

你可以使用“老派”方法测试可选值是否为`nil`，就像在另一种语言中测试指针是否为`NULL`一样。在下面的示例中，`sometimesAnObject`与`nil`进行比较，然后相应地使用：

```swift
if sometimesAnObject != nil {
    println("The date is \(sometimesAnObject!)")
} else {
    println("I better not use sometimesAnObject")
}
```

你通过将可选值与`nil`进行比较（或者使用`optional == nil`，或者使用`optional != nil`）来确定可选值是否有值。事实上，这是你能直接对可选值做的唯一一件事。一旦你确定该变量有值，你就可以使用`optional!`语法访问它的值。感叹号*解包*了可选值，访问了其底层值。

而危险就在这里。如果你解包了一个没有值的可选值，你的程序可能会像在 C 语言中使用`NULL`指针一样快速崩溃。为了帮助你避免过去的错误，Swift 提供了一个专门用于处理可选值的特殊`if`语句。以下代码与前面的示例等效：

```swift
if let date = sometimesAnObject {
    println("The date is \(date)")
} else {
    // 此块中没有 'date' 变量
}
```

`if let variable = optional`语句被称为*可选绑定*，它一步完成了三件事。它首先检查`sometimesAnObject`是否包含一个值（`sometimesAnObject != nil`）。如果包含，它会创建一个新常量（`let date: NSDate`），然后解包可选值并将其赋值给该常量（`= sometimesAnObject!`）。然后执行`if`语句的代码块。由于该常量（`date`）不是可选值，你可以在块体中自由使用它——无需解包。



如果`sometimesAnObject`不包含值，则不会发生任何操作，`if`块不会执行。如果存在`else`块，则会执行它，但其中没有定义`date`值。

你可能会想象你的代码现在会充斥着`if optional != nil`和`if let value = optional`语句。别担心，Swift 有一个简化在许多情况下测试`nil`的快捷方式，我稍后会介绍。

## 可选类型携带

可选类型是一种类型，与其他任何类型都不同。当你让 Swift 选择变量的类型时，最终可能会在你意想不到的地方得到可选类型。以下是一个简单的例子：

```
var dictionary = [String:String]()
let key = "key"
let value = dictionary[key]        // value: String?
if let existingValue = value {
    println("The value for key \"\(key)\" is \(existingValue).")
} else {
    println("There is no value for key \"\(key)\".")
}
```

变量`value`的类型是`String?`。“为什么？”你可能会问。因为字典下标访问器的返回类型是可选类型。字典可能没有与该特定键关联的值。因此，返回类型是一个可选类型，它也可以返回“无值”作为答案。

副作用是，你创建了一个可选变量（`value`），如果你想使用结果，必须考虑键`"key"`可能没有值的可能性。

## 可选链

测试可选类型是否有值的一个明显原因是避免访问不存在的值、不存在的属性，或调用无法工作的函数。Swift 有一种*可选链（optional chaining）*语法，它测试一个变量，并且仅当该变量包含值时才执行表达式的下一部分（属性访问、函数调用等）。如果不包含，则表达式不执行任何操作。示例如下：

```
class Cat {
    var quote: String { return "Most everyone's mad here." }
    var directions: String?
    var friend: Hatter?
    func disappear() { println("I'm not all here myself.") }
}
var cheshire: Cat?

cheshire?.disappear()
```

变量`cheshire`包含一个可选类型`Cat?`对象。语句`cheshire?.disappear()`使用了可选链。`cheshire`变量后面的`?`表示：“检查`cheshire`是否包含值，如果包含，则执行表达式其余部分。”这个简单的语句大致等价于以下代码：

```
if cheshire != nil {
    cheshire!.disappear()
}
```

可选链也可以用于安全地设置属性，如下所示：

```
cheshire?.directions = "Return to the Tulgey woods."
```

当与属性或方法的返回值一起使用时，可选链会将任何属性或返回值转变为可选类型。在以下示例中，变量`words`变成了可选类型：

```
let words = cheshire?.quote
```

常量`words`是一个`String?`。属性`quote`不是可选的，但由于它是通过链式调用（chaining）可选调用的，因此表达式变成了可选类型。当求值`cheshire?`时，Swift 会检查它是否为`nil`。如果是`nil`，Swift 会停止求值表达式并立即返回`nil`。所以，即使`quote`永远不能返回`nil`值，`cheshire?.quote`也可能返回`nil`。在下一个示例中，可选链与一个可选属性复合使用：

```
let whereToGo = cheshire?.directions
```

变量`whereToGo`仍然是一个`String?`。属性`directions`也是可选的，但 Swift 中没有“可选的可选”。此表达式将返回`nil`或一个`String`。如果返回`nil`，则无法（除非你去检查）知道是因为`cheshire`是`nil`还是`directions`是`nil`。可选链可以像你希望的那样嵌套多层，如下例所示：

```
class Mouse {
    func recitePoem() { }
}
class Hatter {
    var doorMouse: Mouse?
    func changePlaces() { }
}

cheshire?.friend?.doorMouse?.recitePoem()
```

最后一条语句仅当`cheshire`、它的`friend`属性以及该属性的`doorMouse`属性都有值时，才会调用`Mouse`对象的`recitePoem()`函数。如果任何一个不存在，该语句什么也不做。

虽然不太明显，但可选链会将*任何*语句转变为可选类型。你可能不认为`recitePoem()`函数会返回值，但对 Swift 来说它确实会，并且你可以利用这一点。

这有点深奥，但在 Swift 中，`Void`类型仍然是一种类型，并且它返回一个值，即空元组（`()`），一个没有值的值。因此，当`recitePoem()`函数变成可选类型时，你可以测试它是返回了空值还是`nil`。（我说过这有点深奥。）以下示例演示了这一点：

```
if cheshire?.friend?.doorMouse?.recitePoem() != nil {
    // cheshire has a friend with a doormouse that recited a poem
}
```

如果所有变量和属性都包含值，则`recitePoem()`函数会执行并返回 void 元组（`()`）。如果任何一个为`nil`，表达式就会停止并结果为`nil`。你可以测试是得到了 void 结果还是`nil`，这告诉你函数是否执行了。看，这并没有那么复杂。

## 可失败的初始化器

初始化器也可以是可选的。这些被称为*可失败的初始化器（failable initializers）*。可失败的初始化器可能会决定它无法创建特定的对象、结构体、枚举器或你试图构建的任何东西。它会中止新类型的构造并返回`nil`。你在前面的章节中使用过可失败的初始化器。在 MyStuff 应用中，你使用类似于以下的代码从项目中的资源创建了一个`UIImage`：

```
let noSuchImage = UIImage(named: "SevenImpossibleImages")
```

名为`SevenImpossibleImages`的资源完全有可能不在你的应用包中。`UIImage`无法构造，并且可选类型`noSuchImage`被设置为`nil`。

你可以使用`init?`函数名编写自己的可失败初始化器。以下示例展示了一个代表地图上兴趣点的类。它有一个已知位置及其坐标的数据库。如果兴趣点未知，则无法创建该对象。

```
class PointOfInterest {
    let name: String
    let location = CLLocationCoordinate2D(latitude: 0, longitude: 0)

    init?(placeName name: String) {
        self.name = name
        if let loc = locationForPointOfInterest(name) {
            self.location = loc
        } else {
            return nil
        }
    }
}

let castle = PointOfInterest(placeName: "Mystery Castle")
```

初始化器函数决定对象是否可以创建。如果不行，它故意返回`nil`，表明初始化失败。这使得初始化器的返回类型为`PointOfInterest?`。

## 隐式解包

我把最糟糕的留到了最后。Swift 允许你通过声明一个隐式解包变量（implicitly unwrapped variable）直接回到上个世纪，如下所示：

```
var dangerWillRobinson: String!
```

变量`dangerWillRobinson`是一个可选类型，但没有正常的安全保障。你仍然可以将其视为可选类型——与`nil`比较、条件绑定以及使用可选链——但这不是必须的。如果你直接使用变量名，它会被自动解包。如果它是`nil`，结果可能从有趣到灾难性。

隐式解包变量有其用武之地，但它们应该是你的最后选择。按偏好顺序：

*   只要有可能，就使用非可选变量。Swift 确保它们始终包含一个有效值。这消除了所有歧义。
*   对于可能合理缺失的值，或者你需要在该变量可以初始化之前很久就创建它，请使用可选类型。
*   将隐式解包可选类型保留给那些不能立即初始化，但你可以合理保证在使用前会是有效的值。



Interface Builder 使用了隐式解包变量。每当你使用 `@IBOutlet` 修饰符时，它都要求变量进行隐式解包。到目前为止，你在本书中已经无数次看到过这样的用法，例如：

```
@IBOutlet var label: UILabel!
```

当视图控制器对象被创建时，这个变量无法被设置。但是当场景从故事板加载时，它会通过连接（outlet connection）被设置。因此，当你的代码执行时，它（应该）会有一个值，并且你的代码可以安全地假设它有一个值。

解包有一些优点。如果谨慎使用，它可以通过将可选类型重新变回非可选值来简化代码。回顾一下 `UIImage` 的问题，假设你有一个作为应用永久组成部分的图像资源。没有理由假设该图像无法加载。你可以使用解包将其变回你所知道的常规值，如下列代码所示：

```
let placeholderImage = UIImage("placeholder")!
```

这里的 `!` 盲目地解包了可选类型并返回其值，我们相信这个值总是有效的。

如果你对隐式解包变量感到不安，可以考虑在代码中添加 `assert` 语句。以下代码假设 `CheshireCat` 资源存在，但通过添加 `assert` 语句来规避风险：

```
var cheshireImage: UIImage! = UIImage(named: "CheshireCat")
assert(cheshireImage != nil, "无法加载 CheshireCat 资源")
catView.image = cheshireImage
```

如果 `CheshireCat` 图像由于某种原因无法加载，`assert` 条件将为假，程序会立即停止，你就能立刻发现这个问题——至少在开发过程中会如此。

## 类型转换

有时，你可能会发现自己有一个基类类型的变量，比如 `IceCream`，而它实际上包含了一个 `BananaSplit` 对象。你可能需要将其作为 `BananaSplit` 对象与之交互，从而访问特定子类的属性和方法。将一种类型视为另一种类型被称为*类型转换*。Swift 提供了工具来探查变量的类并将其转换为另一种类型。

如果你只想知道一个对象的类是否是某个特定子类，可以使用 `is` 操作符。下面的示例创建了三个不同类的对象，它们都与 `IceCream` 类兼容：

```
let cone = IceCream()
let sundae = IceCreamSundae()
let split = BananaSplit()

let mysteryDessert1: IceCream = cone
let mysteryDessert2: IceCream = sundae
let mysteryDessert3: IceCream = split

mysteryDessert1 is IceCreamSundae   // false
mysteryDessert2 is IceCreamSundae   // true
mysteryDessert3 is IceCreamSundae   // true

mysteryDessert1 is BananaSplit      // false
mysteryDessert2 is BananaSplit      // false
mysteryDessert3 is BananaSplit      // true
```

`BananaSplit` 是 `IceCreamSundae` 的子类，而 `IceCreamSundae` 又是 `IceCream` 的子类。所有三个对象都被赋值给了 `IceCream` 变量。如果对象实际是给定类型的类或其子类，`is` 操作符就求值为 `true`。`cone` 既不是 `IceCreamSundae` 也不是 `BananaSplit`。`sundae` 是 `IceCreamSundae`，但不是 `BananaSplit`。`split` 既是 `BananaSplit` 又是 `IceCreamSundae`。

**注意：** Swift 不允许你写出同义反复的表达式 `split is BananaSplit`。`split` 变量已经是 `BananaSplit` 类型，所以根据定义，它必定是一个 `BananaSplit` 对象（或其子类）。

## 向下转型

了解对象的类很有意思，但你实际想做的是使用特定子类型的属性和函数。当你将 `split` 赋值给 `mysteryDessert3` 时，Swift 对对象进行了*向上转型*；它拿了一个 `BananaSplit` 对象，并将其存储在了更通用（但兼容）的 `IceCream` 变量中。要将 `mysteryDessert3` 再次作为 `BananaSplit` 对象来操作，你必须对对象进行*向下转型*，如下所示：

```
let mySundae = mysteryDessert2 as? IceCreamSundae
```

`as?` 操作符检查对象并确定其是否与目标类型兼容。如果是，它就向下转型该对象并返回同一个对象，只是其类型变为 `IceCreamSundae`。现在，你可以对 `mySundae` 做任何你曾对 `sundae` 能做的事情。`as?` 操作符，你可能已经猜到了，返回一个可选类型。如果对象无法安全地向下转型，则返回 `nil`。从技术上讲，表达式 `object as? Type` 的类型是 `Type?`。以下代码演示了向下转型的使用。它来自 第 11 章 的 Shapely 应用。

```
@IBAction func addShape(sender: AnyObject!) {
    if let button = sender as? UIButton {
        ...
    }
}
```

Objective-C 不像 Swift 那样使用强类型。因此，Objective-C 对象中的很多参数和属性只是 `AnyObject`——一种可以持有任何对象引用的泛型类型。为了有用，你必须将值向下转型为某种有意义的类型。在 `addShape(_:)` 中，`sender` 参数是发送动作的 `UIControl`。我们只将 `UIButton` 对象连接到了这个动作，所以 `sender` 应该是一个 `UIButton`（或某个子类）。`if let button = sender as? UIButton` 检查确保它是一个 `UIButton`，向下转型它，将其绑定到 `button` 变量，并执行你的代码，所有这些都在一条语句中完成。如果由于某种原因，`sender` 不是一个 `UIButton`，你的动作不会做太多事情——但也不会崩溃。

**注意：** 如果 `object` 是 `nil`，表达式 `object as? Type` 也会返回 `nil`。在 `let button = sender as? UIButton` 语句中，`sender` 是一个隐式解包可选类型。该语句也能保护你的代码免受 `sender` 为 `nil` 的可能性影响。

一个巧妙的技巧是将可选向下转型操作符与可选链结合使用，像这样：

```
(mysteryDessert2 as? IceCreamSundae)?.nuts = true
```

向下转型操作符有一个危险的“兄弟”：强制向下转型操作符。它的工作方式与 `as?` 类似，但它不返回可选类型；它只是假设向下转换会成功，就像 `!` 假设你的可选类型包含一个值一样。在你绝对确定向下转型会成功的情况下使用它，如下所示：

```
let string = "We want ice cream."
let screaming = (string as NSString).uppercaseString

if mysteryDessert2 is IceCreamSundae {
    let mySundae = mysteryDessert2 as IceCreamSundae
    mySundae.nuts = true
}
```

将 `String` 视为 `NSString` 或将 `Array` 视为 `NSArray` 总是安全的。在第二个示例中，`if` 语句已经确定性地判明了 `mysteryDessert2` 是一个 `IceCreamSundae`，因此表达式 `as IceCreamSundae` 总是会成功。不过，我建议使用绑定和可选向下转型来做这件事，因为它更短、更安全且更易读。

## 向下转型集合

数组或字典只是另一种类型，你可以像向下转型对象一样向下转型集合。Objective-C 的数组和字典不像 Swift 那样有类型。因此，每当你从 Objective-C 对象接收到一个集合时，它都会是一个泛型的 `[AnyObject]` 数组或 `[AnyObject:AnyObject]` 字典。你可以将其向下转型为更具体的类型，如下所示。（你可以在 `Learn iOS Development Projects` → `Ch 20` → `Casting.playground` 文件中找到更多示例。）

```
UIImagePickerController.availableMediaTypesForSourceType(.PhotoLibrary) as [String]
```

`availableMediaTypesForSourceType(_:)` 函数返回一个典型的 `[AnyObject]` 类型 Objective-C 数组。要将其用作 `String` 对象数组，你需要将其向下转型为 `[String]`。

**注意：** 当你向下转型一个集合时，Swift 会检查集合中*每个*元素的类型，以确保它们都与目标类型兼容。



在`SpaceSalad`项目中（来自第 14 章），你已经完成过此操作。一个触摸事件包含所有触摸点的集合，这些点是`UITouch`对象。但由于`UIEvent`是一个 Objective-C 类，`allObjects`是一个泛型数组。你通过以下表达式将其转换为`UITouch`对象数组：

```
let fingers = touches.allObjects as [UITouch]
```

## 值 vs 引用 vs 常量 vs 变量

Swift 类型分为两种：值类型和引用类型。**值类型**是存储值的变量，如整数或结构体，十分简单。**引用类型**是存储值引用的变量，类就是引用类型。持有`UILabel`对象的变量并不直接包含该`UILabel`对象，该对象分配在动态内存中，变量中存储的是指向该对象的*引用*。

理解这一点之所以重要，是因为当你在参数间传递值或将值存入其他变量时，行为会不同。以下是基本规则：

-   值类型总是被复制。将`Int`变量作为参数传入函数时，形参将是该整数值的一份*副本*。修改原始整数变量或形参，不会影响对方。
-   引用类型始终指向同一个对象。将对象变量传入函数时，形参会引用同一个对象。对该对象的所有修改，会通过所有持有其引用的变量可见。
-   尽管字符串、数组和字典在底层是对象（悄悄告诉你），Swift 仍将它们视为值类型。如果你将数组传递给函数并修改它，你修改的是数组的副本。

了解何时复制值、何时传递值的引用有时很重要，尤其对于可变对象。例如，如果你使用`NSMutableSet`这样的集合对象，将其传给函数后函数修改了该集合，函数返回时你的集合也会被修改；但如果你使用的是数组（值类型），就不会发生这种情况。

想象另一个场景：你创建了一个`NSMutableAttributedString`，然后用这个格式化字符串设置`UIButton`。按钮现在持有同一个可变属性字符串的引用。如果你随后再次修改属性字符串对象，会发生什么？

实际上，什么也不会发生。Cocoa Touch 框架的作者并不愚蠢，他们知道这可能会引发问题。有两种技术可以防止这种混淆，你将在整个 iOS 开发中看到它们。

许多基本类型是不可变的。`UIColor`、`UIFont`、`NSURL`以及许多其他类都创建不可变对象。一旦你拥有一个`UIColor`对象，就不能更改它的颜色。其优势在于，你可以在任意多的地方使用该对象的引用，因为没人能更改它，所以它不会影响任何其他地方的使用。

`UILabel`类中的`attributedString`等属性在设置时会复制对象。因此当你为`UIButton`的`title`设置`attributedString`时，`UIButton`对象会复制你的属性字符串并保留副本。之后你可以自由修改原始字符串，不会影响按钮。

在你的代码中也应使用这些技巧。如果你不希望某个参数或属性受后续对象修改的影响，要么在获取对象时复制它，要么使用不可变对象。Swift 有一个特殊的`@NSCopying`关键字，可以添加到存储属性中，如下所示。它会在设置属性时创建对象的副本。

```
class SafeClass: NSObject {
    @NSCopying var fancyTitle: NSAttributedString?
}
```

## `let`和`var`引用

在 Swift 中，`let`创建常量（不可变值），`var`创建变量（可变值）。那么，为什么可以写出以下代码？

```
let button = sender as UIButton
button.enabled = false
```

`button`是一个常量`UIButton`对象。为什么可以修改`UIButton`？因为`button`是一个指向可变`UIButton`对象的常量*引用*。你可以改变变量所引用的按钮对象，但不能改变变量本身，使其后续指向另一个不同的`UIButton`对象。

正因如此，在整个 Swift 中你会看到`let`被广泛用于你实际上打算修改的对象。相反，你不能写出以下代码：

```
let companyName = "Yahoo"
companyName.append(Character("!"))   // <-- 无效，companyName 不可修改
```

在 Swift 中，字符串是值类型。`let`字符串是不可变的，无法更改。对于数组和字典也是如此。但这不适用于 Objective-C 集合，它们是普通对象。`let`引用的`NSMutableArray`仍然是可修改的。

## `var`参数

通常情况下，函数调用的参数是常量，参数值不能在函数体内修改。我猜你可能没注意到这一点。这是因为函数的参数通常是函数执行的输入，你利用输入做某事，很少对输入本身做修改。但有两种技术可以让你做到这一点。

第一种是使用`var`关键字声明可变参数。这不会改变参数的工作方式，但参数值在函数体内是可变的。以下示例来自`Learn iOS Development Projects` ![image](img/arrow.jpg) `Ch 20` ![image](img/arrow.jpg) `References.playground`文件：

```
func pad(var # string: String, with char: Character, var # count: Int) -> String {
    while count > 0 {
        string.append(char)
        count--
    }
    return string
}
```

通常，你不能在`string`参数上使用`append(_:)`函数，也不能递减`count`参数。通过指定它们为`var`参数，它们变成了可变的局部变量。

注意，`string`参数是一个值参数，在函数调用时仍会被复制。因此函数中修改的字符串并不是传入的原始字符串。你可以通过以下示例验证：

```
let originalString = "Yahoo"
let paddedString = pad(string: originalString, with: "!", count: 4)
```

这段代码执行后，`originalString`仍然是“Yahoo”。但如果你确实想要修改原始字符串呢？也有办法实现。

## 值的引用（`inout`）

你已经看到闭包如何创建对现有值的引用，即使是值类型。你可以在函数参数中做类似的事情。如果用`inout`关键字标记参数，它会将你的值参数转换为引用参数。示例如下：

```
func padSurPlace(inout # string: String, with char: Character, var # count: Int) -> String {
    while count > 0 {
        string.append(char)
        count--
    }
    return string
}
```

`inout`关键字将`string`参数转换为对`String`值的引用。调用函数时，需要在变量前加上`&`（与号），如下所示。这是你传递变量引用（而非值副本）的视觉提示。

```
var mutableString = "Yahoo"
padSurPlace(string: &mutableString, with: "!", count: 3)
```

当`padSurPlace(string:,with:,count:)`函数执行时，其`string`参数引用的是你传入的变量。任何修改都会影响原始变量。这段代码执行后，`mutableString`的值将是“Yahoo!!!”这适用于任何类型。

**注意**：如果将参数设为`inout`，它只能与可变变量一起使用，不能传递常量或字面值作为该参数。

## 使用 C 和 Objective-C



Swift 与 C 和 Objective-C 的协作天衣无缝，你几乎不需要深入了解其中任何一门语言，就能用 Swift 编写应用。事实上，本书至此你所使用的几乎所有内容，除了你自己用 Swift 编写的部分外，很可能都是 Objective-C 类或 C 函数。

Swift 可以像使用 Swift 类一样使用任何 Objective-C 类。Objective-C 也可以将大多数 Swift 类当作 Objective-C 类来使用。如果你向 Objective-C 项目中添加 Swift 代码，或反之亦然，Xcode 会自动生成转换接口，将你的 Swift 类描述给 Objective-C，并将你的 Objective-C 类描述给 Swift。

这里我只想提两点。如果你还需要了解 Swift、Objective-C 和 C 三者如何协同工作的其他知识，请参考 Apple 的 *Using Swift with Cocoa and Objective-C*，这本书也在 iBooks 上免费提供。它是 *The Swift Programming Language* 的姊妹篇，将解答你所有关于跨语言的问题，甚至可能包括一些你没想到的问题。

### 无缝桥接

在 Swift 中，你可以无缝地将 Objective-C 方法与 Core Foundation 中的 C 函数一起使用。这意味着你可以轻松混合使用 Swift 值、Objective-C 对象和 Core Foundation 类型。其中有几种类型是重叠且可互换的。表 20-4 列出了等价的 Swift、Objective-C 和 C 类型。

表 20-4. 无缝桥接类型

| Swift | Objective-C | C |
| --- | --- | --- |
| `Array` | `NSArray` | `CFArrayRef` |
|   | `NSAttributedString` | `CFAttributedStringRef` |
|   | `NSCharacterSet` | `CFCharacterSetRef` |
|   | `NSData` | `CFDataRef` |
|   | `NSDate` | `CFDateRef` |
| `Dictionary` | `NSDictionary` | `CFDictionaryRef` |
|   | `NSMutableArray` | `CFMutableArrayRef` |
|   | `NSMutableAttributedString` | `CFMutableAttributedStringRef` |
|   | `NSMutableCharacterSet` | `CFMutableCharacterSetRef` |
|   | `NSMutableData` | `CFMutableDataRef` |
|   | `NSMutableDictionary` | `CFMutableDictionaryRef` |
|   | `NSMutableSet` | `CFMutableSetRef` |
|   | `NSMutableString` | `CFMutableStringRef` |
|   | `NSNumber` | `CFNumberRef` |
|   | `NSSet` | `CFSetRef` |
| `String` | `NSString` | `CFStringRef` |
|   | `NSURL` | `CFURLRef` |

这并非完整列表，但包含了常见的类型。你在 MyStuff 应用中设置图片选择器时（参见第 7 章）已经遇到过这种情况。图片选择器的 `mediaTypes` 属性是一个 URI 类型数组。这些类型在 Core Foundation 中定义为 `CFStringRef` 值。要在应用中使用它，你需要编写如下代码：

```
picker.mediaTypes = [kUTTypeImage as NSString, kUTTypeMovie as NSString]
```

`kUTTypeImage` 符号是一个 Core Foundation 字符串值，但你只需将其转换为所需类型，就可以在需要 `NSString` 或 `String` 类型的任何地方使用它。

再举一个简单的例子：假设你从一个框架中收到了一个 `CFUUIDRef` 类型（Core Foundation 通用唯一标识符）。你更希望它变成 Swift 的 `String` 类型。有一个 Core Foundation 函数可以将 `CFUUIDRef` 转换为 `CFStringRef`。你只需将返回的 `CFStringRef` 转换为所需类型，如下代码所示：

```
let uuid = CFUUIDCreate(nil)
let uuidString: String = CFUUIDCreateString(nil, uuid) as String
```

### 方法签名

Objective-C 中的方法和 Swift 中的函数都有签名。**签名**是函数名称中用于标识自身的那部分。签名用于将函数调用与函数进行匹配。它们也用作**选择器**，即用于动态选择要执行方法的值。

Swift 签名源于 Objective-C 签名，因此我们先从 Objective-C 开始。以下方法在 Objective-C 的 `UIImage` 类中定义：

```
- (void)drawAtPoint:(CGPoint)point blendMode:(CGBlendMode)mode alpha:(CGFloat)alpha
```

将其与 Swift 的 `UIImage` 类中相同的方法进行比较。


好的，作为高级文档工程师和翻译员，我将严格遵循您提供的注意事项和示例，对给定的英文文本进行专业、准确的中文翻译。


