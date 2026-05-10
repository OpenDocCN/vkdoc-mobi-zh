# 13. 杂项

本章涵盖了 Flutter 中杂项主题的实践指南。

## 13.1 使用资源

### 问题

你想要在应用中打包静态资源。

### 解决方案

使用 assets。



### 讨论

Flutter 应用可以同时包含代码和静态资源。共有两种类型的资源：

- 数据文件，包括 JSON、XML 和纯文本文件
- 二进制文件，包括图片和视频

资源在 `pubspec.yaml` 文件的 `flutter/assets` 部分中声明。在构建过程中，这些资源文件会被打包到应用的二进制文件中。这些资源可以在运行时被访问。通常将资源放在 `assets` 目录下。在代码清单 13-1 中，两个文件在 `pubspec.yaml` 文件中被声明为资源。

```
flutter:
assets:
- assets/dog.jpg
- assets/data.json
代码清单 13-1
pubspec.yaml 文件中的资源
```

在运行时，使用 `AssetBundle` 类的子类从资源中加载内容。`load()` 方法检索二进制内容，而 `loadString()` 方法检索字符串内容。使用这两个方法时，你需要提供资源的键。该键与 `pubspec.yaml` 文件中声明的资源路径相同。静态的应用级 `rootBundle` 属性引用了包含与应用打包在一起的资源的 `AssetBundle` 对象。你可以直接使用此属性来加载资源。建议使用静态的 `DefaultAssetBundle.of()` 方法从构建上下文中获取 `AssetBundle` 对象。

在代码清单 13-2 中，JSON 文件 `assets/data.json` 使用 `loadString()` 方法作为字符串加载。

```
class TextAssets extends StatelessWidget {
@override
Widget build(BuildContext context) {
return FutureBuilder(
future: DefaultAssetBundle.of(context)
.loadString('assets/data.json')
.then((json) {
return jsonDecode(json)['name'];
}),
builder: (context, snapshot) {
if (snapshot.connectionState == ConnectionState.done) {
return Center(child: Text(snapshot.data));
} else {
return Center(child: CircularProgressIndicator());
}
},
);
}
}
代码清单 13-2
加载字符串资源
```

如果资源文件是图片，你可以使用 `AssetImage` 类配合 `Image` 小部件来显示它。在代码清单 13-3 中，`AssetImage` 类用于显示 `assets/dog.jpg` 图片。

```
Image(
image: AssetImage('assets/dog.jpg'),
)
代码清单 13-3
使用 AssetImage
```

对于图片资源，同一个文件通常有多个不同分辨率的变体。当使用 `AssetImage` 类加载图片资源时，将使用与当前设备像素比最匹配的变体。

在代码清单 13-4 中，`assets/2.0x/dog.jpg` 文件是 `assets/dog.jpg` 的一个变体，其分辨率为 `2.0`。如果设备像素比为 `1.6`，则会使用 `assets/2.0x/dog.jpg` 文件。

```
flutter:
assets:
- assets/dog.jpg
- assets/2.0x/dog.jpg
- assets/3.0x/dog.jpg
代码清单 13-4
图片资源变体
```

## 13.2 使用手势

### 问题

你希望允许用户使用手势来执行操作。

### 解决方案

使用 `GestureDetector` 小部件来检测手势。

### 讨论

移动应用的用户习惯于在执行操作时使用手势。例如，在查看图片库时，使用滑动手势可以轻松地在不同图片之间导航。在 Flutter 中，我们可以使用 `GestureDetector` 小部件来检测手势，并为手势调用指定的回调。`GestureDetector` 构造函数有大量参数来为不同事件提供回调。一个手势在其生命周期内可能会分发多个事件。例如，水平拖动的手势可以分发三个事件。以下是这三个事件的处理程序参数：

- `onHorizontalDragStart` 回调表示指针可能开始水平移动。
- `onHorizontalDragUpdate` 回调表示指针正在水平方向移动。
- `onHorizontalDragEnd` 回调表示指针不再与屏幕接触。

不同事件的回调可以接收关于事件的详细信息。在代码清单 13-5 中，`GestureDetector` 小部件包装了一个 `Container` 小部件。在 `onHorizontalDragEnd` 回调处理程序中，`DragEndDetails` 对象的 `velocity` 属性是指针的移动速度。我们使用此属性来确定拖动方向。

```
class SwipingCounter extends StatefulWidget {
@override
_SwipingCounterState createState() => _SwipingCounterState();
}
class _SwipingCounterState extends State {
int _count = 0;
@override
Widget build(BuildContext context) {
return Column(
children: [
Text('$_count'),
Expanded(
child: GestureDetector(
child: Container(
decoration: BoxDecoration(color: Colors.grey.shade200),
),
onHorizontalDragEnd: (DragEndDetails details) {
setState(() {
double dx = details.velocity.pixelsPerSecond.dx;
_count += (dx > 0 ? 1 : (dx < 0 ? -1 : 0));
});
},
),
),
] ,
);
}
}
代码清单 13-5
使用 GestureDetector
```

## 13.3 支持多种语言区域

### 问题

你希望应用支持多种语言区域。

### 解决方案

使用 `Localizations` 小部件和 `LocalizationsDelegate` 类。



### 讨论

Flutter 对国际化提供了内置支持。若需支持多种语言区域，需要使用 `Localizations` 组件。`Localizations` 类通过一个 `LocalizationsDelegate` 对象列表来加载本地化资源。`LocalizationsDelegate<T>` 类是一组类型为 `T` 的本地化资源的工厂。这组本地化资源通常是一个包含属性和方法的类，用于提供具体的本地化值。

要创建一个 `Localizations` 对象，你需要提供 `Locale` 对象和一个 `LocalizationsDelegate` 对象列表。大多数情况下，无需显式创建 `Localizations` 对象，因为 `WidgetsApp` 组件已自动创建。`WidgetsApp` 构造函数包含一些参数，这些参数会传递给 `Localizations` 对象。当需要使用本地化值时，可以调用静态方法 `Localizations.of<T>(BuildContext context, Type type)` 来获取指定类型的、最近的本地化资源对象。

默认情况下，Flutter 仅提供美国英语的本地化。要支持其他语言区域，首先需要添加 Flutter 针对这些区域的本地化支持。这通过在 `pubspec.yaml` 文件的 `dependencies` 中添加 `flutter_localizations` 包来实现，如代码清单 13-6 所示。添加此包后，即可使用 `MaterialLocalizations` 类中定义的本地化值。

```
dependencies:
  flutter:
    sdk: flutter
  flutter_localizations:
    sdk: flutter
代码清单 13-6
flutter_localizations
```

添加 `flutter_localizations` 包后，需要启用这些本地化值。在代码清单 13-7 中，通过将 `GlobalMaterialLocalizations.delegate` 和 `GlobalWidgetsLocalizations.delegate` 添加到 `MaterialApp` 构造函数的 `localizationsDelegates` 列表来实现。`localizationsDelegates` 参数的值会传递给 `Localizations` 构造函数。`supportedLocales` 参数则用于指定支持的语言区域。

```
MaterialApp(
  localizationsDelegates: [
    GlobalMaterialLocalizations.delegate,
    GlobalWidgetsLocalizations.delegate,
  ],
  supportedLocales: [
    const Locale('en'),
    const Locale('zh', 'CN'),
  ],
);
代码清单 13-7
启用 Flutter 本地化值
```

在代码清单 13-8 中，`MaterialLocalizations.of()` 方法从构建上下文中获取 `MaterialLocalizations` 对象。`copyButtonLabel` 属性是 `MaterialLocalizations` 类中定义的一个本地化值。在运行时，按钮的标签文本取决于设备的语言区域。`MaterialLocalizations.of()` 方法内部使用了 `Localizations.of()` 来查找 `MaterialLocalizations` 对象。

```
RaisedButton(
  child: Text(MaterialLocalizations.of(context).copyButtonLabel),
  onPressed: () {},
);
代码清单 13-8
使用本地化值
```

`MaterialLocalizations` 类仅提供有限的本地化值。对于你自己的应用，需要创建自定义的本地化资源类。代码清单 13-9 中的 `AppLocalizations` 类就是一个自定义的本地化资源类。`AppLocalizations` 类的 `appName` 属性是一个简单可本地化字符串的示例。`greeting()` 方法是一个需要参数的可本地化字符串的示例。`AppLocalizationsEn` 和 `AppLocalizationsZhCn` 类分别是 `AppLocalizations` 类针对 `en` 和 `zh_CN` 语言区域的实现。

```
abstract class AppLocalizations {
  String get appName;
  String greeting(String name);
  static AppLocalizations of(BuildContext context) {
    return Localizations.of(context, AppLocalizations);
  }
}

class AppLocalizationsEn extends AppLocalizations {
  @override
  String get appName => 'Demo App';
  @override
  String greeting(String name) {
    return 'Hello, $name';
  }
}

class AppLocalizationsZhCn extends AppLocalizations {
  @override
  String get appName => '示例应用';
  @override
  String greeting(String name) {
    return '你好, $name';
  }
}
代码清单 13-9
AppLocalizations 及其本地化子类
```

我们还需要创建一个自定义的 `LocalizationsDelegate` 类来加载 `AppLocalizations` 对象。需要实现三个方法：

*   `isSupported()` 方法用于检查某个语言区域是否被支持。
*   `load()` 方法用于加载指定语言区域的本地化资源对象。
*   `shouldReload()` 方法用于检查是否应该调用 `load()` 方法重新加载资源。

在代码清单 13-10 的 `load()` 方法中，会根据给定的语言区域返回 `AppLocalizationsEn` 或 `AppLocalizationsZhCn` 对象。

```
class _AppLocalizationsDelegate extends LocalizationsDelegate {
  const _AppLocalizationsDelegate();
  static const List _supportedLocales = [
    const Locale('en'),
    const Locale('zh', 'CN')
  ];
  @override
  bool isSupported(Locale locale) {
    return _supportedLocales.contains(locale);
  }
  @override
  Future load(Locale locale) {
    return Future.value(locale == Locale('zh', 'CN')
        ? AppLocalizationsZhCn()
        : AppLocalizationsEn());
  }
  @override
  bool shouldReload(LocalizationsDelegate old) {
    return false;
  }
}
代码清单 13-10
自定义 LocalizationsDelegate
```

需要将 `_AppLocalizationsDelegate` 对象添加到代码清单 13-7 的 `localizationsDelegates` 列表中。代码清单 13-11 展示了如何使用 `AppLocalizations` 类。

```
Text(AppLocalizations.of(context).greeting('John'))
代码清单 13-11
使用 AppLocalizations
```

## 13.4 生成翻译文件

### 问题

你希望从代码中提取可本地化字符串，并整合翻译后的字符串。

### 解决方案

使用 `intl_translation` 包中的工具。



### 讨论

**食谱 13-3** 描述了如何使用 `Localizations` 组件和 `LocalizationsDelegate` 类来支持多语言环境。该方案的主要缺点在于，你需要为所有支持的语言环境手动创建本地化资源类。由于本地化字符串直接嵌入在源代码中，这导致翻译人员很难参与进来。一个更好的选择是使用 `intl_translation` 包提供的工具来自动化这个过程。你需要在 `pubspec.yaml` 文件的 `dev_dependencies` 中添加 `intl_translation: ⁰.17.3`。

**清单 13-12** 展示了新的 `AppLocalizations` 类，它与清单 13-9 拥有相同的 `appName` 属性和 `greeting()` 方法。`Intl.message()` 方法用于描述一个本地化字符串。只有消息字符串是必需的。像 `name`、`desc`、`args` 和 `examples` 这样的参数用于帮助翻译人员理解消息字符串。

```
class AppLocalizations {
  static AppLocalizations of(BuildContext context) {
    return Localizations.of(context, AppLocalizations);
  }
  String get appName {
    return Intl.message(
      'Demo App',
      name: 'appName',
      desc: 'Name of the app',
    );
  }
  String greeting(String name) {
    return Intl.message(
      'Hello, $name',
      name: 'greeting',
      args: [name],
      desc: 'Greeting message',
      examples: const {'name': 'John'},
    );
  }
}
清单 13-12
使用 Intl.message() 的 AppLocalizations
```

现在我们可以使用 `intl_translation` 包提供的工具从源代码中提取本地化消息。以下命令从 `lib/app_intl.dart` 文件中提取使用 `Intl.message()` 声明的消息，并保存到 `lib/l10n` 目录中。运行此命令后，你应该会在 `lib/l10n` 目录中看到生成的 `intl_messages.arb` 文件。生成的文件采用 ARB (Application Resource Bundle) 格式 (https://github.com/googlei18n/app-resource-bundle)，该格式可以作为 Google Translator Toolkit 等翻译工具的输入。ARB 文件实际上是 JSON 文件；你可以直接使用文本编辑器修改它们。

```
$ flutter packages pub run intl_translation:extract_to_arb --locale=en --output-dir=lib/l10n lib/app_intl.dart
```

现在，你可以为每个支持的语言环境复制 `intl_messages.arb` 文件并进行翻译。例如，`intl_messages_zh.arb` 文件就是针对 `zh` 语言环境的翻译版本。翻译文件准备就绪后，你可以使用以下命令生成 Dart 文件。运行此命令后，你应该会看到一个 `messages_all.dart` 文件以及每个语言环境对应的 `messages_*.dart` 文件。

```
$ flutter packages pub run intl_translation:generate_from_arb --output-dir=lib/l10n --no-use-deferred-loading lib/app_intl.dart lib/l10n/intl_*.arb
```

`messages_all.dart` 文件中的 `initializeMessages()` 函数可用于为指定的语言环境初始化消息。清单 13-13 中的静态 `load()` 方法首先使用 `initializeMessages()` 函数初始化消息，然后设置默认语言环境。

```
class AppLocalizations {
  static Future<AppLocalizations> load(Locale locale) {
    final String name =
        locale.countryCode.isEmpty ? locale.languageCode : locale.toString();
    final String localeName = Intl.canonicalizedLocale(name);
    return initializeMessages(localeName).then((_) {
      Intl.defaultLocale = localeName;
      return AppLocalizations();
    });
  }
}
清单 13-13
加载消息
```

这个静态的 `AppLocalizations.load()` 方法可以被 `LocalizationsDelegate` 类的 `load()` 方法使用，以加载 `AppLocalizations` 对象。

## 13.5 绘制自定义元素

### 问题

你想绘制自定义元素。

### 解决方案

使用 `CustomPaint` 组件以及 `CustomPainter` 和 `Canvas` 类。

### 讨论

如果你想完全自定义一个组件的绘制，可以使用 `CustomPaint` 组件。`CustomPaint` 组件提供了一个画布，用于绘制自定义元素。**表 13-1** 显示了 `CustomPaint` 构造函数的参数。在绘制过程中，`painter` 首先在画布上绘制，然后绘制子组件，最后 `foregroundPainter` 在画布上绘制。

**表 13-1** `CustomPaint` 的参数

| 名称 | 类型 | 描述 |
| --- | --- | --- |
| `painter` | `CustomPainter` | 在子组件之前绘制的绘画器。 |
| `foregroundPainter` | `CustomPainter` | 在子组件之后绘制的绘画器。 |
| `size` | `Size` | 绘制的大小。 |
| `child` | `Widget` | 子组件。 |

要创建 `CustomPainter` 对象，你需要创建 `CustomPainter` 的子类，并重写 `paint()` 和 `shouldRepaint()` 方法。在 `paint()` 方法中，`canvas` 参数可用于绘制自定义元素。`Canvas` 类有一组用于绘制不同元素的方法；请参见**表 13-2**。

**表 13-2** `Canvas` 的方法

| 名称 | 描述 |
| --- | --- |
| `drawArc()` | 绘制一个弧。 |
| `drawCircle()` | 用指定的圆心和半径绘制一个圆。 |
| `drawImage()` | 绘制一个 `Image` 对象。 |
| `drawLine()` | 在两点之间绘制一条线。 |
| `drawOval()` | 绘制一个椭圆。 |
| `drawParagraph()` | 绘制文本。 |
| `drawRect()` | 用指定的 `Rect` 对象绘制一个矩形。 |
| `drawRRect()` | 绘制一个圆角矩形。 |

`Canvas` 类中的大多数方法都有一个 `Paint` 类型的参数，用于描述在画布上绘制时使用的样式。在**清单 13-14** 中，`Shapes` 类在画布上绘制了一个矩形和一个圆形。在 `CustomShapes` 组件中，`Text` 组件绘制在 `Shapes` 绘画器的上方。

```
class CustomShapes extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return Container(
      width: 300,
      height: 300,
      child: CustomPaint(
        painter: Shapes(),
        child: Center(child: Text('Hello World')),
      ),
    );
  }
}

class Shapes extends CustomPainter {
  @override
  void paint(Canvas canvas, Size size) {
    Rect rect = Offset(5, 5) & (size - Offset(5, 5));
    canvas.drawRect(
      rect,
      Paint()
        ..color = Colors.red
        ..strokeWidth = 2
        ..style = PaintingStyle.stroke,
    );
    canvas.drawCircle(
      rect.center,
      (rect.shortestSide / 2) - 10,
      Paint()..color = Colors.blue,
    );
  }

  @override
  bool shouldRepaint(CustomPainter oldDelegate) {
    return false;
  }
}
清单 13-14
使用 CustomPaint
```

## 13.6 自定义主题

### 问题

你想在 Flutter 应用中自定义主题。

### 解决方案

对于 Material Design 使用 `ThemeData` 类，对于 iOS 使用 `CupertinoThemeData` 类。



### 讨论

自定义应用的外观和感觉是一种常见需求。对于 Flutter 应用，如果使用 Material Design，可以通过`ThemeData`类来定制主题。`ThemeData`类拥有大量参数，用于配置主题的各个方面。`MaterialApp`类通过`theme`参数提供`ThemeData`对象。对于 iOS 风格，`CupertinoThemeData`类具有相同的作用，用于指定主题。`CupertinoApp`类也拥有类型为`CupertinoThemeData`的`theme`参数，用于自定义主题。

如果需要访问当前主题对象，在 Material Design 中，可以使用静态方法`Theme.of()`来获取构建上下文最近的外层`ThemeData`对象。类似地，`CupertinoTheme.of()`方法可用于 iOS 风格。

在代码清单 13-15 中，第一个`Text`组件使用当前`Theme`对象的`textTheme.headline`属性作为样式。第二个`Text`组件使用`colorScheme.error`属性作为颜色，以显示错误文本。

```
class TextTheme extends StatelessWidget {
@override
Widget build(BuildContext context) {
return Column(
children: [
Text('Headline', style: Theme.of(context).textTheme.headline),
Text('Error',
style: TextStyle(color: Theme.of(context).colorScheme.error)),
],
);
}
}
代码清单 13-15
使用主题
```

## 13.7 总结

本章讨论了 Flutter 中在不同场景下很有用的杂项主题。在下一章中，我们将讨论 Flutter 中的测试与调试。

