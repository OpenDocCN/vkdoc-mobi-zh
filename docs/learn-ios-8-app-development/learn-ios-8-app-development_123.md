# 方法

类中的函数被称为*方法*、*实例方法*、*实例函数*，有时也称为*成员函数*。它是在对象上下文中运行的函数。你可以在类体中像声明函数一样声明方法，如下所示：

```
func pokeUser(name: String, message: String) -> Bool {
    return true
}
```

这个方法接收两个参数并返回一个布尔值。你可以使用如下语句来调用它：

```
let result = object.pokeUser("james", message: "Is that an ice cream truck I hear?")
```

如果函数不返回值，可以省略参数列表后面的返回类型（`-> ReturnType`）。

## 类方法

类方法类似于全局函数，但它属于该类。类方法不在对象上下文中执行。通过使用`class`关键字来声明类方法，如下所示：

```
class GoldenAge {
    class func timeRemaining() -> Double { ...
```

你可以使用类名（而非对象引用）来调用类方法，如下所示：

```
let remaining = GoldenAge.timeRemaining()
```

由于类方法不在对象上下文中执行，因此你不能直接引用该类的任何属性或实例函数。常量也不例外。

类方法通常用于提供对全局资源的访问，作为单例的 getter，或作为创建已配置对象的工厂方法。

## 参数名

函数中的每个参数都有两个名称：*外部参数名*和*局部参数名*。以下函数有三个参数，共有六个名称：

```
func poke(user name: String, say message: String, important priority: Bool) -> Bool {
    if priority {
        sendMessage(message, toPerson: name)
        return true
    }
    return false
}
```

在此示例中，每个参数都有外部参数名和局部参数名，且各不相同。在函数代码中，你使用局部参数名（`name`、`message`和`priority`）来引用它们的值。

调用函数时，你使用外部参数名（`user`、`say`、`important`）。以下语句将调用该函数：

```
poke(user: "james", say: "Wouldn't some ice cream be great?", important: true)
```

## 可选参数名

局部参数名和外部参数名都是可选的。如果没有外部参数名，则调用的参数列表只包含值。以下函数和函数调用展示了这一点：

```
func pokeUser(name: String, message: String) { ... }

pokeUser("james", "I swore I heard the ice cream truck!")
```

如果省略外部参数名，Swift 可能会将局部参数名用作外部参数名。例如，以下两个方法声明是相同的：

```
class ExternalNamesInAClass {
    func pokeUser(name: String, message: String) { ... }
    func pokeUser(name: String, message message: String) { ... }
}
```

在两个函数中，第一个参数没有外部名称，而最后一个参数有。在第一个函数中，Swift 将最后一个参数的局部名称用作其外部名称。这两个函数都必须按如下方式调用：

```
pokeUser("james", message: "I'm going to get ice cream.")
```

那么，为什么会这样呢？Swift 对外部参数名的期望根据函数的上下文而有所不同，具体如下：

*   在类、结构体或枚举外部声明的函数（有时称为*全局函数*）不期望有任何外部参数名。如果声明了外部名称，则在调用函数时必须使用它。如果省略，则该参数将是一个裸参数。
*   方法（在类中声明的函数）不期望第一个参数有外部名称，但期望其余所有参数都有。如果省略外部名称，则从第二个参数开始，局部名称将用作外部名称。
*   如果你的参数提供了默认值（请参阅“默认参数值”一节），Swift 期望它有一个外部名称。
*   类或结构体的初始化函数（`init()`）期望所有参数都有外部名称。如果省略任何一个，则局部名称将被用作外部名称。

第一条规则使 Swift 函数与 C、Java 和类似语言保持一致。如果你不声明外部参数名，你的函数调用看起来会像那些传统语言。你可以选择使用 Swift 更具表现力的命名参数，但这并非必须。

第二条规则旨在便于在类内部采用 Objective-C 的方法命名约定。Objective-C 不区分函数名和参数名。Objective-C 的方法名实际上只是一系列分隔参数的标记。因此，Objective-C 方法的第一个标记通常也命名第一个参数，例如 `setBool(_:,forKey:)` 方法（或者用 Objective-C 写成的 `-setBool:(bool)b forKey:(NSString)key`）。第一个参数是布尔值，已经在函数名中标识出来了。你不会想要写成 `setBool(bool: true, forKey: "key")`。

最后一条规则是为了便于创建多个初始化函数而不至于混淆。所有函数都必须有唯一的签名。由于所有初始化函数的函数名都是 `init`，Swift 必须使用唯一的外部参数名来区分它们。

## 参数名快捷方式

如果你想指定一个外部参数（在 Swift 不强制的环境下），并且你的外部参数名和局部参数名相同，则可以通过使用`#`（井号或哈希符号）作为外部名称来为两者使用同一个名称。以下两个函数声明是相同的：

```
func function(parameter parameter: Int)
func function(# parameter: Int)
```

相反，你可以强制 Swift *不*使用外部参数名——在通常应该使用的情况下——方法是用`_`（下划线）字符替换外部名称。你可以通过如下方式编写 `poke(_:,message:)` 函数来抑制外部参数名：

```
func poke(name: String, _ message: String)
```

现在你可以简单地调用此函数为 `object.poke("james","What flavor?")`。

你还可以忽略局部参数名。当你对该参数不感兴趣时可以这样做。例如，`UIControl` 类通过调用一个方法来发送动作，该方法的唯一参数是引起动作的控制对象。一个典型的动作方法如下所示：

```
@IBAction func sendPoke(sender: AnyObject)
```

很多时候，动作方法并不需要使用`sender`参数。你可以通过用`_`（下划线）字符替换局部参数名来表示你不感兴趣，如下所示：

```
@IBAction func sendPoke(_: AnyObject)
```

参数仍然存在，调用者仍然需要提供它，但函数的代码会忽略它。

## 默认参数值

你可以为一个或多个参数提供默认值。如果这样做，你可以选择在函数调用中省略该参数，函数将接收默认值。你可以在函数声明中分配默认值，如下所示：

```
func pokeUser(name: String, message: String = "Poke!", important: Bool = false )
```



第二和第三个参数拥有默认值。在函数调用时，可以省略其中一个或全部两个。如果你在调用时省略了某个参数，该参数将被设置为默认值。以下四个语句都调用了这个函数：

```
defaulter.pokeUser("james")
defaulter.pokeUser("james", message: "Where's my ice cream?")
defaulter.pokeUser("james", important: true)
defaulter.pokeUser("james", message: "The ice cream is melting!", important: true)
```

为清晰起见，带有默认值的参数应具有外部名称。Swift 会自行处理此问题：如果你省略了外部名称，它会将局部名称用作外部名称。为避免歧义，后续的任何参数也应具有外部名称。

## 继承

一个类可以继承自另一个类。发生继承时，它会获取其超类所有的属性和方法。然后，该类会通过添加额外的属性和方法来扩充这个基础。

一个类可以重写其超类的方法。当你这样做时，必须在该方法前加上 `override` 关键字，如下所示。这可以防止你因恰好选择了一个已被实现的函数名而意外重写方法。

```
class BaseClass {
    var lastUserPoked: String = "james"
    func pokeUser(name: String) {
        sendMessage("Poke!", toUser: name)
        lastUserPoked = name
    }
}

class SubClass: BaseClass {
    override func pokeUser(name: String) {
        println("someone poked \(name)")
    }
}
```

方法有一个特殊的 `super` 变量，用于引用其超类的方法和属性。使用 `super` 变量可以调用超类的方法，而不是你的类重写后的版本，如下所示：

```
class SubClass: BaseClass {
    override func pokeUser(name: String) {
        super.pokeUser(name)
        println("someone poked \(name)")
    }
}
```

## 重写属性

你也可以用计算属性重写任何属性。你重写的属性可以是存储属性，也可以是另一个计算属性。同样，你使用 `override` 关键字来表明意图。在以下示例中，子类正在创建一个计算属性 `lastUserPoked`，用于重写基类中定义的存储属性（`lastUserPoked`）。

```
class SubClass: BaseClass {
    override var lastUserPoked: String {
        get {
            return super.lastUserPoked
        }
        set {
            if newValue != lastUserPoked {
                println("caller is poking a different user")
            }
            super.lastUserPoked = newValue
        }
    }
}
```

请注意计算属性是如何使用 `super.lastUserPoked` 语法来访问基类中定义的存储属性的。

或者，你也可以重写属性观察器，如下所示：

```
override var lastUserPoked: String {
    didSet {
        ...
    }
}
```

被继承的属性可以是存储属性或计算属性，但它必须是可变的；属性观察器仅在值被设置时执行。

## 阻止继承

如果你想切断类、方法或属性的任何继承者，请使用 `final` 关键字。以下类定义了一个属性和一个方法，它们都是 final 的：

```
class ClassWithLimitedInheritance {
    final var trustFundName: String = "Scrooge McDuck"
    final func giveToCharity(amount: Double) {
        if amount > 2.00 {
            println("Too much!")
        }
    }
}
```

你可以创建 `ClassWithLimitedInheritance` 的子类并扩展它，但不能重写它的 `trustFundName` 属性或 `giveToCharity(_:)` 方法。如果你想禁止所有的子类化，将 `final` 关键字添加到类本身，如下所示：

```
final class EndOfTheLine: ClassWithLimitedInheritance { ...
```

## 对象创建

创建 Swift 对象的方式是将类名当作函数调用来编写。以下语句创建了一个新的 `ClassCalculatingProperties` 对象，并将其赋值给 `calculatingObject`：

```
let calculatingObject = ClassCalculatingProperties()
```




此语法用于分配新对象并调用类的某个初始化函数。在示例中，调用的是`init()`——即无参数的初始化函数。你可以编写自己的类初始化函数。编写方式与成员函数类似，但需注意：省略`func`关键字，函数名固定为`init`，且无返回值，如下所示：

```
class SimpleInitializerClass {
    var name: String
    init() {
        name = "james"
    }
}
```

每个类必须至少有一个初始化函数，否则将无法创建对象。在下面的类中，所有存储属性都有默认值，且该类未声明任何自己的初始化函数。在这种情况下，Swift 会自动为你创建一个`init()`初始化函数。

```
class AutomaticInitializerClass {
    var name = "james"
    var superUser = true
    // init() 由 Swift 提供
}
```

你可以根据需要定义任意数量的初始化函数。它们也可以包含默认参数值，如下所示：

```
class IceCream {
    var flavor: String
    var scoops: Int = 1
    init() {
        flavor = "Vanilla"
    }
    init(flavor: String, scoops: Int = 2) {
        self.flavor = flavor
        self.scoops = scoops
    }
}
```

**提示** 如果局部变量名与属性名相同，请使用`self`变量来区分它们。`flavor`指代局部参数。`self.flavor`指代对象的`flavor`属性。

你的初始化函数通过其外部参数名称来区分（因为它们函数名都相同）。Swift 会根据参数名称选择相应的初始化函数，如下所示：

```
let plain = IceCream()
let yummy = IceCream(flavor: "Chocolate")
let monster = IceCream(flavor: "Rocky Road", scoops: 3)
```

**注意** *所有*Swift 值都使用相同的语法创建。表达式`CGFloat(1.0)`会创建一个新的`CGFloat`值，等同于`Double`常量`1.0`。表达式`Weekday(rawValue: 1)`会创建一个等于`.Monday`的枚举值，以此类推。

## 子类初始化函数

如果你的类是子类，那么它的初始化函数必须显式调用父类的某个初始化函数。在下面的子类中，初始化函数先设置自身的属性，然后调用父类的初始化函数，以便父类初始化其属性：

```
class IceCreamSundae: IceCream {
    var topping: String
    var nuts: Bool = true
    init(flavor: String, topping: String = "Caramel syrup") {
        self.topping = topping
        super.init(flavor: flavor, scoops: 3)
        }
    }
}
```

## 安全初始化

Swift 一贯强调，在任何变量都被初始化之前，不能使用任何值、类或结构体。Swift 也允许你将存储属性的初始化推迟到初始化函数中执行。这可能会导致一个“先有鸡还是先有蛋”的问题。

让我通过向`IceCream`类添加一个`addCherry()`函数，然后在`IceCreamSundae`类中重写它来演示这个问题，如下所示：

```
class IceCream {
    ...
    init(flavor: String, scoops: Int = 2) {
        self.flavor = flavor
        self.scoops = scoops
        addCherry()
    }
    func addCherry() {
    }
}

class IceCreamSundae: IceCream {
    ...
    init(flavor: String, topping: String = "Caramel syrup") {
        super.init(flavor: flavor, scoops: 3)  // <-- 无效的初始化函数
    }
    override func addCherry() {
        if topping == "Whipped Cream" {
            ...
        }
    }
}
```

考虑一下`IceCreamSundae`对象是如何被创建的。当`IceCreamSundae`的初始化函数开始执行时，`topping`属性尚未初始化，因为它没有默认值。此时还不能调用父类初始化函数（`super.init(flavor:,scoops:)`），因为它可能会调用你重写后的`addCherry()`函数，而该函数可能会访问尚未初始化的`toppings`属性。

为了解决这个难题，Swift 要求所有初始化函数按以下顺序分三个阶段执行其工作：

1.  初始化所有存储常量和变量，无论是显式初始化还是依赖属性的默认值。
2.  调用父类初始化函数来初始化父类。
3.  执行任何需要调用方法或使用计算属性的额外设置。

`IceCreamSundae`初始化函数首先要做的是确保其所有属性都已初始化。编写`IceCreamSundae`初始化函数的正确方式如下：

```
class IceCreamSundae: IceCream {
    ...
    init(flavor: String, topping: String = "Caramel syrup") {
        self.topping = topping
        super.init(flavor: flavor, scoops: 3)
}
```

单条`self.topping = topping`语句就满足了第一阶段的要求。`topping`属性没有默认值，必须显式设置。`nuts`属性有默认值；如果你没有将其设置为其他值，Swift 会为你将其设置为`true`。此时，所有子类属性都已初始化完成。

**注意** 在对象初始化期间，属性观察器永远不会被执行。

这也是 Swift 中为数不多的可以设置不可变常量的地方。即使一个属性被声明为常量（`let preferDarkChocolate = true`），你也可以在对象初始化的第一阶段对其进行设置。

第二阶段必须调用父类初始化函数。这将初始化父类的所有属性、父类的父类的属性，依此类推。

当父类初始化函数返回时，对象已完全初始化。此时，可以安全地调用任何成员函数或使用任何属性来为对象的使用做准备。

你的初始化函数可能会缺少其中的某些阶段。

*   **如果类没有存储属性或所有存储属性都有默认值**，则不需要第一阶段。
*   **如果类没有父类**，则无需调用父类初始化函数，也没有第二阶段。
*   **如果类除了 Swift 的要求之外没有任何初始化工作**，则不会有第三阶段。

## 便捷初始化函数

到目前为止我介绍的初始化函数被称为指定初始化函数。*指定初始化函数* 是实现了上一节所述三个阶段的初始化函数。

你也可以创建便捷初始化函数。*便捷初始化函数* 通过提供简化的接口来简化对象的创建，但将初始化对象的重要工作委托给*同一类中*的另一个初始化函数。使用`convenience`关键字声明便捷初始化函数。以下代码为`IceCreamSundae`类添加了一个便捷初始化函数：

```
convenience init(special dayOfWeek: Weekday) {
    switch dayOfWeek {
        case .Sunday:
            self.init(flavor: "Strawberry", topping: "Whipped Cream")
        case .Friday:
            self.init(flavor: "Chocolate", topping: "Chocolate syrup")
        default:
            self.init(flavor: "Vanilla")
    }
}
```

现在，你可以使用以下语句创建*今日特色*圣代冰淇淋：

```
let sundaySundae = IceCreamSundae(special: .Sunday)
```

注意使用了`self.init(...)`来调用同一类中的另一个初始化函数。被调用的初始化函数必须属于同一个类，并且必须是一个指定初始化函数，或者是另一个最终能调用到指定初始化函数的便捷初始化函数。

## 初始化函数的继承

初始化函数的继承稍微有点复杂。完整说明请参考 iBooks 中的 *The Swift Programming Guide*，但以下是基本规则：



- 如果子类未定义自己的任何初始化器，且所有存储属性都有默认值，则子类继承父类的所有初始化器。（技术上，`Swift`会自动生成所有指定初始化器。）
- 如果子类重写了一个初始化器，则必须加上`override`关键字前缀，就像方法或属性一样。
- 如果子类声明了任何未初始化的存储属性变量或常量，则必须实现指定初始化器来设置这些值。
- 如果子类实现了自己的任何初始化器，它将不会自动继承任何指定初始化器。如果希望在子类中使用相同的指定初始化器，则必须重写并重新实现它们全部。
- 只有调用子类也提供的指定初始化器（通过继承或重写）的便捷初始化器才会被继承。

例如，以下`BananaSplit`类继承自`IceCreamSundae`。它没有声明没有默认值的存储属性。如果仅此而已，它本可以继承`IceCreamSundae`的所有初始化器。但它选择重写了指定初始化器`init(flavor:,topping:)`，如下所示：

```
class BananaSplit: IceCreamSundae {
    override init(flavor: String = "Vanilla, Chocolate, Strawberry",
                  topping: String = "Caramel syrup") {
        super.init(flavor: flavor, topping: topping)
        scoops = 3  // 香蕉船总是有三个球
    }
}
```

以下代码创建了一个`BananaSplit`对象：

```
let split = BananaSplit(flavor: "Vanilla, Pineapple, Cherry")
```

因为它实现了**至少一个**初始化器，所以不会继承任何其他指定初始化器。但它会自动继承便捷初始化器`init(special:)`，因为该便捷初始化器依赖于指定初始化器`init(flavor:,topping:)`，而该指定初始化器已在子类中实现。以下代码演示了使用继承的便捷初始化器创建香蕉船（`banana split`）：

```
let sundaySplit = BananaSplit(special: .Sunday)
```

必要初始化器

为了让初始化器更复杂（也就是说，更复杂），你可以使用`required`关键字声明初始化器是必需的，如下所示：

```
required init(flavor: String, scoops: Int) { ...
```

必要初始化器**必须**由子类重写并重新实现。子类可以选择仅重写初始化器，或再次将其声明为必需的。如果只是重写初始化器，则子类的子类不需要再次实现该初始化器。如果重新要求初始化器（再次使用`required`关键字），则子类的子类也必须实现它。

最常见的必要初始化器是你在第 19 章中使用的`NSCoding`协议中的反序列化初始化器。`init(coder: NSCoder)`初始化器应在每个子类中重新实现，以便每个子类都能正确地从归档数据流中重构对象。子类必须重新实现`init(coder: NSCoder)`，并且应将其标记为`required`，以便该类的任何子类也必须这样做。

闭包

*闭包*是一个封装了代码块及其执行所需变量的对象。将闭包视为一个自包含的功能包，可以像其他任何值一样传递。这个名字来源于闭包在创建时“闭合”其引用的变量的方式。以下代码片段演示了闭包的一般形式：

```
var externalValue = 2
let closure = { (parameter: Int) -> ReturnType in
    return codeThatDoesSomething(parameter+externalValue)
}
```

闭包写成大括号内一个独立自由的代码块。以下是关键点：

- 像函数一样，闭包可以有参数。调用者在执行闭包时提供这些值。
- 闭包参数没有外部名称，也不能声明默认值。
- 必须指定闭包的返回类型。对于不返回值的闭包，使用`-> Void`。
- 闭包的参数和返回类型在`in`关键字之前声明。闭包的代码在`in`关键字之后。无参数闭包写法为`() -> ReturnType`。
- 闭包可以使用其参数、在代码块中声明的变量以及代码块外部的变量。后者在闭包创建时被“闭合”。
- 闭包通常被称为*代码块*，这让人联想到`Objective-C`中的类似功能。许多其他语言将此类功能称为*lambda*。

通常，一旦变量超出作用域，它就会停止存在。例如，函数参数仅在函数生命周期内存在。函数结束时，其参数变量（以及它创建的任何局部变量）也会消失。在以下代码中，参数`number`在函数调用时创建，并在函数返回时消失：

```
func inScope(number: Int) {
    // 使用 number 执行某些操作的代码
}
```

在以下版本的`inScope(_:)`中，函数返回一个闭包给调用者。该闭包在创建时捕获了`number`参数。

```
func inScope(number: Int) -> ((Int) -> Int) {
    return { (multiplier: Int) -> Int in
        return number * multiplier
        }
}
```

函数返回时，其`number`参数通常应该停止存在。但因为它被闭包“闭合”，所以现在它也存在于闭包的作用域中，并将随着闭包的生命周期而存在。

函数返回后，其对`number`参数的引用消失。该参数现在实际上是闭包中的一个私有变量——因为没有其他引用指向它。每次调用函数时，都会创建一个新的参数变量，并且新的闭包会捕获该变量，如下所示：

```
let times5 = inScope(5)
let times3 = inScope(3)
```

我们可以通过执行这两个闭包来演示这一点。通过将闭包变量视为函数来执行闭包，如下所示：

```
var result = times5(7)  // 返回 35 (5*7)
result = times3(7)      // 返回 21 (3*7)
```

这两个闭包各自将提供的参数与在早期调用`inScope()`时捕获的值相乘。

在另一种场景中，考虑闭包捕获一个变量且该变量仍然存在的情况。闭包捕获的是变量的*引用*，而不是其值。（值和引用之间的区别将在本章稍后讨论。）闭包内外的所有代码都引用同一个变量。为了演示这一点，我们创建一个持久变量和一个捕获它的闭包。

```
var multiplier =  3
let sharedVariable = { (number: Int) -> Int in
    return number * multiplier
}
```

当执行闭包时，它会将参数值与捕获的`multiplier`变量的值相乘，如下所示：

```
result = sharedVariable(4)        // 返回 12 (4 * multiplier)
```

如果随后修改`multiplier`变量，闭包将使用修改后的值，如下所示：

```
multiplier = 5
result = sharedVariable(4)        // 返回 20 (4 * multiplier)
```

捕获 self

`Swift`充满了快捷键，允许你只编写表达意图所需的代码。然而，有少数地方`Swift`要求你明确写出显而易见的内容，这样才名副其实。闭包就是其中之一。考虑以下视图控制器，它动画显示标签的外观：

```
class LabelViewController: UIViewController {
    @IBOutlet var label: UILabel!
```



```swift
override func viewDidAppear(animated: Bool) {
    UIView.animateWithDuration(1.5, animations: { label.alpha = 1.0 })
}
```

请仔细查看这个闭包，告诉我它“捕获”了哪个变量。（提示：答案**不是** `label`。）

没错，正确答案是 `self`。当你在方法中引用某个属性或函数时，实际上是在隐式地引用上下文对象的实例变量或实例函数。在 `viewDidAppear(_:)` 函数的上下文中，表达式 `storyboard` 的实际含义是 `self.storyboard`，而函数调用 `setEditing(true, animated: false)` 实际上就是 `self.setEditing(true, animated: false)`。

闭包中的这一区别非常重要。前面的闭包捕获的是 `self` 变量（指向视图控制器对象的引用），而不是指向特定 `UILabel` 对象的引用。这一点至关重要，因为正如你所见，闭包会保持对其所捕获变量的引用。

假设我创建了这个闭包，但紧接着将 `label` 属性替换为一个不同的 `UILabel` 对象。当闭包执行时，它会使用新的 `UILabel` 对象，因为它捕获的是 `self`，而不是原始的 `UILabel` 对象。这也正是 Swift 不允许你编写前面那段代码的原因。你必须将闭包写成如下形式：

```swift
override func viewDidAppear(animated: Bool) {
    UIView.animateWithDuration(1.5, animations: { self.label.alpha = 1.0 })
}
```

Swift 要求你在编写闭包时必须写出 `self.label`、`self.storyboard` 和 `self.setEditing(true, animated: false)`。这明确地表明你捕获的是 `self`，而不是 `label` 或 `storyboard`。

## 使用闭包

使用闭包再简单不过了。当你创建一个闭包时，它就变成了一个值——你可以将其赋值给一个变量，或作为参数传递。你对闭包的使用很可能从作为参数开始。iOS 提供了许多机会，让你将代码块传递给那些最终会执行该闭包以完成任务的函数。看看本书中的几乎任何一个项目，你都会看到闭包的实际应用。在第 16 章中，当显示区域调整大小时，代码会传递两个闭包给过渡协调器，如下所示：

```swift
coordinator.animateAlongsideTransition( { (context) in self.positionDialViews() },
                            completion: { (context) in self.attachDialBehaviors() })
```

`animateAlongsideTransition(_:,completion:)` 方法接受两个参数，这两个参数都是闭包。第一个闭包用于在过渡期间给你的视图施加动画效果，第二个闭包则在过渡完成后执行。

请注意，每个闭包中的参数都没有指定类型。这是因为 Swift 提供了许多快捷方式，让你能够编写简洁的闭包。

## 闭包快捷方式

Swift 允许你使用多种快捷方式来简化闭包。

*   如果闭包的参数类型和返回类型已由参数或你为其赋值的变量隐式指定，则可以省略这些类型。
*   你可以省略参数名称，而使用其简写名称。
*   如果闭包的代码可以写成单个返回语句，那么你可以只写返回表达式。
*   如果闭包是函数调用的最后一个参数，你可以使用尾随闭包语法。

第一个快捷方式简化了闭包的参数。在类似 `animateAlongsideTransition(_:,completion:)` 方法的情况下，方法的声明已经确定了闭包的描述。如果你查看该方法的声明，会看到类似如下的内容：

```swift
func animateAlongsideTransition(
    _ animation: ((UIViewControllerTransitionCoordinatorContext!) -> Void)!,
    # completion: ((UIViewControllerTransitionCoordinatorContext!) -> Void)!) -> Bool
```

这个函数描述了两个参数。两者都期望一个闭包，该闭包有一个参数（类型为 `UIViewControllerTransitionCoordinatorContext`）且没有返回值（`-> Void`）。你作为参数传递给该调用的闭包*必须*符合这个描述。因为 Swift 已经知道了参数的数量、类型以及闭包将返回的类型，所以 Swift 会为你补充这些细节。你所需要做的只是给参数命名，如下所示：

```swift
{ (context) in self.positionDialViews() }
```

`context` 参数的类型以及闭包的返回类型都是隐含的。在这种情况下，你还可以去掉括号来节省几个按键操作，如下所示：

```swift
{ context in self.positionDialViews() }
```

如果某个参数无关紧要，你可以用 `_`（下划线）替换它的名称，如下所示：

```swift
{ _ in self.positionDialView() }
```

### 简写参数名称

如果你不想给参数命名，可以使用它们的简写名称：`$0`、`$1`、`$2`，以此类推。如果你这样做，就可以省略闭包开头的整个参数子句。以 `SKAction` 的 `customActionWithDuration(_:,actionBlock:)` 方法为例。该函数的定义如下：

```swift
func customActionWithDuration(_ seconds: NSTimeInterval,
                      actionBlock block: (SKNode!, CGFloat) -> Void)
         -> SKAction
```

第二个参数是一个闭包，它接收两个参数（一个 `SKNode` 和一个 `CGFloat`）。当你调用这个函数时，可以像这样编写闭包：

```swift
SKAction.customActionWithDuration( 3.0,
                      actionBlock: {
                          (node, elapsedTime) in
                          if elapsedTime > 0.5 {
                              node.alpha = elapsedTime/3.0
                          }
                      })
```

`actionBlock` 参数的闭包测试了 `elapsedTime` 参数。如果过去了超过半秒，它会根据经过的时间按比例设置节点的 `alpha`。你可以这样编写同样的代码：

```swift
SKAction.customActionWithDuration( 3.0,
                      actionBlock: { if $1 > 0.5 { $0.alpha = $1/3.0 } } )
```

在第二个例子中，整个参数声明都被省略了。Swift 将第一个参数分配简写名称 `$0`，第二个参数分配 `$1`，以此类推。使用这些简写名称的方法与你使用命名参数完全相同。

**提示：** 仅在参数的含义和类型显而易见的上下文中使用简写名称。第二个例子远不如第一个例子清晰明了。

### 单表达式闭包

对于返回值的闭包，还有一种更简短的形式。如果整个闭包的代码可以写成一个返回语句，你可以省略 `return` 语句并将闭包写成一个表达式。这在诸如 Swift 的 `sort(_:)` 和 `map(_:)` 数组方法中特别有用。让我们用以下代码创建一个简单的待办事项数组：

```swift
struct ToDoItem {
    let priority: Int
    let note: String
}
var toDoList = [ ToDoItem(priority: 3, note: "回收冰淇淋杯"),
                 ToDoItem(priority: 1, note: "买冰淇淋"),
                 ToDoItem(priority: 2, note: "邀请朋友来") ]
```

你可以使用数组的 `sort(_:)` 函数对该数组进行排序。该函数接受一个用于比较数组中任意两个元素的闭包。闭包接收两个参数（要比较的两个数组元素）并返回一个布尔值，指示在新的顺序中第一个（左侧）参数是否应该排在第二个（右侧）参数之前。（你可以在 `Learn iOS Development Projects` ![image](img/arrow.jpg) `Ch 20` ![image](img/arrow.jpg) `Closures.playground` 文件中找到相关示例。）

让我们按优先级顺序对数组进行排序。使用命名参数编写这个闭包看起来像下面这样：

```swift
toDoList.sort( { (left, right) in return left.priority < right.priority } )
```



正如你之前所学，你也可以使用简写参数名来编写闭包，如下所示：

```
toDoList.sort( { return $0.priority < $1.priority } )
```

但由于整个闭包是单一返回语句，Swift 允许你省略 `return`，只将闭包写为表达式。以下两条语句与之前的两条等价：

```
toDoList.sort( { (left, right) in left.priority < right.priority } )
toDoList.sort( { $0.priority < $1.priority } )
```

## 尾随闭包

闭包可以变得更短——呃，也许不是闭包本身，而是将闭包作为参数传递的语法。如果函数调用的最后一个参数是闭包参数，你可以省略该参数，并在函数调用后直接编写闭包。这被称为*尾随闭包*。`SKNode` 类有一个 `enumerateChildNodesWithName(_:,usingBlock:)` 方法。以下示例展示了如何将闭包作为常规参数来调用该方法：

```
let scene = SKScene()
scene.enumerateChildNodesWithName("firefly", usingBlock: {
        (node, _) in
        node.hidden = false
    })
```

要使用尾随闭包编写同样的代码，请省略最后一个参数，并在函数调用后紧跟闭包，如下所示：

```
scene.enumerateChildNodesWithName("firefly") { (node, _) in node.hidden = false }
```

同样，这对于像 `sort(_:)` 这样的函数特别有用。使用之前的单一表达式示例，你最终可以将排序函数写成如下形式：

```
toDoList.sort() { $0.priority < $1.priority }
```

最后一个示例结合了类型推断、简写参数名、单一表达式以及尾随闭包这些简写特性。

## 闭包变量

当你准备将闭包存储到变量中，或者编写一个将闭包作为参数的函数时，你需要编写一个闭包类型。*闭包类型*是闭包的签名（`in` 关键字之前的部分）放在括号内。编写一个存储闭包的变量如下所示：

```
var closure: ((Int, String) -> Int)
```

这个变量存储一个接收两个参数（一个 `Int` 和一个 `String`）并返回一个 `Int` 的闭包。闭包参数没有外部名称或默认值，因此只列出了参数类型。你之后可以将一个闭包赋值给这个变量，然后像下面这样执行它：

```
closure = { (number,name) in return 1 }
closure(7,"deborah")
```

类似地，一个带有闭包参数的函数编写如下：

```
func closureAsParameter(key: String, master: ((String) -> Int)) {
    unlockDoor(master(key))
}
```

由于闭包是最后一个参数，这个函数适合使用尾随闭包语法，如下所示：

```
closureAsParameter("The Key") { (key) in return key.hash }
```

## 协议

*协议*是一组属性或方法，类（或其他类型）可以采纳它们。一个协议实际上就是承诺实现一组特定的功能。一旦你的类履行了这个承诺，你的类就可以在任何一个希望使用该协议类型的场景中使用。一个采纳了协议的类被称为*符合*该协议。

声明协议的方式与声明类非常相似，使用 `protocol` 关键字。作为一个简单示例，让我们创建一个 `Flavor` 协议，如下所示。这些示例以及其他示例可以在 `Learn iOS Development Projects` ![image](img/arrow.jpg) `Ch 20` ![image](img/arrow.jpg) `Protocols.playground` 文件中找到。

```
protocol Flavor {
    var flavor: String { get }
    func tastesLike(flavor otherFlavor: Flavor) -> Bool
}
```

该协议要求一个名为 `flavor` 的 `String` 属性，以及一个名为 `tastesLike(flavor:)` 且返回 `Bool` 的函数。

你的类通过在父类名称（如果有的话）之后列出协议来采纳它们，如下所示：

```
class IceCream {
    var flavor: String = "Vanilla"
    var scoops: Int = 1
}

class Sundae: IceCream, Flavor {
    func tastesLike(flavor otherFlavor: Flavor) -> Bool {
        return flavor == otherFlavor.flavor
    }
}
```

在这个示例中，类 `Sundae` 是 `IceCream` 的子类，并采纳了 `Flavor` 协议。采纳该协议要求它具备一个 `flavor` 属性和一个 `tastesLike(flavor:)` 函数。前者已从其父类继承而来，因此实现该函数就是采纳该协议所需的全部操作。

**注意**  Objective-C 可以定义具有可选属性和方法的协议。你的类可以选择不实现某个可选方法，但仍符合该协议。Swift 协议没有可选成员。

采纳协议的类可以通过使用存储属性或计算属性（实现、重写或继承）来满足属性要求。协议不关心类如何实现接口，只关心是否实现了它。协议中的每个属性后面都跟着 `{ get }` 或 `{ get set }`。任何属性都能满足前者。如果协议声明属性为 `{ get set }`，则你必须实现一个可变属性。

一旦 `Sundae` 采纳了 `Flavor`，它就可以在任何一个类型为 `Sundae`、`IceCream` 或 `Flavor` 的变量中使用。这使得不相关的类只要都采纳了同一协议，就能被视为一种通用类型。在以下示例中，完全不相关的类 `Tofu` 也采纳了 `Flavor` 协议：

```
class Tofu: Flavor {
    var flavor: String = "Plain"
    var weight: Double = 1.0

func tastesLike(flavor _: Flavor) -> Bool {
        // 豆腐和其他任何东西的味道都不像
        return false
    }
}
```

现在你可以声明类型为 `Flavor` 的变量和参数，并互换使用 `Tofu` 和 `Sundae` 对象，如下所示：

```
var dessert: Flavor = Sundae()
var chicken: Flavor = Tofu()
if dessert.tastesLike(flavor: chicken) { ...
```

协议通常用于委托。一个类会使用协议来定义委托的接口，然后声明一个 `delegate` 属性，该属性可以存储任何采纳该协议的对象。只需看看几乎任何一个具有 `delegate` 属性的 Cocoa Touch 类即可。

### 扩展

扩展可以向现有的类（或其他类型）添加属性或方法。你可以向几乎任何东西添加方法和计算属性，即使是你没有编写的类。回想一下第 7 章，我曾告诉你向 `MyWhatsit` 类添加一个 `imageView` 属性是不良的 MVC 设计，因为 `MyWhatsit` 类是数据模型，而 `imageView` 实际上是一个视图方法。通过扩展，你可以在保持设计的同时，仍然向 `MyWhatsit` 添加一个 `imageView` 属性。从（简化版的）原始类开始，如下所示：

```
class MyWhatsit {
    var name: String
    var location: String
    var image: UIImage?
    ...
}
```

在一个单独的源文件中，你可以创建一个针对 `MyWhatsit` 的扩展来添加额外的方法，如下所示。`extension` 关键字用于表明你是在*扩展*现有的类（或其他类型），而不是定义一个全新的类。

```
extension MyWhatsit {
    var viewImage: UIImage {
        return image ?? UIImage(named: "camera")!
    }
}
```

现在当你创建 `MyWhatsit` 对象时，它们都会拥有一个 `viewImage` 属性，就像你在原始类中定义了这个属性一样。

```
var thing = MyWhatsit(name: "Robby the Robot")
detailImageView.image = thing.viewImage
```

这保持了 MVC 的分离，因为管理数据模型的代码与处理视图对象职责的代码是分开的，即使两者属于同一个对象。在另一个程序中，你可能会在没有这个扩展的情况下复用 `MyWhatsit` 类，在这种情况下，这些相同的对象将不会有 `viewImage` 属性。



