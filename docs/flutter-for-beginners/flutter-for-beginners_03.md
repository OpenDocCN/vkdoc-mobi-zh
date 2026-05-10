# 3. Dart 核心知识

Flutter 项目可以包含跨平台代码和平台特定代码。跨平台代码使用 Dart 编写。充分掌握 Dart 知识是构建 Flutter 应用的前提条件。本书不涵盖 Dart 语言的细节。你可以找到大量与 Dart 相关的在线资源。然而，介绍构建 Flutter 应用所需的 Dart 核心知识仍然非常有帮助。本章的范例涵盖了 Dart 的不同方面。如果你对自己的 Dart 知识有信心，可以跳过本章。

## 3.1 理解内置类型

### 问题

你想了解 Dart 的内置类型。

### 解决方案

Dart 内置了数字、字符串、布尔值、列表、映射、字符和符号等类型。

### 讨论

Dart 有多种内置类型，包括数字、字符串、布尔值、列表、映射、字符和符号。



#### 数字

Dart 中的数字可以是不超过 64 位的整数值，或符合 IEEE 754 标准的 64 位双精度浮点数。`int` 和 `double` 类型分别代表这两种数值类型。`num` 类型是 `int` 和 `double` 的超类型。与 Java 中的原始类型不同，Dart 中的数字也是对象，它们拥有可供操作的方法。

在代码清单 3-1 中，`x` 的类型是 `int`，而 `y` 的类型是 `double`。方法 `toRadixString()` 通过将数值转换为指定的基数来返回一个字符串值。方法 `toStringAsFixed()` 确保在字符串表示中保留给定数量的小数位数。`double` 的静态方法 `tryParse()` 尝试将一个字符串解析为 `double` 字面量。

```
var x = 10;
var y = 1.5;
assert(x.toRadixString(8) == '12');
assert(y.toStringAsFixed(2) == '1.50');
var z = double.tryParse('3.14');
assert(z == 3.14);
代码清单 3-1
数字
```

#### 字符串

Dart 字符串是 UTF-16 代码单元的序列。可以使用单引号或双引号来创建字符串。使用哪种引号无关紧要，关键在于整个代码库中保持一致。Dart 内置支持字符串插值。可以使用 `$ {表达式}` 的形式将表达式嵌入到字符串中。当使用字符串时，嵌入的表达式的值会被计算。如果表达式是一个标识符，则可以省略 `{}`。在代码清单 3-2 中，`name` 是一个标识符，因此我们可以在字符串中使用 `$name`。

```
var name = 'Alex';
assert('The length of $name is ${name.length}' == 'The length of Alex is 4');
代码清单 3-2
字符串插值
```

如果你想拼接字符串，可以直接将这些字符串字面量相邻放置，而无需使用 `+` 运算符；参见代码清单 3-3。

```
var longString = 'This is a long'
'long'
'long'
'string';
代码清单 3-3
字符串拼接
```

另一种创建多行字符串的方法是使用三引号，单引号或双引号均可；参见代码清单 3-4。

```
var longString2 = '''
This is also a long
long
long
string
''';
代码清单 3-4
多行字符串
```

#### 布尔值

布尔值使用 `bool` 类型表示。`bool` 类型只有两个对象：`true` 和 `false`。值得注意的是，只有 `bool` 值才能用作 `if`、`while` 和 `assert` 中的检查条件。JavaScript 有更宽泛的真值（truthy）和假值（falsy）概念，而 Dart 遵循更严格的规则。例如，`if ('abc')` 在 JavaScript 中是有效的，但在 Dart 中则不行。

在代码清单 3-5 中，`name` 是一个空字符串。要在 `if` 中使用它，我们需要调用 getter `isEmpty`。同时，我们需要显式检查 `null` 和 `0`。

```
var name = '';
if (name.isEmpty) {
  print('name is empty');
}
var value;
assert(value == null);
var count = 5;
while(count-- != 0) {
  print(count);
}
代码清单 3-5
布尔值
```

#### 列表和映射

列表和映射是常用的集合类型。在 Dart 中，数组是 `List` 对象。列表和映射可以使用字面量或构造函数创建。建议尽可能使用集合字面量。代码清单 3-6 展示了如何使用字面量和构造函数创建列表和映射。

```
var list1 = [1, 2, 3];
var list2 = List(3);
var map1 = {'a': 'A', 'b': 'B'};
var map2 = Map();
代码清单 3-6
列表和映射
```

#### Runes

Runes 是字符串的 UTF-32 代码点。要在字符串中表示 32 位的 Unicode 值，我们可以使用 `\uXXXX` 的形式，其中 `XXXX` 是该代码点的四位十六进制值。如果代码点无法用四位十六进制值表示，则需要用 `{}` 包裹这些数字，例如 `\u{XXXXX}`。在代码清单 3-7 中，字符串值包含两个表情符号。

```
var value = '\u{1F686} \u{1F6B4}';
print(value);
代码清单 3-7
Runes
```

#### 符号

Symbol 对象代表一个运算符或标识符。符号可以使用构造函数 `Symbol(<name>)` 或符号字面量 `#<name>` 创建。使用相同名称创建的符号是相等的；参见代码清单 3-8。当你需要按名称引用标识符时，应该使用符号。

```
assert(Symbol('a') == #a);
代码清单 3-8
符号
```

## 3.2 使用枚举类型

### 问题

你想要一种类型安全的方式来声明一组常量值。

### 解决方案

使用枚举类型。

### 讨论

与其他编程语言一样，Dart 拥有枚举类型。要声明一个枚举类型，请使用 `enum` 关键字。枚举中的每个值都有一个 `index` getter 来获取该值从零开始的位置。使用 `values` 可以获取枚举中所有值的列表。枚举通常用于 `switch` 语句中。在代码清单 3-9 中，枚举类型 `TrafficColor` 有三个值。第一个值 `red` 的索引是 `0`。

```
enum TrafficColor { red, green, yellow }
void main() {
  assert(TrafficColor.red.index == 0);
  assert(TrafficColor.values.length == 3);
  var color = TrafficColor.red;
  switch (color) {
    case TrafficColor.red:
      print('stop');
      break;
    case TrafficColor.green:
      print('go');
      break;
    case TrafficColor.yellow:
      print('be careful');
  }
}
代码清单 3-9
枚举类型
```

## 3.3 使用动态类型

### 问题

你不知道对象的类型，或者你不在意其类型。

### 解决方案

使用 `dynamic` 类型。

### 讨论

Dart 是一种强类型语言。大多数时候，我们希望对象具有已定义的类型。然而，有时我们可能不知道或不关心实际的类型；这时我们可以使用 `dynamic` 作为类型。动态类型经常与 `Object` 类型混淆。`Object` 和 `dynamic` 都允许所有值。如果你想声明接受所有对象，则应使用 `Object`。如果类型是 `dynamic`，我们可以使用 `is` 操作符来检查它是否是期望的类型。实际类型可以通过 `runtimeType` 获取。在代码清单 3-10 中，`value` 的实际类型是 `int`，然后类型变更为 `String`。

```
dynamic value = 1;
print(value.runtimeType);
value = 'test';
if (value is String) {
  print('string');
}
代码清单 3-10
使用动态类型
```

## 3.4 理解函数

### 问题

你想了解 Dart 中的函数。

### 解决方案

Dart 中的函数非常强大且灵活。



### 讨论

Dart 中的函数是对象，类型为 `Function`。函数可以赋值给变量、作为函数参数传递，也可以作为函数返回值。在 Dart 中创建高阶函数非常容易。函数可以有零个或多个参数。有些参数是必需的，有些则是可选的。必需的参数在参数列表中排在前面，后面跟着可选参数。可选的位置参数用 `[]` 包裹。

当函数有长串参数时，很难记住这些参数的位置和含义。这时最好使用命名参数。可以使用 `@required` 注解将命名参数标记为必需。参数可以使用 `=` 指定默认值。如果没有提供默认值，默认值为 `null`。

在 3-11 代码清单 中，函数 `sum()` 有一个可选位置参数 `initial`，其默认值为 `0`。函数 `joinToString()` 有一个必需的命名参数 `separator` 和两个可选的命名参数 `prefix` 与 `suffix`。`joinToString()` 中使用的箭头语法，是函数体仅包含单个表达式时的简写形式。语法 `=> expr` 等同于 `{ return expr; }`。使用箭头语法可以使代码更简短且更易读。

```
import 'package:meta/meta.dart';
int sum(List list, [int initial = 0]) {
  var total = initial;
  list.forEach((v) => total += v);
  return total;
}
String joinToString(List list,
    {@required String separator, String prefix = ", String suffix = "}) =>
    '$prefix${list.join(separator)}$suffix';
void main() {
  assert(sum([1, 2, 3]) == 6);
  assert(sum([1, 2, 3], 10) == 16);
  assert(joinToString(['a', 'b', 'c'], separator: ',') == 'a,b,c');
  assert(
      joinToString(['a', 'b', 'c'], separator: '-', prefix: '*', suffix: '?') ==
          '*a-b-c?');
}
```

有时你可能不需要为函数命名。这些匿名函数在提供回调时非常有用。在 3-12 代码清单 中，一个匿名函数被传递给了方法 `forEach()`。

```
var list = [1, 2, 3];
list.forEach((v) => print(v * 10));
```

## 3.5 使用 Typedefs

### 问题

你想为函数类型创建一个别名。

### 解决方案

使用 Typedefs。

### 讨论

在 Dart 中，函数是对象。函数是 `Function` 类型的实例。但函数的实际类型由其参数类型和返回值类型决定。当函数作为参数或返回值时，重要的是其实际的函数类型。Dart 中的 `typedef` 允许我们为函数类型创建一个别名。这个类型别名可以像其他类型一样使用。在 3-13 代码清单 中，`Processor<T>` 是一个函数类型的别名，该函数类型包含一个类型为 `T` 的参数，返回值类型为 `void`。这个类型在函数 `process()` 中被用作参数类型。

```
typedef Processor = void Function(T value);
void process(List list, Processor processor) {
  list.forEach((item) {
    print('processing $item');
    processor(item);
    print('processed $item');
  });
}
void main() {
  process([1, 2, 3], print);
}
```

## 3.6 使用级联运算符

### 问题

你想对同一个对象执行一系列操作。

### 解决方案

使用 Dart 中的级联运算符（`..`）。

### 讨论

Dart 有一个特殊的级联运算符（`..`），它允许我们对同一个对象执行一系列操作。在其他编程语言中，要链式调用同一个对象的方法，我们通常需要创建一个流畅接口（Fluent API），让每个方法都返回当前对象。Dart 中的级联运算符使得这个要求变得不再必要。即使方法不返回当前对象，我们仍然可以进行链式调用。级联运算符还支持属性访问。在 3-14 代码清单 中，级联运算符被用于访问 `User` 和 `Address` 类中的属性和方法。

```
class User {
  String name, email;
  Address address;
  void sayHi() => print('hi, $name');
}
class Address {
  String street, suburb, zipCode;
  void log() => print('Address: $street');
}
void main() {
  User()
    ..name = 'Alex'
    ..email = 'alex@example.org'
    ..address = (Address()
      ..street = 'my street'
      ..suburb = 'my suburb'
      ..zipCode = '1000'
      ..log())
    ..sayHi();
}
```

## 3.7 重写运算符

### 问题

你想在 Dart 中重写运算符。

### 解决方案

在类中为运算符定义重写方法。

### 讨论

Dart 有许多运算符。其中只有一部分运算符可以被重写。这些可重写的运算符包括 `<`、`+`、`|`、`[]`、`>`、`/`、`^`、`[]=`、`<=`、`~/`、`&`、`~`、`>=`、`*`、`<<`、`==`、`-`、`%` 和 `>>`。对于某些类来说，使用运算符比使用方法更简洁。例如，`List` 类重写了 `+` 运算符用于列表拼接。代码 `[1] + [2]` 非常容易理解。在 3-15 代码清单 中，类 `Rectangle` 重写了运算符 `<` 和 `>`，用于按面积比较实例。

```
class Rectangle {
  int width, height;
  Rectangle(this.width, this.height);
  get area => width * height;
  bool operator <(Rectangle rect) => area < rect.area;
  bool operator >(Rectangle rect) => area > rect.area;
}
void main() {
  var rect1 = Rectangle(100, 100);
  var rect2 = Rectangle(200, 150);
  assert(rect1 < rect2);
  assert(rect2 > rect1);
}
```

## 3.8 使用构造函数

### 问题

你想创建 Dart 类的新实例。

### 解决方案

使用构造函数。



### 讨论

与其他编程语言类似，Dart 中的对象通过构造器创建。通常，构造器通过声明与类同名的函数来定义。构造器可以包含参数，用于提供初始化新对象所需的数值。如果某个类未声明任何构造器，系统会提供一个无参数的默认构造器。该默认构造器仅会调用超类中的无参数构造器。然而，一旦声明了构造器，这个默认构造器便不再存在。

一个类可以拥有多个构造器。你可以使用 `ClassName.identifier` 的形式来命名这些构造器，以更清晰地表明其含义。

在代码清单 3-16 中，`Rectangle` 类包含一个接受四个参数的常规构造器，同时还拥有一个命名构造器 `Rectangle.fromPosition`。

```
class Rectangle {
final num top, left, width, height;
Rectangle(this.top, this.left, this.width, this.height);
Rectangle.fromPosition(this.top, this.left, num bottom, num right)
: assert(right > left),
assert(bottom > top),
width = right - left,
height = bottom - top;
@override
String toString() {
return 'Rectangle{top: $top, left: $left, width: $width, height: $height}';
}
}
void main(List args) {
var rect1 = Rectangle(100, 100, 300, 200);
var rect2 = Rectangle.fromPosition(100, 100, 300, 200);
print(rect1);
print(rect2);
}
代码清单 3-16
构造器
```

使用工厂模式来创建对象是很常见的做法。Dart 有一种特殊的 `factory` 构造器来实现这种模式。工厂构造器并不总是返回类的新实例，它可能返回一个缓存实例，或者返回一个子类型的实例。在代码清单 3-17 中，`ExpensiveObject` 类包含一个命名构造器 `ExpensiveObject._create()` 来实际创建新实例。工厂构造器仅在 `_instance` 为 `null` 时才会调用 `ExpensiveObject._create()`。运行这段代码时，你会看到信息 "created" 只打印了一次。

```
class ExpensiveObject {
static ExpensiveObject _instance;
ExpensiveObject._create() {
print('created');
}
factory ExpensiveObject() {
if (_instance == null) {
_instance = ExpensiveObject._create();
}
return _instance;
}
}
void main() {
ExpensiveObject();
ExpensiveObject();
}
代码清单 3-17
工厂构造器
```

## 3.9 扩展类

### 问题

你希望从一个已有的类中继承行为。

### 解决方案

通过扩展已有类来创建一个子类。

### 讨论

Dart 是一种面向对象的编程语言，它提供了对继承的支持。一个类可以使用关键字 `extends` 从一个超类继承。在子类中，超类可以被引用为 `super`。子类可以重写超类的实例方法、getter 和 setter。重写的成员应使用 `@override` 注解进行标记。

抽象类使用 `abstract` 修饰符定义。抽象类不能被实例化。抽象类中的抽象方法没有实现，必须由非抽象子类实现。

在代码清单 3-18 中，`Shape` 类是一个抽象类，包含一个抽象方法 `area()`。`Rectangle` 和 `Circle` 类都继承自 `Shape` 并实现了抽象方法 `area()`。

```
import 'dart:math' show pi;
abstract class Shape {
double area();
}
class Rectangle extends Shape {
double width, height;
Rectangle(this.width, this.height);
@override
double area() {
return width * height;
}
}
class Square extends Rectangle {
Square(double width) : super(width, width);
}
class Circle extends Shape {
double radius;
Circle(this.radius);
@override
double area() {
return pi * radius * radius;
}
}
void main() {
var rect = Rectangle(100, 50);
var square = Square(50);
var circle = Circle(50);
print(rect.area());
print(square.area());
print(circle.area());
}
代码清单 3-18
继承
```

## 3.10 向类添加特性

### 问题

你想要重用某个类的代码，但受到 Dart 单继承的限制。

### 解决方案

使用 Mixin。

### 讨论

继承是重用代码的一种常见方式。Dart 只支持单继承，即一个类最多只能有一个超类。如果你想从多个类中重用代码，则应使用 Mixin。一个类可以使用关键字 `with` 声明多个 Mixin。Mixin 是一个继承自 `Object` 且不声明构造器的类。Mixin 可以使用 `class` 声明为常规类，或使用 `mixin` 声明为专门的 Mixin。在代码清单 3-19 中，`CardHolder` 和 `SystemUser` 是 Mixin。`Assistant` 类继承自 `Student` 并使用了 `SystemUser` Mixin，因此我们可以调用 `Assistant` 实例的 `useSystem()` 方法。

```
class Person {
String name;
Person(this.name);
}
class Student extends Person with CardHolder {
Student(String name) : super('Student: $name') {
holder = this;
}
}
class Teacher extends Person with CardHolder {
Teacher(String name) : super('Teacher: $name') {
holder = this;
}
}
mixin CardHolder {
Person holder;
void swipeCard() {
print('${holder.name} swiped the card');
}
}
mixin SystemUser {
Person user;
void useSystem() {
print('${user.name} used the system.');
}
}
class Assistant extends Student with SystemUser {
Assistant(String name) : super(name) {
user = this;
}
}
void main() {
var assistant = Assistant('Alex');
assistant.swipeCard();
assistant.useSystem();
}
代码清单 3-19
Mixin
```

## 3.11 使用接口

### 问题

你需要一个供类遵循的契约。

### 解决方案

使用类的隐式接口。

### 讨论

你应该熟悉作为类契约的接口。与其他面向对象的编程语言不同，Dart 没有接口的概念。每个类都有一个隐式接口，该接口包含该类以及它所实现的接口的所有实例成员。你可以使用 `implements` 来声明一个类实现了另一个类的 API。在代码清单 3-20 中，`CachedDataLoader` 类实现了 `DataLoader` 类的隐式接口。

```
class DataLoader {
void load() {
print('load data');
}
}
class CachedDataLoader implements DataLoader {
@override
void load() {
print('load from cache');
}
}
void main() {
var loader = CachedDataLoader();
loader.load();
}
代码清单 3-20
接口
```

## 3.12 使用泛型

### 问题

当你的代码设计为适用于不同类型时，你需要保证类型安全。

### 解决方案

使用泛型类和泛型方法。



### 讨论

泛型对开发者来说并非陌生概念，尤其是对于 Java 和 C# 开发者而言。借助泛型，我们可以为类和方法添加类型参数。泛型通常用于集合中，以创建类型安全的集合。清单 3-21 展示了在 Dart 中使用泛型集合的示例。Dart 的泛型类型是可保留的（reified），这意味着类型信息在运行时是可用的。这也正是 `names` 的类型是 `List<String>` 的原因。

```
var names = ['a', 'b', 'c'];
print(names is List);
var values = {'a': 1, 'b': 2, 'c': 3};
print(values.values.toList());
清单 3-21
泛型集合
```

我们可以使用泛型来创建处理不同类型对象的类。在清单 3-22 中，`Pair<F, S>` 是一个带有两个类型参数 `F` 和 `S` 的泛型类。使用 `extends` 来指定泛型类型参数的上界。`CardHolder` 中的类型参数 `P` 的上界是 `Person` 类型，因此 `CardHolder<Student>` 是有效的。

```
class Pair {
F first;
S second;
Pair(this.first, this.second);
}
class Person {}
class Teacher extends Person {}
class Student extends Person {}
class CardHolder {
P holder;
CardHolder(this.holder);
}
void main() {
var pair = Pair('a', 1);
print(pair.first);
var student = Student();
var cardHolder = CardHolder(student);
print(cardHolder is CardHolder);
print(cardHolder);
}
清单 3-22
泛型类型
```

泛型方法可以添加到常规类中。在清单 3-23 中，常规类 `Calculator` 有两个泛型方法 `add` 和 `subtract`。

```
class Calculator {
T add(T v1, T v2) => v1 + v2;
T subtract(T v1, T v2) => v1 - v2;
}
void main() {
var calculator = Calculator();
int r1 = calculator.add(1, 2);
double r2 = calculator.subtract(0.1, 0.2);
print(r1);
print(r2);
}
清单 3-23
泛型方法
```

## 3.13 使用库

### 问题

你想复用 Dart SDK 或社区提供的库。

### 解决方案

使用 `import` 语句导入库，以便在你的应用中使用它们。

### 讨论

在开发非平凡的 Dart 应用时，不可避免地会用到库。这些库可以是 Dart SDK 中的内置库，也可以是社区贡献的库。要使用这些库，我们首先需要使用 `import` 导入它们。`import` 只有一个参数，用于指定库的 URI。内置库使用 `dart:` 的 URI 方案，例如 `dart:html` 和 `dart:convert`。社区包使用 `package:` 的 URI 方案，并由 Dart 的 `pub` 工具管理。清单 3-24 展示了导入库的示例。

```
import 'dart:html';
import 'package:meta/meta.dart';
清单 3-24
导入库
```

有可能两个库导出了相同的标识符。为避免冲突，我们可以使用 `as` 为一个或两个库提供前缀。在清单 3-25 中，`lib1.dart` 和 `lib2.dart` 都导出了类 `Counter`。为这两个库分配不同的前缀后，我们可以使用前缀来访问类 `Counter`。

```
import 'lib1.dart' as lib1;
import 'lib2.dart' as lib2;
lib1.Counter counter;
清单 3-25
重命名库
```

你不需要导入一个库的所有成员。使用 `show` 来显式地包含成员。使用 `hide` 来显式地排除成员。在清单 3-26 中，当导入 `dart:math` 库时，只导入了 `Random`；当导入 `dart:html` 库时，只排除了 `Element`。

```
import 'dart:math' show Random;
import 'dart:html' hide Element;
清单 3-26
显示和隐藏成员
```

## 3.14 使用异常

### 问题

你想处理 Dart 应用中的失败情况。

### 解决方案

使用 `throw` 报告失败。使用 `try-catch-finally` 处理异常。

### 讨论

代码会出错。代码报告失败并处理失败是很自然的事情。Dart 拥有与 Java 类似的异常机制，区别在于 Dart 中的所有异常都是非受检异常。Dart 中的方法不会声明它们可能抛出的异常，因此不强制要求捕获异常。然而，未捕获的异常会导致隔离区挂起，并可能导致程序终止。恰当的失败处理也是健壮应用的一个关键特征。

#### 报告失败

我们可以使用 `throw` 来抛出异常。实际上，所有非 `null` 对象都可以被抛出，而不仅仅是实现了 `Error` 或 `Exception` 类型。建议只抛出 `Error` 和 `Exception` 类型的对象。

`Error` 对象代表代码中不应该出现的 bug。例如，如果一个列表只包含三个元素，尝试访问第四个元素会导致抛出 `RangeError`。与 Exception 不同，Error 不打算被捕获。当错误发生时，最安全的方式是终止程序。`Error` 带有关于其发生原因的清晰信息。

与 `Error` 相比，`Exception` 被设计为通过编程方式捕获和处理。例如，发送 HTTP 请求可能不会成功，因此我们需要在代码中处理异常以应对失败。`Exception` 通常携带关于失败的有用数据。我们应该创建继承自 `Exception` 的自定义类型来封装必要的数据。

#### 捕获异常

当抛出异常时，你可以捕获它以防止其传播，除非你重新抛出它。捕获异常的目的是处理它。如果你不想处理异常，就不应该捕获它。使用 `try`、`catch` 和 `on` 来捕获异常。如果你不需要访问异常对象，仅使用 `on` 就足够了。使用 `catch` 可以访问异常对象和堆栈跟踪。使用 `on` 来指定要捕获的异常类型。

当你捕获一个异常时，你应该处理它。然而，有时你可能只想部分处理它。在这种情况下，你应该使用 `rethrow` 重新抛出异常。捕获异常但不完全处理它是一个坏习惯。

如果你希望无论是否抛出异常，某些代码都能运行，你可以将代码放在 `finally` 子句中。如果没有抛出异常，`finally` 子句会在 `try` 块之后运行。如果抛出了异常，`finally` 子句会在匹配的 `catch` 子句之后运行。

在清单 3-27 中，函数 `getNumber()` 抛出了一个自定义异常类型 `ValueTooLargeException`。在函数 `main()` 中，该异常被捕获并重新抛出。

```
import 'dart:math' show Random;
var random = Random();
class ValueTooLargeException implements Exception {
int value;
ValueTooLargeException(this.value);
@override
String toString() {
return 'ValueTooLargeException{value: $value}';
}
}
int getNumber() {
var value = random.nextInt(10);
if (value > 5) {
throw ValueTooLargeException(value);
}
return value;
}
void main() {
try {
print(getNumber());
} on ValueTooLargeException catch (e) {
print(e);
rethrow;
} finally {
print('in finally');
}
}
清单 3-27
使用异常
```

## 3.15 总结

学习一门新的编程语言并非易事。尽管 Dart 与其他编程语言看起来相似，但它仍然有一些独特的特性。本章仅简要介绍了 Dart 中的重要特性。

