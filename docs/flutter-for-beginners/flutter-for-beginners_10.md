# 10. 状态管理

在构建 Flutter 应用时，你需要管理应用运行时的状态。状态可能因用户交互或后台任务而改变。本章涵盖了在 Flutter 中使用不同解决方案进行状态管理的示例。

## 10.1 使用有状态组件管理状态

### 问题

你希望有一种简单的方式来管理 UI 中的状态。

### 解决方案

创建你自己的 `StatefulWidget` 子类。



### 讨论

`StatefulWidget`类是在 Flutter 中管理状态的基本方式。状态 ful widget 在其状态改变时会重建自身。如果要管理的状态很简单，使用状态 ful widget 通常就足够好了。你不需要使用其他方法中讨论的第三方库。

状态 ful widget 使用`State`对象来存储状态。当创建自己的`StatefulWidget`子类时，需要重写`createState()`方法以返回一个`State`对象。对于每个`StatefulWidget`子类，会有一个对应的`State`类子类来管理状态。`createState()`方法返回对应`State`子类的对象。实际状态通常作为`State`子类的私有变量来保存。

在`State`子类中，你需要实现`build()`方法来返回一个`Widget`对象。当状态改变时，会调用`build()`方法获取新的 widget 以更新 UI。要触发 UI 的重建，需要显式调用`setState()`方法来通知框架。`setState()`方法的参数是一个`VoidCallback`函数，其中包含更新内部状态的逻辑。重建时，`build()`方法使用最新状态来创建 widget 配置。Widget 不会被更新，而是在必要时被替换。

清单 10-1 中的`SelectColor` widget 是一个典型的状态 ful widget 示例。`_SelectColorState`类是`SelectColor` widget 的`State`实现。`_selectedColor`是维护当前选中颜色的内部变量。`DropdownButton` widget 使用`_selectedColor`的值来确定要渲染的选中选项，`Text` widget 使用它来确定要显示的文本。在`DropdownButton`的`onChanged`处理程序中，调用`setState()`来更新`_selectedColor`变量的值，这通知框架重新运行`_SelectColorState.build()`方法以获取新的 widget 配置并更新 UI。

```
class SelectColor extends StatefulWidget {
@override
_SelectColorState createState() => _SelectColorState();
}
class _SelectColorState extends State {
final List _colors = ['Red', 'Green', 'Blue'];
String _selectedColor;
@override
Widget build(BuildContext context) {
return Column(
children: [
DropdownButton(
value: _selectedColor,
items: _colors.map((String color) {
return DropdownMenuItem(
value: color,
child: Text(color),
);
}).toList(),
onChanged: (value) {
setState(() {
_selectedColor = value;
});
},
),
Text('Selected: ${_selectedColor ?? "}'),
],
);
}
}
Listing 10-1
Example of stateful widget
```

`State`对象有自己的生命周期。你可以在`State`子类中重写不同的生命周期方法，以在不同阶段执行操作。表 10-1 显示了这些生命周期方法。

**表 10-1** `State`的生命周期方法

| 名称 | 描述 |
| --- | --- |
| `initState()` | 当此对象被插入 widget 树时调用。应用于执行状态的初始化。 |
| `didChangeDependencies()` | 当此对象的依赖项发生变化时调用。 |
| `didUpdateWidget(T oldWidget)` | 当此对象的 widget 发生变化时调用。旧 widget 作为参数传入。 |
| `reassemble()` | 当应用在调试期间被重新组装时调用。此方法仅在开发期间调用。 |
| `build(BuildContext context)` | 当状态发生变化时调用。 |
| `deactivate()` | 当此对象从 widget 树中移除时调用。 |
| `dispose()` | 当此对象被永久从 widget 树中移除时调用。此方法在`deactivate()`之后调用。 |

在表 10-1 列出的方法中，`initState()`和`dispose()`方法容易理解。这两个方法在生命周期中只会被调用一次。然而，其他方法可能会被多次调用。

`didChangeDependencies()`方法通常在状态对象使用了继承 widget 时使用。当继承 widget 改变时调用此方法。大多数情况下，你不需要重写此方法，因为框架会在依赖项改变后自动调用`build()`方法。有时你可能需要在依赖项改变后执行一些昂贵的任务。在这种情况下，应将逻辑放入`didChangeDependencies()`方法，而不是在`build()`方法中执行。

`reassemble()`方法仅在开发期间使用，例如在热重载期间。此方法在发布版本中不会被调用。大多数情况下，你不需要重写此方法。

`didUpdateWidget()`方法在状态的 widget 发生变化时调用。如果你需要在旧 widget 上执行清理任务或重用旧 widget 中的某些状态，则应重写此方法。例如，`TextField` widget 的`_TextFieldState`类重写了`didUpdateWidget()`方法，以基于旧 widget 的值初始化`TextEditingController`对象。

`deactivate()`方法在状态对象从 widget 树中移除时调用。此状态对象可能会在之后被重新插入到 widget 树的不同位置。如果构建逻辑依赖于 widget 的位置，则应重写此方法。例如，`FormField` widget 的`FormFieldState`类重写了`deactivate()`方法，以从封闭的表单中注销当前表单字段。

在清单 10-1 中，widget 的全部内容都在`build()`方法中构建，因此你可以在`DropdownButton`的`onPressed`回调中直接调用`setState()`方法。如果 widget 结构复杂，你可以向下传递一个更新状态的函数给子 widget。在清单 10-2 中，`RaisedButton`的`onPressed`回调由`CounterButton`的构造函数参数设置。当`CounterButton`在`Counter` widget 中使用时，提供的处理函数使用`setState()`来更新状态。

```
class Counter extends StatefulWidget {
@override
_CounterState createState() => _CounterState();
}
class _CounterState extends State {
int count = 0;
@override
Widget build(BuildContext context) {
return Column(
children: [
CounterButton(() {
setState(() {
count++;
});
}),
CounterText(count),
],
);
}
}
class CounterText extends StatelessWidget {
CounterText(this.count);
final int count;
@override
Widget build(BuildContext context) {
return Text('Value: ${count ?? "}');
}
}
class CounterButton extends StatelessWidget {
CounterButton(this.onPressed);
final VoidCallback onPressed;
@override
Widget build(BuildContext context) {
return RaisedButton(
child: Text('+'),
onPressed: onPressed,
);
}
}
Listing 10-2
Pass state change function to descendant widget
```

## 10.2 使用继承 Widget 管理状态

### 问题

你希望将状态沿 widget 树向下传播。

### 解决方案

创建你自己的`InheritedWidget`子类。



### 讨论

当使用有状态小部件管理状态时，状态存储在 `State` 对象中。如果后代小部件需要访问该状态，则需从子树根节点向下传递，正如代码清单 10-2 中 `count` 状态的传递方式。当小部件的子树结构较深时，通过添加构造函数参数来向下传递状态会很不方便。在这种情况下，使用 `InheritedWidget` 是更好的选择。

使用 `InheritedWidget` 时，可以通过 `BuildContext.inheritFromWidgetOfExactType()` 方法从构建上下文中获取特定类型的最近继承小部件实例。后代小部件可以轻松访问存储在继承小部件中的状态数据。调用 `inheritFromWidgetOfExactType()` 方法时，构建上下文会向该继承小部件注册自身。当继承小部件发生变化时，构建上下文会自动重建，以从继承小部件获取新值。这意味着，对于使用继承小部件中状态的后代小部件，无需手动更新。

代码清单 10-3 中的 `Config` 类代表状态。它包含 `color` 和 `fontSize` 属性。`Config` 类重写了 `==` 运算符和 `hashCode` 属性，以实现正确的相等性检查。通过 `copyWith()` 方法，可以更新部分属性来创建 `Config` 类的新实例。`Config.fallback()` 构造函数会创建一个包含默认值的 `Config` 对象。

```
class Config {
  const Config({this.color, this.fontSize});
  const Config.fallback()
      : color = Colors.red,
        fontSize = 12.0;
  final Color color;
  final double fontSize;
  Config copyWith({Color color, double fontSize}) {
    return Config(
      color: color ?? this.color,
      fontSize: fontSize ?? this.fontSize,
    );
  }
  @override
  bool operator ==(other) {
    if (other.runtimeType != runtimeType) return false;
    final Config typedOther = other;
    return color == typedOther.color && fontSize == typedOther.fontSize;
  }
  @override
  int get hashCode => hashValues(color, fontSize);
}
```

代码清单 10-4 中的 `ConfigWidget` 是一个继承小部件。它将 `Config` 对象作为其内部状态。`updateShouldNotify()` 方法用于检查在继承小部件更改后，是否应通知已注册的构建上下文。这是一种性能优化，可以避免不必要的更新。静态 `of()` 方法是一种常见做法，用于获取继承小部件或与该继承小部件关联的状态。`ConfigWidget` 的 `of()` 方法使用 `inheritFromWidgetOfExactType()` 从构建上下文中获取最近的包围 `ConfigWidget` 实例，并从小部件中获取 `config` 属性。如果未找到 `ConfigWidget` 对象，则返回默认的 `Config` 实例。

```
class ConfigWidget extends InheritedWidget {
  const ConfigWidget({
    Key key,
    @required this.config,
    @required Widget child,
  }) : super(key: key, child: child);
  final Config config;
  static Config of(BuildContext context) {
    final ConfigWidget configWidget =
        context.inheritFromWidgetOfExactType(ConfigWidget);
    return configWidget?.config ?? const Config.fallback();
  }
  @override
  bool updateShouldNotify(ConfigWidget oldWidget) {
    return config != oldWidget.config;
  }
}
```

在代码清单 10-5 中，`ConfiguredText` 和 `ConfiguredBox` 小部件都使用 `ConfigWidget.of(context)` 来获取 `Config` 对象，并在构建 UI 时使用其属性。

```
class ConfiguredText extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    Config config = ConfigWidget.of(context);
    return Text(
      'Font size: ${config.fontSize}',
      style: TextStyle(
        color: config.color,
        fontSize: config.fontSize,
      ),
    );
  }
}
class ConfiguredBox extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    Config config = ConfigWidget.of(context);
    return Container(
      decoration: BoxDecoration(color: config.color),
      child: Text('Background color: ${config.color}'),
    );
  }
}
```

代码清单 10-6 中的 `ConfigUpdater` 小部件用于更新 `Config` 对象。它也使用 `ConfigWidget.of(context)` 来获取要更新的 `Config` 对象。`onColorChanged` 和 `onFontSizeIncreased` 回调用于触发 `Config` 对象的更新。

```
typedef SetColorCallback = void Function(Color color);
class ConfigUpdater extends StatelessWidget {
  const ConfigUpdater({this.onColorChanged, this.onFontSizeIncreased});
  static const List _colors = [Colors.red, Colors.green, Colors.blue];
  final SetColorCallback onColorChanged;
  final VoidCallback onFontSizeIncreased;
  @override
  Widget build(BuildContext context) {
    Config config = ConfigWidget.of(context);
    return Column(
      children: [
        DropdownButton(
          value: config.color,
          items: _colors.map((Color color) {
            return DropdownMenuItem(
              value: color,
              child: Text(color.toString()),
            );
          }).toList(),
          onChanged: onColorChanged,
        ),
        RaisedButton(
          child: Text('Increase font size'),
          onPressed: onFontSizeIncreased,
        )
      ],
    );
  }
}
```

现在我们可以将这些小部件组合起来构建完整的 UI。在代码清单 10-7 中，`ConfiguredPage` 是一个有状态小部件，其状态为 `Config` 对象。`ConfigUpdater` 小部件作为 `ConfiguredPage` 的子组件，用于更新 `Config` 对象。`ConfiguredPage` 构造函数还包含 `child` 参数，用于提供使用 `ConfigWidget.of(context)` 获取正确 `Config` 对象的子小部件。对于 `ConfigWidget` 的 `onColorChanged` 和 `onFontSizeIncreased` 回调，使用 `setState()` 方法来更新 `ConfiguredPage` 小部件的状态，并触发 `ConfigWidget` 的更新。框架会通知 `ConfigUpdater` 和其他小部件使用 `Config` 对象的最新值进行更新。

```
class ConfiguredPage extends StatefulWidget {
  ConfiguredPage({Key key, this.child}) : super(key: key);
  final Widget child;
  @override
  _ConfiguredPageState createState() => _ConfiguredPageState();
}
class _ConfiguredPageState extends State {
  Config _config = Config(color: Colors.green, fontSize: 16);
  @override
  Widget build(BuildContext context) {
    return ConfigWidget(
      config: _config,
      child: Column(
        children: [
          ConfigUpdater(
            onColorChanged: (Color color) {
              setState(() {
                _config = _config.copyWith(color: color);
              });
            },
            onFontSizeIncreased: () {
              setState(() {
                _config = _config.copyWith(fontSize: _config.fontSize + 1.0);
              });
            },
          ),
          Container(
            decoration: BoxDecoration(border: Border.all()),
            padding: EdgeInsets.all(8),
            child: widget.child,
          ),
        ],
      ),
    );
  }
}
```

在代码清单 10-8 中，`ConfigWidgetPage` 小部件使用 `ConfiguredPage` 小部件来包装 `ConfiguredText` 和 `ConfiguredBox` 小部件。

```
class ConfigWidgetPage extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: Text('Inherited Widget'),
      ),
      body: ConfiguredPage(
        child: Column(
          children: [
            ConfiguredText(),
            ConfiguredBox(),
          ],
        ),
      ),
    );
  }
}
```

## 10.3 使用继承模型管理状态

### 问题

你需要根据变化的方面获取通知并重新构建 UI。

### 解决方案

创建你自己的 `InheritedModel` 子类。



### 讨论

如果我们仔细看看食谱 10-2 中清单 10-5 里的 `ConfiguredText` 和 `ConfiguredBox` 组件，可以发现 `ConfiguredBox` 组件只依赖于 `Config` 对象的 `color` 属性。如果 `fontSize` 属性发生变化，`ConfiguredBox` 组件无需重建。这些不必要的重建可能会导致性能问题，尤其是当组件很复杂时。

`InheritedModel` 组件允许你将一个状态划分为多个方面。一个构建上下文可以注册，以便仅针对特定方面接收通知。当 `InheritedModel` 组件中的状态发生变化时，只有注册了匹配方面的依赖构建上下文才会收到通知。

`InheritedModel` 类继承自 `InheritedWidget` 类。它有一个类型参数用于指定方面的类型。清单 10-9 中的 `ConfigModel` 类是 `Config` 对象的 `InheritedModel` 子类。方面的类型是 `String`。在实现 `InheritedModel` 类时，你仍然需要重写 `updateShouldNotify()` 方法来确定是否应该通知依赖项。`updateShouldNotifyDependent()` 方法根据依赖项所依赖的方面集合来决定是否应该通知某个依赖项。`updateShouldNotifyDependent()` 方法仅在 `updateShouldNotify()` 方法返回 `true` 时才会被调用。对于 `ConfigModel`，只定义了 “color” 和 “fontSize” 两个方面。如果依赖项依赖于 “color” 方面，那么只有当 `Config` 对象的 `color` 属性发生变化时才会收到通知。这对于 “fontSize” 方面和 `fontSize` 属性同样适用。

静态方法 `of()` 有一个额外的 `aspect` 参数，用于指定构建上下文所依赖的方面。静态方法 `InheritedModel.inheritFrom()` 用于使构建上下文依赖于指定的方面。当 `aspect` 为 `null` 时，此方法与使用 `BuildContext.inheritFromWidgetOfExactType()` 方法相同。

```
class ConfigModel extends InheritedModel {
const ConfigModel({
Key key,
@required this.config,
@required Widget child,
}) : super(key: key, child: child);
final Config config;
static Config of(BuildContext context, String aspect) {
ConfigModel configModel =
InheritedModel.inheritFrom(context, aspect: aspect);
return configModel?.config ?? Config.fallback();
}
@override
bool updateShouldNotify(ConfigModel oldWidget) {
return config != oldWidget.config;
}
@override
bool updateShouldNotifyDependent(
ConfigModel oldWidget, Set dependencies) {
return (config.color != oldWidget.config.color &&
dependencies.contains('color')) ||
(config.fontSize != oldWidget.config.fontSize &&
dependencies.contains('fontSize'));
}
}
清单 10-9
作为 InheritedModel 的 ConfigModel
```

在清单 10-10 中，`ConfiguredModelText` 组件使用 `null` 作为方面，因为它同时依赖于 “color” 和 “fontSize” 两个方面。`ConfiguredModelBox` 组件使用 `color` 作为方面。如果字体大小被更新，只有 `ConfiguredModelText` 组件会被重建。

```
class ConfiguredModelText extends StatelessWidget {
@override
Widget build(BuildContext context) {
Config config = ConfigModel.of(context, null);
return Text(
'字体大小: ${config.fontSize}',
style: TextStyle(
color: config.color,
fontSize: config.fontSize,
),
);
}
}
class ConfiguredModelBox extends StatelessWidget {
@override
Widget build(BuildContext context) {
Config config = ConfigModel.of(context, 'color');
return Container(
decoration: BoxDecoration(color: config.color),
child: Text('背景颜色: ${config.color}'),
);
}
}
清单 10-10
使用 ConfigModel 获取 Config 对象
```

## 10.4 使用继承通知器管理状态

### 问题

你希望依赖组件能够基于 `Listenable` 对象的通知重建。

### 解决方案

创建你自己的 `InheritedNotifier` 组件子类。

### 讨论

`Listenable` 类通常用于管理监听器并通知客户端进行更新。你可以使用相同的模式通过 `InheritedNotifier` 通知依赖项重建。`InheritedNotifier` 组件也继承自 `InheritedWidget` 类。在创建 `InheritedNotifier` 组件时，你需要提供 `Listenable` 对象。当 `Listenable` 对象发送通知时，该 `InheritedNotifier` 组件的依赖项会收到通知并重建。

在清单 10-11 中，`ConfigNotifier` 使用 `ValueNotifier<Config>` 作为 `Listenable` 的类型。静态方法 `of()` 从 `ConfigNotifier` 对象中获取 `Config` 对象。

```
class ConfigNotifier extends InheritedNotifier> {
ConfigNotifier({
Key key,
@required notifier,
@required Widget child,
}) : super(key: key, notifier: notifier, child: child);
static Config of(BuildContext context) {
final ConfigNotifier configNotifier =
context.inheritFromWidgetOfExactType(ConfigNotifier);
return configNotifier?.notifier?.value ?? Config.fallback();
}
}
清单 10-11
作为 InheritedNotifier 的 ConfigNotifier
```

要使用 `ConfigNotifier` 组件，你需要创建一个 `ValueNotifier<Config>` 的新实例。要更新 `Config` 对象，你可以简单地将 `value` 属性设置为新值。`ValueNotifier` 对象会发送通知，从而通知依赖组件进行重建。

```
class ConfiguredNotifierPage extends StatelessWidget {
ConfiguredNotifierPage({Key key, this.child}) : super(key: key);
final Widget child;
final ValueNotifier _notifier =
ValueNotifier(Config(color: Colors.green, fontSize: 16));
@override
Widget build(BuildContext context) {
return ConfigNotifier(
notifier: _notifier,
child: Column(
children: [
ConfigUpdater(
onColorChanged: (Color color) {
_notifier.value = _notifier.value.copyWith(color: color);
},
onFontSizeIncreased: () {
Config oldConfig = _notifier.value;
_notifier.value =
oldConfig.copyWith(fontSize: oldConfig.fontSize + 1.0);
},
),
Container(
decoration: BoxDecoration(border: Border.all()),
padding: EdgeInsets.all(8),
child: child,
),
],
),
);
}
}
清单 10-12
使用 ConfigNotifier 的 ConfiguredNotifierPage
```

## 10.5 使用作用域模型管理状态

### 问题

你想要一个简单的解决方案来处理模型变化。

### 解决方案

使用 `scoped_model` 包。



### 讨论

在 Recipes 10-1、10-2、10-3 和 10-4 中，你已经看到使用`StatefulWidget`、`InheritedWidget`、`InheritedModel`和`InheritedNotifier`这些组件来管理状态。这些组件由 Flutter 框架提供。由于这些组件是底层 API，因此在复杂应用中用起来并不方便。`scoped_model`包（[`https://pub.dev/packages/scoped_model`](https://pub.dev/packages/scoped_model)）是一个库，可以让你轻松地将数据模型从父组件向下传递给它的后代组件。它建立在`InheritedWidget`之上，但提供了易于使用的 API。要使用这个包，你需要在`pubspec.yaml`文件的`dependencies`中添加`scoped_model: ¹.0.1`。我们将使用与 Recipe 10-2 相同的示例来演示`scoped_model`包的用法。

清单 10-13 展示了使用`scoped_model`包的`Config`模型。`Config`类继承自`Model`类。它有私有字段来存储状态。`setColor()`和`increaseFontSize()`方法分别更新`_color`和`_fontSize`字段。这两个方法在内部使用`notifyListeners()`来通知后代组件进行重建。

```
import 'package:scoped_model/scoped_model.dart';
class Config extends Model {
Color _color = Colors.red;
double _fontSize = 16.0;
Color get color => _color;
double get fontSize => _fontSize;
void setColor(Color color) {
_color = color;
notifyListeners();
}
void increaseFontSize() {
_fontSize += 1;
notifyListeners();
}
}
Listing 10-13
Config model as scoped model
```

在清单 10-14 中，`ScopedModelText`组件展示了如何在后代组件中使用模型。`ScopedModelDescendant`组件用于获取最近的封闭模型对象。类型参数决定了要获取的模型对象。`builder`参数指定了用于构建组件的构建函数。该构建函数有三个参数。第一个参数的类型是`BuildContext`，这在构建函数中很常见。最后一个参数是模型对象。如果组件 UI 的某一部分不依赖于模型，并且不应在模型更改时重建，你可以将其指定为`ScopedModelDescendant`组件的`child`参数，并在构建函数的第二个参数中访问它。

```
class ScopedModelText extends StatelessWidget {
@override
Widget build(BuildContext context) {
return ScopedModelDescendant(
builder: (BuildContext context, Widget child, Config config) {
return Text(
'Font size: ${config.fontSize}',
style: TextStyle(
color: config.color,
fontSize: config.fontSize,
),
);
},
);
}
}
Listing 10-14
ScopedModelText uses ScopedModelDescendant
```

在清单 10-15 中，`ScopedModelUpdater`组件简单地使用`setColor()`和`increaseFontSize()`方法来更新状态。

```
class ScopedModelUpdater extends StatelessWidget {
static const List _colors = [Colors.red, Colors.green, Colors.blue];
@override
Widget build(BuildContext context) {
return ScopedModelDescendant(
builder: (BuildContext context, Widget child, Config config) {
return Column(
children: [
DropdownButton(
value: config.color,
items: _colors.map((Color color) {
return DropdownMenuItem(
value: color,
child: Text(color.toString()),
);
}).toList(),
onChanged: (Color color) {
config.setColor(color);
},
),
RaisedButton(
child: Text('Increase font size'),
onPressed: () {
config.increaseFontSize();
},
)
],
);
},
);
}
}
Listing 10-15
ScopedModelUpdater to update Config object
```

清单 10-16 中的`ScopedModel`组件是将`Model`和`ScopedModelDescendant`结合在一起的最后一部分。`model`参数指定了由`ScopedModel`对象管理的模型对象。`ScopedModel`对象下的所有`ScopedModelDescendant`组件都会获取到同一个模型对象。

```
class ScopedModelPage extends StatelessWidget {
@override
Widget build(BuildContext context) {
return Scaffold(
appBar: AppBar(
title: Text('Scoped Model'),
),
body: ScopedModel(
model: Config(),
child: Column(
children: [
ScopedModelUpdater(),
ScopedModelText()
],
),
),
);
}
}
Listing 10-16
ScopedModelPage uses ScopedModel
```

你也可以使用静态方法`ScopedModel.of()`来获取`ScopedModel`对象，然后通过其`model`属性获取模型对象。

## 10.6 使用 Bloc 管理状态

### 问题

你想使用 Bloc 模式来管理状态。

### 解决方案

使用`bloc`和`flutter_bloc`包。



### 讨论

Bloc（业务逻辑组件）是一种架构模式，用于将表现层与业务逻辑分离。Bloc 被设计为简单、强大且易于测试。让我们从 Bloc 的核心概念开始。

`State`（状态）表示应用程序状态的一部分。当状态发生变化时，会通知 UI 组件根据最新状态进行重建。每个应用程序都有自己的方式来定义状态。通常，你会使用 Dart 类来描述状态。

`Event`（事件）是状态变化的来源。事件可以由用户交互或后台任务生成。例如，按下按钮可能会生成一个描述预期操作的事件。当 HTTP 请求的响应就绪时，也可以生成一个包含响应体的事件。事件通常使用 Dart 类来描述。事件可能还带有它们的载荷。

当一个事件被派发时，处理这些事件可能会导致当前状态转换到新状态。然后 UI 组件会被通知使用新状态进行重建。一个事件转换包括当前状态、事件和下一个状态。如果所有状态转换都被记录，我们可以轻松追踪所有的用户交互和状态变化。我们还可以实现时间旅行调试。

现在我们可以给出 Bloc 的定义。一个 Bloc 组件将事件流转换为状态流。一个 Bloc 有一个初始状态，即在接收到任何事件之前的状态。对于每个事件，Bloc 都有一个`mapEventToState()`函数，该函数接收一个事件并返回一个状态流，供表现层使用。Bloc 还有一个`dispatch()`方法来向它派发事件。

在本教程中，我们将使用 GitHub Jobs API（[`https://jobs.github.com/api`](https://jobs.github.com/api)）来获取 GitHub 上的职位列表。用户可以输入关键词进行搜索并查看结果。为了实现这一点，我们将使用`http`包（[`https://pub.dev/packages/http`](https://pub.dev/packages/http)）。将这个包添加到你的`pubspec.yaml`文件中。

让我们从状态开始。代码清单 10-17 展示了不同状态的类。`JobsState`是所有状态类的抽象基类。`JobsState`类继承自`equatable`包中的`Equatable`类。`Equatable`类用于提供`==`运算符和`hashCode`属性的实现。`JobsEmpty`是初始状态。`JobsLoading`表示职位列表数据仍在加载中。`JobsLoaded`表示职位列表数据已加载。`JobsLoaded`事件的载荷类型是`List<Job>`。`JobsError`表示在获取数据时发生了错误。

```
import 'package:http/http.dart' as http;
abstract class JobsState extends Equatable {
  JobsState([List props = const []]) : super(props);
}
class JobsEmpty extends JobsState {}
class GetJobsEvent extends JobsEvent {
  GetJobsEvent({@required this.keyword})
      : assert(keyword != null),
        super([keyword]);
  final String keyword;
}
class GitHubJobsClient {
  Future> getJobs(keyword) async {
    final response = await http.get('https://jobs.github.com/positions.json?description=${keyword}');
    if (response.statusCode != 200) {
      throw new Exception("Unable to fetch data");
    } else {
      var result = new List();
      final rawResult = json.decode(response.body);
      for (final jsonJob in rawResult) {
        result.add(Job.fromJson(jsonJob));
      }
    }
  }
}
class JobsLoading extends JobsState {}
class JobsLoaded extends JobsState {
  JobsLoaded({@required this.jobs})
      : assert(jobs != null),
        super([jobs]);
  final List jobs;
}
class JobsError extends JobsState {}
Listing 10-17
Bloc states
```

代码清单 10-18 展示了事件。`JobsEvent`是事件类的抽象基类。`GetJobsEvent`类表示获取职位数据的事件。

```
abstract class JobsEvent extends Equatable {
  JobsEvent([List props = const []]) : super(props);
}
class GetJobsEvent extends JobsEvent {
  GetJobsEvent({@required this.keyword})
      : assert(keyword != null),
        super([keyword]);
  final String keyword;
}
Listing 10-18
Bloc events
```

代码清单 10-19 展示了 Bloc。`JobsBloc`类继承自`Bloc<JobsEvent, JobsState>`类。`Bloc`的类型参数是事件和状态类。`JobsEmpty`是初始状态。在`mapEventToState()`方法中，如果事件是`GetJobsEvent`，则首先向流中`yield`一个`JobsLoading`状态。然后使用`GitHubJobsClient`对象来获取数据。如果数据获取成功，则`yield`一个包含已加载数据的`JobsLoaded`状态。否则，`yield`一个`JobsError`状态。

```
class JobsBloc extends Bloc {
  JobsBloc({@required this.jobsClient}) : assert(jobsClient != null);
  final GitHubJobsClient jobsClient;
  @override
  JobsState get initialState => JobsEmpty();
  @override
  Stream mapEventToState(JobsEvent event) async* {
    if (event is GetJobsEvent) {
      yield JobsLoading();
      try {
        List jobs = await jobsClient.getJobs(event.keyword);
        yield JobsLoaded(jobs: jobs);
      } catch (e) {
        yield JobsError();
      }
    }
  }
}
Listing 10-19
Bloc
```

代码清单 10-20 中的`GitHubJobs`类是使用代码清单 10-19 中`JobsBloc`类的组件。`JobsBloc`对象在`initState()`方法中创建，并在`dispose()`方法中释放。在`KeywordInput`组件中，当用户在文本字段中输入关键词并按下搜索按钮时，会向`JobsBloc`对象派发一个`GetJobsEvent`。在`JobsView`组件中，使用`BlocBuilder`组件根据 Bloc 中的状态构建 UI。这里我们检查`JobsState`的实际类型并返回不同的组件。

```
class GitHubJobs extends StatefulWidget {
  GitHubJobs({Key key, @required this.jobsClient})
      : assert(jobsClient != null),
        super(key: key);
  final GitHubJobsClient jobsClient;
  @override
  _GitHubJobsState createState() => _GitHubJobsState();
}
class _GitHubJobsState extends State {
  JobsBloc _jobsBloc;
  @override
  void initState() {
    super.initState();
    _jobsBloc = JobsBloc(jobsClient: widget.jobsClient);
  }
  @override
  Widget build(BuildContext context) {
    return Column(
      children: [
        Padding(
          padding: const EdgeInsets.all(8.0),
          child: KeywordInput(
            jobsBloc: _jobsBloc,
          ),
        ),
        Expanded(
          child: JobsView(
            jobsBloc: _jobsBloc,
          ),
        ),
      ],
    );
  }
  @override
  void dispose() {
    _jobsBloc.dispose();
    super.dispose();
  }
}
class KeywordInput extends StatefulWidget {
  KeywordInput({this.jobsBloc});
  final JobsBloc jobsBloc;
  @override
  _KeywordInputState createState() => _KeywordInputState();
}
class _KeywordInputState extends State {
  final GlobalKey> _keywordFormKey = GlobalKey();
  @override
  Widget build(BuildContext context) {
    return Row(
      children: [
        Expanded(
          child: TextFormField(
            key: _keywordFormKey,
          ),
        ),
        IconButton(
          icon: Icon(Icons.search),
          onPressed: () {
            String keyword = _keywordFormKey.currentState?.value ?? "";
            if (keyword.isNotEmpty) {
              widget.jobsBloc.dispatch(GetJobsEvent(keyword: keyword));
            }
          },
        ),
      ],
    );
  }
}
class JobsView extends StatelessWidget {
  JobsView({this.jobsBloc});
  final JobsBloc jobsBloc;
  @override
  Widget build(BuildContext context) {
    return BlocBuilder(
      bloc: jobsBloc,
      builder: (BuildContext context, JobsState state) {
        if (state is JobsEmpty) {
          return Center(
            child: Text('Input keyword and search'),
          );
        } else if (state is JobsLoading) {
          return Center(
            child: CircularProgressIndicator(),
          );
        } else if (state is JobsError) {
          return Center(
            child: Text(
              'Failed to get jobs',
              style: TextStyle(color: Colors.red),
            ),
          );
        } else if (state is JobsLoaded) {
          return JobsList(state.jobs);
        }
      },
    );
  }
}
Listing 10-20
GitHub jobs widget using Bloc
```

## 10.7 使用 Redux 管理状态

### 问题

你想使用 Redux 作为状态管理方案。

### 解决方案

使用`redux`和`flux_redux`包。



### 讨论

Redux（[`https://redux.js.org/`](https://redux.js.org/)）是一个用于管理应用程序状态的流行库。Redux 起源于 React，但已被移植到不同语言。`redux` 包是 Redux 的 Dart 实现。`flutter_redux` 包允许在构建 Flutter 小部件时使用 Redux 存储。如果您之前使用过 Redux，那么 Flutter 中使用的概念相同。

Redux 使用一个全局对象作为状态。这个对象是应用程序的唯一数据源，被称为 store。动作被派发到 store 以更新状态。Reducer 函数接受当前状态和一个动作作为参数，并返回下一个状态。下一个状态成为 reducer 函数下一次运行的输入。UI 小部件可以从 store 中选择部分数据来构建内容。

要使用 `flutter_redux` 包，您需要将 `flutter_redux: ⁰.5.3` 添加到 `pubspec.yaml` 文件的 `dependencies` 中。我们将使用相同的 GitHub 职位列表示例来演示 Redux 在 Flutter 中的用法。

让我们从状态开始。清单 10-21 中的 `JobsState` 类表示全局状态。该状态有三个属性：`loading` 表示数据是否仍在加载，`error` 表示加载数据时是否发生错误，`data` 表示数据列表。通过使用 `copyWith()` 方法，我们可以通过更新某些属性来创建新的 `JobsState` 对象。

```
class JobsState extends Equatable {
JobsState({bool loading, bool error, List data})
: _loading = loading,
_error = error,
_data = data,
super([loading, error, data]);
final bool _loading;
final bool _error;
final List _data;
bool get loading => _loading ?? false;
bool get error => _error ?? false;
List get data => _data ?? [];
bool get empty => _loading == null && _error == null && _data == null;
JobsState copyWith({bool loading, bool error, List data}) {
return JobsState(
loading: loading ?? this._loading,
error: error ?? this._error,
data: data ?? this._data,
);
}
}
Listing 10-21
用于 Redux 的 JobsState
```

清单 10-22 展示了这些动作。这些动作会触发状态变化。

```
abstract class JobsAction extends Equatable {
JobsAction([List props = const []]) : super(props);
}
class LoadJobAction extends JobsAction {
LoadJobAction({@required this.keyword})
: assert(keyword != null),
super([keyword]);
final String keyword;
}
class JobLoadedAction extends JobsAction {
JobLoadedAction({@required this.jobs})
: assert(jobs != null),
super([jobs]);
final List jobs;
}
class JobLoadErrorAction extends JobsAction {}
Listing 10-22
用于 Redux 的动作
```

清单 10-23 展示了根据动作更新状态的 reducer 函数。

```
JobsState jobsReducers(JobsState state, dynamic action) {
if (action is LoadJobAction) {
return state.copyWith(loading: true);
} else if (action is JobLoadErrorAction) {
return state.copyWith(loading: false, error: true);
} else if (action is JobLoadedAction) {
return state.copyWith(loading: false, data: action.jobs);
}
return state;
}
Listing 10-23
用于 Redux 的 Reducer 函数
```

清单 10-22 中定义的动作只能用于同步操作。例如，如果您想要派发 `JobLoadedAction`，您需要先准备好 `List<Job>` 对象。然而，加载职位数据的操作是异步的。您需要使用 thunk 函数作为 Redux store 的中间件。thunk 函数将 store 作为唯一参数。它使用 store 来派发动作。thunk 动作可以像其他普通动作一样被派发到 store。

清单 10-24 中的 `getJobs()` 函数接受一个 `GitHubJobsClient` 对象和一个搜索关键词作为参数。该函数返回一个类型为 `ThunkAction<JobsState>` 的 thunk 函数。`ThunkAction` 来自 `redux_thunk` 包。在 thunk 函数中，首先派发一个 `LoadJobAction`。然后使用 `GitHubJobsClient` 对象获取职位数据。根据数据加载的结果，派发 `JobLoadedAction` 或 `JobLoadErrorAction`。

```
ThunkAction getJobs(GitHubJobsClient jobsClient, String keyword) {
return (Store store) async {
store.dispatch(LoadJobAction(keyword: keyword));
try {
List jobs = await jobsClient.getJobs(keyword);
store.dispatch(JobLoadedAction(jobs: jobs));
} catch (e) {
store.dispatch(JobLoadErrorAction());
}
};
}
Listing 10-24
用于 Redux 的 Thunk 函数
```

现在我们可以使用 Redux store 来构建小部件。您可以使用两个辅助小部件来访问 store 中的数据。在清单 10-25 中，`StoreBuilder` 小部件用于直接访问 store。store 作为构建函数的第二个参数可用。`StoreBuilder` 小部件通常用于需要派发动作的情况。`StoreConnector` 小部件允许使用转换器函数先转换状态。当搜索图标被按下时，首先调用清单 10-24 中的 `getJobs()` 函数来创建 thunk 函数，然后将 thunk 函数派发到 store。当使用 `StoreConnector` 小部件时，转换器函数简单地从 store 获取当前状态。然后状态对象在构建函数中使用。

```
class GitHubJobs extends StatefulWidget {
GitHubJobs({
Key key,
@required this.store,
@required this.jobsClient,
})  : assert(store != null),
assert(jobsClient != null),
super(key: key);
final Store store;
final GitHubJobsClient jobsClient;
@override
_GitHubJobsState createState() => _GitHubJobsState();
}
class _GitHubJobsState extends State {
@override
Widget build(BuildContext context) {
return StoreProvider(
store: widget.store,
child: Column(
children: [
Padding(
padding: const EdgeInsets.all(8.0),
child: KeywordInput(
jobsClient: widget.jobsClient,
),
),
Expanded(
child: JobsView(),
),
],
),
);
}
}
class KeywordInput extends StatefulWidget {
KeywordInput({this.jobsClient});
final GitHubJobsClient jobsClient;
@override
_KeywordInputState createState() => _KeywordInputState();
}
class _KeywordInputState extends State {
final GlobalKey> _keywordFormKey = GlobalKey();
@override
Widget build(BuildContext context) {
return Row(
children: [
Expanded(
child: TextFormField(
key: _keywordFormKey,
),
),
StoreBuilder(
builder: (BuildContext context, Store store) {
return IconButton(
icon: Icon(Icons.search),
onPressed: () {
String keyword = _keywordFormKey.currentState?.value ?? ";
if (keyword.isNotEmpty) {
store.dispatch(getJobs(widget.jobsClient, keyword));
}
},
);
},
),
],
);
}
}
class JobsView extends StatelessWidget {
@override
Widget build(BuildContext context) {
return StoreConnector(
converter: (Store store) => store.state,
builder: (BuildContext context, JobsState state) {
if (state.empty) {
return Center(
child: Text('输入关键词并搜索'),
);
} else if (state.loading) {
return Center(
child: CircularProgressIndicator(),
);
} else if (state.error) {
return Center(
child: Text(
'获取职位失败',
style: TextStyle(color: Colors.red),
),
);
} else {
return JobsList(state.data);
}
},
);
}
}
Listing 10-25
使用 Redux store 的 GitHub 职位小部件
```

最后一步是创建 store。清单 10-26 中的 store 是使用 reducer 函数、初始状态和来自 `redux_thunk` 包的 thunk 中间件创建的。

```
final store = new Store(
jobsReducers,
initialState: JobsState(),
middleware: [thunkMiddleware],
);
Listing 10-26
创建 store
```

## 10.8 使用 Mobx 管理状态

### 问题

您想要使用 Mobx 来管理状态。

### 解决方案

使用 `mobx` 和 `flutter_mobx` 包。



### 讨论

Mobx（[`https://mobx.js.org`](https://mobx.js.org)）是一个将响应式数据与 UI 连接起来的状态管理库。MobX 源于使用 JavaScript 开发 Web 应用，同时也被移植到了 Dart 平台（[`https://mobx.pub`](https://mobx.pub) ）。在 Flutter 应用中，我们可以使用 `mobx` 和 `flutter_mobx` 这两个包来构建基于 Mobx 的应用。Flutter 的 Mobx 使用 `build_runner` 包来为 store 生成代码。`build_runner` 和 `mobx_codegen` 这两个包需要作为 `dev_dependencies` 添加到 `pubspec.yaml` 文件中。

Mobx 使用可观察对象（observables）来管理状态。应用的整体状态由核心状态和派生状态两部分组成。派生状态是从核心状态计算得出的。动作（Actions）通过改变可观察对象来更新状态。反应（Reactions）是状态的观察者，当它们追踪的任何可观察对象发生变化时，便会收到通知。在 Flutter 应用中，反应被用来更新 widget。

与 Flutter 的 Redux 相比，Mobx 使用代码生成来简化 store 的使用。你无需编写样板代码来创建动作。Mobx 提供了多种注解，你只需使用这些注解来标记代码即可。这与 `json_annotation` 和 `json_serialize` 包的工作方式类似。我们将使用同样的示例（展示 GitHub 上的职位列表）来演示 Mobx 的用法。如果你的 `pubspec.yaml` 文件中还没有这个包，请将其添加进去。

代码清单 10-27 展示了用于 Mobx store 的 `jobs_store.dart` 文件的基本代码。该文件使用了生成的 part 文件 `jobs_store.g.dart`。`_JobsStore` 是职位 store 的抽象类，它实现了 Mobx 中的 `Store` 类。这里我们使用 `@observable` 注解定义了两个可观察对象。第一个可观察对象 `keyword` 是一个简单的字符串，用于管理当前的搜索关键字。`getJobsFuture` 可观察对象是一个 `ObservableFuture<List<Job>>` 类型的对象，用于管理通过 API 获取职位的异步操作。那些使用 `@computed` 注解标记的属性是派生可观察对象，用于检查数据加载的状态。我们还使用 `@action` 注解定义了两个动作。`setKeyword()` 动作将 `getJobsFuture` 可观察对象设置为空状态，并将 `keyword` 可观察对象设置为提供的值。`getJobs()` 动作使用 `GitHubJobsClient.getJobs()` 方法来加载数据。`getJobsFuture` 可观察对象会被更新为一个包装了返回的 future 的 `ObservableFuture` 对象。

```
import 'package:meta/meta.dart';
import 'package:mobx/mobx.dart';
part 'jobs_store.g.dart';
class JobsStore = _JobsStore with _$JobsStore;
abstract class _JobsStore implements Store {
_JobsStore({@required this.jobsClient}) : assert(jobsClient != null);
final GitHubJobsClient jobsClient;
@observable
String keyword = "";
@observable
ObservableFuture<List<Job>> getJobsFuture = emptyResponse;
@computed
bool get empty => getJobsFuture == emptyResponse;
@computed
bool get hasResults =>
getJobsFuture != emptyResponse &&
getJobsFuture.status == FutureStatus.fulfilled;
@computed
bool get loading =>
getJobsFuture != emptyResponse &&
getJobsFuture.status == FutureStatus.pending;
@computed
bool get hasError =>
getJobsFuture != emptyResponse &&
getJobsFuture.status == FutureStatus.rejected;
static ObservableFuture<List<Job>> emptyResponse = ObservableFuture.value([]);
List<Job> jobs = [];
@action
Future<List<Job>> getJobs() async {
jobs = [];
final future = jobsClient.getJobs(keyword);
getJobsFuture = ObservableFuture(future);
return jobs = await future;
}
@action
void setKeyword(String keyword) {
getJobsFuture = emptyResponse;
this.keyword = keyword;
}
}
代码清单 10-27
Mobx store
```

需要运行 `flutter packages pub run build_runner build` 命令来生成代码。`JobsStore` 类就是我们要使用的 store。代码清单 10-28 展示了使用该 store 的 widget。在搜索按钮的 `onPressed` 回调中，首先调用 `setKeyword()` 方法来更新关键字，然后调用 `getJobs()` 方法来触发数据加载。`Observer` widget 使用一个构建函数，通过 `JobsStore` 对象中的计算可观察对象和字段来构建 UI。每当这些可观察对象发生变化时，`Observer` widget 就会重新构建以更新 UI。

```
class GitHubJobs extends StatefulWidget {
GitHubJobs({Key key, @required this.jobsStore})
: assert(jobsStore != null),
super(key: key);
final JobsStore jobsStore;
@override
_GitHubJobsState createState() => _GitHubJobsState();
}
class _GitHubJobsState extends State<GitHubJobs> {
@override
Widget build(BuildContext context) {
JobsStore jobsStore = widget.jobsStore;
return Column(
children: [
Padding(
padding: const EdgeInsets.all(8.0),
child: KeywordInput(
jobsStore: jobsStore,
),
),
Expanded(
child: JobsView(
jobsStore: jobsStore,
),
),
],
);
}
}
class KeywordInput extends StatefulWidget {
KeywordInput({this.jobsStore});
final JobsStore jobsStore;
@override
_KeywordInputState createState() => _KeywordInputState();
}
class _KeywordInputState extends State<KeywordInput> {
final GlobalKey<FormFieldState<String>> _keywordFormKey = GlobalKey();
@override
Widget build(BuildContext context) {
return Row(
children: [
Expanded(
child: TextFormField(
key: _keywordFormKey,
),
),
IconButton(
icon: Icon(Icons.search),
onPressed: () {
String keyword = _keywordFormKey.currentState?.value ?? " ";
if (keyword.isNotEmpty) {
widget.jobsStore.setKeyword(keyword);
widget.jobsStore.getJobs();
}
},
),
],
);
}
}
class JobsView extends StatelessWidget {
JobsView({this.jobsStore});
final JobsStore jobsStore;
@override
Widget build(BuildContext context) {
return Observer(
builder: (BuildContext context) {
if (jobsStore.empty) {
return Center(
child: Text('请输入关键字搜索'),
);
} else if (jobsStore.loading) {
return Center(
child: CircularProgressIndicator(),
);
} else if (jobsStore.hasError) {
return Center(
child: Text(
'获取职位失败',
style: TextStyle(color: Colors.red),
),
);
} else {
return JobsList(jobsStore.jobs);
}
},
);
}
}
代码清单 10-28
使用 Mobx store 的 GitHub 职位 widget
```

## 10.9 总结

本章讨论了适用于 Flutter 应用的不同状态管理方案。在这些方案中，`StatefulWidget`、`InheritedWidget`、`InheritedModel` 和 `InheritedNotifier` 是 Flutter 框架提供的。Scoped model、Bloc、Redux 和 Mobx 库则是第三方的解决方案。你可以自由选择最适合你需求的方案。在下一章中，我们将讨论 Flutter 中的动画。

