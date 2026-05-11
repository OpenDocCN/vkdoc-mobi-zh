# 2. 处理 JSON 和其他流行格式

在 Swift 中，数据远不止基本类型、数组和字典。此外，移动应用程序很少与外界隔绝。它会发出 API 请求并处理响应；会与云服务交换数据并同 SDK 协作。下面，我们来了解如何处理数据交换，并在不同单位之间安全地转换数字。

## 设备如何交换数据

如今，很难想象一台计算设备没有外部数据接口。即使是像咖啡机和加热器这样简单的电子设备，也开始配备 Wi-Fi 和蓝牙接口，以便你从寒冷的街头回家时为你加热房间并煮好咖啡。

对我们移动开发者来说，幸运的是，我们不需要深入研究 Wi-Fi 或蓝牙协议。移动开发者编写几乎任何应用都需要一种通用的数据交换标准：HTTP。

HTTP（超文本传输协议）和 HTTPS（其安全版本）是数据交换的应用层协议。它们采用客户端-服务器模型：客户端发送请求，服务器返回响应。格式、编码和其他属性由头部定义。

HTTP 协议非常流行，以至于它常被用于同一设备内的通信。一个应用充当服务器，另一个则作为客户端。要访问服务器，你需要知道 URL（统一资源定位符）。它包含几个部分：协议方案、主机、端口、路径、参数和片段。

桌面服务器应用通常会为自己预留一个端口。这样，多个服务器就可以在同一台计算机上共存。移动应用通常会预留一个与其唯一应用 ID 相匹配的协议方案。这样，一个应用就可以调用另一个应用，例如用于身份验证。还记得用 Facebook 或 Google 登录吗？它的工作原理正是如此。

当客户端和服务器应用建立连接后，它们需要交换数据。数据格式在头部中定义，它可以采用预定义的 MIME（多用途互联网邮件扩展）类型之一。如果你向 API 发送请求，可以在相关文档中找到可接受的数据格式。

对于 API（应用程序编程接口）而言，最流行的数据格式是 JSON。它的 MIME 类型是 `application/json`。另一种可能的格式是 XML——`text/xml`。你可能会问，为什么 JSON 属于应用程序组，而 XML 属于文本组？JSON 是受数据结构限制的 JavaScript，不含函数。你可以将 JSON 文件复制粘贴或导入到 JavaScript 应用中，它无需任何修改就能像 JavaScript 一样被读取和解析。XML 不是源代码；它始终是数据。如果 XML 不可人工阅读，只能由解析器处理，那么它可以采用 `application/xml` 的 MIME 类型。

要以 JSON 格式向服务器发送数据，你应该添加一个头部：

```
Content-Type: application/json
```

服务器在响应 JSON 时也会做同样的事情。如果你在移动应用中不包含这个头部会怎样？或者如果服务器在响应中不包含它呢？根据服务器软件的不同，它可以自动接受数据并检测 JSON，或者拒绝将请求视为无效。当服务器不包含头部时，你的移动应用可以按自己的方式处理。一般来说，开发者不希望添加不必要的错误消息，从而使应用变得更脆弱，因此如果你知道服务器会以 JSON 格式响应，无论头部如何，都将其作为 JSON 解析。


## 什么是序列化和反序列化

*序列化*是指将自定义对象或数据结构转换为字节数组的过程。在 Swift 中，这表现为一个 `Data` 对象。序列化为 `String` 是另一种选择——毕竟，`String` 本身也是一串字节。可以说，JSON 格式的 `String` 就是 `Dictionary` 或 `Array` 的序列化版本。

*反序列化*是逆向过程，它将字节数组转换回 Swift 对象。

## 使用 JSON

如前所述，JSON 是与 API 进行数据交换最流行的格式。大多数 SDK，例如 Facebook SDK、Firebase SDK 等，底层都使用了 JSON。Firestore 数据库和 MongoDB 的数据也是 JSON 格式（当然还包含索引等数据库特性，但本质上仍是 JSON）。这使得导入、导出和数据交换变得非常容易。

### 将 JSON 解析为 Dictionary 或 Array

在 Swift 应用中使用 `JSONSerialization` 解析 JSON 时，你会得到 `Any` 类型的结果。原因在于数据类型的不确定性。JSON 有一个根元素，它可以是对象 `{ ... }` 或数组 `[ ... ]`。如果根元素是对象，则 `Any` 可以被转换为 `Dictionary`，具体来说是 `[String: Any]`。否则，它就是 `Array`——即 `[Any]`。

**注意**：更常见的 JSON 处理方式是使用 `JSONEncoder`/`JSONDecoder`。本章稍后会对此进行介绍。

大多数网络库允许你从服务器获取 `Data`、`String` 类型的数据，有时也能获取更复杂结构的数据。如果是 JSON，即使它更像文本，你仍然需要以 `Data` 形式接收。我们以 iOS 开发中最流行的网络库 Alamofire 为例。

下一步是将 `Data` 转换为 `Any`，这里的 `Any` 要么是 `Dictionary`，要么是 `Array`。我们稍后会检查数据类型，但首先需要进行解析。Swift 提供了标准的 JSON 解析器，我们将使用它。如果解析出错，会抛出异常，因此我们需要用 `do ... try ... catch` 块包裹起来。当文档不是有效的 JSON 文档或包含无法解析的数据时，就会抛出异常。

最后一步是检查数据类型。JSON 文档不能被解析为 `Int` 或 `String`，它始终是 `Dictionary` 或 `Array`。我们需要检查文档是否属于这两种类型之一，如果不是，则返回 `nil`。最后这种情况理论上不应发生，但我们无法保证所有 Swift 库都没有 bug。此解析的完整版本见配方 2-1。

```
public extension Data {
    var parsedFromJSON: Any? {
        guard let json = try? JSONSerialization.jsonObject(with: self, options: .allowFragments) else {
            return nil
        }
        if let jsonDictionary = json as? [String: Any] {
            return jsonDictionary
        } else if let jsonArray = json as? [Any] {
            return jsonArray
        } else {
            return nil
        }
    }
    var parsedAsJSONDictionary: [String: Any]? {
        try? JSONSerialization.jsonObject(with: self, options: .allowFragments) as? [String: Any]
    }
    var parsedAsJSONArray: [Any]? {
        try? JSONSerialization.jsonObject(with: self, options: .allowFragments) as? [Any]
    }
}

public extension String {
    var parsedFromJSON: Any? {
        asUtf8Data.parsedFromJSON
    }
    var parsedAsJSONDictionary: [String: Any]? {
        asUtf8Data.parsedAsJSONDictionary
    }
    var parsedAsJSONArray: [Any]? {
        asUtf8Data.parsedAsJSONArray
    }
}
```

**配方 2-1**  
解析 JSON

### 从 Dictionary 或 Array 生成 JSON

反向过程更简单，因为无需检查数据类型或进行其他准备。我们将 `Dictionary` 或 `Array` 转换为包含 JSON 的 `Data`，然后将其传递给 API（或使用其他方式）。具体方法见配方 2-2。

有一点需要注意：某些数据类型无法转换为 JSON。例如，如果你的 `Dictionary` 中包含 `UIImage` 或 `UIView`，则无法序列化为 JSON 元素。我们将在下一节深入探讨序列化。我们需要将这些类型转换为基本类型、`Dictionary`，或者将其从原始结构中排除。

```
public extension Dictionary where Key: ExpressibleByStringLiteral {
    var asJSONData: Data? {
        try? JSONSerialization.data(withJSONObject: self, options: .fragmentsAllowed)
    }
    var asJSONString: String? {
        asJSONData?.asUtf8String
    }
}

public extension Array {
    var asJSONData: Data? {
        try? JSONSerialization.data(withJSONObject: self, options: .fragmentsAllowed)
    }
    var asJSONString: String? {
        asJSONData?.asUtf8String
    }
}
```

**配方 2-2**  
生成 JSON

## 使用 XML

虽然 JSON 在数据交换中很流行，但 XML（可扩展标记语言）更常用于存储应用程序内部数据。Storyboard、nib、plist 及其他结构都基于 XML。Android 和 UWP（通用 Windows 平台）的布局文件也使用 XML。

某些 API 提供 XML 格式的响应，这可能是唯一选项，也可能是备选方案之一。

在讨论如何解析 XML 之前，重要的是要理解，与 JSON 不同，XML 不能直接转换为 Swift 的 `Dictionary` 或 `Array`。JSON 是一种*对象表示法*，而 `Dictionary` 是表示对象（至少是其数据）的一种方式。在 JavaScript 中，对象本身就是字典。

XML 是一种*标记语言*，包含元素和属性。这些元素和属性在*模式*中声明。它还包含命名空间等 Swift 字典中不存在的结构。但如果只是解析 API 的响应或准备 API 调用的参数，我们不必深入这些内容。我们需要做的就是提取所需的数据。

### 解析 XML

Swift 提供了内置类 `XMLParser`。它可以提取 XML 中的数据，但其使用方式堪称噩梦。要解析 XML 字符串，你需要声明一个委托，每个 XML 元素（标签）都会触发一个回调：

```
class ParserDelegate: NSObject, XMLParserDelegate {
    func parser(_ parser: XMLParser, didStartElement elementName: String, namespaceURI: String?, qualifiedName qName: String?, attributes attributeDict: [String : String] = [:]) {
        print("找到元素 \(elementName)，属性为 \(attributeDict)")
    }
}
```

如果需要获取某些数据，这种做法可行，但用它来获取 `Dictionary` 则过于复杂。

有一个好用的辅助库：SWXMLHash。它维护良好，兼容多个 Swift 版本，并可通过 CocoaPods、Carthage 和 Swift Package Manager 获取。如果使用 CocoaPods，请将以下内容添加到 Podfile 中：

```
pod 'SWXMLHash'
```

不要忘记运行 `pod install` 或 `pod update`。

现在，只需一行代码即可完成解析：

```
let xmlDict = SWXMLHash.parse(xml)
```

然后，你可以从 `xmlDict` 中提取所需的数据。配方 2-3 展示了完整代码。

```
import SWXMLHash

let xml: String = "..."
let xmlDict = SWXMLHash.parse(xml)
```

**配方 2-3**  
解析 XML



### 生成 XML

对于 iOS 应用来说，生成 XML 是一项非常罕见的任务，我们为了完整性才讨论它。你不太可能需要生成 XML 数据并保存或发送到某个地方。另一方面，不太可能并不意味着不可能。你可以在配方 2-4 中找到完整代码。但首先，让我们看看它是如何工作的。

由于没有标准的解决方案，也没有与最新 Swift 版本兼容的库，我们将编写一个简单的 XML 生成器。XML 是一种递归结构——一个标签可以包含其他标签或多个标签，因此这个生成器也将是递归的。

我们将把所有元素分为三组：

- 字典（Dictionaries）
- 数组（Arrays）
- 基础元素（Basic elements）

XML 中的`Array`由多个同名但内容不同的标签表示。`Dictionary`是一个内部包含标签的标签。而基础元素只是一个内部包含文本字符串（表示任何类型）的标签。

基础元素将使用我们之前讨论过的`String`插值生成。对于其他两种类型的元素，我们将使用两个函数：

```
func convertArrayToXML(array: [Any], startElement: String) -> String {
var xml = ""
for value in array {
if let value = value as? [String: Any] {
xml += convertDictionaryToXML(dictionary: value, startElement: startElement, isFirstElement: false)
}
else if let value = value as? [Any] {
xml += convertArrayToXML(array: value, startElement: startElement)
}
else {
xml += "\(value)\n"
}
}
return xml
}
func convertDictionaryToXML(dictionary: [String: Any], startElement: String, isFirstElement: Bool) -> String {
var xml = ""
let arr = dictionary.keys
if isFirstElement {
xml += "\n"
}
xml += "\n"
for nodeName in arr {
guard let nodeValue = dictionary[nodeName] else { continue }
if let nodeValue = nodeValue as? [Any] {
if !nodeValue.isEmpty {
xml += convertArrayToXML(array: nodeValue, startElement: nodeName)
}
}
else if let nodeValue = nodeValue as? [String: Any] {
xml += convertDictionaryToXML(dictionary: nodeValue, startElement: nodeName, isFirstElement: false)
}
else {
xml += "\(nodeValue)\n"
}
}
xml += "\n"
return xml.replacingOccurrences(of: "&", with: "&")
}
配方 2-4
生成 XML
```

**用法**

```
let dict: [String: Any] = ...
let xml = convertDictionaryToXML(dictionary: dict, startElement: "root", isFirstElement: true)
```

## 处理 YAML

YAML（Yet Another Markup Language）是另一种流行的数据存储和交换格式。其优点是易于阅读，对人类友好。缺点在于 YAML 使用缩进作为分隔符（意味着使用前导空格来分隔数据层级），这与 Python 或 GDScript 类似。你可以用简单的文本编辑器编辑 YAML 文件，但很容易出错。

YAML 最流行的用途之一是配置文件。Flutter 应用使用 YAML 作为主配置文件，Google Cloud 也使用 YAML 配置其服务。

对我们来说幸运的是，GitHub 上有许多用于解析和/或生成 YAML 的库。其中一个是`Yams`。

如果你使用 CocoaPods，将这一行添加到你的`Podfile`中：

```
pod 'Yams'
```

然后，你可以像配方 2-5 所示那样生成和解析 YAML。

```
import Yaml
// 生成 YAML
let strYAML: String? = try? Yams.dump(object: dictionary)
// 解析 YAML
let loadedDictionary = try? Yams.load(yaml: mapYAML) as? [String: Any]
配方 2-5
解析和生成 YAML
```

## URL 编码字符串

另一种编码数据的方式是 URL 编码字符串。这对于生成带有 GET 参数的 URL 以及使用`application/x-www-form-urlencoded`类型的 POST 数据非常有用。

URL 编码字符串是一个由 & 符号分隔的键值对组成的文本字符串：`key1=value1&key2=value2&...`。如果使用 GET 方法（作为 URL 的一部分）发送，它通过问号与主 URL 分隔：`https://some_website.com?key1=value1&key2=value2&...`。作为 POST 数据，它必须不带任何额外符号发送。

如果你向服务器发送数据，你可能需要提供一组键，这些键必须仅包含拉丁字母、数字和可能的千分号。涉及值的情况则更复杂——它们可以包含空格、等号或其他*禁止*字符。因此，我们应该对它们进行编码。Swift 有一个标准的`String`方法`addingPercentEncoding(withAllowedCharacters:)`。配方 2-6 展示了完整的扩展。

```
public extension Dictionary where Key: ExpressibleByStringLiteral {
var urlEncodedString: String {
var result: [String] = []
for key in keys {
let keyEncoded = "\(key)".addingPercentEncoding(withAllowedCharacters: .urlPathAllowed)!
let valueEncoded = "\(self[key]!)".addingPercentEncoding(withAllowedCharacters: .urlPathAllowed)!
result.append("\(keyEncoded)=\(valueEncoded)")
}
return result.joined(separator: "&")
}
}
配方 2-6
URL 编码参数字典
```

用法非常直接：

```
let urlArgsDict: [String: Any] = [
"firstName": "Luis Alberto",
"lastName": "Lopez Garcia",
"age": 15
]
let urlArgs = urlArgsDict.urlEncodedString
print(urlArgs)
```

输出将如下所示：

```
firstName=Luis%20Alberto&lastName=Lopez%20Garcia&age=15
```

它完全适合发送给 API：

```
https://my_api.com/register_user?firstName=Luis%20Alberto&lastName=Lopez%20Garcia&age=15
```

注意：通过 GET 请求注册用户是一种不好的做法。不过，这个例子演示了如何对任何数据进行编码，以便进一步发送给 API。

## 序列化和反序列化

在前面的章节中，我们讨论了如何将 JSON 解析为`Dictionary`或`Array`，如何从中提取类型化数据，以及如何将 API 的 JSON 转换为自定义类或结构体的实例。在这里，我们将走一条捷径，直接将 JSON 转换为对象。

注意：如果你曾经为 Android 开发过应用，你会知道要将参数传递给另一个`Activity`，你需要将它们添加到`Intent`中。这是一个序列化的例子。你也可以传递自定义类的对象，但它必须实现`Parcelable`接口。该接口具有用于序列化和反序列化类数据的函数。



### 序列化对象

要使数据结构可序列化，你需要实现一个 `Encodable` 协议。该协议只有一个方法——`encode(to: Encoder)`。你无需自行实现，因为它有一个默认实现。但这个默认实现仅在类或结构体中的所有存储变量均为 `Encodable` 类型时才生效。所有基本类型（如 `Int`、`String`、`Double` 等）都是 `Encodable` 的。而所有自定义类型则不然，除非它们实现了 `Encodable` 协议。例如：

```
struct User: Encodable {
var name: String
var surname: String
var age: Int
}
```

我们为什么需要它？为什么要让一个结构体或类可序列化（或者如 Swift 所称的 `Encodable`）？当你需要将其转换为 JSON 或保存到文件时，魔力便开始了。以下示例展示了如何将 `Encodable` 转换为 `Data` 和 `Dictionary`：

```
struct User: Encodable {
var name: String
var surname: String
var age: Int
var asData: Data? {
return try? JSONEncoder().encode(self)
}
var asDictionary: [String: Any]? {
guard let data = self.asData else { return nil }
return (try? JSONSerialization.jsonObject(with: data, options: .allowFragments)).flatMap { $0 as? [String: Any] }
}
}
```

如上面的清单所示，只需几行代码，一个 `Encodable` 对象就可以被转换为 JSON 编码的 `Data` 或 Swift 的 `Dictionary`。你可以向 `Encodable` 结构体添加更多变量，它们将被 `Encodable` 协议自动序列化，或者更准确地说，由其默认的 `encode(to: Encodable)` 方法实现自动处理。

```
let encUser = User(name: "John", surname: "Doe", age: 30)
let userDict = encUser.asDictionary
print(userDict ?? {})
```

*输出*

```
["surname": Doe, "name": John, "age": 30]
```

如果出于某种原因序列化失败，将返回 `nil`。

正如我们在开头所讨论的，优秀的代码必须是通用的。它应该易于复制到其他项目中，而无需修改或只需极少的修改。来自配方 2-7 的 Swift 扩展即提供了这种帮助。

```
public extension Encodable {
var asJSONData: Data? {
try? JSONEncoder().encode(self)
}
var asJSONString: String? {
guard let data = self.asData else { return nil }
return String(data: data, encoding: .utf8)
}
var asDictionary: [String: Any]? {
guard let data = self.asData else { return nil }
return (try? JSONSerialization.jsonObject(with: data, options: .allowFragments)).flatMap { $0 as? [String: Any] }
}
}
配方 2-7
将 Encodable 转换为 JSON
```

现在，任何遵循 `Encodable` 协议的类型都将拥有 `asJSONData`、`asJSONDictionary` 和 `asString` 方法。以下代码片段展示了一个可编码的结构体（该结构体拥有 `asJSONData`、`asJSONString` 和 `asDictionary` 方法）：

```
struct User: Encodable {
var name: String
var surname: String
var age: Int
}
```

当你处理接受 JSON 格式参数的 API 时，这些方法极其有用。此外，`asData` 和 `asString` 方法也可用于将文件保存到闪存、硬盘或固态硬盘（如果你编写的代码兼容 macOS）。

### 反序列化对象

与 `Encodable` 类似，Swift 也提供了一个 `Decodable` 协议。该协议声明了一个构造器 `init(from: Decoder)`，而不是方法。它同样拥有默认实现。

反序列化对象是一个稍微复杂的任务，原因有四：

- 提供的数据结构可能缺少 `Decodable` 结构中声明的某些字段。
- 提供的数据结构可能包含额外的字段。
- 提供的数据中的字段名称与声明的结构体不匹配。
- 数据类型必须完全匹配。

在继续之前，我们还需要一个接口——`Codable`。它不是一个独立的接口，而是 `Encodable` 和 `Decodable` 的组合，用 Swift 的术语来说，它是一个类型别名。

```
typealias Codable = Decodable & Encodable
```

通常，当我们需要序列化一个类时，我们也需要对其进行反序列化。让我们将 `User` 结构体改为遵循 `Codable` 而非 `Encodable`。

```
struct User: Codable {
var name: String
var surname: String
var age: Int
}
```

我们不需要为 `Codable` 编写任何扩展；它只是一个别名。相反，我们扩展了 `Decodable` 协议，如配方 2-8 所示。

```
public extension Decodable {
init?(data: Data) {
do {
self = try JSONDecoder().decode(Self.self, from: data)
} catch {
return nil
}
}
init?(dict: [String: Any]) {
guard let jsonData = try? JSONSerialization.data(withJSONObject: dict, options: .fragmentsAllowed) else { return nil }
do {
self = try JSONDecoder().decode(Self.self, from: jsonData)
} catch {
return nil
}
}
init?(jsonString: String) {
guard let jsonData = jsonString.data(using: .utf8) else { return nil }
do {
self = try JSONDecoder().decode(Self.self, from: jsonData)
} catch {
return nil
}
}
}
配方 2-8
将 JSON 转换为 Decodable
```

这段代码可以从 JSON 编码的 `String`、`Data` 或 `Dictionary` 对象中返回一个有效的对象，但前提是所提供的结构体包含 name、surname 和 age 字段。而且这些字段的拼写必须与 `User` 声明中的完全一致。以下是一个用户字典的示例：

```
let decDict: [String: Any] = [
"name": "John",
"surname": "Doe",
"age": 30
]
let decUser = User(dict: decDict)
```

如果缺少其中一个字段怎么办？例如，我们不知道用户的年龄。在这种情况下，`decUser` 将为 `nil`。这个问题可以通过将字段设为可选项来解决。如果某些字段是必需的，则它们应设为非可选项；例如，用户 ID 几乎不可能是可选的。其他字段可以缺失，这没问题。

我们之前讨论的另一个问题是类型不匹配。请看以下示例：

```
struct User: Codable {
var name: String
var surname: String
var age: Int?
}
let decDict: [String: Any] = [
"name": "John",
"surname": "Doe",
"age": "30"
]
let decUser = User(dict: decDict)
```

`decUser` 对象的 age 将为 `nil`。解决这个问题有两种方案。第一种方案是处理好服务端输出。如果你（或你的团队）自己编写 API，你可以创建严格类型化的数据。当处理第三方 API 时，这就不太可行了。于是便有了第二种方案。你可以手动定义一个 `init(from: Decoder)` 构造器。你需要为每个类和结构体单独执行此操作——这里没有通用的解决方案。你可以在配方 2-9 中看到此方案的一个示例。

```
public struct User: Codable {
var name: String
var surname: String
var age: Int?
init(name: String, surname: String, age: Int?) {
self.name = name
self.surname = surname
self.age = age
}
init(from decoder: Decoder) throws {
let container = try decoder.container(keyedBy: CodingKeys.self)
name = try container.decode(String.self, forKey: .name)
surname = try container.decode(String.self, forKey: .surname)
do {
age = try container.decode(Int.self, forKey: .age)
} catch DecodingError.typeMismatch {
age = Int(try container.decode(String.self, forKey: .age))
}
}
}
配方 2-9
手动解码
```

请注意，如果你为结构体添加了自定义构造器，那么如果需要，你还必须提供一个默认构造器。

即使存在这种不便，我们仍然可以在 `Decodable` 扩展中定义构造器，这比使用 `Dictionary` 扩展要方便得多。



## 编码键

JSON 结构包含键值对，并且不能保证这些键会与类或结构体中的变量名匹配。对于 `Encodable` 和 `Decodable` 来说，有一个通用的解决方案——`CodingKeys` 枚举（配方 2-10）：

```
enum CodingKeys: String, CodingKey {
case name = "firstName"
case surname = "lastName"
case age
}
Recipe 2-10
使用编码键
```

*示例*

```
struct User: Codable {
var name: String
var surname: String
var age: Int?
enum CodingKeys: String, CodingKey {
case name = "firstName"
case surname = "lastName"
case age
}
}
let decDict: [String: Any] = [
"firstName": "John",
"lastName": "Doe",
"age": 30
]
let decUser = User(dict: decDict)
```

如果字段根据 API 的不同而有不同的名称，则它们无法用可序列化的 Swift 类型表示；在其他复杂情况下，使用 `Codable` 可能不是最佳选择。从 `Dictionary` 中逐字段解析数据则更为通用。

## 使用 `@propertyWrapper`

如果我们需要严格类型的灵活性又要其便利性，该怎么办？例如，当你需要一个灵活的结构，但又希望字段受到约束时。`Dictionary` 类提供了完全的灵活性，就像 JSON 本身一样。而序列化则需要严格的结构声明。还有另一种解决方案——`@propertyWrapper`。

这是一个我们可以赋予结构体的属性，用于将其声明为其自身属性的包装器。在我们的案例中，属性是以 `Dictionary` 形式呈现的服务器数据。

我们将围绕一个表示电子商店商品特性的结构体来制作包装器。不同的商品可能有不同的特性，而每种特性都有一定的取值范围。这可以是 `String` 长度的限制、最小长度或任何其他特性。这种方法有助于验证数据并避免显示错误输入的值。不幸的是，这并不能杜绝所有错误，但可以过滤掉最不切实际的那部分。

这并非 `@propertyWrapper` 的唯一用途，它可以用于所有具有合理数量规则的动态结构。同时，它很少成为首选工具。只要可能，请优先使用解析为原生 Swift 结构体的方式。

我们编写两个结构体：

- `Item` – 一个保存所有数据的结构体
- `ItemProjection` – 一个提供对带有所需约束的包装数据的访问的结构体

`Item` 应被标记为 `@propertyWrapper`，并包含两个字段：

- 类型为 `Dictionary` 的 `wrappedValue`
- 类型为 `ItemProjection` 的 `projectedValue`

要访问字段，我们通过变量名访问 `wrappedValue`，并通过以美元符号开头的相同名称访问 `projectedValue`。配方 2-11 展示了使用属性包装器的完整示例。

```
public struct ItemProjection {
var wrapper: Item
public var title: String? {
if let title = wrapper.wrappedValue.getString("title") {
if title.count > ItemProjection.titleLengthLimit {
let endIndex = title.index(title.startIndex, offsetBy: 100)
return String(title[..<endIndex]) + "..."
} else {
return title
}
} else {
return nil
}
}
public var length: Double? {
if let length = wrapper.wrappedValue.getDouble("length"),
length > 0 {
return length
} else {
return nil
}
}
public var lengthInches: Double? {
length?.cmAsInch
}
public var volts: Int {
wrapper.wrappedValue.getInt("volts") ?? 220
}
public var watts: Int? {
if let watts = wrapper.wrappedValue.getInt("watts"),
watts > 0,
watts < 10000 {
return watts
} else {
return nil
}
}
static let titleLengthLimit = 100
}
@propertyWrapper
public struct Item {
public var wrappedValue: [String: Any]
public var projectedValue: ItemProjection {
ItemProjection(wrapper: self)
}
public init() {
wrappedValue = [:]
}
public init(wrappedValue: [String: Any]) {
self.wrappedValue = wrappedValue
}
}
Recipe 2-11
使用 @propertyWrapper
```

上面的示例提供了对 `title`、`length`、`volts` 和 `watts` 的快速访问。在返回值之前，它会对其进行处理。例如，如果 `title` 太长，它只保留前 100 个字符并添加三个点。电压在同一区域内通常是相同的，要么是 120 伏，要么是 220 伏。如果我们知道商店的位置，就不需要为每个商品都添加电压；我们可以跳过它，使用默认值。

其他值会进行有效性检查。如果它们为负数、零或太大，则返回 `nil`，以避免向最终用户显示完全没有意义的数据。作为一种选择，应用程序可以向分析框架发送消息。

我们还提供了以英寸为单位的长度访问（假设数据库中存储的是厘米）。这是通过本章稍后展示的扩展 `cmAsInch` 完成的。

在底层，它仍然是同一个 `Dictionary`，这意味着我们可以添加任何我们需要的值。该结构不是固定的。

**注意**

此示例使用了本书其他章节中描述的扩展。

作为使用示例，我们创建一个只销售加热器的小型电子商店（`Company` 结构体）：

```
struct ElectronicsStore {
@Item var heater = [
"title": "Electric heater with too long title, that doesn't fit display or price tag. It should be shortened when requested.",
"length": 50,
"watts": 15000
]
}
```

我们使用字典来初始化它们，这通常是我们从 API 获得的数据格式。然后，我们可以通过访问带有 `@propertyWrapper` 的字段来轻松访问投影数据和 `Dictionary` 本身：

```
var store = ElectronicsStore()
print(store.$heater.title ?? "无标题")
print(store.$heater.length ?? "无长度")
print(store.$heater.lengthInches ?? "无英尺长度")
print(store.$heater.volts)
print(store.$heater.watts ?? "无功率")
```

此示例产生以下输出：

```
Electric heater with too long title, that doesn't fit display or price tag. It should be shortened w...
50.0
19.68503937007874

无功率
```

## 单位转换

在某些情况下，你需要在同一数据类型内进行转换，例如，如果你得到的是以厘米为单位的测量值，但需要的是以英寸为单位的数据。或者，你从 API 获得的是英里，但你理解的是以公里为单位的距离。

由于转换乘数不是整数，即使值没有小数部分，也最好使用 `Double` 类型。

### 距离和长度单位

假设我们需要计算伦敦和柏林之间的总旅行距离。航空公司告诉我们，航班距离是 580 英里。前往机场的路程是 24300 码。而要到达我们在柏林的酒店，我们需要行驶 10400 米。一切都很好，但你想要以公里为单位的结果。

计算机就是为了这种计算而创造的。在我们编写完所有扩展之后，最终的代码将如下所示：

```
let distanceInKm = (24300.0.yardsAsM + 580.0.milesAsM + 10400.0).mAsKm
```

自 iOS 10（包括现在所有可用的 iOS 版本）以来，你可以使用 `Measurement` 结构体。在与转换相关的所有扩展中，我们都将使用它，并在注释中展示其底层实现方式。使用 `Measurement` 是首选方法，因为它可以提供更好的精度并减少潜在错误。除非你使用的是 iOS 不支持的特殊单位，否则请使用它。

直接转换（请参阅以下代码中的注释）的另一个用例是性能。如果你需要进行大量的转换，`Measurement` 类可能比乘法和除法慢。

在配方 2-12 中，我们为 `Double` 类型编写扩展。我们有三个常用的英制单位和三个公制单位：

- 英寸（Inch）
- 英尺（Foot, ft）
- 英里（Mile）
- 厘米（Centimeter, cm）
- 米（Meter, m）
- 千米（Kilometer, km）



```swift
public extension Double {
    var inchAsFt: Double {
        Measurement(value: self, unit: UnitLength.inches)
            .converted(to: UnitLength.feet)
            .value
        // self / 12.0
    }
    var inchAsMile: Double {
        Measurement(value: self, unit: UnitLength.inches)
            .converted(to: UnitLength.miles)
            .value
        // self / 63360.0
    }
    var inchAsCm: Double {
        Measurement(value: self, unit: UnitLength.inches)
            .converted(to: UnitLength.centimeters)
            .value
        // self * 2.54
    }
    var inchAsM: Double {
        Measurement(value: self, unit: UnitLength.inches)
            .converted(to: UnitLength.meters)
            .value
        // self * 0.0254
    }
    var inchAsKm: Double {
        Measurement(value: self, unit: UnitLength.inches)
            .converted(to: UnitLength.kilometers)
            .value
        // self * 0.0000254
    }
    var ftAsInch: Double {
        Measurement(value: self, unit: UnitLength.feet)
            .converted(to: UnitLength.inches)
            .value
        // self * 12.0
    }
    var ftAsMile: Double {
        Measurement(value: self, unit: UnitLength.feet)
            .converted(to: UnitLength.miles)
            .value
        // self / 5280.0
    }
    var ftAsCm: Double {
        Measurement(value: self, unit: UnitLength.feet)
            .converted(to: UnitLength.centimeters)
            .value
        // self * 30.48
    }
    var ftAsM: Double {
        Measurement(value: self, unit: UnitLength.feet)
            .converted(to: UnitLength.meters)
            .value
        // self * 0.3048
    }
    var ftAsKm: Double {
        Measurement(value: self, unit: UnitLength.feet)
            .converted(to: UnitLength.kilometers)
            .value
        // self * 0.0003048
    }
    var mileAsInch: Double {
        Measurement(value: self, unit: UnitLength.miles)
            .converted(to: UnitLength.inches)
            .value
        // self * 63360.0
    }
    var mileAsFt: Double {
        Measurement(value: self, unit: UnitLength.miles)
            .converted(to: UnitLength.feet)
            .value
        // self * 5280.0
    }
    var mileAsCm: Double {
        Measurement(value: self, unit: UnitLength.miles)
            .converted(to: UnitLength.centimeters)
            .value
        // self * 160934.4
    }
    var mileAsM: Double {
        Measurement(value: self, unit: UnitLength.miles)
            .converted(to: UnitLength.meters)
            .value
        // self * 1609.344
    }
    var mileAsKm: Double {
        Measurement(value: self, unit: UnitLength.miles)
            .converted(to: UnitLength.kilometers)
            .value
        // self * 1.609344
    }
    var cmAsInch: Double {
        Measurement(value: self, unit: UnitLength.centimeters)
            .converted(to: UnitLength.inches)
            .value
        // self / 2.54
    }
    var cmAsFt: Double {
        Measurement(value: self, unit: UnitLength.centimeters)
            .converted(to: UnitLength.feet)
            .value
        // self / 30.48
    }
    var cmAsMile: Double {
        Measurement(value: self, unit: UnitLength.centimeters)
            .converted(to: UnitLength.miles)
            .value
        // self / 160934.4
    }
    var cmAsM: Double {
        Measurement(value: self, unit: UnitLength.centimeters)
            .converted(to: UnitLength.meters)
            .value
        // self * 0.01
    }
    var cmAsKm: Double {
        Measurement(value: self, unit: UnitLength.centimeters)
            .converted(to: UnitLength.kilometers)
            .value
        // self * 0.00001
    }
    var mAsInch: Double {
        Measurement(value: self, unit: UnitLength.meters)
            .converted(to: UnitLength.inches)
            .value
        // self * 39.3701
    }
    var mAsFt: Double {
        Measurement(value: self, unit: UnitLength.meters)
            .converted(to: UnitLength.feet)
            .value
        // self * 3.28084
    }
    var mAsMile: Double {
        Measurement(value: self, unit: UnitLength.meters)
            .converted(to: UnitLength.miles)
            .value
        // self / 1609.344
    }
    var mAsCm: Double {
        Measurement(value: self, unit: UnitLength.meters)
            .converted(to: UnitLength.centimeters)
            .value
        // self * 100.0
    }
    var mAsKm: Double {
        Measurement(value: self, unit: UnitLength.meters)
            .converted(to: UnitLength.kilometers)
            .value
        // self * 0.001
    }
    var kmAsInch: Double {
        Measurement(value: self, unit: UnitLength.kilometers)
            .converted(to: UnitLength.inches)
            .value
        // self * 39370.1
    }
    var kmAsFt: Double {
        Measurement(value: self, unit: UnitLength.kilometers)
            .converted(to: UnitLength.feet)
            .value
        // self * 3280.84
    }
    var kmAsMile: Double {
        Measurement(value: self, unit: UnitLength.kilometers)
            .converted(to: UnitLength.miles)
            .value
        // self / 1.609344
    }
    var kmAsCm: Double {
        Measurement(value: self, unit: UnitLength.kilometers)
            .converted(to: UnitLength.centimeters)
            .value
        // self * 100000.0
    }
    var kmAsM: Double {
        Measurement(value: self, unit: UnitLength.kilometers)
            .converted(to: UnitLength.meters)
            .value
        // self * 1000.0
    }
}
```
**配方 2-12**  
**距离/长度转换**

长度和距离单位并不是公制与英制之间的唯一区别。还有面积、体积和重量单位。让我们为它们全部编写扩展。

### 面积单位

常用的面积单位有：

*   平方英尺（以下代码中写作 `sq. ft.` 或 `sqft`）
*   路得（Rood）
*   英亩（Acre）
*   平方米（以下代码中写作 `sq. m.` 或 `sqm`）
*   平方厘米（以下代码中写作 `sq. cm.` 或 `sqcm`）
*   公顷（以下代码中写作 `ha.` 或 `hect`）

由于 `UnitArea` 类中没有 `rood`（路得）这个单位，我们将通过英亩进行转换，假设 4 路得等于 1 英亩。配方 2-13 展示了完整的扩展。



```swift
public extension Double {
    var sqftAsRood: Double {
        Measurement(value: self, unit: UnitArea.squareFeet)
            .converted(to: UnitArea.acres)
            .value
            .acreAsRood
        // self / 10890.0
    }
    var sqftAsAcre: Double {
        Measurement(value: self, unit: UnitArea.squareFeet)
            .converted(to: UnitArea.acres)
            .value
        // self / 43560.0
    }
    var sqftAsSqm: Double {
        Measurement(value: self, unit: UnitArea.squareFeet)
            .converted(to: UnitArea.squareMeters)
            .value
        // self / 10.7639
    }
    var sqftAsSqcm: Double {
        Measurement(value: self, unit: UnitArea.squareFeet)
            .converted(to: UnitArea.squareCentimeters)
            .value
        // self * 929.03
    }
    var sqftAsHect: Double {
        Measurement(value: self, unit: UnitArea.squareFeet)
            .converted(to: UnitArea.hectares)
            .value
        // self / 107639.1
    }
    var roodAsSqft: Double {
        Measurement(value: self.roodAsAcre, unit: UnitArea.acres)
            .converted(to: UnitArea.squareFeet)
            .value
        // self * 10890.0
    }
    var roodAsAcre: Double {
        self / 4.0
    }
    var roodAsSqm: Double {
        Measurement(value: self.roodAsAcre, unit: UnitArea.acres)
            .converted(to: UnitArea.squareMeters)
            .value
        // self * 1011.7141056
    }
    var roodAsSqcm: Double {
        Measurement(value: self.roodAsAcre, unit: UnitArea.acres)
            .converted(to: UnitArea.squareCentimeters)
            .value
        // self * 10117141.056
    }
    var roodAsHect: Double {
        Measurement(value: self.roodAsAcre, unit: UnitArea.acres)
            .converted(to: UnitArea.hectares)
            .value
        // self * 0.10021786
    }
    var acreAsSqft: Double {
        Measurement(value: self, unit: UnitArea.acres)
            .converted(to: UnitArea.squareFeet)
            .value
        // self * 43560.0
    }
    var acreAsRood: Double {
        self * 4.0
    }
    var acreAsSqm: Double {
        Measurement(value: self, unit: UnitArea.acres)
            .converted(to: UnitArea.squareMeters)
            .value
        // self * 4046.8564224
    }
    var acreAsSqcm: Double {
        Measurement(value: self, unit: UnitArea.acres)
            .converted(to: UnitArea.squareCentimeters)
            .value
        // self * 40468564.224
    }
    var acreAsHect: Double {
        Measurement(value: self, unit: UnitArea.acres)
            .converted(to: UnitArea.hectares)
            .value
        // self / 2.47105
    }
    var sqmAsSqft: Double {
        Measurement(value: self, unit: UnitArea.squareMeters)
            .converted(to: UnitArea.squareFeet)
            .value
        // self * 10.7639
    }
    var sqmAsRood: Double {
        Measurement(value: self, unit: UnitArea.squareMeters)
            .converted(to: UnitArea.acres)
            .value
            .acreAsRood
        // self / 1011.7141056
    }
    var sqmAsAcre: Double {
        Measurement(value: self, unit: UnitArea.squareMeters)
            .converted(to: UnitArea.acres)
            .value
        // self / 4046.8564224
    }
    var sqmAsSqcm: Double {
        Measurement(value: self, unit: UnitArea.squareMeters)
            .converted(to: UnitArea.squareCentimeters)
            .value
        // self * 10000.0
    }
    var sqmAsHect: Double {
        Measurement(value: self, unit: UnitArea.squareMeters)
            .converted(to: UnitArea.hectares)
            .value
        // self * 0.0001
    }
    var sqcmAsSqft: Double {
        Measurement(value: self, unit: UnitArea.squareCentimeters)
            .converted(to: UnitArea.squareFeet)
            .value
        // self / 929.03
    }
    var sqcmAsRood: Double {
        Measurement(value: self, unit: UnitArea.squareCentimeters)
            .converted(to: UnitArea.acres)
            .value
            .acreAsRood
        // self / 10117141.056
    }
    var sqcmAsAcre: Double {
        Measurement(value: self, unit: UnitArea.squareCentimeters)
            .converted(to: UnitArea.acres)
            .value
        // self / 40468564.224
    }
    var sqcmAsSqm: Double {
        Measurement(value: self, unit: UnitArea.squareCentimeters)
            .converted(to: UnitArea.squareMeters)
            .value
        // self * 0.0001
    }
    var sqcmAsHect: Double {
        Measurement(value: self, unit: UnitArea.squareCentimeters)
            .converted(to: UnitArea.hectares)
            .value
        // self * 0.00000001
    }
    var hectAsSqft: Double {
        Measurement(value: self, unit: UnitArea.squareCentimeters)
            .converted(to: UnitArea.squareFeet)
            .value
        // self * 107639.1
    }
    var hectAsRood: Double {
        Measurement(value: self, unit: UnitArea.hectares)
            .converted(to: UnitArea.acres)
            .value
            .acreAsRood
        // self * 9.97826087
    }
    var hectAsAcre: Double {
        Measurement(value: self, unit: UnitArea.hectares)
            .converted(to: UnitArea.acres)
            .value
        // self * 2.47105
    }
    var hectAsSqm: Double {
        Measurement(value: self, unit: UnitArea.hectares)
            .converted(to: UnitArea.squareMeters)
            .value
        // self * 10000
    }
    var hectAsSqcm: Double {
        Measurement(value: self, unit: UnitArea.hectares)
            .converted(to: UnitArea.squareCentimeters)
            .value
        // self * 100000000
    }
}
```
**配方 2-13 面积单位转换**

### 体积单位

配方 `2-14` 展示了用于转换体积单位的扩展。常用的体积单位有：

- 液量盎司 (fl. oz)
- 品脱
- 加仑
- 毫升 (ml)
- 升 (l)

```swift
public extension Double {
    var flozAsPint: Double {
        Measurement(value: self, unit: UnitVolume.fluidOunces)
            .converted(to: UnitVolume.pints)
            .value
        // self / 16.0
    }
    var flozAsGallon: Double {
        Measurement(value: self, unit: UnitVolume.fluidOunces)
            .converted(to: UnitVolume.gallons)
            .value
        // self / 128.0
    }
    var flozAsMl: Double {
        Measurement(value: self, unit: UnitVolume.fluidOunces)
            .converted(to: UnitVolume.milliliters)
            .value
        // self * 29.5735
    }
    var flozAsL: Double {
        Measurement(value: self, unit: UnitVolume.fluidOunces)
            .converted(to: UnitVolume.liters)
            .value
        // self * 0.0295735
    }
    var pintAsFloz: Double {
        Measurement(value: self, unit: UnitVolume.pints)
            .converted(to: UnitVolume.fluidOunces)
            .value
        // self * 16.0
    }
    var pintAsGallon: Double {
        Measurement(value: self, unit: UnitVolume.pints)
            .converted(to: UnitVolume.gallons)
            .value
        // self / 8.0
    }
    var pintAsMl: Double {
        Measurement(value: self, unit: UnitVolume.pints)
            .converted(to: UnitVolume.milliliters)
            .value
        // self * 473.176
    }
    var pintAsL: Double {
        Measurement(value: self, unit: UnitVolume.pints)
            .converted(to: UnitVolume.liters)
            .value
        // self * 0.473176
    }
    var gallonAsFloz: Double {
        Measurement(value: self, unit: UnitVolume.gallons)
            .converted(to: UnitVolume.fluidOunces)
            .value
        // self * 128.0
    }
    var gallonAsPint: Double {
        Measurement(value: self, unit: UnitVolume.gallons)
            .converted(to: UnitVolume.pints)
            .value
        // self * 8.0
    }
    var gallonAsMl: Double {
        Measurement(value: self, unit: UnitVolume.gallons)
            .converted(to: UnitVolume.milliliters)
            .value
        // self * 3785.41
    }
    var gallonAsL: Double {
        Measurement(value: self, unit: UnitVolume.gallons)
            .converted(to: UnitVolume.liters)
            .value
        // self * 3.78541
    }
    var mlAsFloz: Double {
        Measurement(value: self, unit: UnitVolume.milliliters)
            .converted(to: UnitVolume.fluidOunces)
            .value
        // self / 29.5735
    }
    var mlAsPint: Double {
        Measurement(value: self, unit: UnitVolume.milliliters)
            .converted(to: UnitVolume.pints)
            .value
        // self / 473.176
    }
    var mlAsGallon: Double {
        Measurement(value: self, unit: UnitVolume.milliliters)
            .converted(to: UnitVolume.gallons)
            .value
        // self / 3785.41
    }
    var mlAsL: Double {
        Measurement(value: self, unit: UnitVolume.milliliters)
            .converted(to: UnitVolume.liters)
            .value
        // self * 0.001
    }
    var lAsFloz: Double {
        Measurement(value: self, unit: UnitVolume.liters)
            .converted(to: UnitVolume.fluidOunces)
            .value
        // self / 0.0295735
    }
    var lAsPint: Double {
        Measurement(value: self, unit: UnitVolume.liters)
            .converted(to: UnitVolume.pints)
            .value
        // self / 0.473176
    }
    var lAsGallon: Double {
        Measurement(value: self, unit: UnitVolume.liters)
            .converted(to: UnitVolume.gallons)
            .value
        // self / 3.78541
    }
    var lAsMl: Double {
        Measurement(value: self, unit: UnitVolume.liters)
            .converted(to: UnitVolume.milliliters)
            .value
        // self * 1000.0
    }
}
```
**配方 2-14 体积单位转换**



### 重量单位

在 Recipe 2-15 中，你可以找到重量转换。常用的重量单位有：

- 盎司（`oz`）
- 磅（`lbs`）
- 英石（`st`）
- 克（`g`）
- 千克（`kg`）

```
public extension Double {
var ozAsLbs: Double {
Measurement(value: self, unit: UnitMass.ounces)
.converted(to: UnitMass.pounds)
.value
// self / 16.0
}
var ozAsSt: Double {
Measurement(value: self, unit: UnitMass.ounces)
.converted(to: UnitMass.stones)
.value
// self / 224.0
}
var ozAsG: Double {
Measurement(value: self, unit: UnitMass.ounces)
.converted(to: UnitMass.grams)
.value
// self * 28.3495
}
var ozAsKg: Double {
Measurement(value: self, unit: UnitMass.ounces)
.converted(to: UnitMass.kilograms)
.value
// self * 0.0283495
}
var lbsAsOz: Double {
Measurement(value: self, unit: UnitMass.pounds)
.converted(to: UnitMass.ounces)
.value
// self * 16.0
}
var lbsAsSt: Double {
Measurement(value: self, unit: UnitMass.pounds)
.converted(to: UnitMass.stones)
.value
// self / 14.0
}
var lbsAsG: Double {
Measurement(value: self, unit: UnitMass.pounds)
.converted(to: UnitMass.grams)
.value
// self * 453.592
}
var lbsAsKg: Double {
Measurement(value: self, unit: UnitMass.pounds)
.converted(to: UnitMass.kilograms)
.value
// self * 0.453592
}
var stAsOz: Double {
Measurement(value: self, unit: UnitMass.stones)
.converted(to: UnitMass.ounces)
.value
// self * 224.0
}
var stAsLbs: Double {
Measurement(value: self, unit: UnitMass.stones)
.converted(to: UnitMass.pounds)
.value
// self * 14.0
}
var stAsG: Double {
Measurement(value: self, unit: UnitMass.stones)
.converted(to: UnitMass.grams)
.value
// self * 6350.29
}
var stAsKg: Double {
Measurement(value: self, unit: UnitMass.stones)
.converted(to: UnitMass.kilograms)
.value
// self * 6.35029
}
var gAsOz: Double {
Measurement(value: self, unit: UnitMass.grams)
.converted(to: UnitMass.ounces)
.value
// self / 28.3495
}
var gAsLbs: Double {
Measurement(value: self, unit: UnitMass.grams)
.converted(to: UnitMass.pounds)
.value
// self / 453.592
}
var gAsSt: Double {
Measurement(value: self, unit: UnitMass.grams)
.converted(to: UnitMass.stones)
.value
// self / 6350.29
}
var gAsKg: Double {
Measurement(value: self, unit: UnitMass.grams)
.converted(to: UnitMass.kilograms)
.value
// self * 0.001
}
var kgAsOz: Double {
Measurement(value: self, unit: UnitMass.kilograms)
.converted(to: UnitMass.ounces)
.value
// self / 0.0283495
}
var kgAsLbs: Double {
Measurement(value: self, unit: UnitMass.kilograms)
.converted(to: UnitMass.pounds)
.value
// self / 0.453592
}
var kgAsSt: Double {
Measurement(value: self, unit: UnitMass.kilograms)
.converted(to: UnitMass.stones)
.value
// self / 6.35029
}
var kgAsG: Double {
Measurement(value: self, unit: UnitMass.kilograms)
.converted(to: UnitMass.grams)
.value
// self * 1000.0
}
}
配方 2-15
重量转换
```

## 总结

我们了解了三种流行的数据表示形式：JSON、XML 和 YAML。JSON 是最流行的数据交换格式，但其他格式在 IT 领域也有其位置。我们还回顾了`Encodable`和`Decodable`结构，它们是 Swift 中数据序列化的核心。如果你想保持灵活性、添加数据处理但保持数据结构化，属性包装器也可能很有用。

最后，我们为数据添加了语义，并介绍了数字单位及其之间的转换。

在下一章中，我们将转向所有现代语言中最流行的数据类型之一——`String`。我们已经看到，它可以使用 JSON 表示几乎所有内容。但它还可以被分析、验证，用于表示二进制数据、安全数据等。

