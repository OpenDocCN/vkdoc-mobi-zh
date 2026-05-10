# 排版后的内容

在下面的修改示例中，每个步骤都在`Let`语句中对单独的变量声明执行。由于它是逐步构建单个值，因此可以使用相同的变量名，以便每个步骤替换前一个值，形成向下级联的流程。

```
Let ( [
result = Contact::Notes ;
result = GetValue ( result ; 1 ) ;
result = TextFormatRemove ( result) ;
result = Substitute ( result ; "-" ; "." )
] ;
result
)
```

这种技术并非在所有情况下都是最佳选择。即使是前面的示例，使用`Let`替代嵌套看起来也有些拥挤。有人可能会争辩说，在这种情况下，通过添加回车和制表符来间隔嵌套会是更好的解决方案。然而，在语句比这个简单示例更复杂的情况下，以这种方式使用`Let`语句可以取得很好的效果。

## `While`

`While`函数是一个复杂的语句，只要指定的条件为真，它就会重复执行一系列逻辑步骤。该函数接受四个参数。首先，一个或多个*初始化变量*作为第一个参数声明。这些用于设置值，类似于`Let`语句。*条件参数*是一个布尔表达式，用于控制循环操作。只要此结果评估为真且未达到最大允许迭代次数，函数就会重复执行逻辑步骤。接下来，一个或多个*逻辑变量*包含变量声明，这些声明将反复执行直到函数终止。它们的结构也类似于初始变量和`Let`语句。最后，*结果参数*是一个生成语句最终结果的表达式。它可以包含存储在任何初始变量或逻辑变量中的值。

```
While (
[ initialVariable ] ;
condition ;
[ logicVariables ] ;
result
)
```

### 使用`While`语句删除双空格

以下简单示例演示了`While`函数的基本结构。首先，一个`data`变量被初始化为`Contact`表中`Notes`字段的值。这是一个语句变量，在执行期间可在`While`语句中使用。接下来，建立一个*条件*，只要`data`变量包含任何双空格，函数就会重复执行。*逻辑*部分重新初始化`data`变量，将双空格替换为单个空格。这将持续循环，直到没有双空格为止，从而纠正任意数量的多余空格：双空格、三空格、四空格等。最后，当*条件*未能找到双空格并终止时，清理后的`data`变量成为*结果*，并返回没有多余空格的文本。

```
While (
[
data = Contact::Notes
] ;
PatternCount ( data ; "  " ) ;
[
data = Substitute ( data ; "  " ; " " )
] ;
data
)
```

将此公式放入自动输入计算（第 8 章，“字段选项：自动输入”）中，该计算替换现有值，以便用户输入的任何双空格都会自动从字段中移除。只需将字段引用更改为`Self`，该公式即可用于任何字段。

> **提示**
> 考虑创建一个自定义函数来自动清理文本（第 15 章），并将其用于任何字段的自动输入公式。

### 使用`While`编译相关记录列表

以下示例从与当前`Company`记录相关的`Contact`记录中提取姓名和职务列表，并将这些值合并以形成单个联系人列表。换句话说，一个由回车分隔的*姓名*列表和一个由回车分隔的*职务*列表，变为每个相关记录的“姓名 职务”回车分隔列表。这两个列表被初始化到一个变量中，并计数，同时初始化两个控制变量。只要*当前*值小于或等于值的*计数*，逻辑变量将重复执行。在那里，为*当前*值创建条目并添加到*结果*变量中。然后，*当前*变量递增一。当所有值处理完毕后，语句退出并返回累积的结果。

```
While (
[
names = List ( Company | Contact::Contact Name Full ) ;
titles = List ( Company | Contact::Contact Title ) ;
count = ValueCount ( names ) ;
current = 1 ;
result = ""
] ;
count ≥ current ;
[
entry = GetValue ( names ; current ) & " " & GetValue ( titles; current ) ;
result = result & Case ( result ≠ "" ; "¶" ) & entry ;
current = current + 1
] ;
result
)
```

> **注意**
> `List`函数排除空值。如果前面示例中即使有一个相关记录在两个字段之一中缺少值，则合并结果将在每个后续记录中不匹配。

### 使用`While`编译本地记录列表

此示例生成与前一示例相同的结果，但不是循环从相关表中提取的列表，而是通过使用`GetNthRecord`函数（本章前面已描述）循环当前表中的记录查找集。

```
While (
[
count = Get ( FoundCount ) ;
current = 1 ;
result = ""
] ;
count ≥ current ;
[
name = GetNthRecord ( Contact::Contact Name Full ; current ) ;
title = GetNthRecord ( Contact::Contact Title ; current ) ;
entry = name & " " & title ;
result = result & Case ( result ≠ "" ; "¶" ) & entry ;
current = current + 1
] ;
result
)
```

## 将函数嵌套到复杂语句中

内置函数可以组合和嵌套，以创建尽可能复杂的语句，以产生任何所需的结果。本节中的示例演示了通过组合各种函数和语句，从简单语句到更复杂的语句的转变。

### 创建记录元数据字符串

此示例收集有关记录的信息，并创建一个显示该信息的字符串。每个结果都可以显示在布局的字段或*公式命名的对象*（如按钮栏）中（第 20 章）。`Let`语句从元数据字段中提取值，并将它们组合成一个显示记录序列号以及创建/修改日期和时间的单个值。

```
Let ( [
id = Record ID ;
creator = Record Creation User ;
created = Record Creation Timestamp ;
modifier = Record Modification User ;
modified = Record Modification Timestamp ;
creation =
Right ( "0" & Month ( created ) ; 2 ) & "." &
Right ( "0" & Day ( created ) ; 2 ) & "." &
Year ( created ) ;
modification =
Right ( "0" & Month ( modified) ; 2 ) & "." &
Right ( "0" & Day ( modified) ; 2 ) & "." &
Year ( modified)
] ;
"ID " & id & " | " &
"Created on " & creation & " by " & creator & " | " &
"Modified on " & modification & " by " & modifier
)
// result = ID 55326 | Created on 07.01.2020 by Admin | Modified on 07.01.2020 by Admin
```

### 创建记录计数字符串

此示例组装一个字符串，显示记录导航字符串，包括*当前记录号*和总记录数。如果用户正在查看查找集，则也会包含该数量。

```
Let ( [
total = Get ( TotalRecordCount ) ;
found = Get ( FoundCount ) ;
current = Get ( ActiveRecordNumber )
] ;
"Record " & current & " of " & found &
Case ( found ≠ total ; " Found ( " & total & " Total )" )
)
// result (viewing all)         = Record 2 of 6
// result (viewing found set)   = Record 2 of 4 Found ( 6 Total )
```

> **注意**
> 使用上述示例作为计算字段公式时，请在*存储选项*下关闭索引，以确保常量更新。



### 从时间差创建语句

本示例根据经过的时间创建一条人类可读的语句。首先，一个 `Let` 语句开始初始化变量，起始变量 `start` 和 `end` 均包含一个时间戳值。虽然在本次演示中这些值被设为硬编码值，但它们也可以通过字段、变量或其他函数提供。接着，用 `end` 减去 `start`，并将结果转换为数字，以得出经过的秒数 `elapsed`。随后，将这个数字经过四个级联步骤处理，依次确定经过的天数 (`days`)、小时数 (`hours`)、分钟数 (`minutes`) 和秒数 (`seconds`)，并且在每一步中，都从 `elapsed` 秒数中扣除已提取的量。这些步骤使用了 `Int` 函数，该函数通过舍弃小数点右侧的所有数字来返回一个数字的整数部分（不进行四舍五入）。计算出这四个值并赋值给变量后，语句的计算部分将生成最终结果。这里使用了 `Substitute` 语句中嵌套 `List` 语句、`List` 语句中再嵌套四个 `Case` 语句的序列。每个 `Case` 语句检查对应的值是否不为零，因为只有非零值才需包含在结果中。如果包含，则构建一个带有条件后缀的语句，例如“1 day（1 天）”或“10 days（10 天）”。这些语句片段通过 `List` 添加到一个以回车符分隔的值列表中，然后通过 `Substitute` 将其转换为一个以逗号加空格分隔的语句。

```
Let ( [
start = GetAsTimestamp ( "8/1/2021 10:00 AM" ) ;
end = GetAsTimestamp ( "8/2/2021 11:15:10 AM" ) ;
elapsed = GetAsNumber ( end - start ) ;
days = Int ( elapsed / 86400 ) ;
elapsed = elapsed - ( days * 86400 ) ;
hours = Int ( elapsed / 3600 ) ;
elapsed = elapsed - ( hours * 3600 ) ;
minutes = Int ( elapsed / 60 );
elapsed = elapsed - ( minutes * 60 ) ;
seconds = Int ( elapsed )
] ;
Substitute (
List (
Case ( days ≠ 0 ; days & " day" & Case ( days > 1 ; "s" ) ) ;
Case ( hours ≠ 0 ; hours & " hour" & Case ( hours > 1 ; "s" ) ) ;
Case ( minutes ≠ 0 ; minutes & " minute" & Case ( minutes > 1 ; "s" ) ) ;
Case ( seconds ≠ 0 ; seconds & " second" & Case ( seconds > 1 ; "s" ) )
) ; "¶" ; ", "
)
)
// 结果 = 1 day, 1 hour, 15 minutes, 10 seconds
```

### 将数字转换为语句

本示例使用 `Let`、`Case`、`Choose` 及其他函数将一个数字转换为语句。在 `Let` 语句的变量声明部分，从一个字段中提取数字并赋值给变量 `n`。`Length` 和 `GetAsText` 函数用于计算该数字的字符数，并将结果放入 `count` 变量中。接着，根据数字的位数，初始化三个变量，每个变量包含数字的一位。通过组合使用 `Left`、`Middle`、`Right`、`GetAsNumber` 和 `Case` 函数，将第一位（右侧）数字放入 `a`，第二位（中间）数字放入 `b`，第三位（左侧）数字放入 `c`。计算部分包含四个 `Case` 语句，它们根据位置数字有条件地插入每个值的文本表示形式，然后将这些片段拼接起来形成最终语句。根据输入的数字，该函数将返回从“1 Year（1 年）”到“Nine Hundred Ninety Nine Years（九百九十九年）”的结果。

```
Let ( [
|
n = Example Number ;
t = GetAsText ( n ) ;
count = Length ( t ) ;
a = GetAsNumber ( Right ( t ; 1 ) ) ;
b = Case ( count > 1 ; GetAsNumber ( Left ( Right ( t ; 2 ) ; 1 ) ) ) ;
c = Case ( count > 2 ; GetAsNumber ( Left ( t ; 1 ) ) )
] ;
Case ( c > 0 ;
Choose( c ; "";
"One "; "Two "; "Three "; "Four "; "Five "; "Six "; "Seven "; "Eight "; "Nine "
) & "Hundred " ) &
Case ( b > 1 ;
Choose( b ; ""; "";
"Twenty "; "Thirty "; "Forty "; "Fifty "; "Sixty "; "Seventy "; "Eighty "; "Ninety ")
) &
Case ( a > 0 and b ≠ 1 ;
Choose( a ; "";
"One "; "Two "; "Three "; "Four "; "Five "; "Six "; "Seven "; "Eight "; "Nine ")
) &
Case ( b = 1 ;
Choose( a ;
"Ten "; "Eleven "; "Twelve "; "Thirteen "; "Fourteen "; "Fifteen "; "Sixteen "; "Seventeen "; "Eighteen "; "Nineteen ") ) &
"Year" & Case ( n > 1 ; "s" )
)
// 结果（如果数字字段包含 32）= Thirty Two Years
```

## 总结

本章讨论了许多有用的内置函数。请记住，FileMaker 拥有超过 `300` 个内置函数，在编写公式时均可使用。后续章节还会提到更多此类函数，而所有函数都在 `指定计算` 对话框的 `函数` 窗格底部提示可访问的联机帮助指南中进行了描述。在下一章中，我们将继续探索内置函数，重点讲解 *JavaScript 对象表示法* (JSON) 函数。

