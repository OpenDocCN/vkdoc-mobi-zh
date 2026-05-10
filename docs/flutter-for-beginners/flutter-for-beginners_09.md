# 9. 服务交互

许多非简单的移动应用需要与后端服务进行交互。本章涵盖了与 Flutter 中服务交互相关的基本概念。

## 9.1 处理 Futures

### 问题

你想处理 `Future` 对象。

### 解决方案

使用 `then()` 和 `catchError()` 方法来处理 `Future` 对象的结果。



### 讨论

在使用 Flutter 和 Dart 库中的代码时，你可能会遇到返回 `Future` 对象的函数。`dart:async` 库中的 `Future<T>` 类代表延迟计算。一个 `Future` 对象表示一个在未来可用的潜在值或错误。当给定一个 `Future` 对象时，你可以注册回调函数，以便在该值或错误可用时进行处理。`Future` 类是 Dart 中异步编程的基本构建块之一。

针对给定的 `Future` 对象，其结果有三种不同的情况：

*   计算从未完成。不会调用任何回调函数。
*   计算完成并返回一个值。值回调函数会携带该值被调用。
*   计算完成并抛出一个错误。错误回调函数会携带该错误被调用。

要向 `Future` 对象注册回调，你可以使用 `then()` 方法来注册一个值回调和可选错误回调，或者使用 `catchError()` 方法仅注册错误回调。建议只使用 `then()` 方法注册值回调。这是因为如果通过 `then()` 方法的 `onError` 参数注册了错误回调，该错误回调无法处理值回调中抛出的错误。大多数情况下，你希望错误回调能处理所有可能的错误。如果某个 `Future` 对象的错误没有被其错误回调处理，该错误将由全局处理器处理。

在代码清单 9-1 中，`Future` 对象可能以值 `1` 或一个 `Error` 对象完成。同时注册了值回调和错误回调来处理结果。

```
Future.delayed(
Duration(seconds: 1),
() {
if (Random().nextBool()) {
return 1;
} else {
throw Error();
}
},
).then((value) {
print(value);
}).catchError((error) {
print('error: $error');
});
代码清单 9-1
使用 then() 和 catchError() 方法处理结果
```

`then()` 和 `catchError()` 方法的返回值也是 `Future` 对象。给定一个 `Future` 对象 A，调用 `A.then(func)` 的结果是另一个 `Future` 对象 B。如果 `func` 回调执行成功，那么 `Future` B 将以调用 `func` 函数的返回值完成。否则，`Future` B 将以调用 `func` 函数时抛出的错误完成。调用 `B.catchError(errorHandler)` 会返回一个新的 `Future` 对象 C。错误处理器可以处理 `Future` B 中抛出的错误，这些错误可能源自 `Future` A 本身或其值处理器。通过使用 `then()` 和 `catchError()` 方法，`Future` 对象形成了一个处理异步计算的链。

在代码清单 9-2 中，多个 `then()` 方法被链接在一起，按顺序处理结果。

```
Future.value(1)
.then((value) => value + 1)
.then((value) => value * 10)
.then((value) => value + 2)
.then((value) => print(value));
代码清单 9-2
链式调用 then() 方法
```

如果你想在 future 完成时调用函数，可以使用 `whenComplete()` 方法。无论 future 是以值还是错误完成，使用 `whenComplete()` 添加的函数都会在 future 完成时被调用。`whenComplete()` 方法相当于其他编程语言中的 `finally` 块。`then().catchError().whenComplete()` 链相当于 "try-catch-finally"。

代码清单 9-3 展示了使用 `whenComplete()` 方法的示例。

```
Future.value(1).then((value) {
print(value);
}).whenComplete(() {
print('complete');
});
代码清单 9-3
使用 whenComplete()
```

`Future` 对象的计算可能需要很长时间才能完成。你可以使用 `timeout()` 方法为计算设置时间限制。当调用 `timeout()` 方法时，需要提供一个 `Duration` 对象作为时间限制，以及一个可选的 `onTimeout` 函数，用于在超时时提供值。`timeout()` 方法的返回值是一个新的 `Future` 对象。如果当前的 `Future` 对象在时间限制内没有完成，则调用 `onTimeout` 函数的结果将作为新 `Future` 对象的结果。如果未提供 `onTimeout` 函数，则当当前 future 超时时，新 `Future` 对象将以 `TimeoutException` 完成。

在代码清单 9-4 中，`Future` 对象将在 5 秒后以值 `1` 完成，但时间限制设置为 2 秒。因此将会使用 `onTimeout` 函数返回的值 `10`。

```
Future.delayed(Duration(seconds: 5), () => 1)
.timeout(
Duration(seconds: 2),
onTimeout: () => 10,
)
.then((value) => print(value));
代码清单 9-4
使用 timeout() 方法
```

## 9.2 使用 async 和 await 处理 Futures

### 问题

你想像处理同步操作一样处理 `Future` 对象。

### 解决方案

使用 `async` 和 `await`。

### 讨论

`Future` 对象代表异步计算。处理 `Future` 对象的常用方式是注册回调来处理结果。这种基于回调的风格可能会给习惯了同步操作的开发者带来障碍。使用 `async` 和 `await` 是 Dart 中的一种语法糖，目的是让处理 `Future` 对象像普通的同步操作一样。

给定一个 `Future` 对象，`await` 可以等待其完成并返回其值。`await` 之后的代码可以直接使用返回的值，就像它是同步调用的结果一样。当使用 `await` 时，其所在的函数必须标记为 `async`。这意味着该函数返回一个 `Future` 对象。

在代码清单 9-5 中，`getValue()` 函数的返回值是一个 `Future` 对象。在 `calculate()` 函数中，使用 `await` 来获取 `getValue()` 函数的返回值并赋值给 `value` 变量。由于使用了 `await`，`calculate()` 函数被标记为 `async`。

```
Future getValue() {
return Future.value(1);
}
Future calculate() async {
int value = await getValue();
return value * 10;
}
代码清单 9-5
使用 async/await
```

当使用 `await` 处理 `Future` 对象时，你可以使用 try-catch-finally 来处理 `Future` 对象中抛出的错误。这使得 `Future` 对象可以像普通同步操作一样使用。代码清单 9-6 展示了同时使用 try-catch-finally 和 `await`/`async` 的示例。

```
Future getErrorValue() {
return Future.error('invalid value');
}
Future calculateWithError() async {
try {
return await getErrorValue();
} catch (e) {
print(e);
return 1;
} finally {
print('done');
}
}
代码清单 9-6
使用 try-catch-finally 和 await/async
```

## 9.3 创建 Futures

### 问题

你想创建 `Future` 对象。

### 解决方案

使用 `Future` 构造函数 `Future()`、`Future.delayed()`、`Future.sync()`、`Future.value()` 和 `Future.error()` 来创建 `Future` 对象。



### Discussion

如果你需要创建`Future`对象，可以使用其构造函数：`Future()`、`Future.delayed()`、`Future.sync()`、`Future.value()`以及`Future.error()`：

- `Future()`构造函数创建一个异步执行计算的`Future`对象。
- `Future.delayed()`构造函数创建一个在由`Duration`对象指定的延迟后执行计算的`Future`对象。
- `Future.sync()`构造函数创建一个立即执行计算的`Future`对象。
- `Future.value()`构造函数创建一个以给定值完成的`Future`对象。
- `Future.error()`构造函数创建一个以给定错误和可选堆栈跟踪完成的`Future`对象。

清单 9-7 展示了使用不同`Future`构造函数的示例。

```
Future(() => 1).then(print);
Future.delayed(Duration(seconds: 3), () => 1).then(print);
Future.sync(() => 1).then(print);
Future.value(1).then(print);
Future.error(Error()).catchError(print);
Listing 9-7
Create Future objects
```

## 9.4 使用 Streams

### 问题

你想处理一个事件流。

### 解决方案

使用`Stream<T>`类及其子类。

### 讨论

借助`Future`类，我们可以表示一个可能在将来可用的单一值。然而，我们可能还需要处理一系列事件。`dart:async`库中的`Stream<T>`类代表了异步事件的源。为此，`Future`类提供了`asStream()`方法，用于创建一个包含当前`Future`对象结果的`Stream`。

如果你有使用响应式流（[`www.reactive-streams.org/`](http://www.reactive-streams.org/)）的经验，你可能会发现 Dart 中的`Stream`是一个类似的概念。流中可以包含三种类型的事件：

- 数据事件：表示流中的实际数据。这些事件也被称为流中的元素。
- 错误事件：表示发生的错误。
- 完成事件：表示已到达流的末尾。之后将不再发出更多事件。

要从流中接收事件，你可以使用`listen()`方法来设置监听器。`listen()`方法的返回值是一个`StreamSubscription`对象，它代表当前活动的订阅。根据流允许的订阅数量，流分为两种类型：

- 单订阅流：在流的整个生命周期中只允许一个监听器。它仅在设置了监听器时才开始发出事件，并在监听器取消订阅时停止发出事件。
- 广播流：允许任意数量的监听器。事件在准备好时就会被发出，即使没有已订阅的监听器。

对于给定的`Stream`对象，可以使用`isBroadcast`属性来检查它是否为广播流。你可以使用`asBroadcastStream()`方法从单订阅流创建一个广播流。

#### 流订阅

表 9-1 展示了`listen()`方法的参数。你可以为不同的事件提供任意数量的处理程序，并忽略那些不感兴趣的事件。

**表 9-1：`listen()`方法的参数**

| 名称 | 类型 | 描述 |
| --- | --- | --- |
| `onData` | `void (T event)` | 数据事件的处理程序。 |
| `onError` | `Function` | 错误事件的处理程序。 |
| `onDone` | `void ()` | 完成事件的处理程序。 |
| `cancelOnError` | `bool` | 是否在发出第一个错误事件时取消订阅。 |

在清单 9-8 中，提供了三种事件类型的处理程序。

```
Stream.fromIterable([1, 2, 3]).listen(
(value) => print(value),
onError: (error) => print('error: $error'),
onDone: () => print('done'),
cancelOnError: true,
);
Listing 9-8
Use listen() method
```

通过`listen()`方法返回的`StreamSubscription`对象，你可以管理订阅。表 9-2 展示了`StreamSubscription`类的方法。

**表 9-2：`StreamSubscription`的方法**

| 名称 | 描述 |
| --- | --- |
| `cancel()` | 取消此订阅。 |
| `pause([Future resumeSignal])` | 请求流暂停事件发送。如果提供了`resumeSignal`参数，则当该`Future`完成时流将恢复。 |
| `resume()` | 在暂停后恢复流。 |
| `onData()` | 替换数据事件处理程序。 |
| `onError()` | 替换错误事件处理程序。 |
| `onDone()` | 替换完成事件处理程序。 |
| `asFuture([E futureValue])` | 返回一个处理流完成的`Future`。 |

`asFuture()`方法在你想要处理流完成时非常有用。由于流可以正常完成或以错误结束，使用此方法会覆盖现有的`onDone`和`onError`回调。在发生错误事件时，订阅会被取消，并且返回的`Future`对象会以该错误完成。在完成事件时，`Future`对象则以给定的`futureValue`完成。



### 流转换

流的强大之处在于可以对流应用各种转换，从而得到另一个流或一个值。表 9-3 展示了 `Stream` 类中返回另一个 `Stream` 对象的方法。

表 9-3  
流转换

| 名称 | 描述 |
| --- | --- |
| `asyncExpand<E>(Stream<E> convert(T event))` | 将每个元素转换成一个流，并将这些流中的元素连接起来作为新的流。 |
| `asyncMap<E>(FutureOr<E> convert(T event))` | 将每个元素转换成一个新的事件。 |
| `distinct([bool equals(T previous, T next)])` | 跳过重复的元素。 |
| `expand<S>(Iterable<S> convert(T element))` | 将每个元素转换成一个元素序列。 |
| `handleError(Function onError, { bool test(dynamic error) })` | 处理流中的错误。 |
| `map<S>(S convert(T event))` | 将每个元素转换成一个新的事件。 |
| `skip(int count)` | 跳过流中的指定数量的元素。 |
| `skipWhile(bool test(T element))` | 当元素满足谓词时跳过它们。 |
| `take(int count)` | 仅从流中取出前指定数量的元素。 |
| `takeWhile(bool test(T element))` | 当元素满足谓词时取出它们。 |
| `timeout(Duration timeLimit, { void onTimeout(EventSink<T> sink) })` | 当两个事件之间的时间超过时间限制时处理错误。 |
| `transform<S>(StreamTransformer<T, S> streamTransformer)` | 对流进行转换。 |
| `where(bool test(T event))` | 过滤流中的元素。 |

清单 9-9 展示了流转换的使用示例。每条语句下方的代码展示了执行结果。

```
Stream.fromIterable([1, 2, 3]).asyncExpand((int value) {
return Stream.fromIterable([value * 5, value * 10]);
}).listen(print);
// -> 5, 10, 10, 20, 15, 30
Stream.fromIterable([1, 2, 3]).expand((int value) {
return [value * 5, value * 10];
}).listen(print);
// -> 5, 10, 10, 20, 15, 30
Stream.fromIterable([1, 2, 3]).asyncMap((int value) {
return Future.delayed(Duration(seconds: 1), () => value * 10);
}).listen(print);
// -> 10, 20, 30
Stream.fromIterable([1, 2, 3]).map((value) => value * 10).listen(print);
// -> 10, 20, 30
Stream.fromIterable([1, 1, 2]).distinct().listen(print);
// -> 1, 2
Stream.fromIterable([1, 2, 3]).skip(1).listen(print);
// -> 2, 3
Stream.fromIterable([1, 2, 3])
.skipWhile((value) => value % 2 == 1)
.listen(print);
// -> 2, 3
Stream.fromIterable([1, 2, 3]).take(1).listen(print);
// -> 1
Stream.fromIterable([1, 2, 3])
.takeWhile((value) => value % 2 == 1)
.listen(print);
// -> 1
Stream.fromIterable([1, 2, 3]).where((value) => value % 2 == 1).listen(print);
// -> 1, 3
```

清单 9-9  
流转换

`Stream` 类中还有其他返回 `Future` 对象的方法；参见表 9-4。这些操作返回的是单个值而非流。

表 9-4  
用于获取单一值的方法

| 名称 | 描述 |
| --- | --- |
| `any(bool test(T element))` | 检查流中是否有任意元素匹配谓词。 |
| `every(bool test(T element))` | 检查流中是否所有元素都匹配谓词。 |
| `contains(Object needle)` | 检查流是否包含给定元素。 |
| `drain<E>([E futureValue])` | 丢弃流中的所有元素。 |
| `elementAt(int index)` | 获取指定索引处的元素。 |
| `firstWhere(bool test(T element), { T orElse() })` | 找到第一个匹配谓词的元素。 |
| `lastWhere(bool test(T element), { T orElse() })` | 找到最后一个匹配谓词的元素。 |
| `singleWhere(bool test(T element), { T orElse() })` | 找到唯一一个匹配谓词的元素。 |
| `fold<S>(S initialValue, S combine(S previous, T element))` | 将流中的元素合并为一个单独的值。 |
| `forEach(void action(T element))` | 对流中的每个元素执行一个操作。 |
| `join([String separator = ""])` | 将元素合并成一个字符串。 |
| `pipe(StreamConsumer<T> streamConsumer)` | 将事件导入一个 `StreamConsumer`。 |
| `reduce(T combine(T previous, T element))` | 将流中的元素合并为一个单独的值。 |
| `toList()` | 将元素收集到一个列表中。 |
| `toSet()` | 将元素收集到一个集合中。 |

清单 9-10 展示了使用表 9-4 中方法的示例。每条语句下方的代码展示了执行结果。

```
Stream.fromIterable([1, 2, 3]).forEach(print);
// -> 1, 2, 3
Stream.fromIterable([1, 2, 3]).contains(1).then(print);
// -> true
Stream.fromIterable([1, 2, 3]).any((value) => value % 2 == 0).then(print);
// -> true
Stream.fromIterable([1, 2, 3]).every((value) => value % 2 == 0).then(print);
// -> false
Stream.fromIterable([1, 2, 3]).fold(0, (v1, v2) => v1 + v2).then(print);
// -> 6
Stream.fromIterable([1, 2, 3]).reduce((v1, v2) => v1 * v2).then(print);
// -> 6
Stream.fromIterable([1, 2, 3])
.firstWhere((value) => value % 2 == 1)
.then(print);
// -> 1
Stream.fromIterable([1, 2, 3])
.lastWhere((value) => value % 2 == 1)
.then(print);
// -> 3
Stream.fromIterable([1, 2, 3])
.singleWhere((value) => value % 2 == 1)
.then(print);
// -> Unhandled exception: Bad state: Too many elements
```

清单 9-10  
返回 `Future` 对象的方法

## 9.5 创建流

### 问题

你想创建 `Stream` 对象。

### 解决方案

使用不同的 `Stream` 构造器。

### 讨论

有几种不同的 `Stream` 构造器用于创建 `Stream` 对象：

*   `Stream.empty()` 构造器创建一个空的广播流。
*   `Stream.fromFuture()` 构造器从一个 `Future` 对象创建单订阅流。
*   `Stream.fromFutures()` 构造器从一个 `Future` 对象列表创建流。
*   `Stream.fromInterable()` 构造器从一个 `Iterable` 对象的元素创建单订阅流。
*   `Stream.periodic()` 构造器创建一个流，按给定的时间间隔定期发射数据事件。

清单 9-11 展示了不同 `Stream` 构造器的使用示例。

```
Stream.fromIterable([1, 2, 3]).listen(print);
Stream.fromFuture(Future.value(1)).listen(print);
Stream.fromFutures([Future.value(1), Future.error('error'), Future.value(2)])
.listen(print);
Stream.periodic(Duration(seconds: 1), (int count) => count * 2)
.take(5)
.listen(print);
```

清单 9-11  
使用 `Stream` 构造器

另一种创建流的方法是使用 `StreamController` 类。一个 `StreamController` 对象可以发送不同的事件到它所控制的流。默认的 `StreamController()` 构造器创建单订阅流，而 `StreamController.broadcast()` 构造器创建广播流。使用 `StreamController`，你可以以编程方式生成流中的元素。

在清单 9-12 中，不同的事件被发送到由 `StreamController` 对象控制的流中。

```
StreamController controller = StreamController();
controller.add(1);
controller.add(2);
controller.stream.listen(print, onError: print, onDone: () => print('done'));
controller.addError('error');
controller.add(3);
controller.close();
```

清单 9-12  
使用 `StreamController`

## 9.6 基于流和 Future 构建小部件

### 问题

你想构建一个小部件，该小部件能根据流或 future 中的数据更新其内容。

### 解决方案

使用 `StreamBuilder<T>` 或 `FutureBuilder<T>` 小部件。



### 讨论

给定一个`Steam`或`Future`对象，您可能希望构建一个根据其中数据更新其内容的小部件。您可以使用`StreamBuilder<T>`小部件来处理`Stream`对象，使用`FutureBuilder<T>`小部件来处理`Future`对象。表 9-5 展示了`StreamBuilder<T>`构造函数的参数。

**表 9-5** `StreamBuilder<T>`的参数

| 名称 | 类型 | 描述 |
| --- | --- | --- |
| `stream` | `Stream<T>` | 构建器所使用的流。 |
| `builder` | `AsyncWidgetBuilder<T>` | 用于构建小部件的构建器函数。 |
| `initialData` | `T` | 用于构建小部件的初始数据。 |

`AsyncWidgetBuilder`是函数类型`Widget (BuildContext context, AsyncSnapshot<T> snapshot)`的 typedef。`AsyncSnapshot`类表示与异步计算交互的快照。表 9-6 展示了`AsyncSnapshot<T>`类的属性。

**表 9-6** `AsyncSnapshot<T>`的属性

| 名称 | 类型 | 描述 |
| --- | --- | --- |
| `connectionState` | `ConnectionState` | 与异步计算的连接状态。 |
| `data` | `T` | 异步计算接收到的最新数据。 |
| `error` | `Object` | 异步计算接收到的最新错误对象。 |
| `hasData` | `bool` | `data`属性是否不为`null`。 |
| `hasError` | `bool` | `error`属性是否不为`null`。 |

您可以使用`connectionState`的值来确定连接状态。表 9-7 展示了`ConnectionState`枚举的值。

**表 9-7** `ConnectionState`的值

| 名称 | 描述 |
| --- | --- |
| `none` | 未连接到异步计算。 |
| `waiting` | 已连接到异步计算并等待交互。 |
| `active` | 已连接到一个活动的异步计算。 |
| `done` | 已连接到一个终止的异步计算。 |

当使用`StreamBuilder`小部件构建 UI 时，典型的方式是根据连接状态返回不同的小部件。例如，如果连接状态是`waiting`，则可能返回一个进度指示器。

在清单 9-13 中，流包含五个元素，每秒钟生成一个。如果连接状态是`none`或`waiting`，则返回一个`CircularProgressIndicator`小部件。如果状态是`active`或`done`，则根据`data`和`error`属性的值返回一个`Text`小部件。

```
class StreamBuilderPage extends StatelessWidget {
final Stream _stream =
Stream.periodic(Duration(seconds: 1), (int value) => value * 10).take(5);
@override
Widget build(BuildContext context) {
return Scaffold(
appBar: AppBar(
title: Text('Stream Builder'),
),
body: Center(
child: StreamBuilder(
stream: _stream,
initialData: 0,
builder: (BuildContext context, AsyncSnapshot snapshot) {
switch (snapshot.connectionState) {
case ConnectionState.none:
case ConnectionState.waiting:
return CircularProgressIndicator();
case ConnectionState.active:
case ConnectionState.done:
if (snapshot.hasData) {
return Text('${snapshot.data ?? "}');
} else if (snapshot.hasError) {
return Text(
'${snapshot.error}',
style: TextStyle(color: Colors.red),
);
}
}
return null;
},
),
),
);
}
}
```

**清单 9-13** 使用`StreamBuilder`

`FutureBuilder`小部件的用法与`StreamBuilder`小部件类似。当将`FutureBuilder`与`Future`对象一起使用时，您可以先使用`asStream()`方法将`Future`对象转换为`Stream`对象，然后对转换后的`Stream`对象使用`StreamBuilder`。

在清单 9-14 中，我们使用了不同的方式来构建 UI。我们不是检查连接状态，而是使用`hasData`和`hasError`属性来检查状态。

```
class FutureBuilderPage extends StatelessWidget {
final Future _future = Future.delayed(Duration(seconds: 1), () {
if (Random().nextBool()) {
return 1;
} else {
throw 'invalid value';
}
});
@override
Widget build(BuildContext context) {
return Scaffold(
appBar: AppBar(
title: Text('Future Builder'),
),
body: Center(
child: FutureBuilder(
future: _future,
builder: (BuildContext context, AsyncSnapshot snapshot) {
if (snapshot.hasData) {
return Text('${snapshot.data}');
} else if (snapshot.hasError) {
return Text(
'${snapshot.error}',
style: TextStyle(color: Colors.red),
);
} else {
return CircularProgressIndicator();
}
},
),
),
);
}
}
```

**清单 9-14** 使用`FutureBuilder`

## 9.7 处理简单 JSON 数据

### 问题

您想要一种简单的方法来处理 JSON 数据。

### 解决方案

使用`dart:convert`库中的`jsonEncode()`和`jsonDecode()`函数。

### 讨论

JSON 是一种流行的 Web 服务数据格式。为了与后端服务交互，您可能需要在两种场景中处理 JSON 数据：

*   JSON 数据序列化：将 Dart 中的对象转换为 JSON 字符串。
*   JSON 数据反序列化：将 JSON 字符串转换为 Dart 中的对象。

对于这两种场景，如果您只是偶尔需要处理简单的 JSON 数据，那么使用`dart:convert`库中的`jsonEncode()`和`jsonDecode()`函数是一个不错的选择。`jsonEncode()`函数将 Dart 对象转换为字符串，而`jsonDecode()`函数将字符串转换为 Dart 对象。在清单 9-15 中，`data`对象首先被序列化为 JSON 字符串，然后 JSON 字符串再次被反序列化为 Dart 对象。

```
var data = {
'name': 'Test',
'count': 100,
'valid': true,
'list': [
1,
2,
{
'nested': 'a',
'value': 123,
},
],
};
String str = jsonEncode(data);
print(str);
Object obj = jsonDecode(str);
print(obj);
```

**清单 9-15** 处理 JSON 数据

`dart:convert`库中的 JSON 编码器仅支持有限的数据类型，包括数字、字符串、布尔值、`null` 、列表以及键为字符串的映射。要编码其他类型的对象，您需要使用`toEncodable`参数提供一个函数，该函数首先将对象转换为可编码的值。默认的`toEncodable`函数会调用对象的`toJson()`方法。为需要序列化为 JSON 字符串的自定义类添加`toJson()`方法是一种常见做法。

在清单 9-16 中，`ToEncode`类的`toJson()`方法返回一个列表，该列表将被用作 JSON 序列化的输入。

```
class ToEncode {
ToEncode(this.v1, this.v2);
final String v1;
final String v2;
Object toJson() {
return [v1, v2];
}
}
print(jsonEncode(ToEncode('v1', 'v2')));
```

**清单 9-16** 使用`toJson()`函数

如果您希望序列化的 JSON 字符串有缩进，您需要直接使用`JsonEncoder`类。在清单 9-17 中，使用了两个空格作为缩进。

```
String indentString = JsonEncoder.withIndent('  ').convert(data);
print(indentString);
```

**清单 9-17** 添加缩进

## 9.8 处理复杂 JSON 数据

### 问题

您想要一种类型安全的方式来处理 JSON 数据。

### 解决方案

使用`json_annotation`和`json_serializable`库。



### 讨论

使用`dart:convert`库中的`jsonEncode()`和`jsonDecode()`函数可以轻松处理简单的 JSON 数据。当 JSON 数据结构复杂时，使用这两个函数就不太方便了。在反序列化 JSON 字符串时，结果通常是列表或映射。如果 JSON 数据具有嵌套结构，从列表或映射中提取值并不容易。在序列化对象时，你需要为这些类添加`toJson()`方法来构建列表或映射。使用`json_annotation`和`json_serializable`库进行代码生成可以简化这些任务。

`json_annotation`库提供了用于自定义 JSON 序列化和反序列化行为的注解。`json_serializable`库提供了生成处理 JSON 数据的代码的构建过程。要使用这两个库，你需要将它们添加到`pubspec.yaml`文件中。在清单 9-18 中，`json_annotation`库被添加到`dependencies`，而`json_serializable`库被添加到`dev_dependencies`。

```
dependencies:
json_annotation: ².0.0
dev_dependencies:
build_runner: ¹.0.0
json_serializable: ².0.0
Listing 9-18
添加 json_annotation 和 json_serializable
```

在清单 9-19 中，`Person`类位于`json_serialize.dart`文件中。注解`@JsonSerializable()`表示要为`Person`类生成代码。生成的代码位于`json_serialize.g.dart`文件中。清单 9-19 中使用的`_$PersonFromJson()`和`_$PersonToJson()`函数来自生成的文件。`_$PersonFromJson()`函数用于`Person.fromJson()`构造函数，而`_$PersonToJson()`函数用于`toJson()`方法。

```
import 'package:json_annotation/json_annotation.dart';
part 'json_serialize.g.dart';
@JsonSerializable()
class Person {
Person({this.firstName, this.lastName, this.email});
final String firstName;
final String lastName;
final String email;
factory Person.fromJson(Map json) => _$PersonFromJson(json);
Map toJson() => _$PersonToJson(this);
}
Listing 9-19
使用 json_serializable
```

要生成代码，你需要运行`flutter packages pub run build_runner build`命令。清单 9-20 展示了生成的文件。

```
part of 'json_serialize.dart';
Person _$PersonFromJson(Map json) {
return Person(
firstName: json['firstName'] as String,
lastName: json['lastName'] as String,
email: json['email'] as String);
}
Map _$PersonToJson(Person instance) => {
'firstName': instance.firstName,
'lastName': instance.lastName,
'email': instance.email
};
Listing 9-20
处理 JSON 数据的生成代码
```

`JsonSerializable`注解具有不同的属性来自定义行为；参见表 9-8。

表 9-8 `JsonSerializable`的属性

| 名称 | 默认值 | 描述 |
| --- | --- | --- |
| `anyMap` | `false` | 当为`true`时，使用`Map`作为映射类型；否则，使用`Map<String, dynamic>`。 |
| `checked` | `false` | 是否添加额外检查以验证数据类型。 |
| `createFactory` | `true` | 是否生成将映射转换为对象的函数。 |
| `createToJson` | `true` | 是否生成可以用作`toJson()`函数的函数。 |
| `disallowUnrecognizedKeys` | `false` | 当为`true`时，无法识别的键被视为错误；否则，它们将被忽略。 |
| `explicitToJson` | `false` | 当为`true`时，生成的`toJson()`函数会在嵌套对象上使用`toJson`。 |
| `fieldRename` | `FieldRename.none` | 用于将类字段名称转换为 JSON 映射键的策略。 |
| `generateToJsonFunction` | `true` | 当为`true`时，生成顶层函数；否则，生成一个包含`toJson()`方法的 mixin 类。 |
| `includeIfNull` | `true` | 是否包含值为`null`的字段。 |
| `nullable` | `true` | 是否优雅地处理`null`值。 |
| `useWrappers` | `false` | 是否使用包装类以最小化序列化期间`Map`和`List`实例的使用。 |

`generateToJsonFunction`属性决定了`toJson()`函数是如何生成的。当值为`true`时，会生成如清单 9-20 中所示的`_$PersonToJson()`等顶层函数。在清单 9-21 中，`User`类的`generateToJsonFunction`属性被设置为`false`。

```
@JsonSerializable(
generateToJsonFunction: false,
)
class User extends Object with _$UserSerializerMixin {
User(this.name);
final String name;
}
Listing 9-21
User 类
```

在清单 9-22 中，生成的不是一个函数，而是包含了`toJson()`方法的`_$UserSerializerMixin`类。清单 9-21 中的`User`类只需使用这个 mixin 类即可。

```
User _$UserFromJson(Map json) {
return User(json['name'] as String);
}
abstract class _$UserSerializerMixin {
String get name;
Map toJson() => {'name': name};
}
Listing 9-22
为 User 类生成的代码
```

`JsonKey`注解指定了字段如何被序列化。表 9-9 展示了`JsonKey`的属性。

表 9-9 `JsonKey`的属性

| 名称 | 描述 |
| --- | --- |
| `name` | JSON 映射键。如果为`null`，则使用字段名称。 |
| `nullable` | 是否优雅地处理`null`值。 |
| `includeIfNull` | 如果值为`null`，是否包含此字段。 |
| `ignore` | 是否忽略此字段。 |
| `fromJson` | 用于反序列化此字段的函数。 |
| `toJson` | 用于序列化此字段的函数。 |
| `defaultValue` | 用作默认值的值。 |
| `required` | JSON 映射中是否必须包含此字段。 |
| `disallowNullValue` | 是否禁止`null`值。 |

清单 9-23 展示了一个使用`JsonKey`的示例。

```
@JsonKey(
name: 'first_name',
required: true,
includeIfNull: true,
)
final String firstName;
Listing 9-23
使用 JsonKey
```

`JsonValue`注解指定用于序列化的枚举值。在清单 9-24 中，`JsonValue`注解被添加到`Color`的所有枚举值上。

```
enum Color {
@JsonValue('R')
Red,
@JsonValue('G')
Green,
@JsonValue('B')
Blue
}
Listing 9-24
使用 JsonValue
```

`JsonLiteral`注解从文件中读取 JSON 数据并将内容转换为对象。它允许轻松访问静态 JSON 数据文件的内容。在清单 9-25 中，`JsonLiteral`注解被添加到`data` getter 上。`_$dataJsonLiteral`是 JSON 文件中数据的生成变量。

```
@JsonLiteral('data.json', asConst: true)
Map get data => _$dataJsonLiteral;
Listing 9-25
使用 JsonLiteral
```

## 9.9 处理 XML 数据

### 问题

你想在 Flutter 应用中处理 XML 数据。

### 解决方案

使用`xml`库。

### 讨论

XML 是一种流行的数据交换格式。你可以在 Flutter 应用中使用`xml`库来处理 XML 数据。首先，你需要将`xml: ³.3.1`添加到`pubspec.yaml`文件的`dependencies`中。与 JSON 数据类似，XML 数据有两种使用场景：

*   解析 XML 文档并查询数据。

*   构建 XML 文档。



#### 解析 XML 文档

要解析 XML 文档，需要使用 `parse()` 函数，该函数接收一个 XML 字符串作为输入，并返回解析后的 `XmlDocument` 对象。利用 `XmlDocument` 对象，你可以查询和遍历 XML 文档树以从中提取数据。

要查询文档树，可以使用 `findElements()` 和 `findAllElements()` 方法。这两个方法接受一个标签名和一个可选的命名空间作为参数，并返回一个 `Iterable<XmlElement>` 对象。它们的区别在于：`findElements()` 方法仅搜索直接子元素，而 `findAllElements()` 方法则搜索所有后代子元素。若要遍历文档树，可以使用表 9-10 中列出的属性。

**表 9-10** `XmlParent` 的属性

| 名称 | 类型 | 描述 |
| --- | --- | --- |
| `children` | `XmlNodeList<XmlNode>` | 该节点的直接子节点。 |
| `ancestors` | `Iterable<XmlNode>` | 该节点的祖先节点，按文档逆序排列。 |
| `descendants` | `Iterable<XmlNode>` | 该节点的后代节点，按文档顺序排列。 |
| `attributes` | `List<XmlAttribute>` | 该节点的属性节点，按文档顺序排列。 |
| `preceding` | `Iterable<XmlNode>` | 该节点开始标签之前的节点，按文档顺序排列。 |
| `following` | `Iterable<XmlNode>` | 该节点结束标签之后的节点，按文档顺序排列。 |
| `parent` | `XmlNode` | 该节点的父节点，可以为 `null`。 |
| `firstChild` | `XmlNode` | 该节点的第一个子节点，可以为 `null`。 |
| `lastChild` | `XmlNode` | 该节点的最后一个子节点，可以为 `null`。 |
| `nextSibling` | `XmlNode` | 该节点的下一个兄弟节点，可以为 `null`。 |
| `previousSibling` | `XmlNode` | 该节点的上一个兄弟节点，可以为 `null`。 |
| `root` | `XmlNode` | 树的根节点。 |

在代码清单 9-26 中，输入的 XML 字符串（摘录自 [`https://msdn.microsoft.com/en-us/windows/desktop/ms762271`](https://msdn.microsoft.com/en-us/windows/desktop/ms762271)）被解析，并查询第一个 `book` 元素。然后提取了 `title` 元素的文本和 `id` 属性的值。

```
String xmlStr = "'

Gambardella, Matthew
XML Developer's Guide
Computer
44.95
2000-10-01
An in-depth look at creating applications
with XML.

Ralls, Kim
Midnight Rain
Fantasy
5.95
2000-12-16
A former architect battles corporate zombies, an evil sorceress, and her own childhood to become queen of the world.

"';
XmlDocument document = parse(xmlStr);
XmlElement firstBook = document.rootElement.findElements('book').first;
String title = firstBook.findElements('title').single.text;
String id = firstBook.attributes
.firstWhere((XmlAttribute attr) => attr.name.local == 'id')
.value;
print('$id => $title');
代码清单 9-26
XML 文档解析与查询
```

#### 构建 XML 文档

要构建 XML 文档，可以使用 `XmlBuilder` 类。`XmlBuilder` 类提供了构建 XML 文档不同组成部分的方法，见表 9-11。利用这些方法，我们可以自上而下地构建 XML 文档，即从根元素开始，逐层构建嵌套内容。

**表 9-11** `XmlBuilder` 的方法

| 名称 | 描述 |
| --- | --- |
| `element()` | 创建带有指定标签名、命名空间、属性和嵌套内容的 `XmlElement` 节点。 |
| `attribute()` | 创建带有指定名称、值、命名空间和类型的 `XmlAttribute` 节点。 |
| `text()` | 创建带有指定文本的 `XmlText` 节点。 |
| `namespace()` | 将命名空间 `prefix` 绑定到 `uri`。 |
| `cdata()` | 创建带有指定文本的 `XmlCDATA` 节点。 |
| `comment()` | 创建带有指定文本的 `XmlComment` 节点。 |
| `processing()` | 创建带有指定 `target` 和 `text` 的 `XmlProcessing` 节点。 |

构建完成后，可以使用 `XmlBuilder` 的 `build()` 方法将结果构建为 `XmlNode`。在代码清单 9-27 中，根元素是一个带有 `id` 属性的 `note` 元素。`nest` 参数的值是一个函数，该函数使用构建器方法来构建节点元素的内容。

```
XmlBuilder builder = XmlBuilder();
builder.processing('xml', 'version="1.0"');
builder.element(
'note',
attributes: {
'id': '001',
},
nest: () {
builder.element('from', nest: () {
builder.text('John');
});
builder.element('to', nest: () {
builder.text('Jane');
});
builder.element('message', nest: () {
builder
..text('Hello!')
..comment('message to send');
});
},
);
XmlNode xmlNode = builder.build();
print(xmlNode.toXmlString(pretty: true));
代码清单 9-27
使用 XmlBuilder
```

代码清单 9-28 展示了通过代码清单 9-27 中的代码构建的 XML 文档。

```

John
Jane
Hello!

代码清单 9-28
构建的 XML 文档
```

## 处理 HTML 数据

### 问题

你希望在 Flutter 应用中解析 HTML 文档。

### 解决方案

使用 `html` 库。

### 讨论

尽管 JSON 和 XML 数据格式在 Flutter 应用中很流行，但你仍可能需要解析 HTML 文档来提取数据。这个过程称为屏幕抓取。你可以使用 `html` 库来解析 HTML 文档。要使用该库，你需要在 `pubspec.yaml` 文件的 `dependencies` 中添加 `html: ⁰.13.4+1`。

`parse()` 函数将 HTML 字符串解析为 `Document` 对象。这些 `Document` 对象可以使用 W3C DOM API 进行查询和操作。在代码清单 9-29 中，首先解析了 HTML 字符串，然后使用 `getElementsByTagName()` 方法获取 `li` 元素，最后从 `li` 元素中提取 `id` 属性和文本。

```
import 'package:html/dom.dart';
import 'package:html/parser.dart' show parse;
void main() {
String htmlStr = "'

John
Jane
Mary

"';
Document document = parse(htmlStr);
var users = document.getElementsByTagName('li').map((Element element) {
return {
'id': element.attributes['id'],
'name': element.text,
};
});
print(users);
}
代码清单 9-29
解析 HTML 文档
```

## 发送 HTTP 请求

### 问题

你希望向后端服务发送 HTTP 请求。

### 解决方案

使用 `dart:io` 库中的 `HttpClient`。



### 讨论

HTTP 协议是公开 Web 服务的常用选择。其表示形式可以是 JSON 或 XML。通过使用 `dart:io` 库中的 `HttpClient` 类，你可以轻松地通过 HTTP 与后端服务进行交互。

要使用 `HttpClient` 类，你首先需要选择一种 HTTP 方法，然后准备用于请求的 `HttpClientRequest` 对象，并处理用于响应的 `HttpClientResponse` 对象。`HttpClient` 类根据不同 HTTP 方法提供了多对方法。例如，`get()` 和 `getUrl()` 方法都用于发送 HTTP GET 请求。区别在于 `get()` 方法接受 `host`、`port` 和 `path` 参数，而 `getUrl()` 方法接受 `Uri` 类型的 `url` 参数。你还可以看到其他方法对，如 `post()` 和 `postUrl()`、`put()` 和 `putUrl()`、`patch()` 和 `patchUrl()`、`delete()` 和 `deleteUrl()`，以及 `head()` 和 `headUrl()`。

这些方法返回 `Future<HttpClientRequest>` 对象。你需要使用 `then()` 方法链式处理返回的 `Future` 对象，以准备 `HttpClientRequest` 对象。例如，你可以修改 HTTP 请求头或写入请求体。`then()` 方法需要返回 `HttpClientRequest.close()` 方法的值，该值是一个 `Future<HttpClientResponse>` 对象。在 `Future<HttpClientResponse>` 对象的 `then()` 方法中，你可以使用此对象来获取响应体、请求头、Cookie 和其他信息。

在清单 9-30 中，`request.close()` 方法在第一个 `then()` 方法中直接被调用，因为我们不需要对 `HttpClientRequest` 对象做任何操作。`_handleResponse()` 函数将 HTTP 响应解码为 UTF-8 字符串并打印出来。`HttpClientResponse` 类实现了 `Stream<List<int>>`，因此响应体可以作为流来读取。

```
void _handleResponse(HttpClientResponse response) {
response.transform(utf8.decoder).listen(print);
}
HttpClient httpClient = HttpClient();
httpClient
.getUrl(Uri.parse('https://httpbin.org/get'))
.then((HttpClientRequest request) => request.close())
.then(_handleResponse);
清单 9-30
发送 HTTP GET 请求
```

如果你需要发送带有请求体的 HTTP POST、PUT 和 PATCH 请求，可以使用 `HttpClientRequest.write()` 方法来写入请求体；请参见清单 9-31。

```
httpClient
.postUrl(Uri.parse('https://httpbin.org/post'))
.then((HttpClientRequest request) {
request.write('hello');
return request.close();
}).then(_handleResponse);
清单 9-31
写入 HTTP 请求体
```

如果你需要修改 HTTP 请求头，可以使用 `HttpClientRequest.headers` 属性来修改 `HttpHeaders` 对象；请参见清单 9-32。

```
httpClient
.getUrl(Uri.parse('https://httpbin.org/headers'))
.then((HttpClientRequest request) {
request.headers.set(HttpHeaders.userAgentHeader, 'my-agent');
return request.close();
}).then(_handleResponse);
清单 9-32
修改 HTTP 请求头
```

如果你需要支持 HTTP 基本认证，可以使用 `HttpClient.addCredentials()` 方法来添加 `HttpClientBasicCredentials` 对象；请参见清单 9-33。

```
String username = 'username', password = 'password';
Uri uri = Uri.parse('https://httpbin.org/basic-auth/$username/$password');
httpClient.addCredentials(
uri, null, HttpClientBasicCredentials(username, password));
httpClient
.getUrl(uri)
.then((HttpClientRequest request) => request.close())
.then(_handleResponse);
清单 9-33
基本认证
```

## 9.12 连接到 WebSocket

### 问题

你希望在 Flutter 应用中连接到 WebSocket 服务器。

### 解决方案

使用 `dart:io` 库中的 `WebSocket` 类。

### 讨论

WebSocket 广泛应用于 Web 应用，以提供浏览器和服务器之间的双向通信。它们还可以提供后端数据的实时更新。如果你已经有一个与浏览器中运行的 Web 应用交互的 WebSocket 服务器，你可能也希望在 Flutter 应用中提供相同的功能。`dart:io` 库中的 `WebSocket` 类可用于实现 WebSocket 连接。

静态的 `WebSocket.connect()` 方法用于连接 WebSocket 服务器。你需要提供带有 `ws` 或 `wss` 协议的服务器 URL。你可以选择性地提供一个子协议列表和一个请求头映射表。`connect()` 方法的返回值是一个 `Future<WebSocket>` 对象。`WebSocket` 类实现了 `Stream` 类，因此你可以将来自服务器的数据作为流来读取。要向服务器发送数据，可以使用 `add()` 和 `addStream()` 方法。

在清单 9-34 中，WebSocket 连接到了演示回显服务器。通过使用 `listen()` 方法订阅 `WebSocket` 对象，我们可以处理来自服务器的数据。两次 `add()` 方法调用向服务器发送了两条消息。

```
WebSocket.connect('ws://demos.kaazing.com/echo').then((WebSocket webSocket) {
webSocket.listen(print, onError: print);
webSocket.add('hello');
webSocket.add('world');
webSocket.close();
}).catchError(print);
清单 9-34
连接到 WebSocket
```

## 9.13 连接到 Socket

### 问题

你希望连接到 Socket 服务器。

### 解决方案

使用 `dart:io` 库中的 `Socket` 类。

### 讨论

如果你希望在 Flutter 应用中连接 Socket 服务器，可以使用 `dart:io` 库中的 `Socket` 类。静态的 `Socket.connect()` 方法用于连接指定 `host` 和 `port` 的 Socket 服务器，并返回一个 `Future<Socket>` 对象。`Socket` 类实现了 `Stream<List<int>>`，因此你可以通过订阅流来读取来自服务器的数据。要向服务器发送数据，可以使用 `add()` 和 `addStream()` 方法。

在清单 9-35 中，一个 Socket 服务器在端口 `10080` 上启动。该服务器将接收到的字符串转换为大写并返回结果。

```
import 'dart:io';
import 'dart:convert';
void main() {
ServerSocket.bind('127.0.0.1', 10080).then((serverSocket) {
serverSocket.listen((socket) {
socket.addStream(socket
.transform(utf8.decoder)
.map((str) => str.toUpperCase())
.transform(utf8.encoder));
});
});
}
清单 9-35
简单的 Socket 服务器
```

在清单 9-36 中，`Socket.connect()` 方法用于连接清单 9-35 中所示的 Socket 服务器。从服务器接收到的数据被打印出来。向服务器发送了两条字符串。

```
void main() {
Socket.connect('127.0.0.1', 10080).then((socket) {
socket.transform(utf8.decoder).listen(print);
socket.write('hello');
socket.write('world');
socket.close();
});
}
清单 9-36
连接到 Socket 服务器
```

## 9.14 与基于 JSON 的 REST 服务交互

### 问题

你希望使用基于 JSON 的 REST 服务。

### 解决方案

使用 `HttpClient`、`json_serialize` 库和 `FutureBuilder` 小部件。



### 讨论

将移动应用后端通过 HTTP 协议以 JSON 格式暴露服务是一种常见选择。通过使用 `HttpClient`、`json_serialize` 库和 `FutureBuilder` 组件，你可以构建与这些 REST 服务交互的用户界面。本示例结合了清单 9-6、9-8 和 9-11 中的内容，提供了一个具体示例。

该示例使用 GitHub Jobs API（[`https://jobs.github.com/api`](https://jobs.github.com/api)）获取 GitHub 网站上的职位列表。在清单 9-37 中，`Job` 类代表一个职位信息。在 `JsonSerializable` 注解中，`createToJson` 属性被设置为 `false`，因为我们只需要从 API 解析 JSON 响应。`_parseDate` 函数用于解析 JSON 对象中 `created_at` 字段的字符串。你需要添加 `intl` 库才能使用 `DateFormat` 类。

```
part 'github_jobs.g.dart';
DateFormat _dateFormat = DateFormat('EEE MMM dd HH:mm:ss yyyy');
DateTime _parseDate(String str) =>
_dateFormat.parse(str.replaceFirst(' UTC', ""), true);
@JsonSerializable(
createToJson: false,
)
class Job {
Job();
String id;
String type;
String url;
@JsonKey(name: 'created_at', fromJson: _parseDate)
DateTime createdAt;
String company;
@JsonKey(name: 'company_url')
String companyUrl;
@JsonKey(name: 'company_logo')
String companyLogo;
String location;
String title;
String description;
@JsonKey(name: 'how-to-apply')
String howToApply;
factory Job.fromJson(Map json) => _$JobFromJson(json);
}
清单 9-37
Job 类
```

在清单 9-38 中，使用 `HttpClient` 对象向 GitHub Jobs API 发送 HTTP GET 请求，并通过 `jsonDecode()` 函数解析 JSON 响应。`FutureBuilder` 组件使用类型为 `Future<List<Job>>` 的 `Future` 对象来构建 UI。`JobsList` 组件接收一个 `List<Job>` 对象，并使用 `ListView` 组件显示列表。

```
class GitHubJobsPage extends StatelessWidget {
final Future> _jobs = HttpClient()
.getUrl(Uri.parse('https://jobs.github.com/positions.json'
'?description=java&location=new+york'))
.then((HttpClientRequest request) => request.close())
.then((HttpClientResponse response) {
return response.transform(utf8.decoder).join("").then((String content) {
return (jsonDecode(content) as List)
.map((json) => Job.fromJson(json))
.toList();
});
});
@override
Widget build(BuildContext context) {
return Scaffold(
appBar: AppBar(
title: Text('GitHub Jobs'),
),
body: FutureBuilder>(
future: _jobs,
builder: (BuildContext context, AsyncSnapshot> snapshot) {
if (snapshot.hasData) {
return JobsList(snapshot.data);
} else if (snapshot.hasError) {
return Center(
child: Text(
'${snapshot.error}',
style: TextStyle(color: Colors.red),
),
);
} else {
return Center(
child: CircularProgressIndicator(),
);
}
},
),
);
}
}
class JobsList extends StatelessWidget {
JobsList(this.jobs);
final List jobs;
@override
Widget build(BuildContext context) {
return ListView.separated(
itemBuilder: (BuildContext context, int index) {
Job job = jobs[index];
return ListTile(
title: Text(job.title),
subtitle: Text(job.company),
);
},
separatorBuilder: (BuildContext context, int index) {
return Divider();
},
itemCount: jobs.length,
);
}
}
清单 9-38
用于展示职位的组件
```

## 9.15 与 gRPC 服务交互

### 问题

你想与 gRPC 服务进行交互。

### 解决方案

使用 `grpc` 库。

### 讨论

gRPC（[`https://grpc.io/`](https://grpc.io/)）是一个高性能、开源的通用 RPC 框架。本示例展示了如何与 gRPC 服务交互。交互的 gRPC 服务是 gRPC 官方示例中的 greeter 服务（[`https://github.com/grpc/grpc/tree/master/examples/node`](https://github.com/grpc/grpc/tree/master/examples/node)）。你需要先启动 gRPC 服务器。

要在 Flutter 应用中使用此 gRPC 服务，你首先需要安装 Protocol Buffers 编译器（[`https://github.com/protocolbuffers/protobuf`](https://github.com/protocolbuffers/protobuf)）。下载适合你平台的发布文件并解压后，你需要将解压后的 `bin` 目录添加到 `PATH` 环境变量中。你可以运行 `protoc --version` 命令来验证安装。本示例使用的版本是 `3.7.1`。

你还需要安装 Dart protoc 插件（[`https://github.com/dart-lang/protobuf/tree/master/protoc_plugin`](https://github.com/dart-lang/protobuf/tree/master/protoc_plugin)）。最简单的安装方式是运行以下命令。

```
$ flutter packages pub global activate protoc_plugin
```

由于我们使用 `flutter packages` 来运行安装，二进制文件会被放在 Flutter SDK 的 `.pub-cache/bin` 目录下。你需要将此路径添加到 `PATH` 环境变量中。该插件需要 `dart` 命令可用，因此你还需要将 Flutter SDK 的 `bin/cache/dart-sdk/bin` 目录添加到 PATH 环境变量中。现在我们可以使用 `protoc` 来生成与 greeter 服务交互的 Dart 文件。在以下命令中，`lib/grpc/generated` 是生成文件的输出路径。`proto_file_path` 是 proto 文件的路径。`helloworld.proto` 文件包含 greeter 服务的定义。还需要将 `protobuf` 和 `grpc` 库添加到 `pubspec.yaml` 文件的 `dependencies` 中。

```
$ protoc --dart_out=grpc:lib/grpc/generated --proto_path= /helloworld.proto
```

生成的 `helloworld.pbgrpc.dart` 文件提供了 `GreeterClient` 类来与服务交互。在清单 9-39 中，创建了一个 `ClientChannel` 来连接 gRPC 服务器。创建 `GreeterClient` 对象时需要该通道。`sayHello()` 方法向服务器发送请求并接收响应。

```
import 'package:grpc/grpc.dart';
import 'generated/helloworld.pbgrpc.dart';
void main() async {
final channel = new ClientChannel('localhost',
port: 50051,
options: const ChannelOptions(
credentials: const ChannelCredentials.insecure()));
final stub = new GreeterClient(channel);
try {
var response = await stub.sayHello(new HelloRequest()..name = 'John');
print('Received: ${response.message}');
} catch (e) {
print('Caught error: $e');
}
await channel.shutdown();
}
清单 9-39
与 gRPC 服务交互
```

## 9.16 总结

本章重点介绍了与后端服务交互的不同方式，包括 HTTP、WebSocket、Socket 和 gRPC。Future 和 Stream 在异步计算中扮演着重要角色。本章还讨论了如何处理 JSON、XML 和 HTML 数据。在下一章中，我们将讨论 Flutter 应用中的状态管理。

