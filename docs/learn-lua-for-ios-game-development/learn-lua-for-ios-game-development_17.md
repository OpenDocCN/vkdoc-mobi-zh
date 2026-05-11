# Lua 库



## 基础函数

Lua 有一些基础函数，它们是 Lua 系统的一部分。我们将在下面的小节中了解它们。这些函数构成了核心库的一部分，通常可用于大多数发行版。

### `assert ( v [, message] )`

此函数类似于 C 语言中使用的`assert`函数；如果参数`v`的值为假（`nil`或`false`），则返回错误。如果提供了`message`，它将作为错误消息显示；如果未提供，则显示默认文本“assertion failed!”。

```
assert(money > 0,"you need to have some money to buy something")
```

### `collectgarbage ( [opt [,arg]] )`

此函数是垃圾回收器的通用接口。该函数根据参数`opt`的不同而表现不同。您可以作为`opt`传递给此函数的选项有：

*   `collect`：执行完整的垃圾回收周期。这是默认选项。
*   `stop`：停止垃圾回收器。
*   `restart`：重启垃圾回收器。
*   `count`：返回 Lua 使用的总内存。
*   `step`：执行一个垃圾回收步骤。步骤大小由`arg`控制。
*   `setpause`：将`arg`设置为`pause`的新值，并返回`pause`的先前值。
*   `setstepmul`：将`arg`设置为步进倍数的新值，并返回`step`的先前值。

**提示** 如果您想知道应用程序的内存使用情况并清除内存和对象，您可以强制垃圾回收器释放并回收已分配的内存，然后使用`print(collectgarbage("count"))`命令打印清理后使用的内存量。

### `dofile ( [filename] )`

您在第 1 章中了解到，Lua 以块的形式执行代码。此函数打开指定的文件并执行其内容作为 Lua 块。当不带参数调用时，它从标准输入（`stdin`）执行内容。它返回块返回的所有值。`stdin`在 iOS 设备上不可用，并且在 CoronaSDK 中该函数被沙盒化（即不允许使用）。

```
dofile("somefile.lua")
```

### `error ( message [,level] )`

此函数终止最后一个受保护函数，并将`message`作为错误消息返回。

```
error("This operations is invalid")
```

### `_G`

这严格来说不是一个函数，而是一个全局变量。Lua 不使用此变量，但它保存了所有全局变量和函数。

### `getfenv ( [f] )`

此函数返回函数当前使用的环境。函数`f`可以是 Lua 函数或数字。

### `getmetatable ( object )`

此函数检索与对象关联的元表。如果不存在元表，则返回`nil`。这通常用于向对象表添加功能。在某些情况下，这也用作对象的签名。因此，虽然`type`函数无法提供太多信息，但可以将元表与已知元表签名列表进行比较，以获取更多信息。

### `ipairs ( t )`

此函数返回一个函数，该函数返回三个值：一个迭代器函数、表`t`和`0`。它仅适用于数组表。以下代码将迭代到表中第一个缺失的整数键为止：

```
t = {1,2,3,4,test =  "test",5,6}
t[3] = nil
for i,v in ipairs(t) do
 -- body
  print(i,v)
end
```

### `load ( func [,chunkname] )`

此函数类似于`dofile`命令；它使用函数`func`加载一个块。每次调用该函数时，它必须返回一个与前一个结果拼接的字符串。当块完成时，它返回`nil`和一个错误消息。`chunkname`用于调试。当函数被调用并发生错误时，Lua 会显示调试信息，包括错误发生的行号和发生错误的 Lua 文件名。当我们有编译后的块时，没有文件名，因此，出于调试目的，需要由您（开发者）以`chunkname`参数的形式提供该信息。

### `loadstring ( string [,chunkname] )`

此函数类似于`load`，但不是从文件加载编译后的字节码，而是从字符串中获取编译后的代码（块）。

```
loadstring(compiledChunk, "OurChunk")
```

### `next ( table [,index] )`

此函数允许程序遍历表中的所有字段。该函数返回多个值。在内部，该函数执行系统命令。

```
t = {"One", "Deux", "Drei", "Quarto"}
print(next(t, 3))
```

### `pairs ( t )`

此函数用于遍历表中的键和值。

```
t = {one =  "Eins",two =  "Zwei", three =  "Drei"}
for k,v in pairs(t) do
 -- body
  print(k,v)
end
```

### `pcall ( f, arg1, … )`

此函数以保护模式调用函数`f`并传入参数。这意味着函数`f`内部的任何错误都不会传播到函数外部。

### `print( … )`

这是您在开发过程中最常使用的函数。它可以接收任意数量的参数，并将它们的所有值打印到`stdout`。它使用`tostring`和`tonumber`进行转换，并提供了一种快速显示值的简便方法。但是，它不适用于格式化输出。

### `rawequal ( v1, v2 )`

此函数用于检查值`v1`是否等于`v2`。它返回一个布尔值，指示比较的结果。

### `rawget ( table, index )`

此函数返回`table[index]`的值，而不调用任何元方法。`table`必须是一个有效的表，`index`必须是一个非`nil`的值。这等价于：

```
table[index]
```

## `rawest ( table, index, value )`

此函数设置`table[index]`的值，而不调用任何元方法。`table`必须是一个有效的表，`index`必须是一个非`nil`的值。这等价于：

```
table[index] = value
```

### `select ( index, … )`

此函数返回从指定索引之后开始传递给函数的所有参数。

### `setfenv ( f, table )`

此函数设置要赋予函数`f`的环境。`f`可以是一个 Lua 函数，也可以是一个指定平台值的数字。

### `setmetatable ( table, metatable )`

此函数用于为任何表设置元表。

### `tonumber( e [,base] )`

此函数用于将数字从字符串形式转换为数值形式。如果参数`e`处于可转换的形式，则进行转换并返回；否则返回`nil`。

```
print( tonumber("42") )
print( tonumber("2A",16))
```

### `tostring ( e )`

此函数尝试将给定参数转换为字符串格式。如果传递的参数是数字，则将其转换为字符串。如果它是一个对象并且其元表具有`__tostring`函数，则调用`__tostring`函数来转换传递的参数。

**注意** Lua 会根据需要（并在可能的情况下）在数字和字符串之间进行转换。然而，调用`tostring`函数是告诉 Lua 将值转换为字符串的一种方式。对象和表不会自动转换为字符串；而是调用`tostring`函数（如果存在），该函数返回对象/表的字符串表示形式。

### `type ( v )`



该函数返回参数的类型，以字符串形式编码。可能的结果有 `"nil"`、`"number"`、`"string"`、`"boolean"`、`"table"`、`"thread"` 和 `"userdata"`。

```
print(type("Hello World"))
print(type(4))
print(type({}))
```

`unpack ( list [, i [, j] ] )`

该函数返回数组表中的元素。该函数等价于：

```
return list[i], list[i + 1], . . . , list[j]
```

手动编写时，这段代码只能针对固定数量的元素编写，而且由于我们需要返回值，所以无法使用循环。参数 `i` 指定起始元素，参数 `j` 默认指定最后一个元素。（当未提供这些值时，`i` 为 `1`，`j` 为由 `#` 运算符定义的列表长度。）

```
local color = {255,255,255}
function tableToParams(theTable)
    return unpack( theTable)
end
print( tableToParams(color))
```

`_VERSION`

这不是一个函数，而是一个全局变量。它保存当前解释器的版本。

```
print ( _VERSION ) -- 这将返回当前使用的 Lua 版本
```

`xpcall ( f, err )`

此函数类似于 `pcall` 函数，区别在于使用此调用时，您可以指定一个新的错误处理程序。如果函数 `f` 内部出现错误，该错误不会被传播，`xpcall` 会捕获该错误，并用原始错误对象调用 `err` 函数。

```
function spawnError()
    -- 此函数将引发一个错误
    local  this = someFunctionNotDeclared()
end
print(xpcall(spawnError, function(err) print("Error:", err) return 1 end))
```

## 系统库

在使用 Lua 时，您最终会用到通常捆绑在一起的系统库包括：

*   `table`
*   `string`
*   `math`
*   `file`
*   `os`

`table` 命名空间提供了与数组操作相关的函数，因为这些函数不适用于哈希数组或关联数组。下一节将对此进行详细说明。

`string` 命名空间提供了处理字符串操作的函数。这些函数允许搜索、拆分和替换字符串，对于解析和显示信息非常有帮助。我们将在第 5 章中仔细研究 `string` 命名空间。

`math` 命名空间提供了所有与数学相关的函数；这些是大多数游戏中所有逻辑的基础。`math` 命名空间提供了所有与数学相关的函数，可以帮助计算诸如玩家位置、物体位置、玩家是赢是输等等。我们将在第 4 章中详细介绍这个命名空间。

`file` 命名空间，将在第 3 章中介绍，它提供了文件相关的函数，包括允许用户读取、写入和删除文件的函数。请注意，文件操作不包含太多与文件系统相关的功能，有一个名为 *Lua 文件系统 (LFS)* 的第三方库提供了缺失的功能。

`os` 命名空间提供了与操作系统特定功能相关的函数，包括处理时间、日期和地区等事物的函数。我们将在本章后面的“操作系统函数”部分进一步探讨。

使用这些库的方法是在库名后加一个点，再跟上函数名。这是将库中可用功能与公共或全局区域分开的好方法。有了库，变量和函数就可以成为命名空间的一部分。例如，如果我们有一个函数 `a1`，而命名空间 `myFunc` 中定义了一个名为 `a1` 的函数，那么这两个将是完全不同的函数。要访问这些函数，只需在函数名前面加上命名空间作为前缀，如下所示：

```
-- 调用全局的 a1 函数
a1()
-- 调用命名空间 myFunc 中的 a1 函数
myFunc.a1()
```

**注意**   实际上并没有名为 `a1` 的函数，因此这些命令不会生效；它们仅用于说明目的。

## 表函数

您可能已经注意到，在 Lua 中，如果某物不是字符串或数字，那么它就是一个对象（即一个表）。表是 Lua 中最重要的数据类型。有一些函数用于操作表并与之配合使用。这些是用于处理数组类型表的有用函数。可用的函数将在以下小节中描述。

`table.concat ( aTable [,sep [,i [,j] ] ] )`

该函数将数组的元素（如果它们是字符串或数字）连接成一个字符串并返回。这些元素由 `sep` 指定的分隔符分隔，默认情况下分隔符是一个空字符串。如果没有为 `i` 和 `j` 传递参数，则 `i` 是第一个元素，`j` 是表的长度。如果 `i` 大于 `j`，则返回一个空字符串。

如果您自己编写这个函数，它看起来会像 `return aTable[i]..sep..aTable[i + 1] . . . sep..aTable[j]`。

```
local aryTable = {1, "deux", 3, "vier", 5}
print( table.concat( aryTable, ",", 1,3))
print(table.concat(aryTable))
```

`table.insert ( aTable, [pos,] value )`

此函数用于向由 `aTable` 指定的数组表中插入一个值。该值被插入到由 `pos` 指示的位置；如果没有为 `pos` 指定值，则将其赋值为表的长度加 1 的默认值（即，在表的末尾）。

```
local aryTable = {1, 3, 4, 5}
table.insert(aryTable, 2, 2)
print(table.concat(aryTable, ",")
table.insert(aryTable, 6)
print(table.concat(aryTable, ",")
```

`table.maxn ( aTable )`

此函数返回给定数组表的最大正数值索引。回顾第 1 章中所述，`#` 运算符返回数组的长度，但仅限于连续部分的长度。因此，如果在程序中以非连续方式（即不连续）向索引添加值，那么 `#` 运算符将返回连续块的长度。而 `table.maxn` 则会遍历所有元素并返回最大的数值索引。

```
local aryTable = {1,2,3,4,5}
print(#aryTable, table.maxn(aryTable))
aryTable[10]= 10
print(#aryTable, table.maxn(aryTable))
aryTable[25]= 25
print(#aryTable, table.maxn(aryTable))
```

`table.remove ( aTable [, pos] )`

此函数类似于 `insert`，但区别在于它从数组表中移除一个元素并返回被移除的元素。在 `insert` 和 `remove` 中，元素会根据需要移动以适应新的索引。`pos` 的默认位置是表的长度（即最后一个元素）。

```
local aryTable = {1, 2, 3, 4, 5}
table.remove(aryTable, 2, 2)
print(table.concat(aryTable, ",")
table.remove(aryTable)
print(table.concat(aryTable, ",")
print(aryTable[3])
```

`table.sort ( aTable [, comp] )`

当处理一系列需要排序的值时，此函数非常有用。当未指定 `comp` 时，它将使用标准的 Lua `>` 运算符来比较两个值，这对于数值来说效果很好。然而，在某些情况下，您可能需要排序多维数组或包含非数值数据的数组。对于这种情况，需要使用 `comp` 函数。`comp` 接收两个表元素，当第一个元素小于第二个元素时返回 `true`。

```
local aryTable =  {4,7,1,3,8,6,5,2}
print(table.concat(aryTable, ","))
table.sort(aryTable)
print(table.concat(aryTable, ","))
```

**注意**   这些函数只能用于数组类型的表，并且位于 `table` 命名空间中。为了使用这些函数，您需要给它们加上 `table` 前缀。

## 操作系统函数

有几个可用于与操作系统交互的函数；它们位于 `os` 命名空间中，因此以 `os` 为前缀。以下小节描述了它们的使用方式。

`os.clock ( )`

此函数返回程序所使用的近似 CPU 时间，以秒为单位。每次执行一些代码时，该值都会增加代码所使用的 CPU 时间量。


```lua
print(os.clock())       -- > 0.030148 (在你的系统上可能不同)
print(os.clock())       -- > 0.030250 (在你的系统上可能不同)
```

**注意**：这并非返回系统时钟的时间；它只是已使用的总 CPU 时间。

`os.date ( [格式 [,时间] ] )`

该函数返回一个字符串或一个包含日期和时间的表，其格式由给定的字符串`format`指定。如果未传递参数，则基于主机系统和当前区域设置返回包含当前日期和时间的字符串。如果格式为`"*t"`，则该函数返回一个包含以下字段的表：

*   `year`（四位年份）
*   `month`（1-12）
*   `day`（1-31）
*   `hour`（0-23）
*   `min`（0-60）
*   `sec`（0-60）
*   `wday`（1-7，从星期日开始）
*   `yday`（一年中的第几天；1-366）
*   `isdst`（布尔值，表示是否处于夏令时）

`os.difftime ( t2, t1 )`

该函数返回`t2`与`t1`之间的秒数差。

`os.execute ( [命令] )`

该函数相当于 C 语言的`system`命令。它在操作系统 shell 中执行传递的命令。它还会返回状态码，该状态码高度依赖于系统。该函数不适用于 iOS 系统，因为它会被沙盒化而无法执行。

`os.exit ( )`

该函数调用 C 函数`exit`，用于终止应用程序。虽然它在 iOS 设备上有效，但不应使用。任何以此方式退出的应用都会被视作崩溃。应用退出的唯一方式应该是用户明确按下 Home 键退出。如果你的应用使用此函数，可能会面临被苹果拒绝的风险。话虽如此，还是有少数应用设法避开了苹果的雷达，以此方式退出应用（其体验确实与应用崩溃时一样）。

`os.getenv ( 变量名 )`

该函数不适用于 iOS 设备。

`os.remove ( 文件名 )`

该函数删除指定名称的文件或目录。空目录会被删除。如果函数执行失败，它会返回`nil`以及描述错误的字符串。该函数仅适用于你可以操作的文件和目录（即 iOS 设备上的 Documents 目录）。其他目录都被沙盒化且不可访问。

`os.rename ( 旧名称, 新名称 )`

该函数将名为`oldname`的文件或目录重命名为`newname`。如果函数执行失败，它会返回`nil`以及描述错误的字符串。该函数仅适用于 Documents 目录中的文件。

**注意**：之所以它只对 Documents 目录中的文件有效，是因为大多数其他目录都被沙盒化或限制使用。任何尝试访问受限目录的命令都会失败且不执行任何操作。

`os.setlocale ( 区域设置 [,类别] )`

该函数在 iOS 设备上不可用。

`os.time ( [表] )`

该函数在无参数调用时返回当前时间。作为参数传递的表应至少设置`year`、`month`和`day`字段。其他字段如`hour`、`min`、`sec`和`idist`是可选的。

`os.tmpname ( )`

该函数返回一个可用于临时文件的文件名字符串。此文件需要显式打开，并在不需要时显式删除。这与`io.tmpfile`函数（将在下一章讨论）不同，后者会在程序结束时自动删除文件。

## 表：快速概览

我之前提到过，表是 Lua 中众多变量类型之一。虽然听起来可能无关紧要，但表是 Lua 的支柱，它们有多种用途：作为数组、哈希表（关联数组）和对象，以及更多其他用途。以下小节将讨论作为数组和哈希表的表。

### 作为数组的表

Lua 中数组的等价物就是表。Lua 中所有数组的索引基数都是 1，这意味着第一个条目的索引从 1 开始（而不是像许多其他语言那样从 0 开始）。数组需要数字索引。它们可以是多维的，并且在声明时不需要定义维度。它们可以在运行时增长和调整维度。

```lua
local theArray = {}
for i = 1, 10 do
  theArray[i] = "item " .. i
end
```

### 作为关联数组的表

表是 Lua 中关联数组、哈希表甚至`NSDictionary`对象的等价物。数组和关联数组之间的区别在于，关联数组可以使用字符串来访问元素，而不是使用数字索引来访问数组表中的元素。

```lua
local theTable = {}
for i = 1, 10 do
  theTable["item"..i] = i
end
```

**注意**：Lua 非常灵活，因此即使你混合了这两种数组类型，它也非常宽容。

```lua
local theMixedTable = {}
for i = 1, 5 do
  theMixedTable[i] = "item ".. i
end
theMixedTable["name"] = "mixedTable"
theMixedTable["subject"] = "coding"
print(theMixedTable.name, theMixedTable[3])
```

## 函数：进阶探讨

在上一章中，我们了解了函数以及 Lua 如何看待它们。Lua 对象和结构是 Lua 表，而好的方面在于表可以包含函数以及其他表、字符串和数字。实际上，每个带有函数的对象就像一个拥有自己专属函数集的命名空间。要访问特定对象的函数，你需要使用该命名空间作为前缀来访问该函数。

```lua
myObj = {
    type = "furry",
   name =  "Furry_1",
   description = "Fluffy furry teddybear",
  display = function ()
    print(description)
 end
}
myObj.show = function()
 print("Now Showing. . .")
end
```

要调用函数，请使用以下方式：

```lua
myObj.display()
myObj.show()
```

### 作为对象的表

如你所见，表可以用作对象。在下一个示例中，我们将创建一个名为`myObj`的对象，它具有`type`、`name`、`description`等属性。在这个示例中，你将创建一个游戏，该游戏生成一系列动物，每个动物具有不同的力量值和不同的移动功能。

由于某些功能可能重叠，为了避免一次又一次地编写相同的代码，你将基于父对象创建一个新对象，以便该对象可以使用所有功能。（我们将在本书后面创建此类对象。）因此，当传递此对象时，你可以简单地调用所有动物共有的`move`函数，但该函数可能根据每个动物的类型而具有特异性。

管理此问题的一种方法如下：

```lua
local cat_01 = animal.new("cat")
local mouse_01 = animal.new("mouse")
animal.move(cat_01)
animal.move(mouse_01)
```

这对某些人来说可能有效；然而，管理此问题的另一种方法如下：

```lua
local cat_01 = animal.new("cat")
local mouse_01 = animal.new("mouse")
cat_01:move()
mouse_01:move()
```

此外，如果你将它们放在一个动物数组中，你几乎可以轻松地编写如下代码：

```lua
local i
for i =  1,#aryAnimals do
  aryAnimals[i]:move()
end
```

或者你也可以使用类似这样的方式：

```lua
aryAnimals[i].move(aryAnimals[i])
```

请注意这里函数是如何使用点号（`.`）或冒号（`：`）来调用的。这是许多 Lua 初学者会遇到的问题，他们经常对例如`cat_01:move()`和`cat_01.move()`之间的区别感到困惑。下一节将描述其区别。

### `.` 和 `:` 的区别

在前面的示例中，调用`cat_01.move()`和`cat_01:move()`之间没有区别。但假设在同一个函数中，我们还需要传递一个参数`speed`，表示动物移动的速度。那么我们可以简单地传递速度作为参数：

```lua
cat_01:move(10)
```

或者

```lua
cat_01.move(10)
```


这在表面上看起来是对的，但会产生两个截然不同的结果。原因在于，当我们使用 `.` 时，我们是在告诉 Lua 调用对象（表）的成员函数；而当我们使用 `:` 时，我们是在告诉 Lua 执行此操作，同时还会向该函数传递一个不可见的自身参数。因此，当我们使用 `:` 调用函数时，我们传递了两个参数：第一个是对象本身，第二个是速度。而使用 `.` 方法时，我们只传递了一个参数，即速度。让我们看看它在代码中是如何定义的（参见图 2-1）。

![9781436246626_Fig02-01.jpg](img/9781436246626_Fig02-01.jpg)

图 2-1 . Lua 在终端中运行该函数的输出结果

请注意，函数的声明方式不同，但两者的输出是相同的。简而言之，你可以说 `obj:func()` 等同于调用 `obj.func(obj)`。

**注意**  人们很容易忘记函数声明的方式，从而以不同的方式调用它，这可能导致代码中出现一些意外情况，例如传递的参数被移位，从而产生奇怪的结果。为了避免这种情况，请保持声明和调用函数的风格一致。

从上一个例子中，你可能会想到一个问题：如果 Lua 不是面向对象的语言，那么什么时候使用点号（`.`），什么时候使用冒号（`:`）？为什么我需要这个？为了回答这些问题，让我们来操作一个对象并赋予它一些功能。这个示例没有使用对象或外部文件，因此可以在 Lua 交互式控制台中毫无问题地运行。

```
function newAnimal(name)
     local animal = {
     name = "unknown",
     says = "pffft",
     position = {x =  0,y =  0},
     }
     animal.name = name
     if name=="cat" then
          animal.says = "meow"
     elseif name=="dog" then
          animal.says = "bow wow"
     elseif name=="mouse" then
          animal.says = "squeak"
     end
     function animal:speak()
          print(animal.says)
     end
     function animal:move(speed)
          animal.position.x = animal.position.x + speed
     end
     return animal
end
```

现在，我们可以通过简单地调用以下代码来创建一个新的动物实例：

```
cat_01 = animal.new("cat")
cat_01:speak()
```

作为一个示例，它在每次被调用时创建一个新对象是完全没问题的。然而，这也是许多初学者编写代码的方式——请注意，在函数中，虽然我们通过 `self` 传递了对象，但我们并没有使用它，仍然通过我们创建的固定 `animal` 表来引用它。如果一开始不检查，这稍后可能会带来一些麻烦。为了避免将来出现这个问题，我们可以将命名空间替换为以 `self` 引用的对象。

修改后的代码如下所示：

```
function newAnimal(name)
     local animal = {
     name = "unknown",
     says = "pffft",
     position = {x =  0,y =  0},
     }
     animal.name = name
     if name=="cat" then
          animal.says = "meow"
     elseif name=="dog" then
          animal.says = "bow wow"
     elseif name=="mouse" then
          animal.says = "squeak"
     end
     function animal:speak()
          print(self.says)
     end
     function animal:move(speed)
          self.position.x = self.position.x + speed
     end
     return animal
end
```

这样就能正常工作，并且不会引发问题。

```
cat_01 = animal.new("cat")
cat_01:speak()
cat_02 = animal.new("dog")
cat_02:speak()
```

当我们运行这段代码时，函数接收到的是对象本身，而不是 `Animal` 命名空间。这也能确保被修改的变量是特定于该对象的，而不是 `Animal` 命名空间中的那些变量。这样就能消除前面提到的问题。

## 总结

本章重点介绍了函数和表，它们是 Lua 编程的基石。务必记住，除了字符串、数字和布尔值之外，所有一切都是表。表在 Lua 中非常强大且重要。在本书的大部分章节中，你都会接触到大量关于表的内容（以某种形式）。

我们提到的另一个重要概念是命名空间。命名空间有助于区分主命名空间中的函数（全局函数）和自定义库中的函数。

