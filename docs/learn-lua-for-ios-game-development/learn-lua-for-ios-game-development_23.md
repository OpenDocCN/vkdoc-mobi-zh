# 第 7 章：技巧和诀窍

许多开发者面临的困境，正如老话所说，达到目的的方法不止一种。简单来说，尽管有多种方法可以实现某个结果，但许多开发者面临的问题是如何开始以及从哪里开始。本章将帮助你找到这些切入点。它将提供大多数框架都可以应用的技巧和诀窍。主要需要注意的区别是框架使用的原生显示对象以及与之相关的一些特殊问题。

## 通用 Lua 函数

本节介绍一些通用函数，它们可以帮助开发者，并可以跨所有框架使用。其中大多数与用于存储、检索和索引的表，以及与游戏开发相关的数学运算有关。

### 使用 `printf` 函数

Lua 有一个 `print` 函数，但它不像 C 语言的 `printf` 那样支持格式化输出。拥有格式化输出并且读起来像句子一样连贯的输出总是很好的，而不是零散信息拼凑在一起。例如，"数学班有 5 个男孩和 4 个女孩"，而不是使用以下代码输出的"男孩：5 女孩：3，班级：数学"：

```lua
print("Boys:", boys, "Girls:", girls, "Class:", className)
```

`printf` 函数非常简单，可以按如下方式使用：

```lua
function printf(format,  ...)
  print(string.format(format,  ...))
end
```

以下是该函数的使用示例：

```lua
local boys = 5
local girls = 3
local className = "Math"
print("Boys:", boys, "Girls:", girls, "Class:", className)
printf("There are %d boys and %d girls in the %s class", boys, girls, className)
```

### 计算表中的元素数量

表在第 2 章中介绍过。存储为数组的表可以通过 `#`（长度）运算符返回元素的数量。但是在处理非数组表或数组组合表时，此功能无法按预期工作。为了解决这个问题，我们可以编写一个快速函数来获取任何表类型的长度：

```lua
function tableLen(theTable)
  local count = 0
  for i,j in pairs(theTable) do
    count = count + 1
  end
  return count
end
```

我们只需遍历表中的每个元素，递增我们的 `count` 变量，然后返回 `count` 值作为结果。

### 使用 `isEmpty`

有时，你想知道一个变量是否为空。但*空*的定义取决于变量的类型。数值变量为零值会被视为空，没有字符的字符串为空，没有元素的表为空，而所有这些变量，如果为 `nil`，也可以被视为空。我们可以使用之前示例中创建的 `tableLen` 函数来确定表类型变量的长度。

```lua
function isEmpty(var)
    if var == nil then return true end

    local varType = type(var)

    if varType == "number" and var == 0 then
        return true
    elseif varType == "string" and #var == 0 then
        return true
    elseif varType == "table" and tableLen(var)==0 then
        return true
    end

    return false
end
```

### 查找元素的索引

在使用表（数组对象）时，如果我们知道元素的索引，就可以获取该元素（例如，如果我们有一个姓名列表，并且知道第五个元素是 `Jayant`，那么我们可以通过 `arrTable[5]` 访问它）。然而，在某些情况下，我们可能不知道索引，但知道值。如果我们想知道表中哪个元素的值是 `Jayant`，可以使用 `indexOf` 函数：

```lua
function indexOf(arrTable, theValue)
    for i, j in pairs(arrTable) do
        if j == theValue then
            return i
        end
    end
end
```

这也适用于非数组表，但返回值将不是数字索引，而是字符串类型的键。

### 判断表是否为数组

在某些情况下，你可能需要知道一个表是哈希表还是数组。我们可以判断这一点，因为 `#` 运算符返回数组中的元素数量，而对于表，它返回 0 或它可以确定为数组的元素数量。非数组表指那些元素数量超过 `#` 运算符返回数量的表。

```lua
function isArray(arrTable)
  return #arrTable == tableLen(arrTable)
end
```

### 设置默认值

如果你使用过其他语言进行开发，你可能习惯于检查值并确保它们已被设置，因为缺失值可能导致错误。你可能熟悉类似下面的写法：

```lua
function foo(bar)
  if bar == nil then
    bar = "Untitled"
  end

  print("We got :", bar)
end
```

在使用 Lua 时，这其实不是问题；我们可以像这样去掉所有这些条件语句：

```lua
function foo(bar, baz, faz)
  local bar = bar or "Bar"
  local baz = baz or "Baz"
  local faz = faz or "Faz"

  print("We got", bar, baz, faz)
end
```

注意，我们通过简单地使用 `or` 来设置变量的默认值。一个好的做法是使用同名的局部变量。

这不仅有助于为未赋值（即 `nil`）的变量分配默认值，也有助于创建空表，你可以像这样使用它：

```lua
local theTable = theTable or {}
```

### 复制表

复制表时需要提醒一点：表类似于指针，所以当你用表变量赋值给一个新变量时，它只是复制了指向表存储位置的引用，而不是表的副本。

```lua
function copyTable(table1)
  local res = {}
  for i, j in pairs(table1) do
    res[i] = j
  end
  return res
end
```

请注意，上述函数被称为*浅拷贝*，因为它不会复制嵌套的表，所以如果表中有任何嵌套表，这些不会被复制。要进行精确复制，我们需要执行所谓的*深拷贝*。

### 执行深拷贝

如前一小节所述，要完整创建表的副本，我们需要进行深拷贝。这将递归复制所有元素，并根据需要设置元表。普通的复制不会设置元表。

```lua
function deepcopy(t)
  if type(t) ~= 'table' then
    return t
  end
  local mt = getmetatable(t)
  local res = {}
  for k,v in pairs(t) do
    if type(v) == 'table' then
      v = deepcopy(v)
    end
    res[k] = v
  end
  setmetatable(res,mt)
  return res
end
```

**注意：** 如果你的表中存在循环引用，该函数将无法处理。

## 复制数组部分

在某些情况下，你可能只想复制数组部分，而不是整个表，如下所示：

```lua
function copyList(table 1)
  local res = {}
  for i,j in ipairs(table 1) do
    res[i] = j
  end
  return res
end
```

如果你认为前面两个函数是相同的，请注意：在第一个函数中我们使用了 `pairs` 函数，而在第二个函数中我们使用了 `ipairs`。

## 复制非数组部分

如果你需要复制非数组部分而保留数组部分不变，可以按如下方式操作：

```lua
function copyHash(table 1)
  local res = {}
  local size = #table 1
    for i, j in pairs(table 1) do
      if type(j) ~= "number" or i<= 0 or i> size then
        res[i] = j
      end
    end
  return res
end
```

## 合并两个表

如果你需要合并两个表，可以这样做：

```lua
function mergeTables(table 1, table 2)
  for i,j in pairs(table 1) do
    table 2[i] = j
  end
end
```

请注意，如果存在数组值，`table 2` 中的值将覆盖这些值。

## 判断表中是否存在某个值

如果你需要检查某个值是否存在于表中，可以按以下方法操作：

```lua
function isValueInTable(theValue, theTable)
  for i,j in pairs(theTable) do
    if j == theValue then
      return true
    end
  end
  return false
end
```

## 查找两个表之间的差异

在你的游戏中，你可能需要让玩家收集物品，以便在关卡结束时显示已收集的物品。但你可能还想显示那些本可以收集但实际未收集的物品。一个简单的方法是使用两个表——一个存储已收集的物品，另一个存储所有可能收集到的物品。然后，使用以下函数，你可以确定存在于 `table 2` 但不存在于 `table 1` 中的物品：

```lua
function differenceOfTables(table1, table2)
  local res = {}
  for i, j in pairs(table1) do
    if not isValueInTable(j,table2) then
      table.insert(res, j)
    end
  end
end
```

## 将返回值作为表获取

某些情况下，你会期望得到一个表，但如果返回值是一个数字或字符串，它就会破坏你的代码并导致错误。你可以通过以下方法确保返回的是一个表值：

```lua
function getTable(theValue)
  if type(theValue) == "table" then
    return theValue
  else
  return {theValue}
  end
end
```

## 对表元素进行排序

`table.sort` 函数对于排序一维且包含数字或字符串值的表非常有用。如果一个表是如下结构的记录，那么通用的排序函数将无法正常工作：

```lua
{
name = "Jayant",
subject = "IT",
grade = "A"
}
```

我们可能需要根据 `grade`、`name` 或 `subject` 这三个字段之一对这些记录进行排序。由于这是在多个维度上排序，`table.sort` 函数将无法正常运作。

我们使用 `table.sort(theTable, functionToSort)` 函数对表进行排序。针对这个示例，我们可以使用：

```lua
function sortOnField(theTable, theField)
  table.sort(theTable,
    function(a,b)
        return a[theField] < b[theField]
    end)
end
```

如果表是一个多维数组，你只需传递维度参数代替字段名称，即可正常工作。

## 确定表中某个项目的出现频率

表可以用作存储空间。例如，你可以保存玩家在游戏中收集的所有物品，以便基于收集到的物品使用乘数来计算关卡结束时的得分。为此，你可能需要知道表中特定类型物品的数量。如果这些是简单的数字，你可能想知道这些数字在表中出现的频率。

```lua
function getCount(theTable, theValue)
  local count = 0
  for i,j in pairs(theTable)
    if j == theValue then
      count = count + 1
    end
  end
  return count
end
```

## 将数字转换为罗马数字

如果你需要将数字转换为罗马数字，可以使用 `toRoman` 函数，如下所示：

```lua
function toRoman(theNumber)
  local res = ""
  local romans = {
      {1000, "M"}, {900, "CM"}, {500, "D"}, {400, "CD"}, {100, "C"},
      {90,  "XC"}, {50, "L"}, {40, "XL"}, {10, "X"}, {9, "IX"},
     {5,   "V"}, {4, "IV"}, {1, "I"}
    }
  local k = theNumber
  for i,j in ipairs(romans) do
    theVal, theLet = unpack(j)
    while k >= theVal do
      k = k – theVal
      res = res .. theLet
    end
  end
  return res
end
```

## 创建链表

在 Lua 中管理数据的方法比我们目前看到的更高效。但是，如果你想在 Lua 中实现链表，可以按以下方式操作（请注意，你需要将此代码放在名为 `linkedlist.lua` 的文件中）：

```lua
local _lists = {}

function _lists:create(theVal)
  local root = { value = theVal,  next = nil}
  setmetatable(root, {__index = _lists })
  return root
end

function _lists:append(theVal)
  local _node = { value = theVal, next = nil}
  if(self.value == nil) then
    self.value = _node.value
    self.next = _node.next
    return
  end

local currNode = self

while currNode.next ~= nil do
    currNode = currNode.next
  end

currNode.next = _node

return
end

function _lists:delete(theVal)
  if type(theVal) == "table" then
    print("Cannot remove a table value from the list")
    return
  end

local currNode = self

if(self.value == theVal) then
    local temp = self.next
    if temp ~= nil then
      self.value = temp.value
      self.next = temp.next
    else
      self.value = nil
      self.next = nil
    end
    return
  end

lastVisited = currNode

while currNode ~= nil do
    if currNode.value == theVal then
      lastVisited.next = currNode.next
      currNode = nil
      return
    end

lastVisited = currNode
    currNode = currNode.next
  end

return

end

function _lists:displayList()
  local currNode = self
  if(self.value == nil) then
    print ("No entries in this list")
    return
  end
  while currNode ~= nil do
    print(currNode.value)
    currNode = currNode.next
  end
end
```

## 扩展标记化变量

在许多 RPG 风格游戏中，当故事情节展开时，可能会要求你输入名字，之后故事会从该点开始继续使用你的名字。管理此问题的一种方法是将名字存储在变量中，并使用 `printf` 函数进行模板化处理，如下所示：

```lua
printf("The %s Wizard looks at you and says, %s, you are worthy", wizardAdjective, yourName)
```

这种方法可行，但要求你将变量放置在精确的位置，以便它们与所需的每个标记匹配。如果你漏掉了 `wizardAdjective`，就会收到错误，因为代码需要两个额外的参数，而不是一个。如果你意外地将它们调换了，即 `yourName` 是 `Damon` 而 `wizardAdjective` 是 `apprentice`，那么显示结果会是 *The Damon Wizard looks at you and says, apprentice, you are worthy*，而不是正确的输出 *The apprentice Wizard looks at you and says, Damon, you are worthy*。

```lua
local sentence = expandVars("The $adj $char1 looks at you and says, $name, you are $result", {adj="glorious", name="Jayant", result="the Overlord", char1="King"})
print(sentence)
```


整个句子可以按需构建。唯一需要注意的是，如果变量无法展开，它将会显示为变量名本身，例如显示`result`而不是`Overlord`。

```
function expandVars(tmpl,t)
  return (tmpl:gsub('%$([%a_][%w_]*)',t))
end
```

你也可以这样使用它：

```
print(expandVars('welcome $USER?',os.getenv))
```

## 给字符串补零

在许多游戏中，显示带前导零的分数（例如`0003`而不是`3`）会很有趣——例如，为了让分数看起来像旧式游戏，许多此类游戏都使用了这种显示方式。这可以用来创造许多有趣的效果——例如，你可以让一个类似里程表的显示在分数增加时滚动数字。

以下是给数字补零的方法：

```
function paddWithZero(theNumber, numOfDigits)
  return string.rep("0", numOfDigits - string.len(theNumber)) .. theNumber
end
```

## 将月份获取为字符串

有时你可能希望将月份名称作为字符串获取，如下所示。你还可以调整此方法以获取完整的名称（January、February 等），然后如果你想要短名称，只返回前三个字符。

```
function getMonthName(theMonth)
  local months = {"Jan", "Feb", "Mar", "Apr", "May", "Jun", "Jul", "Aug", "Sep", "Oct", "Nov", "Dec"}
  return months[theMonth]
end
```

## 像数组一样访问字符串

在其他一些编程语言中，字符串可以使用方括号来访问。然而，在 Lua 中，我们使用`string.sub`函数。Lua 的灵活性允许一些修改，并为我们提供了这样的功能，例如：

```
getmetatable('').__index = function(str,i)
    return string.sub(str,i,i)
end
```

声明此函数后，我们可以快速测试它，如下所示：

```
theString = "abcdef"
print(theString[4])
```

## 在一维或二维空间中计算两点之间的距离

这几乎是不言而喻的。处理同一轴上的两个点时，我们可以使用简单的减法来找到它们之间的距离，如下所示：

```
function getDistance(pos1, pos2)
  return pos1 - pos2
end
```

然而，如果我们试图计算平面上两点之间的距离，就必须借助三角函数。示例如下：

```
function getDistanceBetween(ptA, ptB)
  return math.sqrt((ptA.x - ptB.x)² + (ptA.y - ptB.y)²)
end
```

## 计算两点之间的角度

例如，如果你正在创建一个塔防类游戏，你可能需要转动玩家、枪支或其他物体，使其指向接近的敌人方向，或者将物体旋转到触摸屏幕的点。为此，你需要计算两点之间的角度：炮塔或玩家所在的中心点，以及触摸点或敌人当前所在的位置。两点之间的角度可以再次通过三角函数来确定，如下所示：

```
function angleBetween(ptA, ptB)
    local angle = math.deg(math.atan2(ptA.y - ptB.y, ptA.x - ptB.x))
     angle = angle + 90
     return angle
end
```

## 将数字保持在给定范围内

有时你可能希望将一个值保持在给定范围内。这样，与其为每个值编写多个`if...then`语句，我们可以创建一个函数来将值保持在范围内。

```
function keepInRange(theVal, low, high)
  return math.min(math.max(low, theVal), high)
end
```

使用`if...then`语句的替代方法如下所示：

```
function _keepInRange(theVal, low, high)
  if theVal < low then
    return low
  end
  if theVal > high then
    return high
  end
  return theVal
end
```

## 执行线性插值

无论你为游戏使用什么框架，游戏都可能具有某种形式的过渡——例如，淡入或移动。实现过渡的最简单方法是将一个值随时间均等（线性）分配。使用以下函数，你可以通过指定起始值和结束值来在给定时间内检索一个值。这里，我们将检索到的值称为`thePercent`，它是一个介于 0 和 1 之间的值，表示进度状态——0 表示开始，1 表示完成。

```
function getLinearInterpolation(theStartValue, theEndValue, thePercent)
  return (theStartValue + (theEndValue - theStartValue) * thePercent)
end
```

## 获取值的符号

有时确定一个值是否为负数非常重要。检查一个数字是否小于 0 很容易，但在执行计算时，可能无法使用`if...then`语句。相反，你可以创建一个函数。为了保持条理清晰，你应该将其添加到`math`命名空间中。

```
function math.sign(theValue)
 return theValue > 0 and 1 or theValue < 0 and -1 or 0
end
```

上述函数等效于：

```
function math.sign(theValue)
  if theValue > 0 then
    return 1
  elseif theValue < 0 then
    return -1
  else
    return 0
  end
end
```

## 碰撞检测

在你的游戏中最基本的需求之一是能够检查屏幕上的两个对象是否发生了碰撞。例如，在吃豆人中，你需要确定吃豆人与幽灵和水果之间的碰撞。在太空入侵者中，你需要确定外星子弹与玩家护盾以及玩家本身之间的碰撞。然而需要注意的一点是，现实世界中的物体并非矩形图章，因此它们并不总是矩形形状。因此，我们有多种方法，如本节所述，可以用来检查两个圆形、两个矩形、一个矩形和一个圆形等等之间的碰撞。

### 使用`isPointInRect`

这是我们在日常使用的用户界面中最常用的函数。用户界面中的每个按钮都有一个矩形边界，程序检查触摸点是否在此边界内，以确定该按钮是否被点击。

它的工作原理是：由`x`和`y`定义的点应包含在矩形内——即`x`值应大于或等于矩形的`x`坐标，并且点`x`应小于或等于矩形的`x`点加上宽度（`y`轴同理）。

```
function isPointInRect( x, y, rectX, rectY, rectWidth, rectHeight )
  if x >= rectX and x <= rectX + rectWidth and
     y >= rectY and y<= rectY + rectHeight then
    return true
  end

return false
end
```

### 使用`pointInCircle`

在塔防类游戏中，每个防御塔或结构都有一个范围；这个范围以围绕结构中心的圆形形式显示。我们可以检查敌人的中心点是否在此圆内，以确定敌人是否在此结构的范围内，如下所示：

```
function pointInCircle( ptX, ptY, circleX, circleY, radius )
  local dx = ptX - circleX
  local dy = ptY - circleY
  if dx * dx + dy * dy <= radius * radius then
    return true
  end

return false
end
```

### 确定一个矩形是否在另一个矩形内

这在需要等待物体到达安全区域的游戏中可能很有用。在这种情况下，当第二个矩形完全包含在第一个矩形内时，可以确定该事件已发生。

```
function rectInRect( rect1X, rect1Y, rect1Width, rect1Height, rect2X, rect2Y, rect2Width, rect2Height )
  if rect1X >= rect2X and rect1X + rect1Width <= rect2X + rect2Width and
     rect1Y >= rect2Y and rect1Y + rect1Height <= rect2Y + rect2Height then
    return true
  end

return false
end
```

### 确定一个圆是否在另一个圆内



类似地，你可能想检查一个圆形是否完全包含在另一个圆形内。以下是用于此的函数：

```
function circleInCircle( circle1X, circle1Y, circle1Radius, circle2X, circle2Y, circle2Radius )
  local dx = circle1X – circle2X
  local dy = circle1Y – circle2Y
  local rx = circle1Radius – circle2Radius
  if dx * dx + dy * dy <= rx * rx then
    return true
  end
  return false
end
```

### 检测矩形重叠

`rectOverlaps` 函数可用于判断两个矩形是否重叠。例如，这可用于判断玩家是否与屏幕上的尖刺或其他物体接触，并触发相应事件。

```
function rectOverlaps( rect1X, rect1Y, rect1Width, rect1Height, rect2X, rect2Y, rect2Width, rect2Height )
  if rect1X + rect1Width >= rect2X and
     rect2X + rect2Width <= rect1X and
     rect1Y + rect1Height >= rect2Y and
     rect2Y + rect2Height <= rect1Y then
      return true
  end

return false
end
```

### 检测圆形重叠

此函数可用于判断两个圆形是否重叠。

```
function circleOverlaps( circle1X, circle1Y, circle1Radius, circle2X, circle2Y, circle2Radius )
  local dx = circle1X – circle2X
  local dy = circle1Y – circle2Y
  local rx = circle1Radius + circle2Radius
  if dx * dx + dy * dy <= rx * rx then
    return true
  end

return false
end
```

### 判断圆形是否与矩形重叠

此函数可帮助判断一个圆形是否与一个矩形重叠。

```
function circleOverlapsRectangle( rectX, rectY, rectWidth, rectHeight, circleX, circleY, circleRadius )

if rectX <= circleX and rectX + rectWidth >= circleX then
    if rectY + rectHeight >= circleY - circleRadius and
       rectY <= circleY + circleRadius then
         return true
    else
         return false
    end
  elseif rectX <= circleY and rectX + rectHeight >= circleY then
    if rectX + rectWidth >= circleX - circleRadius and
       rectX <= circleX + circleRadius then
         return true
    else
         return false
    end
  else
    if rectX < circleX then
      if rectY < circleY then
        return pointInCircle( rectX + rectWidth, rectY + rectHeight,
            circleX, circleY, circleRadius )
      else
        return pointInCircle( rectX + rectWidth, rectY,
            circleX, circleY, circleRadius )
       end
    else
      if rectX < circleY then
        return pointInCircle( rectX, rectY + rectHeight,
             circleX, circleY, circleRadius )
      else
        return pointInCircle( rectX, rectY, circleX, circleY, circleRadius )
      end
    end
  end
end
```

### 使用 `pointInTriangle`

在某些情况下，三角形比矩形或圆形更适合你的需求。有很多方法可以检查三角形的边界值。不过，下面描述的方法是最简单的。它只是从三角形的每个顶点向我们要检查的点延伸连线，从而将三角形分成三个小三角形；然后计算每个小三角形的面积。如果 `totalArea` 与生成的三个新三角形的面积之和相等，那么就可以认为该点位于这个边界三角形内。以下是判断一个点是否在三角形内的函数：

```
function triangleArea( pt1x, pt1y, pt2x, pt2y, pt3x, pt3y )
  local dA = pt1x - pt3x
  local dB = pt1y - pt3y
  local dC = pt2x - pt3x
  local dD = pt2y - pt3y
  return 0.5 * math.abs( ( dA * dD ) - ( dB * dC ) )
end

function pointInTriangle(ptx, pty, pt1x, pt1y, pt2x, pt2y, pt3x, pt3y)
  local areaT = triangleArea(pt1x, pt1y, pt2x, pt2y, pt3x, pt3y)
  local areaA = triangleArea(ptx, pty, pt1x, pt1y, pt2x, pt2y)
  local areaB = triangleArea(ptx, pty, pt3x, pt3y, pt2x, pt2y)
  local areaC = triangleArea(ptx, pty, pt1x, pt1y, pt3x, pt3y)
  return (areaT == (areaA + areaB + areaC))
end
```

### 使用 `pointInPolygon`

你还可以判断一个点是否在多边形内。下面的代码（改编自 Paul Bourke 的文章“Determining if a point lies on the interior of a polygon” [`paulbourke.net/geometry/insidepoly/`](http://paulbourke.net/geometry/insidepoly/) 和 Eric Haynes 的文章“Point in Polygon Strategies” [`erich.realtimerendering.com/ptinpoly/`](http://erich.realtimerendering.com/ptinpoly/) 中的函数）可以实现此功能：

```
function pointInPolygon(x,y,poly)
  n = #poly
  inside = false

p1x,p1y = poly[1], poly[2]
  for i=1,n-2, 2 do
    p2x,p2y = poly[i+2], poly[i+3]
    if y > math.min(p1y,p2y) then
      if y <= math.max(p1y,p2y) then
        if x <= math.max(p1x,p2x) then
          if p1y ~= p2y then
            xinters = (y-p1y)*(p2x-p1x)/(p2y-p1y)+p1x
          end
          if p1x == p2x or x <= xinters then
            inside = not inside
          end
        end
      end
    end
    p1x,p1y = p2x,p2y
  end
  return inside
end
```

## 其他通用函数

本节将介绍一些你在 Lua 开发项目中可能会发现有用的附加函数。

### 比较布尔值

在 Lua 中，另一个常见的任务是比较布尔值。乍一看，你可能会认为以下两个命令是相同的：

```
if someValueOn then print("On") end
```

和

```
if someValueOn==true then print("On") end
```

在第一种情况下，只要 `someValueOn` 不是 nil 或 false，它就会返回 true。然而，如果 `someValueOn` 是 `−1` 或 `"Off"`，该语句仍然会判定为 true；而在第二种情况下，只有当值为 true 时，`someValueOn==true` 才会返回 true，其他任何情况都不会。

### 将 C/Java 风格循环转换为 Lua 循环

如果你有 Java/C/C++ 或 JavaScript 背景，你应该习惯这样的循环：

```
for (initialize; condition; iteration){
}
```

其中，在 `initialize` 中可以设置多个值，每个值用逗号分隔。`condition` 可以由复合条件组成，迭代器可以有多个操作。

例如，考虑这个循环：

```
score =  110  --something
for (i=20, k = 120; i> −1 && score < 100; --i, --k ){
}
```

在 Lua 中，这会被写成如下形式：

```
score = 110
k = 120
for i=20, 0, -1 do
  if score < 100 then
      break
  end

-- 在这里执行任何操作
  k = k - 1
end
```

### 对物体应用摩擦力

在屏幕上移动物体的最简单方法是让物体以恒定速度移动（即，随时间以相同值增加或减少轴坐标值）。当然，这种行为是非常不真实的。例如，在现实世界中，当箱子被推着穿过大厅时，它开始时移动得很快，然后逐渐变慢，直到最终停止。许多开发者喜欢的一个捷径是使用物理对象；这确实可行，但我们也可以通过一点数学运算，在不使用物理对象的情况下模拟摩擦力。

```
local speed = 20
local friction = 0.9
repeat
  print(speed)
  speed = speed * friction
until speed <= 1
```

根据所使用的框架，你可以将这个 `speed` 值应用到 `y` 值的减少上。这会营造出一种带有摩擦力的向上滑动或轻弹的视觉效果。物体起初会快速滑动，然后逐渐减速，直至完全停止。

请注意，这个示例使用了 `repeat` 循环。如果你在处理显示对象，则需要一种非阻塞机制来实现更新。换句话说，你需要使用像每隔一段时间就会被调用的 `draw` 函数，或者 `enterFrame` 事件，这样每一帧都会设置新的位置，你就能看到物体的过渡变化了。如果你希望过渡非常平滑，可以尝试将最大值设为 `0.99`。这可用于模拟轻弹、让物体在屏幕上带惯性滑动等效果。

### 模拟弹簧玩偶盒



### 模拟弹簧玩偶盒

当你打开弹簧玩偶盒时，弹簧驱动的“小丑”会全力弹出，然后随时间推移摆动幅度逐渐减小，直至停止。借助一点三角学知识，我们可以模拟弹簧玩偶盒的效果。

**注意：** 由于我们没有使用基于 GUI 的框架，这里的代码是通用的，会生成可应用于 GUI 中显示对象的 `x` 和 `y` 值，以模拟视觉效果。不过，为了本函数的目的，我们会将数值输出到控制台。

```
frequency  = 5.0
amplitude  = 35
decay      = 1.0
time       = 0

function jackInTheBox(time)
  y = amplitude * math.cos(frequency * time * 2 * math.pi) / math.exp(decay * time)
  return y
end
```

要使用此函数，我们需要调用它并传入一个 `time` 值。如果你要在循环中使用它，则需要按增量（例如 `0.01` 或适合调用函数延迟的其他值）递增 `time` 变量。

### 使用正弦滚屏

前述函数使用了余弦波。类似地，你可以调整参数，甚至使用正弦波。这在 20 世纪 80 年代的正弦滚屏以及为攻击性外星人制作波浪动画（例如在《*R-Type*》、《*Zynaps*》和《*Crosswise*》等游戏里）中最为常见。以类似方式，以下函数将计算 `x` 和 `y` 值，这些值可用于设置显示对象的 `x` 坐标和 `y` 坐标，以模拟视觉效果。

```
amplitude = 55
frequency = 0.6
speed     = frequency

function sineScroll(time)
     y = (amplitude * math.sin(frequency * time * 2 * math.pi) )
    return y
end
```

要使物体移动，你还需要操纵 `x` 位置。每次调用此函数时，你都需要通过以 `frequency`（`speed`）值增加或减少 `x` 值来改变它。为了更好地理解 `amplitude` 和 `frequency` 如何影响输出，你可以调整这些设置。与之前的函数一样，推荐的初始时间增量是 `0.01`。

### 在棋盘上放置不与同行或同列其他棋子重叠的棋子

在棋盘游戏中，有时你可能需要快速检查棋盘上的空位，或者可能与其他棋子共享行或列的格子。此函数有助于判断我们在棋盘上放置的棋子是否与其他棋子共享行或列。

```
N = 8
board = {}
for i=1, N do
  board[i] = {}
  for j=1, N do
    board[i][j] = false
  end
end

function allowedAt(x,y)

for i=1, N do
    if board[x][i] == true or board[i][y] == true then
      return false
    end
  end

return true
end
```

这可以扩展来解决其他问题，例如 N 皇后问题或 N 城堡问题。

### 使用数组通过模板输出大量文本

童谣《吞下苍蝇的老太太》中，一位女士为了抓住之前吞下的动物，吞下越来越大的动物，这是一个可以用数组重构的好例子。她有一系列吞下的动物，每个动物都有对应的输出行。

```
animals = {
  {"fly"  , "But I don't know why she swallowed a fly, \nperhaps she will die"},
  {"spider", "That wriggled and jiggled and tickled inside her"},
  {"bird"  , "quite absurd"},
  {"cat"   , "Fancy that"},
  {"dog"   , "what a hog"},
  {"pig"   , "Her mouth was so big"},
  {"goat"  , "she just opened her throat"},
  {"cow"   , "I don't know how"},
  {"donkey", "It was rather wonky"},
  {"horse" , "She's dead, of course!"},
}
line1 = "I knew an old lady who swallowed a %s,"
line2 = " She swallowed the %s to catch the %s,"

function printf(format,  ...)
  print(string.format(format,  ...))
end

for i=1, #animals do
  if i==1 then
    printf(line1, animals[i][1])
  else
    printf(line2, animals[i][1], animals[i-1][1])
  end
  print(animals[i][2])
end
```

### 参数处理

你可以编写函数来分解冗长且重复的任务，使其模块化。为了让这些函数为你工作，需要向它们传递参数。在某些 C 类型语言中，可以声明具有相同名称但不同参数类型的函数。例如，你可以有：

```
function one(int, int)
```

以及：

```
function one (int)
```

对于 Lua，函数可以接受任何内容，包括可变参数，正如我们之前看到 `printf` 函数时所提到的。你可以向函数传递三种类型的参数：

- **原始类型**：可以是数字、字符串、布尔值或 `nil`。
- **表**：可以是数组或哈希表。
- **可变参数**：任意数量的参数，由三个点（`...`）表示。

在函数中，你可以使用 `type` 函数检查参数的类型，该函数将返回参数变量的类型。

#### 固定参数

向函数传递参数的默认约定是使用固定参数。这些参数告诉函数将有多少个参数以及参数的顺序。使用固定参数时，你知道函数期望什么，并根据传递的参数值，可以采取适当的操作。

```
function listParams(one, two, three)
  print("We got :", one, two, three)
  if one=="one" then
     print("Something")
  else
    print("Nothing")
  end
end
```

在调用此函数时，第一个参数必须是 `one`，第二个是 `two`，第三个是 `three`。

#### 可变参数

当我们想要传递多个参数时，可以使用可变参数（以表形式传递）。另一种方法是使用可变参数（varargs）；这两种方法都充当参数表。

```
function arrayParams(arrTable)
  print("We got :")
  for i, j in pairs(arrTable) do
    print("", i, j)
  end
end
```

#### 可变命名参数

当你不知道传递了什么参数，但想检查某些特定参数时，可以使用可变命名参数。这类似于上面的示例中的表，但它包含的是命名项，而非表项，可以通过名称而不是索引来引用。

```
function namedArrayParams(arrTable)
  if arrTable.debug == true then
    print("We are now in Debug Mode")
  else
    print("We are not in Debug Mode")
  end
  if arrTable.color then
    print("We got the color as ", arrTable.color)
  else
    print("We shall use the default color")
  end
end
```

向此类函数传递参数的方式如下：

```
namedArrayParams({debug=true, 56,"One", color="red", false})
```

**注意：** 在 Lua 中，当使用表调用函数时（例如，`someFunction({one=1,two=2})`），你也可以这样调用：`someFunction {one=1, two=2}`。这仅当表是传递给函数的唯一参数时才可以使用。不能用于传递除表值以外的任何类型的参数。

### 使用可变参数

可变参数（Varargs）类似于前面描述的可变参数，但不是由变量名定义，而是由三个点的特殊组合定义。尽管它们看起来很像表数组，但它们在交互方式上是不同的。

```
function passvarargs(...)
  local a, b = ...
  print(a, b)
end
```

`a` 和 `b` 接收传递的第一个和第二个参数；这在表数组中是不可能的。

你可以通过简单地将可变参数括在花括号中来从中创建元素列表，例如：`local tbl = {...}`。这现在就是一个表数组。

使用 `return...` 命令，你可以返回所有接收到的可变参数。

**注意：** 如前所述，`...` 也可以作为命名变量 `arg` 使用，其类型为表，等同于 `{...}`。

### 解析传递的参数列表

有些函数具有可选参数，例如：

```
function addObjectAtPosition( [parent,] object, [x [, y [,position ] ] ] )
```



方括号中的值是可选的。在此函数中，第一个参数（`parent`）是可选的，第二个参数（`object`）是必填的，第三和第四个参数（`x` 和 `y`）是可选的（但如果设置了`x`或`y`中的任意一个，则另一个也必须设置）。最后一个参数（`position`）是可选的。

解析这个问题可能会有些困难，但 Lua 让这变得简单。以下是实现方式。我们知道`parent`和`object`是表类型的变量，`x`和`y`是数值类型，而`position`是字符串类型。首先，我们检查传递的参数数量——如果只传递了一个参数，它必须是必填参数`object`。如果传递了两个参数且第二个参数的类型是`table`，则第一个是`parent`，第二个是`object`。如果第二个参数的类型是`string`，则它是`position`。否则，它就是`x`位置。

```
function addObjectAtPosition(param1, param2, param3, param4, param5)
  local lstPassed = {param1, param2, param3, param4, param5}
  -- 设置默认值
  local parent = nil -- 或者如果你想设置任何默认值
  local object = nil
  local x = 0
  local y = 0
  local position = "left"

local numPassed = #lstPassed
  if numPassed == 1 then
    object = lstPassed[1]
  elseif numPassed == 2 then
    if type(lstPassed[2])=="number" then
      x = lstPassed[2]
      object = lstPassed[1]
    elseif type(lstPassed[2])=="string" then
      position = lstPassed[2]
      object = lstPassed[1]
    elseif type(lstPassed[2])=="table" then
      object = lstPassed[2]
      parent = lstPassed[1]
    end
  elseif numPassed == 3 then
    if type(lstPassed[2])=="number" then
      object = lstPassed[1]
      x = lstPassed[2]
      y = lstPassed[3]
    else
      parent = lstPassed[1]
      object = lstPassed[2]
      x = lstPassed[3]
    end
  elseif numPassed == 4 then
    if type(lstPassed[4])=="string" then
      object = lstPassed[1]
      x = lstPassed[2]
      y = lstPassed[3]
      position = lstPassed[4]
    else
      parent = lstPassed[1]
      object = lstPassed[2]
      x = lstPassed[3]
      y = lstPassed[4]
    end
  else
    parent = param1
    object = param2
    x = param3
    y = param4
    position = param5
  end
end
```

这有助于创建非常灵活的解析方法。其思路是检查参数的类型以确定传递了哪些参数。虽然前面的例子有些复杂，但也有更简单的例子。例如，考虑以下函数，其中第一个参数是可选的：

```
parseParams( [parent, ] theMessage, theTime )
```

我们可以按如下方式轻松解析它：

```
function parseParams( param1, param2, param3 )
  local lstPassed = {param1, param2, param3}
  local parent = nil
  if type(lstPassed[1]) == "table" then
    parent = lstPassed[1]
    table.remove(lstPassed, 1)
  end
  theMessage = lstPassed[1]
  theTime = lstPassed[2]
end
```

在这个函数中，如果第一个参数是`parent`，我们将其从表中移除，之后函数的其余部分就可以按需访问参数了。

## 创建只读表

有时你可能希望表的一部分是只读的，而另一部分可修改。当大量的`if...then`语句开始充斥你的代码时，这一切就变得不那么美好了。元表修改可以实现很多有趣的技巧。

```
function readonlyTable(theTable)
  return setmetatable({}, {
    __index = theTable,
    __newindex = function (table, key, value)
      -- 不更新或创建该键值对
    end,
    __metatable = false
    })
end
```

如果你希望保留表的一部分不锁定，你可以通过`readonlyTable`函数保留一个未锁定的表类型成员，并像这样向其添加数据：

```
local myRO = readonlyTable{
    name = "Jayant",
    device = "iOS",
    model = "iPhone5",
    apps = {},
}
```

这样一来，`name`、`device`和`model`无法被更改；但是`apps`表可以通过各种函数进行修改。

```
print(myRO.name)
RO.name = "Something Else"
print(myRO.name)
```



如果出于某种特殊原因，你想要覆盖只读表中的某个值，有一种方法可以实现（但如果必须使用此方法，那么使用只读表的全部意义也就丧失了）。实现方法是绕过 `__newindex` 元方法，直接设置该值，具体如下：

```
function overrideValue(theTable, theKey, theValue)
  rawset(theTable, theKey, theValue)
end
```

## 实现栈

在你的游戏中，你可能想要创建一个栈，将对象放入栈中（即*压入*栈），然后稍后将其移除（即从栈中*弹出*）。栈的使用场景会根据你编写逻辑和代码的方式而有所不同。在某些场景中，你可以将收集到的所有物品放入一个栈，然后在关卡结束时，根据收集到的物品来奖励分数，同时从栈中移除这些物品。为了尝试这个示例，请将以下代码放入一个名为 `stacks.lua` 的文件中。

```
local _stack = {}

-- 创建一个包含栈函数的表
function _stack:create()

local theStack = {}
  theStack._entries = {}

-- 将一个值压入栈
  function theStack:push(...)
    if  ... then
      local tArgs = {...}
      -- 添加这些值
      for i,v in pairs(tArgs) do
        table.insert(self._entries, v)
      end
    end
  end

-- 从栈中弹出指定数量的值
  function theStack:pop(num)

-- 从栈中获取指定数量的值
    local num = num or 1

-- 返回表
    local entries = {}

-- 将值获取到 entries 中
    for i = 1, num do
      -- 获取最后一个条目
      if #self._entries ~= 0 then
        table.insert(entries, self._entries[#self._entries])
        -- 移除最后一个值
        table.remove(self._entries)
      else
        break
      end
    end
    -- 返回解包后的条目
    return unpack(entries)
  end

-- 获取栈中条目的数量
  function theStack:getn()
    return #self._entries
  end

-- 打印栈中的列表值
  function theStack:list()
    for i,v in pairs(self._entries) do
      print(i, v)
    end
  end

return theStack
end
```

使用方法非常简单：

```
myStack = _stack:create()
myStack:push("A","small","world")
print(myStack:pop(2))
myStack:list()
print(myStack:pop(1))
```

## 在参数和表之间转换

有时函数需要接受可变数量的参数，有时则需要接受表。一个简单的例子是颜色被指定为元组的情况，但在某些函数中，它们需要以 `r`、`g` 和 `b` 值的形式给出，而不是表。

```
local theColor = {255,0,0}
--[[
  这样指定比以下方式更简单
  theColorR = 255
  theColorG = 0
  theColorB = 0
--]]
```

接受颜色的函数通常接受四个变量，如下函数所示：

```
setTheColor(r, g, b, a)
```

`theColor` 是一个表，无法传递给该函数，因为该函数期望的是数字，而不是表。使用 `unpack` 函数可以轻松解决此问题，如下所示：

```
setTheColor(unpack(theColor))
```

将值转换为表可能是最简单的方法；我们可以使用 `table.insert` 将它们添加到表中，如下所示：

```
table.insert(theTable, param1); table.insert(theTable, param2); table.insert(theTable, param3)
```

或者用花括号将它们括起来，如下所示：

```
theTable = {param1, param2, param3}
```

## 二维向量

本节演示一组有助于使用向量执行任务的函数。本书不会深入解释向量；不过，如果你正在寻找一些好的阅读资料，位于 [`tonypa.pri.ee/vectors/index.html`](http://tonypa.pri.ee/vectors/index.html) 的 Vectors for Flash 网站是一个很好的起点。请将以下全部代码放入一个名为 `vectors.lua` 的文件中。

```
_vec2D = {}

function _vec2D:new(x, y)
  local _newVec = {x = x, y = y}
  setmetatable(_newVec, { __index = _vec2D } )
  return _newVec
end

function _vec2D:magnitude()
 return math.sqrt(self.x * self.x + self.y * self.y)
end

function _vec2D:normalize()
  local t = self:magnitude()
  if t > 0 then
    self.x = self.x / t
    self.y = self.y / t
  end
end

function _vec2D:limit(theLimit)
  if self.x > theLimit then
    self.x = theLimit
  end

if self.y > theLimit then
      self.y = theLimit
  end
end

function _vec2D:equals(theVec)
  if self.x == theVec.c and self.y == theVec.y
    return true
  else
    return false
  end
end

function _vec2D:add(theVec)
  self.x = self.x + theVec.x
  self.y = self.y + theVec.y
end

function _vec2D:sub(theVec)
  self.x = self.x - theVec.x
  self.y = self.y - theVec.y
end

function _vec2D:multiply(scalar)
  self.x = self.x * scalar
  self.y = self.y * scalar
end

function _vec2D:divide(scalar)
  self.x = self.x / scalar
  self.y = self.y / scalar
end

function _vec2D:dot(theVec)
  return self.x * theVec.x + self.y * theVec.y
end

function _vec2D:dist()
  return math.sqrt((self.x * self.x) + (self.y * self.y))
end

function _vec2D:Normalize(theVec)
  local tmpVec = _vec2D:new(theVec.x, theVec.y)
  local vecMag = tmpVec:magnitude()
  if vecMag > 0 then
    tmpVec.x = tmpVec.x / vecMag
    tmpVec.y = tmpVec.y / vecMag
  end
  return tmpVec
end

function _vec2D:addVec(theVec, theVec2)
  local tmpVec = Vector2D:new(0,0)
  tmpVec.x = theVec.x + theVec2.x
  tmpVec.y = theVec.y + theVec2.y
  return tmpVec
end

function _vec2D:subVec(theVec, theVec2)
  local tmpVec = Vector2D:new(0,0)
  tmpVec.x = theVec.x - theVec2.x
  tmpVec.y = theVec.y - theVec2.y
  return tmpVec
end

function _vec2D:multiplyVec(theVec, scalar)
  local tmpVec = Vector2D:new(0,0)
  tmpVec.x = theVec.x * scalar
  tmpVec.y = theVec.y * scalar
  return tmpVec
end

function _vec2D:divideVec(theVec, scalar)
  local tmpVec = Vector2D:new(0,0)
  tmpVec.x = theVec.x / scalar
  tmpVec.y = theVec.y / scalar
  return tmpVec
end

function _vec2D:distance(theVec, theVec2)
  return math.sqrt((theVec2.x – theVec1.x)² + (theVec2.y – theVec1.y)²)
end

return _vec2D
```

## 总结

本章描述的函数仅代表创建游戏时可能需要的一小部分功能。此处包含了一些最常用的函数，并且它们可以以多种方式使用。本节中提到的技巧和诀窍是通用的，不依赖于任何特定框架，这意味着它们适用于本书中提到的所有基于 Lua 的框架。在你读完接下来介绍各个框架的几章之后，第 13 章和第 14 章将为你提供更多关于框架特定库和第三方应用程序的详细信息，以帮助你完成工作。

