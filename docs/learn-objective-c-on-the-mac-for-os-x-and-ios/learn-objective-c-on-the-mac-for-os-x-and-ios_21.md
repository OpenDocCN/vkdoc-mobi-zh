# NSPredicate

编写软件时一个相当常见的操作是获取一个对象集合，并根据某种已知条件对它们进行评估。你保留符合条件（即“真”）的对象，丢弃不符合的对象，最终得到一堆可以处理的有趣对象。

你在日常使用的软件（比如 iPhoto）中会经常看到这种情况。如果你告诉 iPhoto 只显示评级为三颗星或更高的照片，那么你指定的条件就是“照片的评级必须为三颗星或更高”。所有照片都会经过这个过滤器。评级达到三颗星或以上的照片通过筛选，其余则被隐藏。然后 iPhoto 会向你展示所有好照片。

类似地，iTunes 也有搜索框。你寻找的条件可能是：歌手是玛丽莲·曼森或巴里·曼尼洛。于是，所有非重摇滚和非抒情歌曲都会被隐藏，让你能创建一份最奇特的混音歌单。

Cocoa 提供了一个名为 `NSPredicate` 的类，让你能够指定这些条件过滤器。你可以创建 `NSPredicate` 对象，精确描述你认为的条件，然后让每个对象通过谓词进行匹配。

此处的“谓词”与你可能在英语语法课上学到的“谓语”不同。这里的“谓词”是数学和计算机科学意义上的用法：一个返回真或假值的函数。

`NSPredicate` 是 Cocoa 用于描述查询的方式，就像你在数据库中使用的那样。你可以将 `NSPredicate` 与 Core Data 和 Spotlight 等数据库风格的 API 结合使用，尽管我们在此不会深入介绍这些技术（但本章中的大部分内容同样可以应用于这两种技术以及你自己的对象）。你可以将 `NSPredicate` 视为另一种间接手段。你可以使用谓词对象来进行检查，而不是在代码中显式询问：“这些是我要找的机器人吗？”通过切换不同的谓词对象，你可以让通用代码过滤数据，而无需硬编码你要寻找的条件。这也是你在第 3 章中遇到的“开闭原则”的另一个应用。

## 创建谓词

在将 `NSPredicate` 对象应用于你的对象之前，你需要创建它，有两种基本方式。一种涉及创建大量对象并进行组装。这需要大量代码，但在构建用于指定搜索的通用用户界面时会很方便。另一种方式则是在代码中使用查询字符串。在刚开始时，这种方式更容易处理，因此本书将重点介绍查询字符串。字符串型 API 的常见问题在此同样适用，尤其是编译器缺乏错误检查，以及偶尔出现的奇怪运行时错误。

我们无法回避“汽车零件”的例子——本章的示例将基于第 18 章中构建的车库。你可以在 *20.01 Car-Part-Predicate* 项目中找到所有内容。

首先，我们只看一辆车：

```objc
Car *car;

car = makeCar (@"Herbie", @"Honda", @"CRX", 1984, 2, 34000, 58);

[garage addCar: car];
```

回想一下，我们编写了 `makeCar` 函数来构建一辆汽车，并为其配备发动机和轮胎。在这个例子中，我们有一辆 Herbie，一辆 1984 年款双门本田 CRX，搭载 58 马力的发动机，里程数为 34000 英里。

现在，我们来创建一个谓词：

```objc
NSPredicate *predicate;

predicate = [NSPredicate predicateWithFormat: @"name == 'Herbie'"];
```

让我们分析一下。`predicate` 是我们常见的 Objective-C 对象指针，它将指向一个 `NSPredicate` 对象。我们使用 `NSPredicate` 的类方法 `+predicateWithFormat:` 来实际创建谓词。我们给它一个字符串，`+predicateWithFormat:` 会将该字符串在后台构建成一个用于评估谓词的对象树。

`predicateWithFormat` 听起来很像 `NSString` 提供的 `stringWithFormat`，后者允许你使用 `printf` 风格的格式说明符插入内容。稍后你会看到，你也可以对 `predicateWithFormat` 做同样的事情。Cocoa 的命名方案具有一致性——这一点很友好。

这个谓词字符串看起来像一个标准的 C 表达式。左侧是一个键路径 `name`。接下来是相等运算符（`==`），右侧是一个带引号的字符串。如果谓词字符串中的一段文本未加引号，它会被视为键路径。如果加了引号，则被视为字符串字面量。你可以使用单引号或双引号（只要它们成对出现）。通常你会使用单引号；否则，你需要在字符串中对每个双引号进行转义。

## 评估谓词

好了，现在我们有了一个谓词。接下来呢？我们针对一个对象来评估它！

```objc
BOOL match = [predicate evaluateWithObject: car];

NSLog (@"%s", (match) ? "YES" : "NO");
```

`-evaluateWithObject:` 告诉接收对象（谓词）使用给定的对象对自身进行评估。在本例中，它获取汽车对象，使用 `name` 作为键路径调用 `valueForKeyPath:` 来获取名称。然后，它将名称与“Herbie”进行相等性比较。如果名称和“Herbie”相同，`-evaluateWithObject:` 返回 `YES`；否则返回 `NO`。`NSLog` 使用三元运算符将数值型的 `BOOL` 转换为人类可读的字符串。

这是另一个谓词：

```objc
predicate = [NSPredicate predicateWithFormat:
            @"engine.horsepower > 150"];

match = [predicate evaluateWithObject: car];
```

该谓词字符串的左侧是一个键路径。这个键路径深入汽车对象，找到发动机，然后找到发动机的马力值。接着，它将这个值与 150 进行比较，看是否大于 150。

在我们对 Herbie 进行此评估后，`match` 的值为 `NO`，因为小 Herbie 的马力（58）并不大于 150。

针对特定谓词的条件检查单个对象固然不错，但当你拥有对象集合时，事情会变得更有趣。假设我们想查看车库中哪些汽车马力最大。我们可以遍历所有汽车，并用这个谓词逐一测试：

```objc
NSArray *cars = [garage cars];

for (Car *car in [garage cars])
{
    if ([predicate evaluateWithObject: car])
    {
        NSLog (@"%@", car.name);
    }
}
```

## 总结

静态分析器在一个像这样的小程序中发现了四个问题。哇！想象一下，当你在一个庞大复杂的项目上运行分析器时，它会发现什么。你不必每次构建时都运行分析器，但在开发过程中将其纳入工作流程绝对是一个好习惯。

一个提醒：正如你在本章中所看到的，分析器有助于指出问题，但你仍然需要自己进行一些侦查才能准确找出问题所在。毫无疑问，总有一天分析器会精确定位你的错误，然后自动为你修复问题。但今天还不是那一天。



我们从车库里取出所有汽车，遍历它们，并根据谓词逐一评估每辆车。以下代码块会打印出马力最高的汽车：

Elvis

Phoenix

Judge

有道理吧？在继续之前，请确保你清楚其中涉及的所有语法要素。仔细看看`NSLog`中对汽车名称的调用。这里使用了 Objective-C 2.0 的点语法，等价于调用`[car name]`。这里面没有什么魔法。这里的谓词字符串是`"engine.horsepower > 150"`。`engine.horsepower`是一个键路径，它可能在底层牵涉到各种魔法。

## 燃油滤清器

程序员一个著名的美德/缺点是懒惰。如果不用写那个`for`循环和`if`语句，岂不美哉？虽然那只是几行代码，但零行代码当然更好。幸运的是，有几个分类为 Cocoa 的集合类添加了谓词过滤方法。

`–filteredArrayUsingPredicate:`是`NSArray`上的一个分类方法，它会遍历数组的内容，根据谓词评估每个对象，并将评估结果为`YES`的对象累积到一个新数组中返回：

```objc
NSArray *results;
results = [cars filteredArrayUsingPredicate: predicate];
NSLog (@"%@", results);
```

这会输出以下结果：

```
(
    Elvis, a 1989 Acura Legend, has 4 doors, 28123.4 miles, 151 hp and 4 tires,
    Phoenix, a 1969 Pontiac Firebird, has 2 doors, 85128.3 miles, 345 hp and 4 tires,
    Judge, a 1969 Pontiac GTO, has 2 doors, 45132.2 miles, 370 hp and 4 tires
)
```

好的，这些结果与之前的结果并不完全一致。这是一个汽车对象的数组；而在之前的示例中，我们得到的是名字。我们可以使用键值编码（KVC）来提取名字，请记住：当`valueForKey:`发送给一个数组时，该键会被应用于数组中的所有元素：

```objc
NSArray *names;
names = [results valueForKey:@"name"];
NSLog (@"%@", names);
```

如果我们打印出`names`，会看到：

```
(
    Elvis,
    Phoenix,
    Judge
)
```

假设你有一个可变数组，并且想要移除所有不符合条件的元素。`NSMutableArray`有一个`–filterUsingPredicate`方法，它正好能完成我们需要的移除工作：

```objc
NSMutableArray *carsCopy = [cars mutableCopy];
[carsCopy filterUsingPredicate: predicate];
```

如果你打印出`carsCopy`，它将是之前我们看到的那三辆汽车的集合。

你仍然可以对`NSMutableArray`使用`–filteredArrayUsingPredicate:`来创建一个新的（不可变）数组，因为`NSMutableArray`是`NSArray`的子类。`NSSet`也有类似的调用。

正如我们在 KVC 中提到的，使用谓词确实很方便，但它的运行速度并不比你亲自编写所有代码更快。因为无论如何都无法避免遍历所有汽车并对每辆车进行一些处理。在大多数情况下，如果你是在为 OS X 编写程序，这种遍历不是什么大问题，因为如今的电脑速度非常快。尽可能编写最方便的代码，然后如果遇到性能问题，再使用 Apple 的工具（如 Instruments）来测量性能。不过，iOS 程序员应该始终密切关注程序的性能。

## 格式说明符

有经验的程序员知道，硬编码并非总是好主意。如果我们想知道哪些汽车的马力超过 200，之后又想了解哪些汽车的马力超过 50，该怎么办？我们可以使用像`"engine.horsepower > 200"`和`"engine.horsepower > 50"`这样的谓词字符串，但这样我们就得重新编译程序，又回到我们在第 3 章中摆脱的那个糟糕世界。

我们可以通过两种方式将可变内容放入谓词格式字符串中：格式说明符和变量名。首先，我们来看看格式说明符。你可以像平时熟悉的那样，使用`%d`和`%f`来放入数值：

```objc
predicate = [NSPredicate predicateWithFormat: @"engine.horsepower > %d", 50];
```

当然，与其在代码中直接使用`50`，你可以让这个值来自用户界面或某些外部机制。



除了常用的 `printf` 格式说明符之外，你还可以使用 `%@` 来插入字符串值。`%@` 会被当作一个带引号的字符串来处理：

```objectivec
predicate = [NSPredicate predicateWithFormat: @"name == %@", @"Herbie"];
```

请注意，在此格式字符串中，`%@` 没有被引号括起来。如果你像这样给 `%@` 加上引号：`"name == '%@'"`，那么 `%` 和 `@` 这两个字符就会直接出现在谓词字符串中。

`NSPredicate` 字符串还允许你使用 `%K` 来指定一个键路径。下面的谓词与其他谓词相同，其真值条件为 `name == 'Herbie'`：

```objectivec
predicate =
[NSPredicate predicateWithFormat: @"%K == %@", @"name", @"Herbie"];
```

使用格式说明符是构建灵活谓词的一种方式。另一种方式是在字符串中放入变量名，类似于环境变量的用法：

```objectivec
NSPredicate *predicateTemplate =
[NSPredicate predicateWithFormat:@"name == $NAME"];
```

现在我们有了一个包含变量的谓词，可以使用 `predicateWithSubstitutionVariables` 调用来创建新的特化谓词。你需要创建一个键值对字典，其中键是变量名（不带美元符号），值是要替换到谓词中的内容：

```objectivec
NSDictionary *varDict;
varDict = [NSDictionary dictionaryWithObjectsAndKeys:
             @"Herbie", @"NAME", nil];
```

这会将字符串 `"Herbie"` 作为键 `"NAME"` 对应的值。接着，我们可以创建新谓词：

```objectivec
predicate =
[predicateTemplate predicateWithSubstitutionVariables: varDict];
```

这个谓词的工作原理与其他你已经见过的谓词完全相同。

你可以为变量值使用不同类型的对象，比如 `NSNumber`。以下谓词用于过滤发动机功率：

```objectivec
predicateTemplate =
[NSPredicate predicateWithFormat: @"engine.horsepower > $POWER"];

varDict = [NSDictionary dictionaryWithObjectsAndKeys:
             [NSNumber numberWithInt: 150], @"POWER", nil];

predicate =
[predicateTemplate predicateWithSubstitutionVariables: varDict];
```

这会创建一个真值条件为 `engine.horsepower > 150` 的谓词。

除了 `NSNumber` 和 `NSString`，你还可以使用 `[NSNull null]` 来表示 `nil` 值，甚至可以像本章稍后介绍的那样使用数组。请注意，你不能对键路径使用 `$VARIABLE`，它只适用于值。如果需要在程序运行时动态改变谓词格式字符串中的键路径，你需要使用 `%K` 格式说明符。

谓词机制不会进行类型检查。你可能会不小心将一个字符串插入到期望为数字的位置，这可能会导致运行时错误提示，或产生令人困惑的行为。

## Hello Operator，给我数字 9

`NSPredicate` 的格式字符串包含许多不同的运算符。我们将介绍其中大部分运算符，并为每个运算符提供一个简单的示例。其余运算符可以在苹果在线文档中找到，可通过 Xcode 访问。

### 比较运算符与逻辑运算符

谓词字符串语法支持你常用的 C 语言风格运算符，例如相等运算符 `==` 和 `=`。

不等运算符有多种形式，如表 20-1 所示。

**表 20-1.** 不等运算符

| 运算符 | 含义 |
| --- | --- |
| `>` | 大于 |
| `>=` 和 `=>` | 大于或等于 |
| `<` | 小于 |
| `<=` 和 `=<` | 小于或等于 |
| `!=` 和 `<>` | 不等于 |

该语法还支持括号表达式（真的！），以及 `AND`、`OR`、`NOT` 逻辑运算符，或它们对应的 C 语言风格的 `&&`、`||` 和 `!`。

以下是一个示例。你可以过滤掉动力最强和最弱的汽车，只保留中档车型：

```objectivec
predicate = [NSPredicate predicateWithFormat:
               @"(engine.horsepower > 50) AND
                (engine.horsepower < 200)"];

results = [cars filteredArrayUsingPredicate: predicate];
NSLog (@"oop %@", results);
```

如果我们将其应用于我们的车队，会得到以下结果：

```
Herbie, a 1984 Honda CRX, has 2 doors, 34000.0 miles, 58 hp and 4 tires,
Badger, a 1987 Acura Integra, has 5 doors, 217036.7 miles, 130 hp and 4 tires,
Elvis, a 1989 Acura Legend, has 4 doors, 28123.4 miles, 151 hp and 4 tires,
```



`Paper Car`，一辆 1965 年款普利茅斯 Valiant，有 2 个车门，里程 76800.0 英里，105 马力以及 4 个轮胎。

谓词字符串中的运算符不区分大小写。你可以随意使用 `AnD`、`ANd`、`AND` 或 `and`。此处我们将全部使用大写字母，但在实际代码中你无需如此。

不等关系同样适用于数值和字符串值。要查看按字母表顺序排列的所有汽车，请使用以下谓词：

```
predicate = [NSPredicate predicateWithFormat: @"name < 'Newton'"];
results = [cars filteredArrayUsingPredicate: predicate];
NSLog (@"%@", [results valueForKey: @"name"]);
```

```
(
    Herbie,
    Badger,
    Elvis,
    Judge
)
```

## 数组运算符

谓词字符串 `(engine.horsepower > 50) OR (engine.horsepower < 200)` 是一种非常常见的模式。我们正在寻找马力值介于这两个数字之间的结果。如果我们能使用一个运算符来查看介于两个值之间的内容，那岂不是很酷？事实证明，我们可以这样做：

```
predicate = [NSPredicate predicateWithFormat:
@"engine.horsepower BETWEEN { 50, 200 }"];
```

花括号表示一个数组，而 `BETWEEN` 将数组的第一个元素视为下限，第二个元素视为上限。

你可以通过使用 `%@` 格式说明符来插入你自己的 `NSArray` 对象：

```
NSArray *betweens = [NSArray arrayWithObjects:
    [NSNumber numberWithInt: 50],
    [NSNumber numberWithInt: 200], nil];
predicate = [NSPredicate predicateWithFormat:
    @"engine.horsepower BETWEEN %@", betweens];
```

你也可以使用变量：

```
predicateTemplate =
    [NSPredicate predicateWithFormat: @"engine.horsepower BETWEEN $POWERS"];
varDict =
    [NSDictionary dictionaryWithObjectsAndKeys: betweens, @"POWERS", nil];
predicate = [predicateTemplate predicateWithSubstitutionVariables: varDict];
```

数组的用途不仅限于指定区间的端点。你可以使用 `IN` 运算符来检查某个特定值是否包含在数组中。这对于有 SQL 经验的程序员来说应该很熟悉：

```
predicate = [NSPredicate predicateWithFormat:
    @"name IN { 'Herbie', 'Snugs', 'Badger', 'Flap' }"];
```

我们有一些名为 Herbie 和 Badger 的汽车，因此它们会通过过滤：

```
results = [cars filteredArrayUsingPredicate: predicate];
NSLog (@"%@", [results valueForKey: @"name"]);
```

果然，这只返回了两个结果：

```
(
    Herbie,
    Badger
)
```

## 自引用（SELF）

有时，你会对简单的值应用谓词，例如普通的字符串，而不是可以通过键路径操作的复杂对象。假设我们有一个汽车名称的数组，并且想要应用之前使用的相同过滤器。既然 `NSString` 在请求其 `name` 时表现不佳，那么我们应该用什么呢？

`SELF` 可解此忧！`SELF` 指的是正在被谓词评估的对象。实际上，我们可以将所有键路径表达为相对于 `SELF` 的形式。以下谓词与上一个完全相同：

```
predicate = [NSPredicate predicateWithFormat:
    @"SELF.name IN { 'Herbie', 'Snugs', 'Badger', 'Flap' }"];
```

现在，回到那个字符串数组。我们如何判断一个字符串是否也在那个名称数组中呢？让我们来看一下。

首先，你需要从某处获取仅包含名称的数组。由于我们已经深入涉及 `CarParts`，我们将通过使用针对数组的 `valueForKey:` 的 KVC 技巧来处理它们：

```
names = [cars valueForKey: @"name"];
```

这是一个包含我们拥有的所有汽车名称的字符串数组。接下来，创建一个谓词：

```
predicate = [NSPredicate predicateWithFormat:
    @"SELF IN { 'Herbie', 'Snugs', 'Badger', 'Flap' }"];
```

然后，对其进行评估：

```
results = [names filteredArrayUsingPredicate: predicate];
```

如果你现在查看结果，它和我们之前看到的两个名称相同：`Herbie` 和 `Badger`。这是一个小测验。以下代码会产生什么输出？

```
NSArray *names1 = [NSArray arrayWithObjects:
    @"Herbie", @"Badger", @"Judge", @"Elvis", nil];
NSArray *names2 = [NSArray arrayWithObjects:
    @"Judge", @"Paper Car", @"Badger", @"Phoenix", nil];
predicate = [NSPredicate predicateWithFormat: @"SELF IN %@", names1];
results = [names2 filteredArrayUsingPredicate: predicate];
NSLog (@"%@", results);
```

以下是答案：

```
(
    Judge,
    Badger
)
```

这是一种获取两个数组交集的好方法。好了，它是如何工作的呢？该谓词包含了第一个数组的内容，因此它看起来像这样：

`SELF IN {"Herbie", "Badger", "Judge", "Elvis"}`

现在，第二个名称数组使用此谓词进行过滤。同时存在于两个数组中的字符串将位于 `names2` 中，并且会导致 `SELF IN` 子句返回真，因此该字符串将出现在结果数组中。仅在第二个数组中的对象将被丢弃，因为它们与谓词中的任何字符串都不匹配。仅在第一个数组中的字符串则会留在原地等待比较，但永远不会进入结果。

## 字符串操作

你之前看到的关系运算符同样适用于字符串。还有一些特定于字符串的运算符，如表 20-2 所示。

表 20-2. 字符串专用运算符

| 运算符 | 含义 |
| --- | --- |
| `BEGINSWITH` | 检查一个字符串是否以另一个字符串开头。 |
| `ENDSWITH` | 检查一个字符串是否以另一个字符串结尾。 |
| `CONTAINS` | 检查一个字符串是否包含在另一个字符串内部某处。 |

使用关系运算符可以让你实现一些技巧，例如使用 `name BEGINSWITH 'Bad'` 来匹配 `Badger`，使用 `name ENDSWITH 'vis'` 来匹配 `Elvis`，以及使用 `name CONTAINS 'udg'` 来匹配 `Judge`。

如果你编写一个像 `name BEGINSWITH 'HERB'` 这样的谓词字符串会发生什么？这不会匹配 `Herbie` 或任何其他内容，因为这些匹配是区分大小写的。同样地，`name BEGINSWITH 'Hérb'` 也不会匹配，因为字母“e”带有重音符号。为了放宽名称匹配的规则，我们可以用 `[c]`、`[d]` 或 `[cd]` 来修饰这些运算符。其中 `c` 代表“不区分大小写”，`d` 代表“不区分变音符号”（即忽略重音符号），而 `[cd]` 则两者兼具。通常，除非有充分理由需要区分大小写和重音，否则你会使用 `[cd]`。你永远不知道用户何时会粘住大写锁定键，最终以全大写字母与你的应用对话。

以下谓词字符串将匹配 `Herbie`：`name BEGINSWITH[cd] 'HERB'`

## 像 (LIKE) 一样匹配，确实如此

有时，在字符串的开头、结尾或中间进行字符串匹配还不够强大。对于这些情况，谓词格式字符串包含了 `LIKE` 运算符。使用此运算符时，问号匹配一个字符，而星号匹配任意数量的字符。SQL 和 Unix shell 程序员会识别出这种行为（有时称为“通配符匹配”）。

谓词字符串 `name LIKE '*er*'` 将匹配任何名称中间包含“er”的字符串。这等价于 `CONTAINS`。

谓词字符串 `name LIKE '???er*'` 将匹配 `Paper Car`，因为它有三个字符，后跟“er”，以及“er”后的任意数量字符。它不匹配 `Badger`，因为后者在“er”之前有四个字符。

`LIKE` 也接受 `[cd]` 修饰符以实现大小写和变音符号的不敏感匹配。

如果你对正则表达式感兴趣，可以使用 `MATCHES` 运算符。给它一个正则表达式，谓词就会对其进行评估。

表达你自己：正则表达式是一种非常强大、非常紧凑地指定字符串匹配逻辑的方式。有时，正则表达式会变得复杂难懂，相关主题的书籍也层出不穷。`NSPredicate` 的正则表达式使用国际组件 for Unicode (ICU) 语法，你可以通过你最喜欢的互联网搜索引擎来了解它。

虽然正则表达式非常强大，但它们在计算上也可能很昂贵。如果你的谓语中有更简单的操作，例如基本字符串运算符和比较运算符，请在使用 `MATCHES` 之前先执行这些操作。这应该能给你带来速度上的提升。

## 就这些，各位



