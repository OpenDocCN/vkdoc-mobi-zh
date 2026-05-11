# 文件 I/O 在游戏中的用途

虽然这显而易见，但读取或写入存储数据的原因有很多。它可以是用于关卡数据和高分等小事情，也可以是用于保存包含整个库存、单位等游戏状态的复杂事情。在游戏中，你有责任保存和加载数据，以帮助为玩家提供连续性和无缝的游戏体验。

让我们看看在处理生命值、健康值、分数、等级等内容时，我们将如何做到这一点。

### 保存变量

在许多游戏中，你通常只有大约三条命。因此，请考虑以下代码：

```
local lives = 3
print("You have " .. lives .. " lives left")
```

处理此问题的方法有很多。一个好方法是，在更改数据后立即保存数据——类似于自动保存选项。所以，让我们将 `lives` 变量写入存储，以便它持久化。

```
function writeToFileSingle(value, filename)
    local hfile = io.open(filename,"w")
    if hfile==nil then return end -- 未能获取文件句柄
    hfile:write(value)
    hfile:close()
end
```

在前面的代码中，我们声明了一个名为 `writeToFileSingle` 的函数，它接受两个参数：`value` 和 `filename`。该函数打开一个文件，将数据写入文件，然后关闭文件。如果我们打开了一个文件，我们也有责任关闭它。当我们尝试使用 `io.open()` 函数打开文件时，它会返回文件句柄和一个包含失败原因（如果打开文件失败）的字符串。

要使用此函数保存我们的值，我们只需调用：

```
writeToFileSingle(score, "score")
writeToFileSingle(health, "health")
```

等等。

恭喜你，你现在已经编写了保存变量的代码，并且可以按照预期在交互式 Lua shell 中运行。

**注意**：无论你作为开发者选择哪个平台，上述 Lua 代码都应该能在大多数平台和 Lua 框架上运行。

### 获取数据

除了保存数据，我们还需要检索数据。在游戏中，丢弃无法检索的数据与根本不保存数据一样糟糕。我们使用之前的函数 `writeToFileSingle()` 将数据保存到了变量中。现在到了棘手的部分：从文件中读取值。

理论上，从文件读取数据应该是写入文件的逆过程。所以，我们打开保存数据的文件，然后从中读取所有数据。

```
function readFromFileSingle(filename)
    local hfile = io.open(filename, "r")
    if hfile==nil then return end
    local value = hfile:read("*a")
    hfile:close()
    return value
end
```

由于我们使用 `writeToFileSingle()` 函数保存了数据，我们可以轻松地检索数据，如下所示：

```
print(readFromFileSingle("score"))
print(readFromFileSingle("health"))
```



**注意** 由于每个文件只包含一条数据，因此保存和加载数据的顺序无关紧要。

如果文件没有任何数据，它只会返回 `nil`，这基本意味着相同的情况，并且在需要时可以进行处理。

#### 代码工作原理

这相当简单。大部分代码与我们之前在 `writeToFileSingle` 中使用的代码类似，都涉及打开文件，但此处是读取模式（如果在打开文件进行读取时未指定模式，则默认模式为读取模式）。然后我们使用带有 `"*a"` 参数的 `read` 函数来读取文件的全部内容。这种情况非常适合，因为我们要读取的数据量很小。接着我们关闭文件，并返回从文件中读取的数据。

### 潜在问题

这两个函数在加载和保存数据方面表现良好。但如前所述，为每一条要保存的数据都创建一个文件并不好；最好是只有一个文件，而不是多个包含零散数据的文件。因此，下一个可行的方案是将所有数据保存到一个文件中。这样做与之前代码的区别不大，不同之处在于我们要向文件中保存多个值，如下所示：

```
function readFromFile(filename)
    local results = {}
    local hfile  = io.open(filename)
    if hfile==nil then return end
    for line in hfile:lines() do
       table.insert(results, line)
    end
    return results
end
```

此代码与之前的代码相比有几个不同之处。首先，我们使用一个名为 `results` 的数组表来存储从文件中读取的所有内容。其次，我们没有使用 `hfile:read("*a")` 读取内容，而是使用 `hfile:lines()` 逐行从文件获取数据。换行符（Enter）用于分隔数据，是两行数据之间的分隔符。获取数据后，我们使用 `table.insert` 函数将数据添加到表末尾，从而将其存储到数组 `results` 中。

一旦数组返回给我们，数据的位置就很重要了。数据的位置必须是固定的，因此我们需要提前知道哪个位置对应哪条数据。

### 将数据保存到变量

这里有一个关于如何操作全局变量的快速技巧。在第 2 章中，你了解了一个变量 `_G`，它实际上是一个表对象，保存了所有全局变量、函数等。当我们声明一个变量时（没有局部作用域），它会作为全局变量被赋值到这个表中。我们在这个快速练习中要做的是：声明一个全局变量，读取该变量的值，然后使用 `_G` 表来操作它。

```
myVar = 1
print(myVar)
myVar = myVar + 1
print(_G["myVar"])
_G["myVar"] = _G["myVar"] + 1
print(myVar)
_G.myVar = _G.myVar + 1
print(myVar)
```

#### 代码工作原理

我们从声明一个名为 `myVar` 的全局变量并将其赋值为 `1` 开始，然后将其打印到屏幕上。接下来，我们使用 `myVar = myVar + 1` 将其增加 `1`。这次我们再次打印该值，但不是使用 `print(myVar)`，而是通过 `print(_G["myVar"])` 直接从全局表 `_G` 中访问它。然后我们再次将变量增加 `1`，但这次使用 `_G["myVar"]`，并通过 `print(myVar)` 打印该值。

最后，我们再次将值增加 `1`，但这次使用 `_G.myVar` 方法来访问值并打印结果。这个练习的目的是演示 Lua 如何使用全局变量，以及如何通过 `_G` 访问它们。

**注意** 虽然可以以这种方式访问全局变量，但建议仍使用局部变量；这样可以减少使用的变量数量，节省栈内存，并且相比全局变量还拥有速度优势。

### 回到数据的保存与加载

我们希望将数据整合到一个文件中，以便一次性读取数据。在修改后的版本中，我们将数据读取到一个名为 `results` 的数组中。唯一的问题在于，要访问这些值，我们需要通过数字索引来访问数据——但我们如何知道每个数字索引对应什么内容呢？

让我们尝试读取数据并同时用这些值设置全局变量。

```
function readFromFile(filename)
    local results = {}
    local hfile = io.open("filename")
    if hfile==nil then return end
    local lineNo = 0
    for line in hFile:lines() do
        table.insert(results, line)
        lineNo = lineNo + 1
        if lineNo == 1 then
            _G["score"] = line
        elseif lineNo == 2 then
            _G["lives"] = line
        elseif lineNo == 3 then
            _G["health"] = line
        elseif lineNo == 4 then
            _G["level"] = line
        end
    end
    return results
end
```

这基本上与我们之前的代码相似，只是添加了一个名为 `lineNo` 的变量，并根据读取的行数不断递增它。当 `lineNo` 为 `1` 时，我们将读取到的值保存到变量 `score` 中。然后将 `lineNo` `2` 保存到 `lives`，依此类推，直到 `lineNo` 为 `4`，之后（如果还有更多行）我们就不再读取它们。

### 潜在问题

在你的应用中，可能存储的变量数量因应用而异。你永远不知道数据保存的顺序，如果保存或读取数据的顺序发生变化，可能会导致值存入了错误的变量。最重要的是，要设置多个 `if ... then` 语句来加载变量几乎是不可能的，更不用说为这些变量命名了。

为了解决这个问题，我们将创建一个包含数据和字段名称的表。这将为我们将来进一步扩展提供灵活性。

```
function readFromFile(filename,resTable)
    local hfile = io.open(filename)
    if hfile == nil then return
    local results = {}
    local a = 1
    for line in hfile:lines() do
        _G[resTable[a]] = line
        a = a + 1
    end
end
```

我们逐行读取数据，每次循环递增索引变量 `a`。我们将读取到的值存储到一个全局变量中，该变量的名称由数组指定。例如，如果我们在数组中指定希望 `Line1 = Score`, `Line2 = Lives`, `Line3 = health`，那么函数将自动创建具有这些名称的全局变量并为其赋值。

现在，这个函数将读取数据，将其保存到数组表中，并通过全局变量进行访问。不过，我们需要为此传入要修改的表。

```
local aryTable = {
    "Score",
   "Lives",
   "Health",
}
```

我们只需如下调用函数：

```
readFromFile("datafile", aryTable)
```

这种方法本身存在一些问题。如果我们在 `aryTable` 中间（而不是末尾）添加一些数据，会怎样？这会导致变量全部混淆，位置错乱。那么如何解决这个问题呢？有很多简单的方法可以处理这个问题，每种方法都各有优缺点。不过，最好的方法是从文件中获取变量名及其值。

### 将数据写入文件

我们已经编写了一些代码来从文件读取数据。现在，我们将编写一些代码来写入数据，这些数据可以使用前面的函数读取。

```
function writeToFile(filename, resTable)
    local  hfile = io.open(filename, "w")
    if hfile == nil then return end
    local  i
   for i = 1, #resTable do
        hfile:write(_G[resTable[i]])
   end
end
```



将数据写入文件的代码与之前差异不大。我们以写入模式打开文件，然后遍历要保存到文件中的变量列表。这些变量来自传入的`resTable`参数。因此，无论此表中有何内容，该函数都将尝试将这些变量保存到文件中。

### 保存表

我们刚才保存的数据要么是字符串，要么是数字形式。如果要保存的数据是其他类型，函数就会开始出错，如果变量是一个表，则问题更严重。表的最大难点在于遍历表的深度。

如果你使用任何其他编程语言，你会听到这样的建议：“为什么不序列化数据？” 我将解释为什么这在 Lua 中不是一个好主意。

优秀开发者的标志是能够避免重复造轮子，而是将时间花在值得关注的事情上。Lua 有一个第三方 Lua 库，允许你在 Lua 项目中使用 JSON；这个库可以帮助你在表对象和 JSON 编码的字符串之间进行编码或解码信息，如下所示：

```lua
local json = require("json")
local theTable = {
    title = "Learn Lua for iOS game development",
    author = "Jayant Varma",
    publisher = "Apress",
    eBook = true,
    year = 2012,
}
local resString = json.encode(theTable)
writeToFileSingle(restring,"mytable")
local readString = readFromFileSingle("mytable")
local resTable = json.decode(readString)
for k,v in pairs(resTable) do
    print(k,v)
end
```

运行这段代码时，你会看到我们将创建的数据作为字符串保存到名为`mytable`的文件中。在示例中，我们读取回数据，并将 JSON 编码的字符串解码为展开的 Lua 形式表。然后，我们列出该表中可用的字段，以证明代码可以工作。

**注意**  尽管这段代码完全有效，但如果你在 Lua 交互式 shell 中运行它，可能会遇到错误消息等问题，因为路径中没有`json.lua`文件。

### 动态变量

如何创建动态变量？这是许多开发者，尤其是那些刚踏上开发之旅的人，最常问的问题之一。一些初学开发者会像下面这样使用变量：

```lua
Object1 = "Apple"
Object2 = "Ball"
Object3 = "Cat"
Object4 = "Dog"
```

在这段代码中，开发者希望动态创建一系列这样的变量，并通过指定最后一个数字来访问它们，因此期望的访问方式是：

```lua
number = 3
print("Object" .. number)
```

并且期望它应该输出`Cat`。然而，它实际输出的是`Object3`。大多数初学开发者认为它本应正常工作。如果你需要以这种方式访问变量，那并不是通过创建多个变量来实现的。正确的方法是创建一个数组。可以通过按预期传入数字来访问该数组。

```lua
Objects = {"Apple", "Ball", "Cat", "Dog"}
number = 3
print(Objects[number])
```

这对于我们的需求来说完美运行。这次我们看到了输出`Cat`。

### 本章小结

在本章中，你学习了与文件 I/O 相关的隐式和显式函数。你还了解了如何读取和写入单个值的数据，以及如何将整个数据表写入设备上的存储空间。文件适用于保存少量数据；然而，当你需要访问大量数据时，最好使用数据库。

在下一章中，我们将介绍数学命名空间，它提供了我们可以用来为应用程序执行所有必要计算的函数。

