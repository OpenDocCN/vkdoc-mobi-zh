# Kotlin 最佳实践

开发工作不仅仅是解决信息技术相关的问题或实现需求，你还希望编写“优质”的软件。不过，在这个语境下，“优质”具体意味着什么，稍微有些模糊。很多方面都起着作用：快速开发、高执行性能、简洁的程序、可读性强的程序、高程序稳定性等等。所有这些都有其优点，但过度强调其中任何一个方面都会损害其他方面。

实际上，你应该将所有这些方面都牢记在心。不过，经验表明，需要特别强调以下几个方面：

-   **让程序易于理解（或富有表现力）。** 一个别人都看不懂的超级优雅解决方案也许能让你自己高兴，但请记住，以后可能其他人需要理解你的软件。
-   **保持程序简单。** 过于复杂的解决方案容易导致不稳定。当然，你不会某天早上醒来就说：“好，今天我要写一个简单的程序来解决需求 XYZ。”编写能可靠解决问题的简单程序，是经验的体现，并且需要多年的实践积累。但你可以不断尝试，在编写简单程序方面持续进步。一个好的起点是，对于你软件的任何部分，都时常自问“这个问题难道没有更简单的解决方案吗？”，并且查阅 API 文档和编程语言参考手册。在很多情况下，你*将会*找到比当前实现更简单的、能达到同样目的的解决方案。
-   **不要重复自己。** 这个通常被称为“DRY”编程的原则，再怎么强调也不为过。每当你发现自己要使用 `Ctrl+C` 和 `Ctrl+V` 复制某些程序段落时，请考虑转而使用一个函数或一个 lambda 表达式，只提供一个执行操作的地方。
-   **做符合预期和可预见的事情。** 在 Kotlin 中，你可以重写类的方法和运算符，也可以动态地向现有类（甚至像 `String` 这样的基础类）添加函数。无论如何，请确保此类扩展的行为与其名称所暗示的预期一致，如果名不副实，程序将难以理解。

Kotlin 在这些方面都提供了帮助，并且常常比老牌的 Java 做得更好。在接下来的章节中，我们将指出一些你可以用来使程序更简洁、简单且富有表现力的 Kotlin 概念。请注意，这些概念的集合远非 Kotlin 的完整文档。因此，如需更多细节，请参阅在线文档或 Kotlin 书籍。

## 函数式编程

虽然函数式编程作为一种开发范式是在 Java 8 才引入的，但 Kotlin 从一开始就支持函数式编程风格。在函数式编程中，你更倾向于使用不可变的值而非变量，避免状态机，并允许将函数作为参数传递给其他函数。同时，lambda 演算允许传递无名函数。Kotlin 为我们提供了所有这些特性。

在 Java 中，你有 `final` 修饰符来表达一个变量在首次初始化后不会改变。虽然大多数 Java 开发者会像下面这样使用 `final` 修饰符来定义常量：

```
public class Constants {
public final static int CALCULATION_PRECISION = 10;
public final static int MAX_ITERATIONS = 1000;
...
}
```

但我很少看到开发者在其编码内部使用它，这很可惜，因为它既能提高可读性也能提高稳定性。为了节省几次按键而省略它的诱惑实在太大了。在 Kotlin 中情况则不同——你使用 `val` 来表达一个数据对象在其生命周期内保持不变，使用 `var` 来表示你需要一个真正的变量：

```
fun getMaxFactorial():Int = 13
fun fact(n:Int):Int {
val maxFactorial = getMaxFactorial()
if(n > maxFactorial)
throw RuntimeException("Too big")
var x = 1
for( i in 1..(n) ) {
x *= i
}
return x
}
val x = fact(12)
System.out.println("12! = ${x}")
```



在以下简短代码片段中，您将看到`maxFactorial`是一个`val`，这意味着“这是不可更改的”。然而，`x`是一个`var`，它在初始化之后会被修改。

为了计算阶乘，我们甚至可以避免在代码片段中使用`var x`，并将其替换为函数式构造。这是另一个函数式编程要旨：优先使用表达式而非语句或语句链。为此，我们使用递归并编写如下代码：

```
fun fact(n:Int):Int = if(n>getMaxFactorial())
throw RuntimeException("Too big") else
if(n > 1) n * fact(n-1) else 1
val x = fact(10)
System.out.println("10! = ${x}")
```

这个简单的阶乘计算器只是一个简短示例——使用集合时，情况会变得更有趣。Kotlin 标准库包含许多函数式构造，您可以用它们编写优雅的代码。为了向您展示所有可能性，我们再次重写阶乘计算器，并使用集合包中的`fold`函数：

```
fun fact(n:Int) = (1..n).fold(1, { acc,i -> acc * i })
System.out.println("10! = ${fact(10)}")
```

为简单起见，我移除了范围检查；如果您愿意，可以将之前的`if...`检查添加到`{...}`内的 lambda 表达式中。您可以看到，我们甚至不需要保留`val`——尽管`i`和`acc`在内部是作为`val`处理的。这甚至可以进一步缩短。由于我们只使用了`Int`类型的“乘法”功能，我们可以直接引用它并编写如下代码：

```
fun fact(n:Int) = (1..n).fold(1, Int::times)
System.out.println("10! = ${fact(10)}")
```

借助集合包中的其他函数式构造，您可以对集合、列表和映射执行更有趣的转换。但函数式编程还涉及将函数作为对象在代码中传递。在 Kotlin 中，您可以将函数赋值给`val`或`var`，如下所示：

```
val factEngine:  (acc:Int,i:Int) -> Int =
{ acc,i -> acc * i }
fun fact(n:Int) = (1..n).fold(1, factEngine)
System.out.println("10! = ${fact(10)}")
```

或者更短，因为 Kotlin 在某些情况下可以推断类型：

```
val factEngine = { acc:Int, i:Int -> acc * i }
fun fact(n:Int) = (1..n).fold(1, factEngine)
System.out.println("10! = ${fact(10)}")
```

在本书中，我们尽可能使用函数式构造，以提高代码的全面性和简洁性。

## 顶级函数与数据

在 Java 世界中，拥有过多全局可用的函数和数据被认为是一种不良风格，例如，通过某个工具类内部具有静态作用域的定义来实现。然而，在 Kotlin 中，这种做法复兴了，而且看起来也更自然。这是因为您可以在文件中、在任何类之外声明函数和变量/值。尽管如此，要使用它们，您必须像`import com.example.global.*`这样导入这些元素，其中包`com/example/global`内任意名称的文件不包含任何类，只包含`fun`、`var`和`val`元素。

例如，在`com/example/app/util`中编写一个文件“common.kt”，并在内部添加如下内容：

```
package com.example.app.util
val PI_SQUARED = Math.PI * Math.PI
fun logObj(o:Any?) =
o?.let { "(" + o::class.toString() + ") " +
o.toString() } ?: ""
```

以及其他一些工具函数和常量。要使用它们，请编写如下代码：

```
import com.example.app.util.*
...
val ps = PI_SQUARED
logObj(ps)
```

但是，您应该谨慎使用此功能——过度使用很容易导致结构混乱。完全避免在此类作用域中放置可变变量！不过，您可以将工具函数和全局常量放在此类全局文件中。

## 类扩展

与 Java 语言不同，Kotlin 允许您动态地向类添加方法。为此，请编写如下代码：

```
fun TheClass.newFun(...){ ... }
```

操作符也可以这样使用，这使您可以创建诸如`"Some Text" % "magic"`（它做什么由您想象）这样的扩展，并将其应用于像`String`这样的通用类。您可以通过以下方式实现这个特定的扩展：

```
infix operator fun String.rem(s:String){ ... }
```

只需确保您不会无意中覆盖现有的类方法和操作符——这会使您的程序难以阅读，因为它会执行意料之外的操作。请注意，大多数标准操作符（如`Double.times()`）无论如何都无法被覆盖，因为它们在内部被标记为`final`。

可以通过`operator fun TheClass.<OPERATOR>`定义的操作符列表如表 [11-2] 所示。

**表 11-2: Kotlin 操作符**

| 符号 | 转换为 | 中缀 | 默认函数 |
| :--- | :--- | :--- | :--- |
| +a | `a.unaryPlus()` | | 通常什么都不做 |
| -a | `a.unaryMinus()` | | 取反一个数字 |
| !a | `a.not()` | | 取反一个布尔表达式 |
| a++ | `a.inc()` | | 递增一个数字 |
| a-- | `a.dec()` | | 递减一个数字 |
| a + b | `a.plus(b)` | x | 加法 |
| a – b | `a.minus(b)` | x | 减法 |
| a * b | `a.times(b)` | x | 乘法 |
| a / b | `a.div(b)` | x | 除法 |
| a % b | `a.rem(b)` | x | 除法取余 |
| a .. b | `a.rangeTo(b)` | x | 定义一个范围 |
| a in b | `b.contains(a)` | x | 包含性检查 |
| a !in b | `!b.contains(a)` | x | 非包含性检查 |
| a[i] | `a.get(i)` | | 索引访问 |
| a[i,j,...] | `a.get(i,j,...)` | | 索引访问，通常不使用 |
| a[i] = b | `a.set(i,b)` | | 索引设置访问 |
| a[i,j,...] = b | `a.set(i,j,...,b)` | | 索引设置访问，通常不使用 |
| a() | `a.invoke()` | | 调用 |
| a(b) | `a.invoke(b)` | | 调用 |
| a(b,c,...) | `a.invoke(b,c,...)` | | 调用 |
| a += b | `a.plusAssign(b)` | x | 加到`a`。不得返回值——必须修改`this` |
| a -= b | `a.minusAssign(b)` | x | 从`a`减去。不得返回值——必须修改`this` |
| a *= b | `a.timesAssign()` | x | 乘以`a`。不得返回值——必须修改`this` |
| a /= b | `a.divAssign(b)` | x | 将`a`除以`b`并赋值。不得返回值——必须修改`this` |
| a %= b | `a.remAssign(b)` | x | 取除以`b`的余数并赋值。不得返回值——必须修改`this` |
| a == b | `a?.equals(b) ?: (b === null)` | x | 检查相等性 |
| a != b | `!(a?.equals(b) ?: (b === null))` | x | 检查不等性 |
| a > b | `a.compareTo(b) > 0` | x | 比较 |
| a < b | `a.compareTo(b) < 0` | x | 比较 |
| a >= b | `a.compareTo(b) >= 0` | x | 比较 |
| a <= b | `a.compareTo(b) <= 0` | x | 比较 |

要定义一个扩展，对于表中类型为“中缀”的任何操作符，您可以编写：

```
infix operator fun TheClass.( ... ){ ... }
```

其中函数参数是第二个及后续的操作数，函数体内部的`this`引用第一个操作数。对于非“中缀”类型的操作符，只需省略`infix`。为您自己的类定义操作符当然是个好主意。通过操作符来扩充标准的 Java 或 Kotlin 库类也可以提高代码的可读性。

### 命名参数

使用命名参数，例如：

```
fun person(fName:String = "", lName:String = "",
age:Int=0) {
val p = Person().apply { ... }
return p
}
```

您可以进行更具表达性的调用，比如：

```
val p = person(age = 27, lName = "Smith")
```

使用参数名称，您不必关心参数的顺序，并且在许多情况下，您可以避免为不同的参数组合重载构造函数。

## 作用域函数

作用域函数允许您以不同于使用类和方法的方式来组织代码。例如，考虑以下代码：

```
val person = Person()
person.lastName = "Smith"
person.firstName = "John"
person.birthDay = "2011-01-23"
val company = Company("ACME")
```


```kotlin
// 虽然这是有效的代码，但重复出现"person."令人厌烦。
// 此外，前四行是关于构造一个 person 对象，
// 而最后一行与 person 毫无关系。如果能直观地表达
// 这一点并避免重复就好了。
// Kotlin 中有一种结构，即：

val person = Person().apply {
    lastName = "Smith"
    firstName = "John"
    birthDay = "2011-01-23"
}
val company = Company("ACME")
```

与原始代码相比，这段代码表达力更强。它清晰地表明：构造一个 person 并对其进行操作；然后做其他事情。共有五种这样的结构，尽管它们相似，但在含义和用法上有所不同：`also`、`apply`、`let`、`run` 和 `with`。表 11-3 对此进行了说明。

**表 11-3** 作用域函数

| 语法 | “this” 是什么 | “it” 是什么 | 返回值 | 用途 |
| --- | --- | --- | --- | --- |
| `a.also { ... }` | 外部上下文的 `this` | `a` | `a` | 用于横切关注点，例如添加日志。 |
| `a.apply { ... }` | `a` | - | `a` | 用于构造后对象成型。 |
| `a.let { ... }` | 外部上下文的 `this` | `a` | 最后一个表达式 | 用于转换。 |
| `a.run { ... }` | `a` | - | 最后一个表达式 | 使用对象进行某些计算，仅产生副作用。为了清晰起见，最好不要使用其返回值。 |
| `with(a) { ... }` | `a` | - | 最后一个表达式 | 对对象进行分组操作。为了清晰起见，最好不要使用其返回值。 |

使用作用域函数能极大地提升代码的表达力——我在本书中经常使用它们。

### 可空性

Kotlin 在语言层面解决了可空性问题，以避免烦人的 `NullPointerException` 抛出。对于任何变量或常量，默认情况下不允许赋值为 `null`——你必须通过添加 `?` 来显式声明可空性，如下所示：

```kotlin
var name:String? = null
```

编译器随后知道示例中的 `name` 可能为 `null`，并采取各种预防措施来避免 `NullPointerException`。例如，你不能编写 `name.toUpperCase()`，而必须使用 `name?.toUpperCase()` 代替，后者仅在 `name` 不为 `null` 时才执行大写转换，否则返回 `null` 本身。

使用我们之前描述的作用域函数，有一种非常优雅的方法可以避免像 `if( x != null ) { ... }` 这样的结构。你可以改为编写：

```kotlin
x?.run {
    ...
}
```

这实现了相同的效果，但更具表现力：凭借 `?.`，只有当 `x` 不为 `null` 时，`run{}` 才会执行。

`Elvis` 运算符 `?:` 也非常有用，它用于处理仅当接收变量为 `null` 时才想计算某个表达式的情况，如下所示：

```kotlin
var x:String? = ...
...
var y:String = x ?: "default"
```

这等同于 Java 中的 `String y = (x != null) ? x : "default");`。

## 数据类

数据类是负责承载结构化数据的类。通常，对数据类内部的数据进行操作是不必要的，或者至少不重要。

在 Kotlin 中声明数据类非常简单；你只需编写：

```kotlin
data class Person(
    val fName:String,
    val lName:String,
    val age:Int)
```

或者，如果你想为某些参数使用默认值：

```kotlin
data class Person(
    val fName:String="",
    val lName:String,
    val age:Int=0)
```

这个简单的声明已经定义了一个构造函数、一个用于比较的合适的 `equals()` 方法、一个默认的 `toString()` 实现，以及参与解构的能力——请参见下文。要创建一个对象，你只需编写：

```kotlin
val pers = Person("John","Smith",37)
```

或者更具表达力的写法：

```kotlin
val pers = Person(fName="John", lName="Smith", age=37)
```

在这种情况下，如果参数有默认声明，你也可以省略它们。

这一点，加上你可以在函数内部声明类和函数，使得定义临时的复杂函数返回类型变得非常容易，如下所示：

```kotlin
fun someFun() {
    ...
    data class Person(
        val fName:String,
        val lName:String,
        val age:Int)
    fun innerFun():Person = ...
    ...
    val p1:Person = innerFun()
    val fName1 = p1.fName
    ...
```

## 解构

*解构声明*允许你进行多值或变量赋值。假设你有一个上一节中定义的数据类 `Person`。那么你可以编写：

```kotlin
val p:Person = ...
val (fName,lName,age) = p
```

这会给你三个不同的值。对于数据类，其顺序由类成员声明的顺序决定。通常，任何具有 `component1()`、`component2()` …… 访问器的对象都可以参与解构，因此你也可以为自己的类使用解构。例如，map 条目默认就支持这一点，所以你可以编写：

```kotlin
val m = mapOf( 1 to "John", 2 to "Greg", ... )
for( (k,v) in m) { ... }
```

因为 `to` 是一个中缀运算符，它创建一个 `Pair` 类，而该类又定义了 `fun component1()` 和 `fun component2()`。

作为解构声明的附加特性，你可以使用 `_` 通配符来表示未使用的部分，如下所示：

```kotlin
val p:Person = ...
val (fName,lName,_) = p
```

### 多行字符串字面量

Java 中的多行字符串字面量定义起来总是有点笨拙：

```java
String s = "First line\n" +
        "Second line";
```

在 Kotlin 中，你可以像这样定义多行字符串字面量：

```kotlin
val s = """
First line
Second Line"""
```

你甚至可以通过添加 `.trimIndent()` 来去除前导缩进空格，如下所示：

```kotlin
val s = """
First line
Second Line""".trimIndent()
```

这会移除开头的换行符以及每行开头的公共空格。

## 内部函数和类

在 Kotlin 中，函数和类也可以在其他函数内部声明，这进一步有助于组织代码：

```kotlin
fun someFun() {
    ...
    class InnerClass { ... }
    fun innerFun() = ...
    ...
}
```

这种内部构造的作用域当然仅限于声明它们的函数内部。

## 字符串插值

在 Kotlin 中，你可以将值传递到字符串中，如下所示：

```kotlin
val i = 7
val s = "And the value of 'i' is ${i}"
```

这借鉴自 Groovy 语言，你可以将其用于所有类型，因为所有类型都有一个 `toString()` 成员。唯一的要求是 `${}` 的内容计算结果必须是一个表达式，因此你甚至可以编写：

```kotlin
val i1 = 7
val i2 = 8
val s = "The sum is: ${i1+i2}"
```

或者使用方法调用和 lambda 函数构建更复杂的结构：

```kotlin
val s = "8 + 1 is: ${ { i: Int -> i + 1 }(8) }"
```

## 限定“this”

如果 `this` 不是你想要的，而是想引用来自外部上下文的 `this`，在 Kotlin 中你可以使用 `@` 限定符，如下所示：

```kotlin
class A {
    val b = 7
    init {
        val p = arrayOf(8,9).apply {
            this[0] += this@A.b
        }
        ...
    }
}
```

## 委托

Kotlin 允许轻松实现*委托*模式。在

```kotlin
interface Printer {
    fun print()
}
class PrinterImpl(val x: Int) : Printer {
    override fun print() { print(x) }
}
class Derived(b: Printer) : Printer by b
```

中，类 `Derived` 是 `Printer` 类型，并将它的所有方法调用委托给 `b` 对象。因此你可以编写：

```kotlin
val pi = PrinterImpl(7)
Derived(pi).print()
```

你可以随意覆盖方法调用，因此可以调整委托以使用新功能：

```kotlin
class Derived(val b: Printer) : Printer by b {
    override fun print() {
        print("Printing:")
        b.print()
    }
}
```

## 重命名导入

在某些情况下，导入的类可能使用很长的名称，而你频繁使用它们，因此你希望它们有更短的名称。例如，如果你经常在代码中使用 `SimpleDateFormat` 类，并且不想每次都写出完整的类名。为了帮助我们解决这个问题并稍微缩短它，你可以引入导入别名并编写：

```kotlin
import java.text.SimpleDateFormat as SDF
```

此后，你可以使用 `SDF` 作为 `SimpleDateFormat` 的替代，例如：



```
val dateStr = SDF("yyyy-MM-dd").format(Date())
```

不过，请不要过度使用此功能，因为你的同事开发者可能需要记住太多新名称，这会让你的代码难以阅读。

## Kotlin on JavaScript

当你同时听到 Android 和 Kotlin 时，自然会想到 Kotlin 是 Java 的替代品，并用于处理 Android 运行时和 Android API。但还有另一种不那么显而易见却充满趣味的可能性。单独看 Kotlin，你会发现它可以生成字节码，在 Java 虚拟机上运行，或者在 Android 的类似 Java 的 Dalvik 虚拟机上运行。它也可以生成 JavaScript，供浏览器使用。问题是：我们能在 Android 中也使用它吗？答案是肯定的，在接下来的章节中，我将向你展示如何实现。

### 创建一个 JavaScript 模块

我们从一个包含 Kotlin 文件的 JavaScript 模块开始，这些文件会被编译成 JavaScript 文件。在启动新模块时，并没有所谓的"JavaScript"模块向导，但我们可以轻松地从一个标准的智能手机应用模块开始，并将其转换以满足我们的需求。

在 Android Studio 项目中，选择 New ➤ New Module，然后选择 Phone & Tablet Module。给它起个合适的名字，暂时就叫 "kotlinjsSample"。生成模块后，删除以下文件夹和文件，因为我们不需要它们：

```
src/test
src/androidTest
src/main/java
src/main/res
src/main/AndroidManifest.xml
```

**注意：** 如果你想在 Android Studio 内部执行删除操作，需要先将视图类型从 "Android" 切换到 "Project"。

然后，添加两个文件夹：

```
src/main/kotlinjs
src/main/web
```

现在，替换模块 `build.gradle` 文件的内容，使其如下所示：

```
buildscript {
ext.kotlin_version = '1.2.31'
repositories {
mavenCentral()
}
dependencies {
classpath "org.jetbrains.kotlin:" +
"kotlin-gradle-plugin:$kotlin_version"
}
}
apply plugin: 'kotlin2js'
sourceSets {
main.kotlin.srcDirs += 'src/main/kotlinjs'
}
task prepareForExport(type: Jar) {
baseName = project.name + '-all'
from {
configurations.compile.collect {
it.isDirectory() ? it : zipTree(it) } +
'src/main/web'
}
with jar
}
repositories {
mavenCentral()
}
dependencies {
implementation "org.jetbrains.kotlin:" +
"kotlin-stdlib-js:$kotlin_version"
}
```

此构建文件启用了 Kotlin ➤ JavaScript 编译器，并引入了一个新的导出任务。现在，你可以在 Android Studio 窗口右侧打开 "Gradle" 视图，然后在 "others" 下找到 `prepareForExport` 任务。双击即可运行它。之后，在 `build/libs` 目录下，你会发现一个新文件 `kotlinjsSample-all.jar`。这个文件就是供其他应用或模块使用的 JavaScript 模块。

在 `src/main/kotlinjs` 目录下创建第一个文件 `Main.kt`，内容如下：

```
import kotlin.browser.document
fun main(args: Array) {
val message = "Hello JavaScript!"
document.getElementById("cont")!!.innerHTML = message
}
```

最终我们面向的是一个网站，所以我们也需要一个初始的 HTML 页面。将其制作成标准的登录页面 `index.xhtml`，在 `src/main/web` 目录下创建，并输入：

```

Kotlin-JavaScript

```

再次执行 `prepareForExport` 任务，使模块输出的工件反映我们刚刚所做的更改。

### 使用 JavaScript 模块

为了使用我们在上一节构建的 JavaScript 模块，我们在应用的 `build.gradle` 文件中添加几行代码：

```
task syncKotlinJs(type: Copy) {
from zipTree('../kotlinjsSample/build/libs/' +
'kotlinjsSample-all.jar')
into 'src/main/assets/kotlinjs'
}
preBuild.dependsOn(syncKotlinJs)
```

这将导入 JavaScript 模块的输出文件，并将其解压到应用的 `assets` 文件夹中。通过 `dependsOn()` 声明，这个额外的构建任务会在正常构建过程中自动执行。

现在，在你的布局文件中，放置一个 `WebView` 元素，例如：

为了用一个网页填充该视图，在你的主 Activity 的 `onCreate()` 回调中，编写：

```
wv.webChromeClient = WebChromeClient()
wv.settings.javaScriptEnabled = true
wv.loadUrl("file:///android_asset/kotlinjs/index.xhtml")
```

这将为 `WebView` 控件启用 JavaScript 支持，并从 JavaScript 模块加载主 HTML 页面。

作为扩展，你可能希望将网页内的 JavaScript 连接到应用（而非 JavaScript 模块）中的 Kotlin 代码。这并不复杂。你只需添加：

```
class JsObject {
@JavascriptInterface
override fun toString(): String {
return "Hi from injectedObject"
}
}
wv.addJavascriptInterface(JsObject(), "injectedObject")
```

此后，就可以在 JavaScript 模块中通过以下方式使用 `injectedObject`：

```
val message = "Hello JavaScript! injected=" +
window["injectedObject"]
```

利用这些技术，你可以完全使用 HTML、CSS、Kotlin（转译为 JavaScript）以及一些用于访问 Android API 的访问器对象来设计你的应用。

## 12. 构建

在本章中，我们将讨论应用的构建过程。虽然从源文件构建应用既可以通过终端，也可以通过 Android Studio IDE 中的按钮和菜单项来完成，但本章并非 Android Studio 的入门指南或参考手册——这些内容请参阅其内置帮助文档、相关书籍或在线资源。我们本章要做的是，探讨构建相关的概念和方法，以便你能根据自身需求调整构建过程。

## 构建相关文件

一旦你在 Android Studio 中创建一个新项目，你会看到以下构建相关文件：

- **build.gradle**  
  这是顶层项目级的构建文件。它包含项目所有模块共用的仓库和依赖项声明。对于简单的应用，你通常无需编辑此文件。

- **gradle.properties**  
  与 Gradle 构建相关的技术设置。通常无需编辑此文件。

- **gradlew** 和 **gradlew.bat**  
  包装脚本，这样你就可以使用终端而非 Android Studio IDE 来运行构建。

- **local.properties**  
  生成的、与你的 Android Studio 安装相关的技术属性。你不应编辑此文件。

- **settings.gradle**  
  告知哪些模块属于该项目。如果你添加新模块，Android Studio 会自动处理此文件。

- **app/build.gradle**  
  模块级别的构建文件。这很重要——模块的依赖项和构建过程在此处进行配置。Android Studio 会为你创建一个名为 "app" 的第一个模块，并包含相应的构建文件，但 "app" 这个名称只是一个惯例。其他模块会有你随意选择的不同名称，且它们都有各自的构建文件。如果你愿意，甚至可以重命名 "app"，以更好地满足你的需求。

## 模块配置

项目中的每个模块都包含自己的构建文件 `build.gradle`。如果你让 Android Studio 为你创建一个新项目或模块，它也会创建一个初始构建文件。一个支持 Kotlin 的模块的基本构建文件示例如下：



```groovy
plugins {
    id 'com.android.application'
    id 'org.jetbrains.kotlin.android'
    id 'com.google.gms.google-services'
}
android {
    compileSdk 32
    defaultConfig {
        applicationId "book.andrkotlpro.example1"
        minSdk 23
        targetSdk 32
        versionCode 1
        versionName "1.0"
        testInstrumentationRunner "androidx.test.runner.AndroidJUnitRunner"
        vectorDrawables {
            useSupportLibrary true
        }
    }
    buildTypes {
        release {
            minifyEnabled false
            proguardFiles getDefaultProguardFile('proguard-android-optimize.txt'), 'proguard-rules.pro'
        }
    }
    compileOptions {
        sourceCompatibility JavaVersion.VERSION_1_8
        targetCompatibility JavaVersion.VERSION_1_8
    }
    kotlinOptions {
        jvmTarget = '1.8'
    }
    buildFeatures {
        compose true
    }
    composeOptions {
        kotlinCompilerExtensionVersion compose_version
    }
    packagingOptions {
        resources {
            excludes += '/META-INF/{AL2.0,LGPL2.1}'
        }
    }
}
dependencies {
    implementation 'androidx.core:core-ktx:1.7.0'
    implementation "androidx.compose.ui:ui:$compose_version"
    implementation "androidx.compose.material:material:$compose_version"
    implementation "androidx.compose.ui:ui-tooling-preview:$compose_version"
    implementation 'androidx.lifecycle:lifecycle-runtime-ktx:2.3.1'
    implementation 'androidx.activity:activity-compose:1.3.1'
    implementation 'com.google.firebase:firebase-messaging:20.1.0'
    testImplementation 'junit:junit:4.13.2'
    androidTestImplementation 'androidx.test.ext:junit:1.1.3'
    androidTestImplementation 'androidx.test.espresso:espresso-core:3.4.0'
    androidTestImplementation "androidx.compose.ui:ui-test-junit4:$compose_version"
    debugImplementation "androidx.compose.ui:ui-tooling:$compose_version"
    debugImplementation "androidx.compose.ui:ui-test-manifest:$compose_version"
}
```

（忽略 ¬ 及其后的换行符。）请注意，Gradle 中 `"..."` 字符串允许包含 `${}` 占位符，而 `'...'` 字符串则不允许。除此之外，两者可以互换使用。

其元素含义如下：

*   `plugins { }` 各加载并应用 Android 和 Kotlin 开发所需的 Gradle 插件。
*   `android { }` 元素指定 Android 插件的设置。
*   `dependencies { }` 元素描述模块的依赖关系。`implementation` 关键字表示该依赖在编译模块和运行模块时都需要。后者意味着该依赖会被包含在 APK 文件中。类似 `"xyzImplementation"` 这样的标识符指的是名为 `"xyz"` 的构建类型或源代码集——你可以看到，对于 `src/test` 下的单元测试，添加了 JUnit 库；而对于 `src/androidTest`，则同时使用了测试运行器和 Espresso。如果你引用的是构建类型或产品风味（将在后续章节详细介绍），可以用构建类型名称或产品风味名称替换 `"xyz"`。如果你想引用一个变体（即构建类型和产品风味的组合），你还必须在 `configurations { }` 元素内声明它，例如：

*   关于 `defaultConfig { }` 和 `buildTypes { }`，请参见后续章节。

```groovy
configurations {
    // flavor = "free", type = "debug"
    freeDebugCompile {}
}
```

`dependencies {...}` 部分中的其他关键字包括：

*   **`implementation`** – 我们已经讨论过这个。它表示该依赖在编译和运行应用时都需要。
*   **`api`** – 与 `implementation` 相同，但额外允许该依赖泄露给应用的客户端。
*   **`compile`** – 这是 `api` 的旧别名，请勿使用。
*   **`compileOnly`** – 该依赖仅编译时需要，但不会包含在应用中。这通常用于仅含源代码的库，例如源代码预处理器等。
*   **`runtimeOnly`** – 该依赖编译时不需要，但会包含在应用内部。

请注意，变量 `compose_version` 是在项目的 `build.gradle` 文件中定义的：

```groovy
buildscript {
    ext {
        compose_version = '1.1.0-beta01'
    }
    dependencies {
        classpath 'com.google.gms:google-services:4.3.3'
    }
}
// 顶层构建文件，可在此添加适用于所有子项目/模块的配置选项。
plugins {
    id 'com.android.application' version '7.2.1' apply false
    id 'com.android.library' version '7.2.1' apply false
    id 'org.jetbrains.kotlin.android' version '1.5.31' apply false
}
task clean(type: Delete) {
    delete rootProject.buildDir
}
```

（请移除 `apply false` 前面的换行符。在此文件中，你还可以找到适用于所有项目模块的其他非常通用的设置。）

## 模块通用配置

模块 `build.gradle` 文件中的 `defaultConfig { ... }` 元素指定了构建的配置设置，与所选变体无关（参见下一节）。可用的设置项可在 Android Gradle DSL 参考文档中查阅，但常见配置如下：

```groovy
defaultConfig {
    // 用于发布的唯一标识符。
    applicationId 'com.example.myapp'
    // 运行应用所需的最低 API 级别。
    minSdk 23
    // 用于测试应用的 API 级别。
    targetSdk 32
    // 应用的版本号。
    versionCode 42
    // 对用户友好的应用版本名称。
    versionName "42.0"
}
```

## 模块构建变体

构建变体对应构建过程生成的不同 `.apk` 文件。构建变体的数量由下式给出：

```
构建变体数量 = (构建类型数量) x (产品风味数量)
```

在 Android Studio 中，您可以通过菜单 **Build > Select Build Variant** 选择构建变体。在以下章节中，我们将描述什么是构建类型和产品风味。

## 构建类型

```
构建类型对应应用开发的不同阶段——在启动项目时，Android Studio 会为您设置两种构建类型：开发和发布。打开模块的 build.gradle 文件，你可以在 `android { ... }` 中看到：
```

```groovy
buildTypes {
    release {
        minifyEnabled false
        proguardFiles getDefaultProguardFile('proguard-android.txt'), 'proguard-rules.pro'
    }
}
```

（忽略 ¬ 及其后的换行符。）即使你没有看到 `"debug"` 类型，它也实际存在。它没有出现只是因为 `"debug"` 类型使用了其默认设置。如果需要更改默认设置，只需在此处添加一个 `"debug"` 部分，如下所示：

```groovy
buildTypes {
    release {
        ...
    }
    debug {
        ...
    }
}
```

你不限于使用预定义的构建类型。可以像下面这样定义额外的构建类型：

```groovy
buildTypes {
    release {
        ...
    }
    debug {
        ...
    }
    integration {
        initWith debug
        manifestPlaceholders = [hostName:"internal.mycompany.com"]
        applicationIdSuffix ".integration"
    }
}
```

这定义了一个新的构建类型 `"integration"`，它通过 `"initWith"` 继承了 `"debug"`，除此之外还添加了一个自定义的应用文件后缀，并为清单文件提供了一个占位符。你可以在此处指定的设置相当多——如果你在常用搜索引擎中输入“Android Gradle plugin DSL reference”，就能找到它们。另一个我们尚未提及的标识符是 `proguardFiles`。它用于在分发应用之前，对要包含在应用中的文件进行过滤和/或混淆。如果你用它进行过滤，请首先权衡其收益与工作量——在当今设备上，节省几兆字节已不太重要。如果你打算用它进行混淆，请注意，如果你的代码或所引用的库使用了反射，这可能会引发问题。而且混淆并不能真正阻止劫持者在反编译后使用你的代码——它只是稍微增加了难度。因此，请仔细考虑使用 Proguard 的优势。如果你认为它符合你的需求，关于如何使用它的详细信息可以在在线文档中找到。


