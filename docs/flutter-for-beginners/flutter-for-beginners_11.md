# 11. 动画

动画在移动应用中扮演着重要角色，可以为最终用户提供视觉反馈。本章涵盖了与 Flutter 动画相关的实践。

## 11.1 创建简单动画

### 问题

你想要创建简单的动画。

### 解决方案

使用 `AnimationController` 类来创建简单的动画。



### 讨论

Flutter 中的动画具有值和状态。动画的值可能会随时间变化。动画使用抽象的 `Animation<T>` 类来表示。 `Animation` 类继承自 `Listenable` 类。你可以向 `Animation` 对象添加监听器，以在值或状态发生变化时收到通知。

`AnimationController` 类是 `Animation<double>` 类的子类。 `AnimationController` 类提供对其创建的动画的控制。要创建 `AnimationController` 对象，你可以提供下限、上限和持续时间。 `AnimationController` 对象的值会在持续时间内从下限变化到上限。此外还需要一个 `TickerProvider` 对象。对于有状态 widget，你可以使用 `TickerProviderStateMixin` 或 `SingleTickerProviderStateMixin` 类作为状态类的 mixin。如果状态只使用一个 `AnimationController` 对象，则使用 `SingleTickerProviderStateMixin` 效率更高。

代码清单 11-1 展示了一个在有状态 widget 中使用 `AnimationController` 来实现图片尺寸动画的示例。 `AnimationController` 对象在 `initState()` 方法体中创建，并在 `dispose()` 方法中释放。这是使用 `AnimationController` 的典型模式。 `_GrowingImageState` 类混入了 `SingleTickerProviderStateMixin`，因此 `AnimationController` 构造函数将该对象用作 `vsync` 参数。在 `AnimationController` 对象的监听器中，调用 `setState()` 方法来触发 widget 的重建。 `forward()` 方法以正向启动动画运行。在 `build()` 方法中，使用 `AnimationController` 对象的当前值来控制 `SizedBox` widget 的尺寸。在运行时，`SizedBox` widget 的尺寸在 10 秒内从 `0` 增长到 `400`。

```
class GrowingImage extends StatefulWidget {
@override
_GrowingImageState createState() => _GrowingImageState();
}
class _GrowingImageState extends State
with SingleTickerProviderStateMixin {
AnimationController controller;
@override
void initState() {
super.initState();
controller = AnimationController(
lowerBound: 0,
upperBound: 400,
duration: const Duration(seconds: 10),
vsync: this,
)
..addListener(() {
setState(() {});
})
..forward();
}
@override
Widget build(BuildContext context) {
return SizedBox(
width: controller.value,
height: controller.value,
child: Image.network('https://picsum.photos/400'),
);
}
@override
void dispose() {
controller.dispose();
super.dispose();
}
}
代码清单 11-1
使用 AnimationController
```

表 11-1 展示了控制动画进度的 `AnimationController` 方法。

**表 11-1** 控制动画的方法

| 名称 | 描述 |
| --- | --- |
| `forward()` | 以正向启动动画运行。 |
| `reverse()` | 以反向启动动画运行。 |
| `stop()` | 停止动画运行。 |
| `repeat()` | 启动动画运行，并在完成后重新开始。 |
| `reset()` | 将值设置为下限并停止动画。 |

动画可能处于不同的状态。`AnimationStatus` 枚举表示动画的不同状态。表 11-2 展示了该枚举的所有值。你可以使用 `addStatusListener()` 方法添加监听器，以便在状态改变时收到通知。

**表 11-2** `AnimationStatus` 的值

| 名称 | 描述 |
| --- | --- |
| `forward` | 动画正在正向运行。 |
| `reverse` | 动画正在反向运行。 |
| `dismissed` | 动画在开始处停止。 |
| `completed` | 动画在结束处停止。 |

在代码清单 11-2 中，为 `AnimationController` 对象添加了一个状态监听器。当动画处于 `completed` 状态时，它会以反向开始运行。

```
var controller = AnimationController(
lowerBound: 0,
upperBound: 300,
duration: const Duration(seconds: 10),
vsync: this,
)
..addListener(() {
setState(() {});
})
..addStatusListener((AnimationStatus status) {
if (status == AnimationStatus.completed) {
controller.reverse();
}
})
..forward();
代码清单 11-2
状态监听器
```

代码清单 11-1 展示了在有状态 widget 中使用动画的典型模式。`AnimatedWidget` widget 使得动画的使用更加简单。 `AnimatedWidget` 构造函数需要一个 `Listenable` 对象。每当 `Listenable` 对象发出一个值时，widget 会自动重建。代码清单 11-3 展示了一个使用 `AnimatedWidget` 的示例。虽然 `AnimatedWidget` 类通常与 `Animation` 对象一起使用，但你仍然可以将其与任何 `Listenable` 对象一起使用。

```
class AnimatedImage extends AnimatedWidget {
AnimatedImage({Key key, this.animation})
: super(key: key, listenable: animation);
final Animation animation;
@override
Widget build(BuildContext context) {
return SizedBox(
width: animation.value,
height: animation.value,
child: Image.network('https://picsum.photos/300'),
);
}
}
代码清单 11-3
AnimatedWidget 示例
```

## 11.2 使用线性插值创建动画

### 问题

你想使用线性插值为其他数据类型创建动画。

### 解决方案

使用 `Tween` 类及其子类。

### 讨论

`AnimationController` 类使用 `double` 作为其值类型。`double` 值对于尺寸或位置的动画非常有用。你可能还需要对其他类型的数据进行动画处理。例如，你可以将背景颜色从红色动画变化到绿色。对于这些场景，你可以使用 `Tween` 类及其子类。

`Tween` 类表示起始值和结束值之间的线性插值。要创建 `Tween` 对象，你需要提供这两个值。`Tween` 对象可以为动画提供所需的值。通过将 `animate()` 方法与另一个 `Animation` 对象结合使用，你可以创建一个新的 `Animation` 对象，该对象由提供的 `Animation` 对象驱动，但使用来自 `Tween` 对象的值。`Tween` 的子类需要实现 `lerp()` 方法，该方法接收一个动画值并返回插值后的值。

在代码清单 11-4 中，`AnimatedColor` widget 使用 `Animation<Color>` 对象来更新背景颜色。`ColorTween` 对象使用起始值 `Colors.red` 和结束值 `Colors.green` 创建。

```
class AnimatedColorTween extends StatefulWidget {
@override
_AnimatedColorTweenState createState() => _AnimatedColorTweenState();
}
class _AnimatedColorTweenState extends State
with SingleTickerProviderStateMixin {
AnimationController controller;
Animation animation;
@override
void initState() {
super.initState();
controller = AnimationController(
duration: const Duration(seconds: 10),
vsync: this,
);
animation =
ColorTween(begin: Colors.red, end: Colors.green).animate(controller);
controller.forward();
}
@override
Widget build(BuildContext context) {
return AnimatedColor(
animation: animation,
);
}
@override
void dispose() {
controller.dispose();
super.dispose();
}
}
class AnimatedColor extends AnimatedWidget {
AnimatedColor({Key key, this.animation})
: super(key: key, listenable: animation);
final Animation animation;
@override
Widget build(BuildContext context) {
return Container(
width: 300,
height: 300,
decoration: BoxDecoration(color: animation.value),
);
}
}
代码清单 11-4
ColorTween 示例
```

还有许多其他针对不同对象的 `Tween` 子类，包括 `AlignmentTween`、`BorderTween`、`BoxConstraintsTween`、`DecorationTween`、`EdgeInsetsTween`、`SizeTween`、`TextStyleTween` 等等。

## 11.3 创建曲线动画

### 问题

你想创建曲线动画。

### 解决方案

使用 `CurvedAnimation` 或 `CurveTween` 类。



### Discussion

除了线性动画，你还可以创建使用曲线来调整变化速率的曲线动画。曲线是单位区间到另一个单位区间的映射。`Curve`类及其子类是内置的曲线类型。`Curve`类的`transform()`方法返回给定点在曲线上的映射值。曲线必须将输入`0.0`映射到`0.0`，`1.0`映射到`1.0`。表 11-3 展示了不同类型的曲线。

**表 11-3** 不同类型的曲线

| 名称 | 描述 |
| --- | --- |
| `Cubic` | 由两个控制点定义的立方曲线。通过四个`double`值（这两个点的 x 和 y 坐标）创建。 |
| `ElasticInCurve` | 在超出边界时幅度增长的振荡曲线。通过振荡持续时间创建。 |
| `ElasticOutCurve` | 在超出边界时幅度缩小的振荡曲线。通过振荡持续时间创建。 |
| `ElasticInOutCurve` | 在超出边界时幅度先增长后缩小的振荡曲线。通过振荡持续时间创建。 |
| `Interval` | 使用`begin`、`end`和一个`curve`创建。其值在`begin`之前为`0.0`，在`end`之后为`1.0`。`begin`和`end`之间的值由曲线定义。 |
| `SawTooth` | 重复指定次数的锯齿曲线。 |
| `Threshold` | 直到阈值时值为`0.0`，然后跳变到`1.0`的曲线。 |

你可以使用表 11-3 中`Curve`子类的构造函数来创建新曲线，或者使用`Curves`类中的常量。`Curves`类中的常量在大多数情况下已经足够使用。对于一个`Curve`对象，你可以使用其`flipped`属性来获取当前曲线的反转曲线。

使用`Curve`对象，你可以通过`CurvedAnimation`类创建曲线动画。表 11-4 展示了`CurvedAnimation`构造函数的参数。如果`reverseCurve`参数为`null`，则在两个方向都使用指定的曲线。

**表 11-4** `CurvedAnimation`的参数

| 名称 | 类型 | 描述 |
| --- | --- | --- |
| `parent` | `Animation<double>` | 要应用曲线的动画。 |
| `curve` | `Curve` | 在正向方向使用的曲线。 |
| `reverseCurve` | `Curve` | 在反向方向使用的曲线。 |

在清单 11-5 中，`AnimatedBox`组件使用动画值来确定盒子的左侧位置。`CurvedAnimation`对象通过`Curves.easeInOut`曲线创建。

```
class CurvedPosition extends StatefulWidget {
  @override
  _CurvedPositionState createState() => _CurvedPositionState();
}

class _CurvedPositionState extends State<CurvedPosition>
    with SingleTickerProviderStateMixin {
  AnimationController controller;
  Animation<double> animation;

  @override
  void initState() {
    super.initState();
    controller = AnimationController(
      duration: const Duration(seconds: 5),
      vsync: this,
    )..forward();
    animation = CurvedAnimation(parent: controller, curve: Curves.easeInOut);
  }

  @override
  Widget build(BuildContext context) {
    return AnimatedBox(
      animation: animation,
    );
  }

  @override
  void dispose() {
    controller.dispose();
    super.dispose();
  }
}

class AnimatedBox extends AnimatedWidget {
  AnimatedBox({Key key, this.animation})
      : super(key: key, listenable: animation);
  final Animation<double> animation;
  final double _width = 400;

  @override
  Widget build(BuildContext context) {
    return Container(
      width: _width,
      height: 20,
      child: Stack(
        children: [
          Positioned(
            left: animation.value * _width,
            bottom: 0,
            child: Container(
              width: 10,
              height: 10,
              decoration: BoxDecoration(color: Colors.red),
            ),
          )
        ],
      ),
    );
  }
}
```

**清单 11-5** `CurvedAnimation`

`CurveTween`类使用`Curve`对象来转换动画的值。当你需要将曲线动画与另一个`Tween`对象链式组合时，可以使用`CurveTween`对象。

## 11.4 链式组合补间动画

### 问题

你想要链式组合多个补间动画。

### 解决方案

使用`Animatable`类的`chain()`方法或`Animation`类的`drive()`方法。

### 讨论

`Animatable`是`Tween`、`CurveTween`和`TweenSequence`类的超类。给定一个`Animatable`对象，你可以使用`chain()`方法并将另一个`Animatable`对象作为父级。对于给定的输入值，首先计算父级`Animatable`对象，然后将结果作为当前`Animatable`对象的输入。你可以使用多个`chain()`方法来创建复杂的动画。

在清单 11-6 中，`Tween`对象与另一个`CurveTween`对象进行了链式组合。

```
var animation = Tween<double>(begin: 0.0, end: 300.0)
    .chain(CurveTween(curve: Curves.easeOut))
    .animate(controller);
```

**清单 11-6** 链式组合补间动画

你也可以使用`Animation`类的`drive()`方法来链式组织一个`Animatable`对象。

## 11.5 创建补间动画序列

### 问题

你想要为不同阶段创建一个补间动画序列。

### 解决方案

使用`TweenSequence`类。

### 讨论

通过使用`TweenSequence`类，你可以为动画的不同阶段使用不同的`Animatable`对象。一个`TweenSequence`对象由一组`TweenSequenceItem`对象列表定义。每个`TweenSequenceItem`对象包含一个`Animatable`对象和一个权重。权重定义了这个`TweenSequenceItem`对象在其父级`TweenSequence`对象的整个持续时间中所占的相对百分比。

在清单 11-7 中，动画由 40%的线性补间和 60%的曲线补间组成。

```
var animation = TweenSequence([
  TweenSequenceItem(
    tween: Tween<double>(begin: 0.0, end: 100.0),
    weight: 40,
  ),
  TweenSequenceItem(
    tween: Tween<double>(begin: 100.0, end: 300.0)
        .chain(CurveTween(curve: Curves.easeInOut)),
    weight: 60,
  )
]).animate(controller);
```

**清单 11-7** `TweenSequence`示例

## 11.6 运行同步动画

### 问题

你想要在`AnimatedWidget`中运行多个同步动画。

### 解决方案

使用`Animatable`类的`evaluate()`方法。

### 讨论

`AnimatedWidget`的构造函数只支持单个`Animation`对象。如果你想在`AnimatedWidget`对象中使用多个动画，你需要在`AnimatedWidget`对象中创建多个`Tween`对象，并使用`evaluate()`方法来获取对应`Animation`对象的值。

在清单 11-8 中，`_leftTween`和`_bottomTween`对象分别决定了盒子的左侧和底部位置属性。

```
class AnimatedBox extends AnimatedWidget {
  AnimatedBox({Key key, this.animation})
      : super(key: key, listenable: animation);
  final Animation<double> animation;
  final double _width = 400;
  final double _height = 300;
  static final _leftTween = Tween<double>(begin: 0, end: 1.0);
  static final _bottomTween = CurveTween(curve: Curves.ease);

  @override
  Widget build(BuildContext context) {
    return Container(
      width: _width,
      height: _height,
      margin: EdgeInsets.all(10),
      decoration: BoxDecoration(border: Border.all()),
      child: Stack(
        children: [
          Positioned(
            left: _leftTween.evaluate(animation) * _width,
            bottom: _bottomTween.evaluate(animation) * _height,
            child: Container(
              width: 10,
              height: 10,
              decoration: BoxDecoration(color: Colors.red),
            ),
          )
        ],
      ),
    );
  }
}
```

**清单 11-8** 同步动画

## 11.7 创建交错动画

### 问题

你想要创建顺序执行或重叠的动画。

### 解决方案

使用`Interval`类。



### 讨论

通过`TweenSequence`类，你可以创建一个由多个补间动画组成的序列。然而，在`TweenSequence`对象中指定的补间动画不能重叠。为了创建重叠的动画，你可以使用`Interval`曲线来指定动画的开始和结束时间。

在代码清单 11-9 中，三个`Tween`对象根据`Interval`对象指定的不同时间间隔进行动画。这些`Tween`对象都由同一个`Animation`对象控制。

```dart
class AnimatedContainer extends StatelessWidget {
AnimatedContainer({Key key, this.animation})
: width = Tween(begin: 0.0, end: 300.0).animate(CurvedAnimation(
parent: animation,
curve: Interval(0.0, 0.5, curve: Curves.easeInOut))),
height = Tween(begin: 0.0, end: 200.0).animate(CurvedAnimation(
parent: animation,
curve: Interval(0.2, 0.7, curve: Curves.bounceInOut))),
backgroundColor = ColorTween(begin: Colors.red, end: Colors.green)
.animate(CurvedAnimation(
parent: animation,
curve: Interval(0.3, 1.0, curve: Curves.elasticInOut))),
super(key: key);
final Animation animation;
final Animation width;
final Animation height;
final Animation backgroundColor;
Widget _build(BuildContext context, Widget child) {
return Container(
width: width.value,
height: height.value,
decoration: BoxDecoration(color: backgroundColor.value),
child: child,
);
}
@override
Widget build(BuildContext context) {
return AnimatedBuilder(
animation: animation,
builder: _build,
);
}
}
Listing 11-9
Staggered animations
```

## 11.8 创建 Hero 动画

### 问题

你希望让一个元素在两个路由之间实现动画过渡。

### 解决方案

使用`Hero`组件。

### 讨论

当从当前路由切换到新路由时，最好在新路由中包含一些元素来指示导航的上下文。例如，当前路由显示了一个列表项。当用户点击某一项导航到详情路由时，新路由应包含一个组件来显示选中项的简要信息。

`Hero`组件在两个路由之间共享。创建`Hero`组件需要提供一个标签（`tag`）和一个子组件。标签是`Hero`组件的唯一标识符。如果源路由和目标路由都拥有相同标签的`Hero`组件，那么在路由过渡期间，源路由中的`Hero`组件会以动画方式移动到目标路由中的位置。`Hero`组件的标签在同一组件树中必须是唯一的。

在代码清单 11-10 中，`ImageHero`类包装了一个`Hero`组件，该组件在`SizedBox`组件中显示一张图片。标签被设置为图片的 URL。

```dart
class ImageHero extends StatelessWidget {
ImageHero({Key key, this.imageUrl, this.width, this.height})
: super(key: key);
final String imageUrl;
final double width;
final double height;
@override
Widget build(BuildContext context) {
return SizedBox(
width: width,
height: height,
child: Hero(
tag: imageUrl,
child: Image.network(imageUrl),
),
);
}
}
Listing 11-10
Hero widget
```

代码清单 11-11 展示了显示图片列表的当前路由。`ImageHero`组件被包装在`GridTile`组件中。点击图片会通过`ImageView`组件导航到新路由。

```dart
class ImagesPage extends StatelessWidget {
@override
Widget build(BuildContext context) {
return Scaffold(
appBar: AppBar(
title: Text('Images'),
),
body: GridView.count(
crossAxisCount: 2,
children: List.generate(8, (int index) {
String imageUrl = 'https://picsum.photos/300?random&$index';
return GridTile(
child: InkWell(
onTap: () {
Navigator.push(
context,
MaterialPageRoute(builder: (BuildContext context) {
return ImageView(imageUrl: imageUrl);
}),
);
},
child: ImageHero(
imageUrl: imageUrl,
width: 300,
height: 300,
),
),
);
}),
),
);
}
}
Listing 11-11
Current route with ImageHero
```

代码清单 11-12 展示了`ImageView`组件。它也包含一个`ImageHero`组件，其标签与选中的图片相同。这是使动画生效的必要条件。

```dart
class ImageView extends StatelessWidget {
ImageView({Key key, this.imageUrl}) : super(key: key);
final String imageUrl;
@override
Widget build(BuildContext context) {
return Scaffold(
appBar: AppBar(
title: Text('Image'),
),
body: Row(
children: [
ImageHero(
width: 50,
height: 50,
imageUrl: imageUrl,
),
Expanded(
child: Text('Image Detail'),
),
],
),
);
}
}
Listing 11-12
New route with ImageHero
```

## 11.9 使用通用过渡动画

### 问题

你希望有一种简单的方式来使用不同类型的`Tween`对象进行动画。

### 解决方案

使用不同类型的过渡动画。

### 讨论

通常使用不同类型的`Tween`对象来动画化组件的不同方面。你可以使用`AnimatedWidget`或`AnimatedBuilder`类配合`Tween`对象工作。Flutter SDK 提供了几个过渡组件，使得某些动画更易于使用。

`ScaleTransition`组件用于动画化组件的缩放。要创建一个`ScaleTransition`对象，你需要提供一个`Animation<double>`对象作为缩放比例。`alignment`参数指定了缩放坐标原点相对于盒子的对齐方式。代码清单 11-13 展示了`ScaleTransition`的一个示例。

```dart
class ScaleBox extends StatelessWidget {
ScaleBox({Key key, Animation animation})
: _animation = CurveTween(curve: Curves.ease).animate(animation),
super(key: key);
final Animation _animation;
@override
Widget build(BuildContext context) {
return ScaleTransition(
scale: _animation,
alignment: Alignment.centerLeft,
child: Container(
height: 100,
decoration: BoxDecoration(color: Colors.red),
),
);
}
}
Listing 11-13
Example of ScaleTransition
```

另一个过渡组件的例子是`FadeTransition`组件，它用于动画化透明度。代码清单 11-14 展示了`FadeTransition`的一个示例。

```dart
class FadeBox extends StatelessWidget {
FadeBox({Key key, Animation animation})
: _animation = CurveTween(curve: Curves.ease).animate(animation),
super(key: key);
final Animation _animation;
@override
Widget build(BuildContext context) {
return FadeTransition(
opacity: _animation,
child: Container(
height: 100,
decoration: BoxDecoration(color: Colors.red),
),
);
}
}
Listing 11-14
Example of FadeTransition
```

## 11.10 创建物理仿真

### 问题

你希望使用物理仿真。

### 解决方案

使用`physics`库中的仿真。



### 讨论

`animation` 库中的动画分为线性动画和曲线动画。`physics` 库提供了物理模拟，包括弹簧、摩擦力和重力。`Simulation` 类是所有模拟的基类。模拟同样随时间变化。对于某个时间点，`x()` 方法返回位置，`dx()` 方法返回速度，`isDone()` 方法返回模拟是否结束。给定一个 `Simulation` 对象，可以使用 `AnimationController` 类的 `animateWith()` 方法来驱动该模拟的动画。

`SpringSimulation` 类表示附着在弹簧上的粒子的模拟。要创建 `SpringSimulation` 对象，可以提供表 11-5 中列出的参数。

**表 11-5**  
`SpringSimulation` 的参数

| 名称 | 类型 | 描述 |
| --- | --- | --- |
| `spring` | `SpringDescription` | 弹簧的描述。 |
| `start` | `double` | 起始距离。 |
| `end` | `double` | 结束距离。 |
| `velocity` | `double` | 初始速度。 |
| `tolerance` | `Tolerance` | 距离、持续时间和速度被视为相等的差异大小。 |

要创建 `SpringDescription` 对象，可以使用 `SpringDescription()` 构造函数，并传入参数来指定质量、刚度和阻尼系数。`SpringDescription.withDampingRatio()` 构造函数使用阻尼比代替阻尼系数。清单 11-15 展示了一个创建 `SpringSimulation` 对象的示例。

```
SpringSimulation _springSimulation = SpringSimulation(
SpringDescription.withDampingRatio(
mass: 1.0,
stiffness: 50,
ratio: 1.0,
),
0.0,
1.0,
1.0)
..tolerance = Tolerance(distance: 0.01, velocity: double.infinity);
```

**清单 11-15**  
弹簧模拟

使用弹簧模拟的一种更简单的方法是使用 `AnimationController` 类的 `fling()` 方法。该方法使用临界阻尼弹簧来驱动动画。

`GravitySimulation` 类表示遵循牛顿第二运动定律的粒子的模拟。表 11-6 显示了 `GravitySimulation` 构造函数的参数。

**表 11-6**  
`GravitySimulation` 的参数

| 名称 | 类型 | 描述 |
| --- | --- | --- |
| `acceleration` | `double` | 粒子的加速度。 |
| `distance` | `double` | 初始距离。 |
| `endDistance` | `double` | 模拟结束时的距离。 |
| `velocity` | `double` | 初始速度。 |

在清单 11-16 中，`SimulationController` 组件使用一个 `Simulation` 对象来驱动动画。

```
typedef BuilderFunc = Widget Function(BuildContext, Animation);
class SimulationController extends StatefulWidget {
SimulationController({Key key, this.simulation, this.builder})
: super(key: key);
final Simulation simulation;
final BuilderFunc builder;
@override
_SimulationControllerState createState() => _SimulationControllerState();
}
class _SimulationControllerState extends State
with SingleTickerProviderStateMixin {
AnimationController controller;
@override
void initState() {
super.initState();
controller = AnimationController(
vsync: this,
)..animateWith(widget.simulation);
}
@override
Widget build(BuildContext context) {
return widget.builder(context, controller.view);
}
@override
void dispose() {
controller.dispose();
super.dispose();
}
}
```

**清单 11-16**  
在动画中使用模拟

## 11.11 总结

本章涵盖与 Flutter 动画相关的实践方法。`AnimationController` 类用于控制动画。`Tween` 类的子类为不同类型的数据创建线性动画。`AnimatedWidget` 和 `AnimatedBuilder` 是使用动画的有用组件。在下一章中，我们将讨论 Flutter 中与原生平台的集成。

