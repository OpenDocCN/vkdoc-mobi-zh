# 14. 测试与调试

本章涵盖了与测试和调试 Flutter 应用相关的实践方法。

## 14.1 编写单元测试

### 问题

你想要编写单元测试。

### 解决方案

使用`test`包中的 API。

### 讨论

单元测试在应用开发中非常重要。要在 Flutter 应用中编写测试，需将`test: ¹.5.3`添加到`pubspec.yaml`文件的`dev_dependencies`部分。测试文件通常放在`test`目录下。代码清单 14-1 中的`MovingBox`类是需要测试的类。`move()`方法更新内部的`_offset`变量。

```
class MovingBox {
MovingBox({Offset initPos = Offset.zero}) : _offset = initPos;
Offset _offset;
get offset => _offset;
void move(double dx, double dy) {
_offset += Offset(dx, dy);
}
}
代码清单 14-1
要测试的 Dart 类
```

代码清单 14-2 展示了`MovingBox`类的测试。`group()`函数创建一个组来描述一组测试。`test()`函数使用给定的描述和函数体创建一个测试用例。函数体是一个使用`expect()`函数声明要验证的期望的函数。要调用`expect()`函数，你需要提供实际值和一个用于检查该值的匹配器。匹配器可以是简单值，也可以是来自`matcher`包的函数。常见的匹配器函数包括`contains()`、`startsWith()`、`endsWith()`、`lessThan()`、`greaterThan()`和`inInclusiveRange()`。

```
void main() {
group('MovingBox', () {
test('position should be (0.0) by default', () {
expect(MovingBox().offset, Offset.zero);
});
test('postion should be initial value', () {
expect(MovingBox(initPos: Offset(10, 10)).offset, Offset(10, 10));
});
test('postion should be moved', () {
final box = MovingBox();
box.move(5, 5);
expect(box.offset, Offset(5, 5));
box.move(-1, -1);
expect(box.offset, Offset(4, 4));
});
});
}
代码清单 14-2
MovingBox 的测试
```

你可以使用`async`函数作为`expect()`函数的函数体来编写异步测试。在代码清单 14-3 中，第一个测试用例使用带有`await`的`async`函数来获取`Future`对象的值。在第二个测试用例中，`completion()`函数等待`Future`对象完成并验证其值。`throwsA()`函数验证`Future`对象是否抛出了给定的错误。在第三个测试用例中，`expectAsync1()`函数包装另一个函数以验证结果并检查其调用次数。

```
void main() {
test('future with async', () async {
var value = await Future.value(1);
expect(value, equals(1));
});
test('future', () {
expect(Future.value(1), completion(equals(1)));
expect(Future.error('error'), throwsA(equals('error')));
});
test('future callback', () {
Future.error('error').catchError(expectAsync1((error) {
expect(error, equals('error'));
}, count: 1));
});
}
代码清单 14-3
异步测试
```

你可以使用`setUp()`函数来添加一个在测试之前运行的函数。类似地，`tearDown()`函数用于添加一个在测试之后运行的函数。`setUp()`函数应用于准备测试用例运行的上下文。`tearDown()`函数应用于执行清理任务。`setUp()`和`tearDown()`函数通常成对出现。在代码清单 14-4 中，`setUp()`和`tearDown()`函数将被调用两次。

```
void main() {
setUp(() {
print('setUp');
});
test('action1', () {
print('action1');
});
test('action2', () {
print('action2');
});
tearDown(() {
print('tearDown');
});
}
代码清单 14-4
setUp() 和 tearDown() 函数
```

运行代码清单 14-4 中的测试用例后，输出应如代码清单 14-5 所示。

```
setUp
action1
tearDown
setUp
action2
tearDown
代码清单 14-5
使用 setUp() 和 tearDown() 函数的输出
```

## 14.2 在测试中使用模拟对象

### 问题

你想要在测试用例中模拟依赖项。

### 解决方案

使用`mockito`包。



### 讨论

在编写测试用例时，待测试的类可能依赖于需要外部资源的依赖项。例如，一个服务类需要访问后端 API 来获取数据。在测试这些类时，你不应使用真实的依赖关系。依赖外部资源会给测试用例的执行带来不确定性，并使其不稳定。使用实时服务也难以测试所有可能的场景。

更好的方法是创建模拟对象来替代这些依赖关系。借助模拟对象，你可以轻松模拟不同的场景。模拟对象是类的替代实现。你可以手动创建模拟对象，或使用 `mockito` 包。要使用 `mockito` 包，你需要在 `pubspec.yaml` 文件的 `dev_dependencies` 部分添加 `mockito: ⁴.0.0`。

清单 14-6 中的 `GitHubJobsClient` 类使用了来自 `http` 包的 `Client` 类来访问 GitHub Jobs API。

```
class GitHubJobsClient {
  GitHubJobsClient({@required this.httpClient}) : assert(httpClient != null);
  final http.Client httpClient;
  Future<List<Job>> getJobs(String keyword) async {
    Uri url = Uri.https(
      'jobs.github.com', '/positions.json', {'description': keyword});
    http.Response response = await httpClient.get(url);
    if (response.statusCode != 200) {
      throw Exception('Failed to get job listings');
    }
    return (jsonDecode(response.body) as List)
        .map((json) => Job.fromJson(json))
        .toList();
  }
}
清单 14-6
待测试的 GitHubJobsClient 类
```

为了测试 `GitHubJobsClient` 类，我们可以为 `http.Client` 对象创建一个模拟对象。在清单 14-7 中，`MockHttpClient` 类是对 `http.Client` 类的模拟类。在第一个测试用例中，当使用指定的 `Uri` 对象调用 `MockHttpClient` 的 `get()` 方法时，会返回一个包含 JSON 字符串的 `Future<Response>` 对象作为结果。我们可以验证 `GitHubJobsClient` 的 `getJobs()` 方法能够解析该响应并返回一个包含一个元素的 `List` 对象。在第二个测试用例中，`MockHttpClient` 的 `get()` 方法的返回结果被设置为一个包含 HTTP 500 错误的 `Future<Response>`。然后我们通过调用 `getJobs()` 方法来验证程序抛出了异常。

```
import 'package:mockito/mockito.dart';
class MockHttpClient extends Mock implements http.Client {}
void main() {
  group('getJobs', () {
    Uri url = Uri.https(
      'jobs.github.com', '/positions.json', {'description': 'flutter'});
    test('should return list of jobs', () {
      final httpClient = MockHttpClient();
      when(httpClient.get(url))
          .thenAnswer((_) async => http.Response('[{"id": "123"}]', 200));
      final jobsClient = GitHubJobsClient(httpClient: httpClient);
      expect(jobsClient.getJobs('flutter'), completion(hasLength(1)));
    });
    test('should throws an exception', () {
      final httpClient = MockHttpClient();
      when(httpClient.get(url))
          .thenAnswer((_) async => http.Response('error', 500));
      final jobsClient = GitHubJobsClient(httpClient: httpClient);
      expect(jobsClient.getJobs('flutter'), throwsException);
    });
  });
}
清单 14-7
使用模拟对象的 GitHubJobsClient 测试
```

## 14.3 编写 Widget 测试

### 问题

你想要编写测试用例来测试 Widget。

### 解决方案

使用 `flutter_test` 包。



### 讨论

使用 `test` 和 `mockito` 包足以编写 Dart 类的测试。然而，你需要使用 `flutter_test` 包来编写小部件的测试。`flutter_test` 包已经包含在由 `flutter create` 命令创建的新项目的 `pubspec.yaml` 文件中。小部件的测试用例使用 `testWidgets()` 函数声明。调用 `testWidgets()` 时，你需要提供一个描述和一个在 Flutter 测试环境中运行的回调。该回调接收一个 `WidgetTester` 对象，用于与小部件和测试环境进行交互。创建待测试的小部件后，你可以使用 `Finder` 对象和匹配器来验证小部件的状态。

表 14-1 显示了 `WidgetTester` 类的方法。`pumpWidget()` 方法通常是测试的入口点，它创建要测试的小部件。测试有状态小部件时，更改状态后，你需要调用 `pump()` 方法触发重建。如果小部件使用动画，你应该使用 `pumpAndSettle()` 方法等待动画完成。像 `enterText()` 和 `ensureVisible()` 这样的方法使用 `Finder` 对象来查找要交互的小部件。

**表 14-1**  
`WidgetTester` 的方法

| 名称 | 描述 |
| --- | --- |
| `pumpWidget()` | 渲染指定的小部件。 |
| `pump()` | 触发一个帧，导致小部件重建。 |
| `pumpAndSettle()` | 重复调用 `pump()` 方法，直到没有计划执行的帧。 |
| `enterText()` | 向文本输入小部件输入文本。 |
| `pageBack()` | 关闭当前页面。 |
| `runAsync()` | 异步运行回调。 |
| `dispatchEvent()` | 分发事件。 |
| `ensureVisible()` | 通过滚动其祖先 `Scrollable` 小部件使小部件可见。 |
| `drag()` | 按给定偏移量拖动小部件。 |
| `press()` | 按下小部件。 |
| `longPress()` | 长按小部件。 |
| `tap()` | 轻触小部件。 |

清单 14-8 中的 `ToUppercase` 小部件是一个要测试的有状态小部件。它有一个 `TextField` 小部件用于输入文本。当按钮被按下时，输入文本的大写形式会通过 `Text` 小部件显示出来。

```dart
class ToUppercase extends StatefulWidget {
@override
_ToUppercaseState createState() => _ToUppercaseState();
}
class _ToUppercaseState extends State {
final _controller = TextEditingController();
@override
Widget build(BuildContext context) {
return Column(
children: [
Row(
children: [
Expanded(child: TextField(controller: _controller)),
RaisedButton(
child: Text('Uppercase'),
onPressed: () {
setState(() {});
},
),
],
),
Text((_controller.text ?? ").toUpperCase()),
],
);
}
}
```
**清单 14-8**  
要测试的小部件

清单 14-9 展示了 `ToUppercase` 小部件的测试用例。`_wrapInMaterial()` 函数在测试前将 `ToUppercase` 小部件包裹在 `MaterialApp` 中。这是因为 `TextField` 小部件需要一个祖先 `Material` 小部件。在测试用例中，首先使用 `pumpWidget()` 渲染小部件。`find` 对象是 `CommonFinders` 类的一个顶级常量。它有便捷的方法来创建不同类型的 `Finder` 对象。这里我们查找类型为 `TextField` 的小部件，并使用 `enterText()` 输入文本 "abc"。然后轻触 `RaisedButton` 小部件，状态发生改变。需要调用 `pump()` 方法来触发重建。最后，我们验证是否存在一个文本为 "ABC" 的 `Text` 小部件。

**表 14-2**  
`CommonFinders` 的方法

| 名称 | 描述 |
| --- | --- |
| `byType()` | 按类型查找小部件。 |
| `byIcon()` | 按图标数据查找 `Icon` 小部件。 |
| `byKey()` | 通过特定的 `Key` 对象查找小部件。 |
| `byTooltip()` | 查找具有给定消息的 `Tooltip` 小部件。 |
| `byWidget()` | 按给定的小部件实例查找小部件。 |
| `text()` | 查找具有给定文本的 `Text` 和 `EditableText` 小部件。 |
| `widgetWithIcon()` | 查找包含具有该图标的子代小部件的小部件。 |
| `widgetWithText()` | 查找包含具有给定文本的 `Text` 子代的小部件。 |

```dart
Widget _wrapInMaterial(Widget widget) {
return MaterialApp(
home: Scaffold(
body: widget,
),
);
}
void main() {
testWidgets('ToUppercase', (WidgetTester tester) async {
await tester.pumpWidget(_wrapInMaterial(ToUppercase()));
await tester.enterText(find.byType(TextField), 'abc');
await tester.tap(find.byType(RaisedButton));
await tester.pump();
expect(find.text('ABC'), findsOneWidget);
});
}
```
**清单 14-9**  
测试 `ToUppercase` 小部件

`Finder` 对象与匹配器一起用于验证状态。有四个匹配器可与 `Finder` 对象一起使用：

*   `findsOneWidget`：期望恰好找到一个小部件。
*   `findsNothing`：期望没有找到小部件。
*   `findsNWidgets`：期望找到指定数量的小部件。
*   `findsWidgets`：期望至少找到一个小部件。

## 14.4 编写集成测试

### 问题

你希望编写在模拟器或真实设备上运行的集成测试。

### 解决方案

使用 `flutter_driver` 包。



### Discussion

单元测试和部件测试只能测试单个类、函数或部件。这些测试在开发或测试机器上运行，无法测试应用不同组件之间的集成。针对这种情况，应使用集成测试。

集成测试包含两个部分：第一部分是部署到模拟器或真实设备上的被检测应用；第二部分是驱动应用并验证应用状态的测试代码。被测试的应用与测试代码相互隔离，以避免干扰。

编写集成测试需要使用`flutter_driver`包。你需要将`flutter_driver`包添加到`pubspec.yaml`文件的`dev_dependencies`部分，如清单 [14-10] 所示。

```
dev_dependencies:
  flutter_driver:
    sdk: flutter
```

清单 14-10 添加`flutter_driver`包

集成测试文件通常放在`test_driver`目录中。本次测试的目标是用于搜索 GitHub 职位列表的页面。为需要被集成测试使用的部件提供`ValueKey`对象作为`key`参数非常重要，这有助于在测试用例中更容易地找到这些部件。在清单 [14-11] 中，`Key('keyword')`创建了一个名称为 "keyword" 的`ValueKey`对象。

```
TextField(
  key: Key('keyword'),
  controller: _controller,
)
```

清单 14-11 为部件添加 key

`test_driver`目录中的`github_jobs.dart`文件包含待测试页面的被检测版本。清单 [14-12] 展示了`github_jobs.dart`文件的内容。`flutter_driver`包中的`enableFlutterDriverExtension()`函数使 Flutter Driver 能够连接到应用。

```
void main() {
  enableFlutterDriverExtension();
  runApp(SampleApp());
}
```

清单 14-12 使用 Flutter Driver 测试的应用

清单 [14-13] 展示了`github_jobs_test.dart`文件的内容。文件名通过在应用文件名后附加`_test`后缀来选择。这是 Flutter Driver 用于查找运行被测应用的 Dart 文件的约定。在`setUpAll()`函数中，使用`FlutterDriver.connect()`连接到应用。在测试用例中，`find`是`CommonFinders`对象的顶级常量，它提供了方便的方法来创建`SerializableFinder`对象。`byValueKey()`方法通过指定的键找到清单 [14-11] 中的`TextField`部件。`FlutterDriver`的`tap()`方法点击该`TextField`部件使其获得焦点。然后使用`enterText()`方法将搜索关键词输入到已聚焦的`TextField`部件中。接着点击搜索按钮触发数据加载。如果数据加载成功，则出现带有`jobsList`键的`ListView`部件。`waitFor()`方法等待`ListView`部件出现。

```
void main() {
  group('GitHub Jobs', () {
    FlutterDriver driver;
    setUpAll(() async {
      driver = await FlutterDriver.connect();
    });
    test('searches by keyword', () async {
      await driver.tap(find.byValueKey('keyword'));
      await driver.enterText('android');
      await driver.tap(find.byValueKey('search'));
      await driver.waitFor(find.byValueKey('jobsList'),
          timeout: Duration(seconds: 5));
    });
    tearDownAll(() {
      if (driver != null) {
        driver.close();
      }
    });
  });
}
```

清单 14-13 使用 Flutter Driver 进行测试

现在我们可以使用以下命令来运行集成测试。Flutter Driver 将应用部署到模拟器或真实设备上，并运行测试代码以验证结果。

```
$ flutter driver --target=test_driver/github_jobs.dart
```

表 [14-3] 展示了`FlutterDriver`类的方法，这些方法可用于在测试期间与应用进行交互。如果你想执行自定义操作，可以在调用`enableFlutterDriverExtension()`函数时提供一个`DataHandler`函数。通过`requestData()`方法发送的消息将由该`DataHandler`处理。

**表 14-3** FlutterDriver 的方法

| 名称 | 描述 |
| --- | --- |
| `enterText()` | 向当前聚焦的文本输入框输入文本。 |
| `getText()` | 获取`Text`部件中的文本。 |
| `tap()` | 点击部件。 |
| `waitFor()` | 等待直到查找器定位到部件。 |
| `waitForAbsent()` | 等待直到查找器无法再定位到部件。 |
| `scroll()` | 按给定偏移量在部件中滚动。 |
| `scrollIntoView()` | 滚动部件的`Scrollable`祖先，直到部件可见。 |
| `scrollUntilVisible(SerializableFinder scrollable, SerializableFinder item)` | 在`scrollable`部件中重复调用`scroll()`，直到`item`可见，然后对`item`调用`scrollIntoView()`。 |
| `traceAction()` | 执行操作并返回其性能跟踪。 |
| `startTracing()` | 开始记录性能跟踪。 |
| `stopTracingAndDownloadTimeline()` | 停止记录性能跟踪并下载结果。 |
| `forceGC()` | 强制执行垃圾回收。 |
| `getRenderTree()` | 返回当前渲染树的转储。 |
| `requestData()` | 向应用发送消息并接收响应。 |
| `screenshot()` | 截取屏幕截图。 |

`FlutterDriver`类中的方法使用`SerializableFinder`对象来定位部件。表 [14-4] 展示了`CommonFinders`类中用于创建`SerializableFinder`对象的方法。这些方法仅支持使用`String`或`int`值作为参数，这是因为在发送到应用时需要对值进行序列化。

**表 14-4** flutter_driver 中 CommonFinders 的方法

| 名称 | 描述 |
| --- | --- |
| `byType()` | 通过类名查找部件。 |
| `byValueKey()` | 通过键查找部件。 |
| `byTooltip()` | 查找具有给定消息的工具提示的部件。 |
| `text()` | 查找包含给定文本的`Text`和`EditableText`部件。 |
| `pageBack()` | 查找返回按钮。 |

## 14.5 调试应用

### 问题

你想调试在应用中发现的问题。

### 解决方案

使用 Flutter SDK 提供的 IDE 和实用工具。



### 讨论

当代码在运行时未按预期工作，你需要调试代码以找出原因。借助 IDE 可以非常直接地调试 Flutter 应用。你可以在代码中添加断点，并以调试模式启动应用。

另一种常见的代码调试方法是使用 `print()` 函数将输出写入系统控制台。这些日志可以通过 `flutter logs` 命令查看。Android Studio 也会在控制台视图中显示这些日志。你也可以使用 `debugPrint()` 函数来限制输出，以避免日志被 Android 系统丢弃。

在创建你自己的组件时，应重写 `debugFillProperties()` 方法以添加自定义诊断属性。这些属性可以在 Flutter Inspector 中查看。在代码清单 14-14 中，`DebugWidget` 拥有 `name` 和 `price` 属性。在 `debugFillProperties()` 方法中，通过 `DiagnosticPropertiesBuilder` 对象添加了两个 `DiagnosticsProperty` 对象。

```
class DebugWidget extends StatelessWidget {
DebugWidget({Key key, this.name, this.price}) : super(key: key);
final String name;
final double price;
@override
Widget build(BuildContext context) {
return Text('$name - $price');
}
@override
void debugFillProperties(DiagnosticPropertiesBuilder properties) {
super.debugFillProperties(properties);
properties.add(StringProperty('name', name));
properties.add(DoubleProperty('price', price));
}
}
代码清单 14-14
debugFillProperties()
```

根据属性类型的不同，可以使用不同类型的 `DiagnosticsProperty` 子类。表 14-5 展示了常用的 `DiagnosticsProperty` 子类。

**表 14-5** CommonFinders 的方法

| 名称 | 描述 |
| --- | --- |
| `StringProperty` | 用于 `String` 属性。 |
| `DoubleProperty` | 用于 `double` 属性。 |
| `PercentProperty` | 将 `double` 属性格式化为百分比。 |
| `IntProperty` | 用于 `int` 属性。 |
| `FlagProperty` | 将 `bool` 属性格式化为标志。 |
| `EnumProperty` | 用于 `enum` 属性。 |
| `IterableProperty` | 用于 `Iterable` 属性。 |

## 14.6 小结

本章涵盖了与测试和调试 Flutter 应用相关的主题。

## 索引

### A

`Align` 组件  
`heightFactor` `widthFactor`  
`Alignment` 类  
`Alignment` 常量  
`AlignmentDirectional` 类  
`AlignmentDirectional` 实例  
`AlignmentDirectional` 常量  
`AlignmentGeometry` 类  
`ancestorWidgetOfExactType()` 方法  
`android` 目录  
动画创建  
`AnimatedWidget`  
`AnimationController` 类  
`AnimationStatus`, 值  
`build()` 方法  
`forward()` 方法  
`initState()` 方法  
方法  
状态监听器  
`curve` `Curves.easeInOut`  
曲线参数类型  
线性插值  
`animate()` 方法  
`ColorTween` 过渡  
`FadeTransition` `ScaleTransition`  
`AppLocalizations.load()` 方法  
`apply()` 方法  
`asBroadcastStream()` 方法  
`AspectRatio` 构造函数  
`AspectRatio` 组件  
`AssetImage` 类  
`asStream()` 方法  
`async` 函数

### B

`BoxConstraints` 类  
`BoxConstraints` 实例  
`BoxFit` 值  
`build()` 方法  
`BuildContext` 方法使用  
业务逻辑组件 (Bloc)  
核心概念  
定义  
`Equatable` 类  
事件  
`GitHubJobs` 类  
`http` 包  
`mapEventToState()` 函数  
`byValueKey()` 方法

### C

`calculate()` 函数  
摄像头  
摄像头插件  
`CameraController`, 方法  
iOS, 隐私要求  
选择  
`canGoBack()` 方法  
`catchError()` 方法  
`Center` 组件  
`chain()` 方法  
`Chained` `then()` 方法  
`Child` 组件  
`ChoiceChip` 组件  
`colorScheme.error` 属性  
`CommonFinders`  
`completion()` 函数  
复杂页面流程  
`Config` 类, 继承自组件  
`ConfigWidget`  
`ConstrainedBox` 构造函数  
`Constraints` 类  
`Container` 构造函数  
`Container` 组件  
`copyWith()` 方法  
`createElement()` 方法  
`createState()` 方法  
创建, Flutter  
Android Studio  
命令行  
VS Code  
`CrossAxisAlignment` 值  
跨平台代码  
内置类型  
布尔值  
列表和映射  
数字  
符文  
字符串  
符号  
级联运算符  
构造函数  
`dynamic` 类型  
枚举类型  
异常  
扩展类  
函数  
泛型  
继承  
接口  
库  
覆写  
运算符  
类型定义  
`CupertinoDialogAction`, 参数  
`CupertinoIcons` 类  
`CupertinoSwitch`  
`CupertinoThemeData` 类  
`CupertinoTheme.of()` 方法  
自定义主题

### D

`dart:io` 库  
Dart 观察器  
Dart 字符串  
`DateFormat` 类  
`debugFillProperties()` 方法  
`debugPrint()` 函数  
`DecoratedBox` 组件  
`DefaultAssetBundle.of()` 方法  
`Descendant` 组件  
`didChangeDependencies()` 方法  
离散值集合  
显示图标图片  
显示网页  
方法  
参数  
`WebView` `webview_flutter` 插件  
`dispose()` 方法  
`DragEndDetails` 对象  
`drive()` 方法  
下拉列表  
动态路由匹配

### E

`EdgeInsets` 构造函数  
`EdgeInsetsDirectional` 类  
`EdgeInsetsDirectional.fromSTEB()` 构造函数  
`EditUserDetailsPage` 组件  
`enableFlutterDriverExtension()` 函数  
`ensureVisible()` 方法  
`enterText()` 方法  
`evaluateJavascript()` 方法  
`example` 目录  
异常处理  
`catch` `Error` 对象  
`try-catch-finally`  
`expect()` 函数  
`expectAsync1()` 函数

### F

`FilterChip` 组件  
`Finder` 对象  
`FittedBox` 组件  
`FixedPositionLayoutDelegate` 类  
`FlatButton` `FlatButton.icon()` 构造函数  
Flex Box 布局算法  
`Flexible` 组件  
`flipped` 属性  
`flutter analyze`  
Flutter 应用  
代码结构  
配置  
运行  
`flutter attach`  
`flutter bash-completion` 命令  
`flutter_bloc` 包  
`flutter devices` 命令  
`flutter drive` 命令  
`flutter_driver` 包  
Flutter Driver  
`FlutterDriver.connect()` 函数  
Flutter Inspector  
渲染树  
组件树  
`flutter logs` 命令  
`flutter packages`  
Flutter SDK  
构建应用二进制文件  
APK 文件  
iOS  
渠道  
清理构建  
文件  
配置  
调试  
模拟器  
管理  
`flutter run` 参数  
构建变体  
输出格式  
源代码  
安装  
集成测试  
列出已连接设备  
管理缓存  
大纲视图，Android Studio  
包管理  
项目，创建  
配置  
启用/禁用功能  
示例代码类型  
运行应用  
显示应用日志  
截屏  
测试  
参数  
覆盖率报告  
调试  
跟踪，运行应用  
更新  
VS Code, 调试  
`flutter test` 命令  
Font Awesome 图标  
`formatEditUpdate()` 方法  
表单组件  
日期和时间选择  
格式化文本  
表单创建  
多值选择  
单值选择  
设置文本限制  
文本选择  
使用 Chip  
包裹表单字段  
`Future` 对象  
`FutureBuilder`

### G

`genhtml` 命令  
`GestureDetector` 组件  
`get()` 方法  
`getApplicationDocumentsDirectory()` 函数  
`getJobs()` 方法  
`getNetworkOperator` 方法  
`getTemporaryDirectory()` 函数  
`getValue()` 函数  
`GitHubJobsClient` 类  
`GridView`  
`group()` 函数  
`GrowingSizeLayoutDelegate`  
gRPC 服务，交互

### H

热重载  
编译错误  
控制台输出  
调试模式  
Flutter SDK  
热重启  
`HttpClient.addCredentials()` 方法

### I

`Icon()` 构造函数  
`IconButton` 构造函数  
`ImageBox` 组件  
`Image.network()` 构造函数  
`ImageRepeat` 值  
`IndexedStack` 类  
膨胀  
继承模型  
继承通知器  
`InheritedWidget` 类  
`inheritFromWidgetOfExactType()` 方法  
`initializeMessages()` 函数  
安装, Flutter  
Android 设备，设置  
Android 模拟器，设置  
Android 平台，设置  
iOS 设备，设置  
iOS 平台  
iOS 模拟器，设置  
Linux 机器  
macOS 机器  
Windows  
`invokeListMethod()` 方法  
`ios` 目录  
IOS 对话框  
`isSupported()` 方法

### J, K

`JavascriptMessageHandler` 函数  
`Job` 类  
`json_annotation`  
`jsonDecode()` 函数  
`jsonEncode()` 函数  
`json_serializable`

### L

`layout()` 方法  
布局算法  
`BoxConstraints` 实例  
Flutter  
多子元素  
`RenderObject` 实例  
单子组件  
`layoutChild()` 方法  
`lib` 目录  
生命周期方法  
`LimitedBox` 构造函数  
`LimitedBox` 组件  
`listen()` 方法  
`ListTile`  
`ListView` 组件  
创建  
项目构建器  
静态子元素  
`load()` 方法  
`loadString()` 方法  
`LocalizationsDelegate` 类  
`LoggingNavigatorObserver` 类  
`Login` 表单  
`loosen()` 方法



### M

`main()` 方法  
`MainAxisAlignment` 的值  
`MethodCallHandler` 接口  
`Mobx`，状态管理  
`MockHttpClient` 类  
`mockito` 库  
`move()` 方法  
`MultiChildLayoutDelegate`  
`MultiChildRenderObjectWidget` 类  
多语言环境  
`AppLocalizations`/本地化子类  
`Custom LocalizationsDelegate`  
`flutter_localizations`  
`Localizations` 组件  
`LocalizationsDelegate` 类  
`MaterialLocalizations` 对象方法  
多值选择  

### N

`Navigator.of()` 方法  
`NetworkPlugin.java` 文件  

### O

引导页  
`onHorizontalDragEnd` 回调  
`onHorizontalDragStart` 回调  
`onHorizontalDragUpdate` 回调  
`OutlineButton`  
`OutlineButton.icon()` 构造函数  
`OverflowBox` 构造函数  

### P

`padding` 参数  
`Padding` 组件  
页面导航：路由间数据传递  
动态路由匹配实现  
iOS 操作表与对话框  
Material Design 对话框与菜单  
命名路由  
绘制自定义元素  
Canvas 方法  
`CustomPaint` 组件参数  
`Shapes` 类  
`parentData` 属性  
`Parent` 组件  
`parse()` 函数  
`performLayout()` 方法  
物理模拟  
`GravitySimulation`  
`Simulation` 类  
`SimulationController` 组件  
`SpringSimulation` 类  
`Placeholder()` 构造函数  
平台特定代码  
`Flutter` 应用  
`Future` 对象  
获取网络运营商  
Android 实现  
Swift 实现  
`MethodChannel` 类  
播放视频  
Android iOS HTTP 安全配置方法  
`video_player` 插件  
`VideoPlayerController`，构造函数  
`VideoPlayerView` 类  
插件创建  
`PopupMenuItem` 构造函数  
`positionChild()` 方法  
`postMessage` 函数  
`print()` 函数  
`pump()` 方法  
`pumpAndSettle()` 方法  
`pumpWidget()` 方法  

### Q

`quarterTurns` 参数  

### R

`Radio` 组件  
`RaisedButton`  
`RaisedButton` 组件  
`RaisedButton.icon()` 构造函数  
连续范围  
`readConfig()` 方法  
读写文件  
异步方法  
`config.txt` 文件  
`Directory` 类  
`readAsString()` 方法  
`readAsStringSync()` 方法  
`receiveNetworkOperator()` 函数  
Redux 动作  
GitHub 任务组件  
`jobsstate`  
reducer 函数  
thunk 函数  
注册命名路由  
`RenderBox` 类  
`RenderObject` 类  
`RenderObjectWidget` 类  
渲染树  
`request.close()` 方法  
`requestData()` 方法  
`reset()` 方法  
`resolve()` 方法  
REST 服务  
`RichText()` 构造函数  
`RichText` 组件  
命名参数  
`RotatedBox` 组件  
`Route` 类  
`RouteAware` 方法  
`RouteSettings` 属性  
`runApp()` 方法  

### S

`save()` 方法  
`Scaffold`  
`AppBar` 组件  
`BottomAppBar`  
`BottomNavigationBar`  
`BottomSheet` 组件  
`drawer` 组件元素  
`FloatingActionButton` 组件  
iOS 页面  
Material Design 页面  
`SnackBar` 组件  
有状态组件  
Scoped model  
`_SelectColorState.build()` 方法  
顺序/叠加动画  
服务交互  
async 和 await  
构建组件  
复杂 JSON 数据  
创建 Future 对象  
创建 Stream  
Future 对象  
gRPC 服务  
HTML 数据处理  
HTTP 请求  
`JsonKey` 属性  
`JsonSerializable` 的 `JsonValue` 属性  
REST 服务  
简单 JSON 数据  
Socket 服务器  
Stream 的使用  
使用 `JsonLiteral`  
用户类  
WebSocket 服务器  
处理 Future 对象  
XML 数据处理  
`setState()` 方法  
`setUp()` 函数  
`setUpAll()` 函数  
`share()` 方法  
`share` 插件  
`shared_preferences` 插件  
`shouldRelayout()` 方法  
`shouldReload()` 方法  
`showDatePicker()` 函数  
`showDialog()` 函数  
`showSnackBar()` 方法  
`showTimePicker()` 函数  
同时动画  
`SingleChildRenderObjectWidget` 类  
`SizedBox` 组件  
`SizedBox` 构造函数  
`SizedOverflowBox` 组件  
`Slider` 组件  
`Socket.connect()` 方法  
Socket 服务器  
`SpringDescription.withDampingRatio()` 构造函数  
`Stack` 构造函数  
`StackFilt.expand`  
`StackFilt.passthrough`  
`StackFit.loose`  
`Stack` 组件阶段  
`State.build()` 方法  
`StatefulWidget` 类  
`StatelessWidget.build()` 方法  
`StatelessWidget` 类  
状态管理  
Bloc 模式  
Inherited model  
Inherited notifier  
Inherited widgets  
Mobx  
Redux  
Scoped model  
有状态组件  
导航器状态  
静态资源  
`AssetImage` 类  
加载字符串  
`pubspec.yaml` 文件类型与变体  
阻止路由返回  
存储键值对  
`getBool()` 方法  
`setBool()` 方法  
`SharedPreference`  
`StreamBuilder`  
事件流的订阅与转换  
`SwiftNetworkPlugin.swift` 文件  
`Switch` 组件  
系统分享面板  

### T

标签布局  
iOS Material Design  
表格数据  
`tap()` 方法  
`tearDown()` 函数  
测试目录  
`test()` 函数  
`testWidgets()` 函数  
`Text` 组件构造函数  
命名参数  
`TextAlign` 的值  
`TextDecoration` 类  
`TextDecoration.combine()` 构造函数  
`TextDecoration` 常量  
`TextDecorationStyle` 的值  
`TextField` 组件  
文本字段，Material Design  
边框  
自定义键盘与文本输入  
`InputDecoration`  
前缀和后缀文本  
`TextField` 组件  
`TextInputAction` 的值  
`TextInputFormatter.withFunction()` 方法  
文本输入，收集回调  
`CupertinoTextField` 组件  
`EditableText` 组件  
Material Design  
使用监听器的 `TextEditingController`  
`TextInputType` 常量  
`TextOverflow` 的值  
`TextSpan()` 构造函数  
`TextSpan` 对象命名参数  
`TextStyle()` 构造函数命名参数  
`TextStyle`，更新  
`ThemeData` 对象  
`throwsA()` 函数  
`timeout()` 方法  
开关状态  
`toJson()` 函数  
`Tranform.rotate()` 构造函数  
`transform()` 方法  
`Transform` 构造函数  
`Transform.scale()` 构造函数  
`Transform.translate()` 构造函数  
翻译文件  
`Intl.message()`  
`intl_messages.arb` 文件  
`intl_translation` 包  
加载消息  
`TweenSequence` 类  

### U

`UnconstrainedBox` 组件  
未知路由处理  
`updateShouldNotify()` 方法  
用户详情页  

### V

`validate()` 方法  

### W

`waitFor()` 方法  
`WebSocket.connect()` 方法  
WebSocket 服务器  
`WebViewController` 对象  
`whenComplete()` 方法  
组件  
对齐  
按钮  
图标类型  
居中  
约束  
弹性盒布局方面  
布局约束  
多水平/垂直排列  
重叠  
占位符  
尺寸测试  
变换  
组件树  
`WidgetTester`  
`_wrapInMaterial()` 函数  
包装表单字段  
方法命名参数  
`TextFormField`  
包装 `LayoutId` 组件  
`Wrap` 组件  
`writeConfig()` 方法  

### X, Y, Z

XML 数据处理  
解析属性  
查询  
`XmlBuilder` 构建文档方法  
使用
