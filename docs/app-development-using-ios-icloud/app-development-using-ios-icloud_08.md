# 3. 类扩展

扩展提供了一种强大的方式，可以为现有的类、结构体、枚举或协议添加新功能。添加后，该函数可作为被添加类或对象的自然扩展使用。扩展可以添加到用户定义的对象，也可以添加到系统定义的类。在本章中，我们将学习如何为系统定义的类添加扩展，而在专门讨论图像处理的第 5 章中，我们将学习如何为用户定义的视图控制器添加扩展。

一旦定义了扩展，我们就可以像在定义函数时所期望的那样做任何事情。例如，你可以：

*   添加委托
*   定义方法、结构体和类型
*   定义初始化器
*   遵循或确认某个协议

## 示例代码说明

在下面的例子中，我们将扩展系统定义的类 `String`，以添加一个当前不存在的字符串操作函数。我们将添加以下三个函数：

*   获取字符串的最后一个索引
*   检查用户提供的字符串输入是否是有效的电子邮件地址
*   检查用户提供的字符串输入是否是有效的 URL

在我们的示例中，我们将在视图控制器类之上添加扩展。请注意，该扩展不在视图控制器类定义内部，并且一旦定义，它在整个项目范围内都是可见的。

### 字符串扩展

```
extension String {
}
```

要定义扩展，我们需要使用系统定义的关键字 `extension`（注意区分大小写）。在关键字 `extension` 之后，我们需要提供扩展的名称。在我们的例子中，我们扩展的是系统定义的 `String` 类；因此我们使用相同的系统定义的类名。

#### 方法：查找最后一个索引

```
func lastIndex(of string: String) -> Int? {
}
```

函数的名称为 `lastIndex`。它将接受一个 `String` 字面量，并返回该字符串的最后一个索引号，该索引是一个整数。请注意，`lastIndex` 将作为 `String` 函数的一个新增函数在整个项目中可用。

```
guard let index = range(of: string, options: .backwards) else { return nil }
```

在这一行中，我们定义了一个变量 `index`。请注意，该变量被定义为 `let`，因为我们不会在函数作用域内重复使用它。此外，我们使用 `guard` 来确保如果用户提供的字符串有问题，不会导致抛出系统错误。

系统定义的 `range` 函数会向后查找用户提供的字符串，如果找到则返回一个有效的范围给 `index` 变量；如果未找到或遇到错误，它将返回 `nil`（执行函数的 `else` 部分）。

```
return self.distance(from: self.startIndex, to: index.lowerBound)
```

这行代码计算字符串开头与找到的子字符串起始位置之间的距离，并返回其位置。

关键字 `self` 指的是当前上下文中的字符串（即传递给函数的字符串），而 `distance` 是 Swift 中 `String` 类的一个系统定义方法，它接收两个参数，如下所述：

*   `self.startIndex` 指的是字符串中的第一个字符。
*   `index.lowerBound` 指的是上一个 `range` 函数返回的子字符串的第一个字符的索引。

以下是该方法的完整定义：

```
func lastIndex(of string: String) -> Int? {
guard let index = range(of: string, options: .backwards) else { return nil }
return self.distance(from: self.startIndex, to: index.lowerBound)
}
```



#### 方法：检查给定字符串是否为有效电子邮件

```
func isValidEmail() -> Bool {
}
```

该函数命名为 `isValidEmail`，不接受任何参数并返回一个布尔值。（如果字符串是有效电子邮件，则返回 `true`，否则返回 `false`。）请注意，`isValidEmail` 将作为 `String` 函数的扩展方法在整个项目中可用。计算属性类似于方法，但行为像属性，当我们在任何字符串字面量上访问它们时会自动调用。

```
let regex = try! NSRegularExpression(pattern: "^[a-zA-Z0-9.!#$%&'*+/=?^_`{|}~-]+@a-zA-Z0-9?(?:\\.a-zA-Z0-9?)*$", options: .caseInsensitive)
```

这行代码定义了一个名为 `regex` 的常量，并尝试使用 `NSRegularExpression` 创建一个正则表达式对象。所定义的模式是验证的核心，用于检查电子邮件地址格式。

符号 `^` 定义了字符串的开始，并允许出现一次或多次字母（区分大小写）、数字和少数特殊字符。

符号 `@` 将用户名与域名分隔开，并允许出现一次或多次字母（区分大小写）和数字。

符号 `?` 是非捕获组，允许可选的子域名。它匹配零到 61 次出现的字母、数字和连字符，后跟一个字母或数字。这部分可以通过 `?` 量词重复零次或多次。

下一个非捕获组允许零次或多次出现的域名分隔符 `.`，后跟一个子域名。

`$` 匹配字符串的结尾。

`.caseInsensitive`：此选项使正则表达式不区分大小写，意味着它不会区分大写和小写字母。

```
return regex.firstMatch(in: self, options: [], range: NSRange(location: 0, length: count)) != nil
```

这行代码返回电子邮件地址是否有效。

`regex.firstMatch(in: self, options: [], range: NSRange(location: 0, length: count))`：这部分使用（上面定义的）`regex` 对象在当前字符串对象（`self`）中搜索电子邮件地址模式的首次匹配。它指定要搜索整个字符串（`NSRange(location: 0, length: count)`），并使用空选项数组（`[]`）。

`!= nil`：这会检查 `firstMatch` 函数是否返回了一个值。非空值表示在字符串中找到了该模式，这表明根据定义的正则表达式，电子邮件格式看似有效。

以下是该方法的完整定义：

```
func isValidEmail() -> Bool {
    let regex = try! NSRegularExpression(pattern: "^[a-zA-Z0-9.!#$%&'*+/=?^_`{|}~-]+@a-zA-Z0-9?(?:\\.a-zA-Z0-9?)*$", options: .caseInsensitive)
    return regex.firstMatch(in: self, options: [], range: NSRange(location: 0, length: count)) != nil
}
```

#### 方法：检查给定字符串是否为有效 URL

```
var isValidURL: Bool {
}
```

该函数命名为 `isValidURL`，不接受任何参数并返回一个布尔值。（如果字符串是有效 URL，则返回 `true`，否则返回 `false`。）请注意，`isValidURL` 将作为 `String` 函数的扩展方法在整个项目中可用。计算属性类似于方法，但行为像属性，当我们在任何字符串字面量上访问它们时会自动调用。

```
let detector = try! NSDataDetector(types: NSTextCheckingResult.CheckingType.link.rawValue)
```

这行代码创建了一个名为 `detector` 的 `NSDataDetector` 类型常量。该检测器对象用于识别文本字符串中的特定数据类型。在我们的代码中，它被配置为检测链接（`types: NSTextCheckingResult.CheckingType.link.rawValue`）。`try!` 展开一个可选值，并强制创建检测器，假设初始化过程中不会发生错误。

```
if let match = detector.firstMatch(in: self, options: [], range: NSRange(location: 0, length: self.utf16.count)) {
    return match.range.length == self.utf16.count
} else {
    return false
}
```

这个条件块检查字符串中是否找到 URL。

`detector.firstMatch` 在当前字符串对象中搜索链接的首次出现。示例中的 `NSRange` 指定必须搜索整个范围（从零开始直到最后一个字符，`self` 字符串的计数给出字符串长度）。

通过比较整个字符串的长度，代码确保整个字符串代表一个有效的 URL，而不仅仅是其中的一部分。

完整代码如下所示：

```
extension String {
    func lastIndex(of string: String) -> Int? {
        guard let index = range(of: string, options: .backwards) else { return nil }
        return self.distance(from: self.startIndex, to: index.lowerBound)
    }
    
    func isValidEmail() -> Bool {
        let regex = try! NSRegularExpression(pattern: "^[a-zA-Z0-9.!#$%&'*+/=?^_`{|}~-]+@a-zA-Z0-9?(?:\\.a-zA-Z0-9?)*$", options: .caseInsensitive)
        return regex.firstMatch(in: self, options: [], range: NSRange(location: 0, length: count)) != nil
    }
    
    var isValidURL: Bool {
        let detector = try! NSDataDetector(types: NSTextCheckingResult.CheckingType.link.rawValue)
        if let match = detector.firstMatch(in: self, options: [], range: NSRange(location: 0, length: self.utf16.count)) {
            return match.range.length == self.utf16.count
        } else {
            return false
        }
    }
}
```

