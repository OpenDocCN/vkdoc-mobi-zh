# 14. 使用 JSON

*JavaScript 对象表示法* (JSON) 是一种开放标准、轻量级的数据交换格式，最初由道格拉斯·克罗克福特于 2000 年制定，于 2013 年标准化，并于 2017 年最终定稿为当前版本。它源自 JavaScript 编程语言，旨在满足一种独立于语言、实时的、无需插件即可实现服务器到浏览器交换协议的需求。JSON 对象使用相对简单的键/值对结构进行格式化，这种结构易于人类和机器读写。因此，它已成为 *表述性状态转移* (REST) Web 服务中不可或缺的数据交换工具。FileMaker 在版本 16 中添加了一组内置函数，可用于创建和操作 JSON 数据。这些函数在处理外部服务提供格式的数据时至关重要，也可在将 JSON 用作脚本与其他函数之间进行数据交换的内部格式时使用。本章将介绍 JSON，涵盖以下主题：

- 定义 JSON 格式
- 解析 JSON
- 操作 JSON



## 定义 JSON 格式

一个 **JSON 对象**是括号内一系列 **元素**的列表，这些元素将标识 *key*（键）与关联的 *value*（值）结合在一起。类似的结构在其它语言中有多种称呼，包括 *array*（数组）、*dictionary*（字典）、*hash table*（哈希表）、*keyed list*（键控列表）、*record*（记录）和 *struct*（结构体）。*key* 始终是一个文本字符串，充当标签来命名和标识元素。元素中的 *value* 包含一些数据内容，是以下类型之一：

- *JSONString* – 文本字符串
- *JSONNumber* – 数值
- *JSONBoolean* – `true` 或 `false` 值
- *JSONArray* – 包含在方括号内、由逗号分隔的有序值列表
- *JSONObject* – 嵌套在父对象元素中的 JSON 对象
- *JSONNull* – 空值
- *JSONRaw* – 将由 JSON 解析器确定的值

一个包含单个元素（含 `id` 号码）的 JSON 对象格式如下：标签放在引号中，后跟冒号和值，所有内容包裹在花括号中。此处键的值是 *JSONNumber*：

```
{"id":5103}
```

在此示例中，值包含 *JSONText*，即一个人的名字：

```
{"First":"John"}
```

多元素对象使用逗号分隔每个唯一命名的元素。例如，包含人员数据的对象可能组合 *id*、*first name*、*last name* 和 *title* 等元素，如下所示：

```
{"id":5103,"First":"John","Last":"Smith","Title":"Chief Technology Officer"}
```

JSON 不关心元素周围是否存在空白，因此上述对象可以格式化为多行格式，如下所示：

```
{
"id":5103,
"First":"John",
"Last":"Smith",
"Title":"Chief Technology Officer"
}
```

**JSON 数组**是一种包含未标记值或元素列表的对象类型。其格式为用方括号括起来的逗号分隔值，如下列数字数组示例所示：

```
[1,2,3,4,5]
```

数组可以包含其它数据类型，包括文本，如下例所示：

```
["Karen","Charlene","Jeff","Susan","Howard"]
```

元素的值甚至可以是另一个对象，从而形成对象的嵌套层次结构。此示例显示一个包含两个 **product 元素**的对象，每个元素包含一个嵌套的 *JSONObject* 作为产品元数据：

```
{
"Product1":{"name":"Widget 1","price":39.99,"vendor":15},
"Product2":{"name":"Widget 2","price":55.48,"vendor":38}
}
```

元素的值也可以包含数组，如下所示，其中 `friends` 和 `colleagues` 元素各自是一个包含名称列表的 *JSONArray*：

```
{
"friends":["Dan","Brian","Carolyn","Karen"],
"colleagues":["Brian","Michael","Mary","Walker","Nadya"]
}
```

类似地，数组可以包含对象。此示例显示一个包含两个项目的数组：一个包含两个 `Product` 元素的对象，以及另一个包含两个 `Employee` 元素的对象：

```
[
{"Product1":"Widget 1","Product2":"Widget 2"},
{"Employee1":"William","Employee2":"Janice"}
]
```

虽然某些系统和服务器会返回它们定义的 JSON 格式的数据，但在 FileMaker 中创建 JSON 时，对象的结构可以由你自由定义。任何数量的对象、数组和数据类型都可以混合搭配、连接或嵌套，使用你自定义的标签来创建基于自定义系统要求的独特结构。

> **提示**：在 [`www.jsonlint.com`](http://www.jsonlint.com) 验证 JSON 对象的格式。

## 解析 JSON

FileMaker 提供了三个用于解析数据的 JSON 函数，每个函数都需要两个参数：一个 `json` 对象（或空字符串）和一个 `keyOrIndexPath`（可选的，用于引用特定元素键、数组索引位置或嵌套元素的路径）：`JSONGetElement`、`JSONListKeys`、`JSONListValues`。

让我们基于以下假设来学习一些示例：Sandbox 表中的 `Example Text` 字段包含以下包含供应商信息的 JSON：

```
{
"id":350,
"name":"First Class Widgets",
"category":"Manufacturing",
"contact":
{
"phone":"555-867-5309",
"email":"sales@widgets.nope",
"web":"www.widgets.nope"
},
"products":
[
{
"aisles":[3,8],
"id":1000,
"name":"Widget 1",
"price":39.99
},
{
"aisles":[2,4],
"id":1001,
"name":"Widget 2",
"price":59.99
}
]
}
```

### 使用 `JSONGetElement`

`JSONGetElement` 函数将从提供的 JSON 数据中返回指定元素的值。

```
JSONGetElement ( json ; keyOrIndexOrPath )
```

#### 通过键引用元素

要通过键引用元素，请在 `keyOrIndexOrPath` 参数中使用其标签名称。此示例显示请求 `name` 元素的调用：

```
JSONGetElement ( Sandbox::Example Text ; "name" )    // 结果 = First Class Widgets
```

此示例请求 `contact` 元素，因此结果是一个 JSON 对象。

```
JSONGetElement ( Sandbox::Example Text ; "contact" )
// 结果 =
{
"phone":"555-867-5309",
"email":"sales@widgets.nope",
"web":"www.widgets.nope"
}
```

#### 通过数组索引引用元素

当对象是数组时，`keyOrIndexOrPath` 参数应为方括号内的数字，指示所需元素的零基位置。通过使用索引位置 `2`，此示例将从名称数组中提取第三个值，如下所示：

```
JSONGetElement ( ["Michael","Mary","Walker","Karen"] ; "[2]" ) // 结果 = Walker
```

#### 通过路径引用元素

当解析包含嵌套对象和数组元素的复杂 JSON 时，可以使用 *path*（路径）来引用比第一层更深的元素。*JSON path* 通过指定遍历层级结构到达所需元素所需的每个键来表示，每个键之间用句点分隔。使用供应商示例，要获取 `phone` 元素，我们必须指定它包含在 `contact` 元素中，如下所示：

```
JSONGetElement ( Sandbox::Example Text ; "contact.phone" )    // 结果 = 555-867-5309
```

*path* 可以根据需要深入到任意层级，并且可以混合使用对象键和数组索引位置。在此示例中，我们通过包含索引位置 `[1]` 来获取数组中第二个位置的 `product` 的 `price`，如下所示：

```
JSONGetElement ( Sandbox::Example Text ; "products.[1].price" )    // 结果 = 59.99
```

类似地，此示例将从 `product` 元素第一个数组位置的 `aisles` 元素中提取第二个数组位置：

```
JSONGetElement ( Sandbox::Example Text ; "products.[0].aisles.[1]" )    // 结果 = 8
```

### 使用 `JSONListKeys`

`JSONListKeys` 函数返回对象或指定元素中每个键的名称列表。

```
JSONListKeys ( json ; keyOrIndexOrPath )
```

此示例显示如何获取示例 JSON 中的键列表：

```
JSONListKeys ( Sandbox::Example Text ; "" )
// 结果 =
category
contact
id
name
products
```

`keyOrIndexOrPath` 参数可用于指定包含对象或数组的嵌套元素。此示例将返回 `contact` 元素的键列表：

```
JSONListKeys ( Sandbox::Example Text ; "contact" )
// 结果 =
phone
email
web
```

因为数组是未标记的列表，返回的键将是项目的零基索引位置列表，如下所示指定 `products` 元素：

```
JSONListKeys ( Sandbox::Example Text ; "products" )
// 结果 =

```



### 使用 `JSONListValues`

`JSONListValues` 函数会返回包含对象或数组的元素中，每个键所对应的值组成的列表。

```
JSONListValues ( json ; keyOrIndexOrPath )
```

其工作方式与 `JSONListKeys` 相同，区别在于它返回的是 *值* 而非 *键*，如下例所示，它返回了 `contact` 元素中每个元素的值：

```
JSONListValues ( Sandbox::Example Text ; "contact" )
// 结果 =
555-867-5309
sales@widgets.nope
www.widgets.nope
```

## 创建与操作 JSON

FileMaker 提供了三个用于操作 JSON 对象中元素的函数：`JSONSetElement`、`JSONDeleteElement` 和 `JSONFormatElements`。

### 使用 `JSONSetElement`

`JSONSetElement` 函数用于设置对象中一个或多个元素的值，如果元素不存在则会创建它们。该函数调用包含四个参数，如下所示：

```
JSONSetElement ( json ; keyOrIndexOrPath ; value ; type )
```

`json` 参数可以包含一个对象、数组，或者在创建新对象时使用空字符串。`keyOrIndexOrPath` 和 `value` 是必填参数，用于指定要创建或修改的元素键及其应包含的内容。`type` 参数可以指定 `value` 的数据类型，也可以使用空字符串，此时 FileMaker 会根据提供的内容自动推断类型。下面的示例创建了一个包含 `name` 元素的简单对象：

```
JSONSetElement ( "" ; "name" ; "First Class Widgets"; "" )
// 结果 = {"name":"First Class Widgets"}
```

假设上述结果被存入一个名为 `data` 的变量，以下示例展示了如何向现有对象添加一个元素，此处为 `category` 元素：

```
JSONSetElement ( data ; "category" ; "Manufacturing"; "" )
// 结果 =
{
"name":"First Class Widgets",
"category":"Manufacturing"
}
```

设置对象中已存在的元素的值时，会用新值替换旧值。

```
JSONSetElement ( {"name":"Honda"} ; "name" ; "Ford"; "" )
// 结果 = {"name":"Ford"}
```

**提示**：不要手动编写 JSON，应始终使用 `JSONSetElement` 函数来创建新元素，以帮助避免错误。

#### 指定数据类型

前面的示例将 `type` 参数留空，因为提供的数据显然是文本，我们可以依赖 FileMaker 选择正确的格式。但通常你需要指定类型以确保得到正确的结果。例如，如果 `value` 以数字开头且未指定类型，FileMaker 会自动将其视为数字。这意味着，带有前导零的文本或日期将被转换为数字，除非你指定了正确的数据类型。为了说明为基于文本的字符串指定类型的重要性，请比较以下结果的差异：

```
JSONSetElement ( "" ; "phone" ; "555-867-5309" ; "" )
// 结果 = {"phone":555}
JSONSetElement ( "" ; "phone" ; "555-867-5309" ; JSONString )
// 结果 = {"phone":"555-867-5309"}
```

#### 同时设置多个值

你可以通过使用分号分隔的、方括号括起来的 `keyOrIndexOrPath`、`value` 和 `type` 参数集，在一次调用中设置多个键，如下例所示，它同时设置了两个元素：

```
JSONSetElement ( "" ;
[ "name" ; "First Class Widgets"; "" ] ;
[ "category" ; "Manufacturing"; "" ]
)
// 结果 =
{
"name":"First Class Widgets",
"category":"Manufacturing"
}
```

#### 通过路径设置值

在处理需要包含对象和数组元素的复杂对象时，有必要指定一个*路径*来引用第一层以下的元素。此示例在顶层设置 `name` 元素，然后将 `contact` 元素设置为一个包含 `phone` 和 `email` 元素的对象：

```
JSONSetElement ( "" ;
[ "name" ; "First Class Widgets"; "" ] ;
[ "contact.phone" ; "555-867-5309"; JSONString ] ;
[ "contact.email" ; " sales@widgets.nope "; "" ]
)
// 结果 =
{
"name":"First Class Widgets",
"contact":
{
"phone":"555-867-5309",
"email":"sales@widgets.nope"
}
}
```

#### 设置数组值

`JSONSetElement` 可以定位数组中的索引位置，正如 `JSONGetElement` 所述。索引值必须用方括号括起来，以防止数字被用于创建或引用键而非数组位置，如下例所示。第一个示例展示了如何正确地在数组中创建值。第二个示例展示了未使用括号时的错误结果。

```
JSONSetElement ( "" ; "[0]" ; "Claris" ; "" )
// 结果 = ["Claris"]
JSONSetElement ( "" ; "0" ; "Claris" ; "")
// 结果 = {"0":"Claris"}
```

在现有数组中设置元素时，该索引位置的值将被新值替换，即使数据类型不同也是如此。此示例将第三个数组位置中的*数字*替换为一些*文本*：

```
JSONSetElement ( "[1,2,3,4]" ; "[2]" ; "Claris" ; "" )
// 结果 = [1,2,"Claris",4]
```

如果指定的索引位置超出了现有值的范围，FileMaker 会插入一个或多个 *null* 占位符，以定位到目标位置。

```
JSONSetElement ( "" ; "[3]" ; "Claris" ; "" )
// 结果 = [null,null,null,"Claris"]
JSONSetElement ( "[1,2,3,4]" ; "[6]" ; "Claris" ; "" )
// 结果 = [1,2,3,4,null,null,"Claris"]
```

数组在路径中的使用方式与键相同。此示例将更改供应商示例中第一个 `product` 的 `price`：

```
JSONSetElement ( Sandbox::Example Text ; "products.[0].price" ; "45.00" ; "" )
```

### 使用 `JSONDeleteElement`

`JSONDeleteElement` 函数将从对象中删除一个元素。该函数调用包含两个参数，如下所示：

```
JSONDeleteElement ( json ; keyOrIndexOrPath )
```

指定的元素将被完全移除，如下例所示，它移除了 `name` 元素，仅保留 `id`：

```
JSONDeleteElement ( {"name":"Honda", "id":350} ; "name" )
// 结果 = {"id":350}
```

删除数组中的索引位置会自动移动后续值，以避免出现空位。

```
JSONDeleteElement ( "[1,2,3,4,5]" ; "[2]" )
// 结果 = [1,2,4,5]
```

删除操作也适用于路径。此示例将完全移除供应商示例中第一个 `product` 的第一个 `price`，并将第二个 price 移动到第一个的位置：

```
JSONDeleteElement ( Sandbox::Example Text ; "products.[0].price" )
```

### 使用 `JSONFormatElements`

`JSONFormatElements` 函数通过插入额外的空格来重新格式化对象，使其更易于阅读。FileMaker 在执行任何操作函数时都会移除对象中的空格，因此此函数在审查结果时非常有用：

```
JSONFormatElements(
{"id":5103,"First":"John","Last":"Smith","Title":"Chief Technology Officer"}
)
// 结果
{
"id":5103,
"First":"John",
"Last":"Smith",
"Title":"Chief Technology Officer"
}
```

## 总结

本章介绍了 JSON，并探讨了用于创建、解析和操作对象与元素的内置函数。在下一章中，我们将学习如何创建自定义函数。


