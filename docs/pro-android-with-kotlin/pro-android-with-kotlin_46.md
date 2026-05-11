# 在 Kotlin 中编写可重用库

你在网络上能找到的大多数教程都涉及活动、服务、广播接收器和内容提供者。这些组件是可重用的，因为你可以或多或少轻松地将它们从一个项目中提取出来并复制到另一个项目中。Android 操作系统中的封装已达到相当精细的阶段，这使得重用成为可能。然而，在更底层的层面，在某些情况下，Android 内部提供的库或 API 可能无法完全满足你的所有需求，因此你可能会想自行开发此类库，然后在可行的项目之间*复制*源码。

当然，这种源码层面的复制并不符合现代的可重用库方法论。只需考虑一下维护和版本控制问题，就会引入大量重复性的工作。最好的方法是，你将此类可重用库设计为专门的开发工件，这样它们就可以轻松地被不同项目复用。

在接下来的章节中，我们将开发一个基础的*正则表达式*库，作为你自己库项目的概念基础。

## 启动一个库模块

库项目以包含一个或多个模块的项目形式存在。打开 Android Studio 后，创建一个新项目。然后，在新项目内部，转到*新建 ➤ 新建模块*，并选择*Android 库*。

> **注意**  
> Android 库不仅仅是类的集合。它还可能包含资源和配置文件。出于我们的目的，我们只关注类——从开发角度看，这些额外的可能性并无坏处，你可以直接忽略它们。然而，与仅使用 JAR 文件相比，对于你的项目来说，使用 Android 库类型会为未来的扩展提供更多可能性。

## 创建库

在库模块内部，创建一个新的 Kotlin 类，并在其中编写：

```
package com.example.regularexpressionlib
infix operator fun String.div(re:String) :
Array =
Regex(re).findAll(this).toList().toTypedArray()
infix operator fun String.rem(re:String) :
MatchResult? =
Regex(re).findAll(this).toList().firstOrNull()
operator fun MatchResult.get(i:Int) =
this.groupValues[i]
fun String.onMatch(re:String, func: (String)-> Unit)
: Boolean =
this.matches(Regex(re)).also { if(it) func(this) }
```

这四个操作符和函数的作用不亚于让我们能够编写 `searchString / regExpString` 来搜索正则表达式匹配项，并使用 `searchString % regExpString` 来搜索第一个匹配项。此外，我们可以使用 `searchString.onMatch()` 来仅在存在匹配项时执行某个代码块。

这个代码清单与我们目前在本章中看到的所有代码清单都不同。首先，你可以看到这里没有任何类。这是可行的，因为 Kotlin 了解文件工件的概念——它在幕后会根据包名生成一个隐藏类。任何通过 `import com.example.regularexpressionlib.*` 导入该库的客户端，其行为都如同在 Java 中执行了所有这些函数的静态导入一样。

`infix operator fun String.div( re:String )` 为字符串定义了一个除法运算符。这种除法在标准中是不可能的，因此与 Kotlin 内置运算符没有冲突。它使用 Kotlin 库中的 `Regex` 类来查找搜索字符串中所有匹配的正则表达式，并将其转换为数组，这样我们稍后就可以使用 `[]` 运算符按索引访问结果。`infix operator fun String.rem( re:String )` 几乎做了同样的事情，但它为字符串定义了 `%` 运算符，执行正则表达式搜索，并且只取第一个结果，如果没有结果则返回 `null`。

`operator fun MatchResult.get(i:Int) = ...` 是对前面运算符返回的 `MatchResult` 的一个扩展。它允许通过索引访问匹配的分组。例如，你在 "Hello Nelo" 中搜索 "(el)"，可以编写 `("Hello Nelo" / "(e.)") [0][1]` 来获取第一个匹配的第一个分组，在本例中就是从 "Hello" 中获取的 "el"。

## 测试库

我们需要一种在开发过程中测试库的方法。不幸的是，Android Studio 3.0 版本不允许启动类似 `main()` 函数的东西。我们唯一能做的就是创建一个*单元测试*，对于我们的情况，这样的单元测试可以这样写：

```
import org.junit.Assert.*
import org.junit.Test
...
class RegularExpressionTest {
@Test
fun proof_of_concept() {
assertEquals(1, ("Hello Nelo" / ".*el.*").size)
assertEquals(2, ("Hello Nelo" / ".*?el.*?").size)
var s1:String = ""
("Hello Nelo" / "e[lX]").forEach {
s1 += it.groupValues
}
assertEquals("[el][el]", s1)
var s2: String = ""
("Hello Nelo" / ".*el.*").firstOrNull()?.run {
s2 += this[0]
}
assertEquals("Hello Nelo", s2)
assertEquals("el",
("Hello Nelo" % ".*(el).*")?.let{ it[1] } )
assertEquals("el",
("Hello Nelo" / ".*(el).*")[0][1])
var match1: Boolean = false
"Hello".onMatch(".*el.*") {
match1 = true
}
assertTrue(match1)
}
}
```

然后你可以像运行其他任何单元测试一样，使用 Android Studio 的上下文菜单来运行这个测试。请注意，在开发的早期阶段，你可以向测试中添加 `println()` 语句，以便在测试运行时在测试控制台上打印一些信息。

## 使用库

一旦你调用*构建 ➤ 重建项目*，你就可以在模块的：

```
build/outputs/aar
```

文件夹中找到 Android 库。要从客户端使用它，请通过*新建 ➤ 新建模块*在客户端项目中创建一个新模块，选择“导入 .JAR/.AAR 包”，然后导航到库项目生成的 `.aar` 文件。

> **警告**  
> 此过程会复制 `.aar` 文件。如果你有库的新版本，你可以删除客户端项目中的库项目 (!) 并重新导入，或者手动将 `.aar` 文件从库项目复制到客户端项目。

要在客户端中使用该库，只需在文件头部添加 `import com.example.regularexpressionlib.*`，之后你就可以应用前面测试中所示的新匹配结构了。



