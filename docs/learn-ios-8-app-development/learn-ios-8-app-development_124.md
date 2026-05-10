# Swift 扩展、结构体、元组和枚举

### 扩展

扩展（Extensions）也可以方便地为常见类添加新函数。如果你需要为`String`类型添加一个`inKlingon`属性，可以在扩展中实现。然后你可以编写`"Hello".inKlingon`，Swift 将按如下方式执行翻译：

```
extension String {
    var inKlingon: String { return self == "Hello" ? "nuqneH" : "nuqjatlh?" }
}
...
let greeting = "Hello"
println(greeting.inKlingon)
```

扩展有一些限制：

- 扩展不能向现有类型添加存储属性。你只能添加方法和计算属性。
- 不能在扩展中重写现有属性或方法。

## 结构体

Swift 中的结构体（structure 或 struct）与类非常相似。与大多数其他语言不同，Swift 结构体与类的共同点比不同点更多。以下是一个 Swift 结构体的示例：

```
struct SwiftStruct {
    var storedProperty: String = "Remember me?"
    let someFixedNumber = 9
    var computedProperty: String {
        get {
            return storedProperty.lowercaseString
        }
        set {
            storedProperty = newValue
        }
    }

    static func globalMethod() {
    }
    func instanceMethod() {
    }
    init(what: String) {
        storedProperty = what
    }
}
```

在 Swift 中，结构体与类共享以下特征：

- 可以定义存储属性（存储属性可以有观察者）
- 可以定义计算属性
- 可以定义方法（实例函数）
- 可以定义全局函数
- 可以拥有自定义初始化器
- 可以遵循协议
- 可以通过扩展进行增强

在类和结构体之间，这些特征在能力或语法上几乎没有区别。不过也存在一些重要和次要的差异：

- 结构体不能继承另一个结构体。
- 结构体是值类型，而不是引用类型。
- 结构体没有`deinit`函数。
- 结构体中的全局函数使用`static`关键字声明，而不是`class`关键字。

第一点可能是最大的区别。每个结构体都是一个独立的类型。结构体不形成继承层次结构，不能参与子类型多态，也不能测试其类型（这毫无意义，因为它们只能是单一类型）。

结构体是值类型。我稍后会解释值类型和引用类型的区别。现在只需知道，结构体是存储属性的集合。当你将其赋值给变量或作为参数传递时，整个结构体会被复制。对副本所做的更改不会影响原始结构体。

与类一样，如果所有存储属性都有默认值，Swift 会自动创建默认初始化器。与类不同的是，结构体还会获得一个成员逐一初始化器——前提是它没有定义任何自定义初始化器。*成员逐一初始化器*是一个`init`方法，其参数是所有存储属性的名称。以下示例定义了一个包含两个存储属性的结构体。后续代码使用其自动生成的初始化器创建了该结构体的两个实例：

```
struct StructWithDefaults {
    var number: Int = 1
    var name: String = "amber"
}

let struct1 = StructWithDefaults()
let struct2 = StructWithDefaults(number: 3, name: "john")
```

## 元组

元组（Tuples）是临时的、匿名的结构体。元组是一种便捷的方式，可以将几个相关的值组合在一起，并将其视为单个值。编写元组时，用括号括起一个值列表，如下所示：

```
var iceCreamOrder = ("Vanilla", 1)
```

该语句创建了一个包含两个值的元组，一个`String`和一个`Int`，并将其赋值给单个变量`iceCreamOrder`。以下示例创建了相同类型的元组，但为其成员指定了名称：

```
let wantToGet = (flavor: "Chocolate", scoops: 4)
```

元组的类型就是其内容的类型。你可以将元组赋值给任何具有相同类型的变量，如下所示：

```
iceCreamOrder = wantToGet
```

**注意**：元组中可以包含多少个值或哪种类型的值，实际上没有限制。元组可以由浮点数、字符串、数组、对象、结构体、枚举、字典、闭包甚至其他元组组成。

元组对于从函数返回多个值非常方便。你在第 14 章的`score()`函数中使用过这一点；它返回了玩家的数字分数和一个指示游戏是否获胜的标志，以元组形式返回。在以下示例中，`nextOrder()`函数返回一个`(String, Int)`元组。请在这些示例所在的`Learn iOS Development Projects` → `Ch 20` → `Tuples.playground`文件中进行尝试。

```
func nextOrder() -> (flavor: String, scoops: Int) {
    return ("Vanilla", 4)
}
...
let order = nextOrder()
```

你可以像访问结构体或类的属性一样访问元组中的各个值。如果元组成员有名称，你可以像属性一样访问它们，如下所示：

```
if order.scoops > 3 {
    println("\(order.scoops) scoops of \(order.flavor) in a dish.")
} else {
    println("\(order.flavor) cone")
}
```

如果元组的某个成员是匿名的（即它们没有像`iceCreamOrder`中的成员名称），你仍然可以使用索引来访问该成员。*元组成员索引*是一个从 0 开始的数字，分配给元组中的每个成员值。你可以像使用属性名称一样使用索引，如下所示：

```
if iceCreamOrder.0 == "Chocolate" {
    println("\(iceCreamOrder.1) scoops of the good stuff.")
}
```

还有第三种方式可以访问元组的各个值。当你赋值元组时，可以将元组*分解*为单独的变量，如下所示：

```
let (what, howMany) = nextOrder()
if howMany > 3 {
    println("\(howMany) scoops of \(what) in a dish.")
} else {
    println("\(what) cone")
}
```

第一条语句声明了两个新变量`what`和`howMany`。每个变量都被赋值为元组中的一个值。这是一种为具有匿名成员的元组分配名称或更直接地访问每个值的便捷方式。如果你对某个特定值不感兴趣，可以用`_`（下划线）替换其变量名，就像忽略参数一样，例如`let (what, _) = nextOrder()`。

## 枚举

*枚举*（enumeration 或 enum）为一组唯一的字面值分配符号名称。编写枚举时，列出要定义的符号，如下所示：

```
enum Weekday {
    case Sunday
    case Monday
    case Tuesday
    case Wednesday
    case Thursday
    case Friday
    case Saturday
}
```

这定义了一个新类型`Weekday`，可以像任何其他类型一样使用。该枚举定义了七个唯一值。以下语句定义了一个`Weekday`类型的新变量（`dueDay`），并将其赋值为`Thursday`：

```
var dueDay = Weekday.Thursday
```

当 Swift 已经知道变量或参数的类型时，你可以省略枚举类型名称，如下所示：

```
dueDay = .Friday
```

这在将枚举值作为参数传递时特别方便。

### 原始值

Swift 对枚举值的内部表示是不透明的——用计算机术语来说就是“你不知道，也无法知道”。你只需要知道这些值是唯一的，并且可以赋值给该枚举类型的变量。

你可以选择定义枚举中的值与另一种类型中的值之间的关系。这个替代值称为枚举的*原始值*。

在以下示例中，定义了一个包含七个值的枚举。每个值都可以与对应的`Int`值进行相互转换。


```swift
enum WeekdayNumber: Int {
    case Sunday = 0
    case Monday
    case Tuesday
    case Wednesday
    case Thursday
    case Friday
    case Saturday
}
```

原始值的选择包括整数、浮点数、字符串或字符。如果选择整数类型，则会像前面展示的那样，为每个枚举值自动分配一个唯一的数字。当然，你也可以为特定枚举值指定具体数值，就像对 `Sunday` 所展示的那样。

枚举值可以通过其 `rawValue` 属性转换为原始值。反之，也可以使用其 `init(rawValue:)` 初始化器从原始值创建枚举值，这两种方式都在此处展示：

```swift
let dayNumber = WeekdayNumber.Wednesday.rawValue
let dayFromNumber = WeekdayNumber(rawValue: 3)
```

**注意**  从原始值转换为枚举可能会失败；所有枚举值都有唯一的原始值，但并非所有原始值都有对应的枚举值。有关可失败初始化器的更多信息，请参阅“可选值”部分。

`dayNumber` 是一个值为 `3` 的 `Int` 类型。这是通过将 `WeekdayNumber.Wednesday` 值转换为其原始值获得的。`dayFromNumber` 是一个包含值 `Wednesday` 的 `WeekdayNumber` 变量，该值是通过将原始值 `3` 转换为 `WeekdayNumber` 获得的。

原始值也可以是字符串或字符。在下面的示例中，定义了一个具有字符串原始值的枚举。对于非整数值，你必须为每个枚举值提供一个原始值，并且不能有重复。

```swift
enum WeekdayName: String {
    case Sunday = "Sunday"
    case Monday = "Monday"
    case Tuesday = "Tuesday"
    case Wednesday = "Wednesday"
    case Thursday = "Thursday"
    case Friday = "Friday"
    case Saturday = "Saturday"
}
```

与数字原始值一样，你可以与枚举值进行相互转换。以下代码将打印消息“报告应在星期一提交。”

```swift
let reportDay: WeekdayName = .Monday
println("Reports are due on \(reportDay.rawValue).")
```

## 关联值

每个枚举值还可以存储一个其他值的元组，称为*关联值*。这允许你将其他有意义的值与特定的枚举值打包在一起。例如，一个表示失败原因的枚举可能具有包含每种特定失败类型信息的关联值。`.DocumentNotFound` 值可能包含缺失文档的名称，而 `.SessionExpired` 值可能包含会话结束的时间。

当你为枚举添加关联值时，会为这些额外的元组预留存储空间。每个元组与一个特定的枚举值配对。该元组仅在枚举变量被设置为该值时才能使用。

通过一个例子来解释更容易理解。在下面的代码中，几个 `WeekdayChild` 枚举值具有关联值：

```swift
enum Sadness {
    case Sad
    case ReallySad
    case SuperSad
}

enum WeekdayChild {
    case Sunday
    case Monday
    case Tuesday(sadness: Sadness)
    case Wednesday
    case Thursday(distanceRemaining: Double)
    case Friday(friends: Int, charities: Int)
    case Saturday(workHours: Float)
}
```

`.Tuesday` 和 `.Saturday` 值存储一个关联的浮点数。`.Friday` 值存储两个额外的整数值。而 `.Thursday` 值则更进一步，存储了另一个枚举值。其他枚举值没有关联值；像对待任何其他枚举那样对待它们即可。

枚举及其关联值通过初始化器来构造。以下是一些示例，你也可以在 `Learn iOS Development Projects` → `Ch 20` → `Enumerations.playground` 文件中找到它们。

```swift
let graceful = WeekdayChild.Monday
let woeful = WeekdayChild.Tuesday(sadness: .ReallySad)
let traveler = WeekdayChild.Thursday(distanceRemaining: 100.0)
let social = WeekdayChild.Friday(friends: 23, charities: 4)
let worker = WeekdayChild.Saturday(workHours: 90.5)
```

**提示**  关联值元组成员的外部名称是可选的。你可以省略它们，直接使用值，例如 `WeekdayChild.Friday(23,4)`。

奇怪的是，你无法直接访问关联值。检索它们的唯一机制是通过 `switch` 语句，像这样：

```swift
var child = traveler
switch child {
    case .Sunday, .Monday, .Wednesday:
        break
    case .Tuesday(let mood):
        if mood == .ReallySad {
            println("Tuesday's child is really sad.")
        }
    case .Thursday(let miles):
        println("Thursday's child has \(miles) miles to go.")
    case .Friday(let friendCount, let charityCount):
        println("Friday's child has \(friendCount) friends.")
    case .Saturday(let hours):
        println("Saturday's child is working \(hours) hours this week.")
}
```

如上所示，你通过在每个 `switch` case 中使用 `let` 语句分解元组来获取关联值。这种特殊机制的原因在于，枚举值理论上可以包含任何可能的枚举值。但关联值仅当它被设置为特定枚举值时才是有效的。`switch` case 确保仅允许访问该特定枚举值的关联值。

## 扩展枚举

枚举是完整的类型，类似于类和结构体。枚举可以定义计算属性，并拥有静态方法和实例方法。它可以采纳协议，可以通过扩展进行扩展，并且可以拥有自定义初始化器。你不能添加存储属性；为此请使用关联值。以下是一个具有计算属性、方法和特殊初始化器的枚举：

```swift
enum WeekdayNumber: Int {
    case Sunday = 0
    case Monday
    case Tuesday
    case Wednesday
    case Thursday
    case Friday
    case Saturday

    var weekend: Bool { return self == .Sunday || self == .Saturday }
    var weekday: Bool { return !weekend }
    init(random: Bool) {
        if random {
            nextWeekDayNumber = Int(arc4random_uniform(7))
        }
        self = WeekdayNumber(rawValue: nextWeekDayNumber)!
        nextWeekDayNumber++
        if nextWeekDayNumber > WeekdayNumber.Saturday.rawValue {
            nextWeekDayNumber = WeekdayNumber.Sunday.rawValue
        }
    }
    func dayOfJulienDate(date: NSTimeInterval) -> WeekdayNumber {
        return WeekdayNumber(rawValue: Int(date) % 7 + 2)!
    }
}
var nextWeekDayNumber: Int = WeekdayNumber.Monday.rawValue
```

当编写在枚举上下文中的代码时，`self` 变量代表其当前值。你通过给 `self` 赋值来在初始化器中设定枚举的值。你可以像使用结构体或对象那样使用这些特性，如下所示：

```swift
let today = WeekdayNumber(random: true)
if today.weekend {
    clock.alarm.enabled = false
}
```

## 数值类型

现在，你将接触到最简单的类型，但可能仍然会有一些惊喜等着你。Swift 拥有整数和浮点数数值类型。你在本书中一直在使用它们。

整数类型有多种大小和符号：`Int`、`UInt`、`Int8`、`UInt8`、`Int16`、`UInt16`、`Int32`、`UInt32`、`Int64` 和 `UInt64`。你可以在 `Learn iOS Development Projects` → `Ch 20` → `Numbers.playground` 文件中找到它们的示例。

绝大多数情况下，你应该使用 `Int`，偶尔使用 `UInt`，几乎不使用其他类型。稍后你会明白原因。其余类型仅在处理特定数据大小（例如二进制消息格式）时才有用。


