# 11. 触摸手势

让用户通过按钮、切换开关或菜单来控制应用固然方便，但这些控件都会占用屏幕空间。为了消除对用户界面上额外对象的需求，你的应用还可以检测并响应触摸手势，从而允许直接操作屏幕上显示的项目。

iOS 应用能够检测并响应的不同类型触摸手势包括：

- **点按** – 指尖触摸屏幕并抬起。
- **空间点按** – 一个或多个指尖触摸屏幕并报告点按位置。
- **捏合** – 两个指尖并拢或分开。
- **旋转** – 两个指尖以圆形运动向左或向右旋转。
- **平移** – 指尖在屏幕上滑动拖动。
- **清扫** – 指尖在屏幕上向上、向下、向左或向右滑动并抬起。
- **长按** – 指尖触摸并按压屏幕。

你可以将触摸手势应用于任何视图，例如`Image`视图、`Text`视图，或者像`Rectangle`或`Ellipse`这样的形状。

## 检测点按手势

点按手势仅用于检测用户何时点击屏幕。默认情况下，点按手势识别一个手指的单次点按，但你可以定义两个或多个手指的多次点按。

要了解如何检测点按手势，请按照以下步骤操作：

1.  创建一个新的 SwiftUI iOS App 项目，并为其任意命名，例如“TapGesture”。
2.  在导航器面板中点击`ContentView`文件。
3.  在`struct ContentView: View`行下方添加以下`State`变量：

    ```
    @State var changeMe = false
    ```

4.  在 body 中添加一个`VStack`和一个`Rectangle`，如下所示：

    ```
    var body: some View {
        VStack {
            Rectangle()
                .frame(width: 175, height: 125)
                .foregroundColor(changeMe ? .red : .yellow)
                .onTapGesture {
                    changeMe.toggle()
                }
        }
    }
    ```

    这会创建一个填满整个屏幕的`Rectangle`。然后，`.frame`修饰符将其宽度限制为 175，高度限制为 125。`.foregroundColor`修饰符使用`changeMe`这个`State`变量来决定将`Rectangle`着色为红色还是黄色。

    `.onTapGesture`修饰符使`Rectangle`能够检测单次点按手势。当检测到点按手势时，它会将`changeMe`这个`State`变量的值从`true`切换为`false`（或从`false`切换为`true`）。整个`ContentView`文件应如下所示：

    ```
    import SwiftUI
    struct ContentView: View {
        @State var changeMe = false
        var body: some View {
            VStack {
                Rectangle()
                    .frame(width: 175, height: 125)
                    .foregroundColor(changeMe ? .red : .yellow)
                    .onTapGesture {
                        changeMe.toggle()
                    }
            }
        }
    }
    struct ContentView_Previews: PreviewProvider {
        static var previews: some View {
            ContentView()
        }
    }
    ```

5.  在画布面板中点击“实时”图标。
6.  点击`Rectangle`。注意，每次点击`Rectangle`时，`.onTapGesture`修饰符都会切换`changeMe`这个`State`变量的值，从而使`Rectangle`的颜色在红色和黄色之间交替变化。

默认情况下，`.onTapGesture`修饰符检测单次点按手势。如果你想检测多次点按，例如双击或三击，可以像这样定义`count:`参数：

```
.onTapGesture(count: 2)
```

值为 2 表示双击，而值为 3 则表示三击。



### 检测空间轻点手势

空间轻点手势与普通轻点手势类似，但它最常用于检测一次或多次轻点的位置。要检测轻点位置，你需要一个能存储 `CGPoint` 数据类型的 `State` 变量，如下所示：

```
@State var tapLocation: CGPoint = .zero
```

接着，你需要定义一个空间轻点手势来检测位置，如下所示：

```
var spatialTapGesture: some Gesture {
SpatialTapGesture()
.onEnded { event in
tapLocation = event.location
}
}
```

`event.location` 会检测轻点手势的位置，并将 `x` 和 `y` 数据存储在 `tapLocation` `State` 变量中，其中 `tapLocation.x` 包含距原点（对象的左上角）的水平距离，`tapLocation.y` 包含距原点的垂直距离。原点由附加了空间轻点手势的对象定义。

一旦你能检测到轻点位置，就可以根据用户点击的区域来做出响应。默认情况下，空间轻点手势只检测单次轻点，但你可以通过定义 `count` 参数来指定所需的点击次数，例如双击，如下所示：

```
var spatialTapGesture: some Gesture {
SpatialTapGesture(count: 2)
.onEnded { event in
tapLocation = event.location
}
}
```

要了解如何使用空间轻点手势，请遵循以下步骤：

1. 创建一个新的 SwiftUI iOS App 项目，并为其任意命名，例如 "SpatialTapGesture"。
2. 在导航器窗格中点击 `ContentView` 文件。
3. 在 `struct ContentView: View` 这行下方添加以下 `State` 变量和常量：

```
    @State var tapLocation: CGPoint = .zero
    let dimension: CGFloat = 360
```

4. 在 `State` 变量和常量下方添加一个空间轻点手势变量，如下所示：

```
    var spatialTapGesture: some Gesture {
    SpatialTapGesture()
    .onEnded { event in
    tapLocation = event.location
    }
    }
```

在本例中，空间轻点手势的名称为 `spatialTapGesture`，但你可以使用任何你想要的名称。你的空间轻点手势的名称稍后将用于将其附加到一个对象上。

5. 在 `body` 内部添加一个 `VStack`，以及一个 `Rectangle` 和两个 `Text` 视图，如下所示：

```
    VStack {
    Rectangle()
    .foregroundStyle(tapLocation.y > dimension / 2.0 ? Color.orange : Color.purple)
    .overlay(Text("Tap Me").font(.largeTitle).foregroundColor(tapLocation.x > dimension / 2.0 ? Color.black : Color.white))
    .frame(width: dimension, height: dimension)
    .gesture(spatialTapGesture)
    Text("Tap x location = \(tapLocation.x)")
    Text("Tap y location = \(tapLocation.y)")
    }
```

这会创建一个宽度和高度由 `dimension` 常量（设置为 360）定义的 `Rectangle`。`.foregroundStyle` 修饰符会根据用户在 `Rectangle` 上点击的位置来改变颜色。如果用户点击在 `Rectangle` 的下半部分（`tapLocation.y > dimension / 2.0`），那么 `Rectangle` 的颜色是橙色。否则，`Rectangle` 的颜色是紫色。

覆盖在 `Rectangle` 之上的是一个 `Text` 视图，如果用户点击（轻点）了 `Rectangle` 的右半部分（`tapLocation.x > dimension / 2.0`），文本的前景色将显示为黑色；如果用户点击（轻点）了 `Rectangle` 的左半部分，则显示为白色。

`Rectangle` 下方的两个 `Text` 视图仅显示用户点击（轻点）位置的 x 和 y 坐标，原点 (0,0) 是 `Rectangle` 的左上角。

整个 `ContentView` 文件应如下所示：

```
    import SwiftUI
    struct ContentView: View {
    @State var tapLocation: CGPoint = .zero
    let dimension: CGFloat = 360
    var spatialTapGesture: some Gesture {
    SpatialTapGesture()
    .onEnded { event in
    tapLocation = event.location
    }
    }
    var body: some View {
    VStack {
    Rectangle()
    .foregroundStyle(tapLocation.y > dimension / 2.0 ? Color.orange : Color.purple)
    .overlay(Text("Tap Me").font(.largeTitle).foregroundColor(tapLocation.x > dimension / 2.0 ? Color.black : Color.white))
    .frame(width: dimension, height: dimension)
    .gesture(spatialTapGesture)
    Text("Tap x location = \(tapLocation.x)")
    Text("Tap y location = \(tapLocation.y)")
    }
    }
    }
    struct ContentView_Previews: PreviewProvider {
    static var previews: some View {
    ContentView()
    }
    }
```

6. 在画布窗格中点击 Live 图标。
7. 依次点击 `Rectangle` 的左上角、右上角、右下角和左下角。注意，每次你在不同区域点击时，两个 `Text` 视图会标识出你点击的位置。你在 `Rectangle` 上点击的位置也决定了 `Rectangle` 和文本颜色如何变化。

尝试像这样向 `SpatialTapGesture` 添加一个 `count` 参数，并再次测试应用，确保在空间轻点手势被识别前需要双击：

```
SpatialTapGesture(count: 2)
```



### 检测长按手势

当用户用一个或多个手指尖按住屏幕一段时间且指尖没有大幅移动时，就会触发长按手势。要定义长按手势，可以修改以下属性：

- `minimumDuration` – 定义一个或多个指尖需要按住屏幕多长时间才能被识别为长按
- `maximumDistance` – 定义在长按手势失败前指尖可以移动的最大距离

最简单的`.onLongPressGesture`修饰器写法如下：

```
.onLongPressGesture {
//  要运行的代码
}
```

如果需要，可以像这样添加`minimumDuration:`和`maximumDistance:`参数：

```
.onLongPressGesture(minimumDuration: 3, maximumDistance: 2) {
//  要运行的代码
}
```

上述版本的`.onLongPressGesture`修饰器强制用户至少按住 3 秒。如果希望在用户按下时执行某些操作，可以使用`pressing:`参数，如下所示：

```
.onLongPressGesture(minimumDuration: 2, maximumDistance: 2, pressing: {stillPressed in
//  长按期间要运行的代码
}) {
//  检测到长按手势后要运行的代码
}
```

要了解如何检测长按手势，请按照以下步骤操作：

1. 创建一个新的 SwiftUI iOS App 项目，并为其任意命名，例如“LongPressGesture”。
2. 在导航面板中点击`ContentView`文件。
3. 在`struct ContentView: View`行下方添加以下 State 变量：

    ```
    @State var changeMe = false
    @State var message = ""
    ```

4. 在`VStack`内添加一个`Text`视图，如下所示：

    ```
    var body: some View {
    VStack {
    Text(message)
    }
    }
    ```

5. 在`Text`视图下方添加一个`Rectangle`：

    ```
    Rectangle()
    .frame(width: 175, height: 125)
    .foregroundColor(changeMe ? .red : .yellow)
    ```

6. 为`Rectangle`添加`.onLongPressGesture`修饰器，如下所示：

    ```
    .onLongPressGesture(minimumDuration: 2, maximumDistance: 2, pressing: {stillPressed in
    message = "正在进行长按：\(stillPressed)"
    }) {
    changeMe.toggle()
    }
    ```

    `pressing:`参数会在长按手势进行时显示`true`，长按手势完成后立即显示`false`。长按手势完成后，它会将`changeMe` State 变量从`false`切换为`true`（或从`true`切换为`false`）。

    整个`ContentView`文件应如下所示：

    ```
    import SwiftUI
    struct ContentView: View {
    @State var changeMe = false
    @State var message = ""
    var body: some View {
    VStack {
    Text(message)
    Rectangle()
    .frame(width: 175, height: 125)
    .foregroundColor(changeMe ? .red : .yellow)
    .onLongPressGesture(minimumDuration: 2, maximumDistance: 2, pressing: {stillPressed in
    message = "正在进行长按：\(stillPressed)"
    }) {
    changeMe.toggle()
    }
    }
    }
    }
    struct ContentView_Previews: PreviewProvider {
    static var previews: some View {
    ContentView()
    }
    }
    ```

7. 在画布面板中点击“Live”图标。
8. 将鼠标指针移到`Rectangle`上，按住鼠标左键模拟按下手势。注意`Text`视图会显示“正在进行长按：true”。
9. 继续按住鼠标左键，直到`Rectangle`改变颜色。这表明已达到`minimumDuration`的 2 秒阈值，长按手势已被识别。

## 检测缩放手势

当用户用两个指尖按住屏幕并使指尖相互远离或靠近时，就会触发缩放手势（也称为捏合手势）。用户通常希望放大图片或缩小以查看更多内容（例如地图的更大视图）时，会用到这种缩放手势。

由于缩放手势会改变视图的大小，你需要定义两个 State 变量来表示当前尺寸和最终尺寸，数据类型为`CGFloat`，如下所示：

```
@State private var tempValue: CGFloat = 0
@State private var finalValue: CGFloat = 1
```

要调整视图大小，缩放手势需要与要调整大小的视图上的`.scaleEffect`修饰器配合使用，例如：

```
Image(systemName: "star.fill")
.font(.system(size: 200))
.foregroundColor(.green)
.scaleEffect(finalValue + tempValue)
```

然后，你需要将要调整大小的视图附加`.gesture`修饰器，并使用缩放手势，如下所示：

```
.gesture(
)
```

在`.gesture`修饰器的括号内，就是定义缩放手势的地方。你需要检测缩放手势何时发生变化以及何时最终结束，如下所示：

```
.gesture(
MagnificationGesture()
.onChanged { amount in
//  要运行的代码
}
.onEnded { amount in
//  要运行的代码
}
)
```

`.onChanged`修饰器测量用户将两个指尖相互远离或靠近的距离。`.onEnded`修饰器测量用户通过将两个指尖抬离屏幕来结束缩放手势时两个指尖之间的距离。

要了解如何检测缩放手势，请按照以下步骤操作：

**图 11-1** 在画布面板中模拟双指按压手势

1. 创建一个新的 SwiftUI iOS App 项目，并为其任意命名，例如“MagnificationGesture”。
2. 在导航面板中点击`ContentView`文件。
3. 在`struct ContentView: View`行下方添加以下 State 变量：

    ```
    @State private var tempValue: CGFloat = 0
    @State private var finalValue: CGFloat = 1
    ```

4. 在 body 中添加一个包含`Image`的`VStack`，如下所示：

    ```
    var body: some View {
    VStack {
    Image(systemName: "star.fill")
    .font(.system(size: 200))
    .foregroundColor(.green)
    .scaleEffect(finalValue + tempValue)
    }
    }
    ```

    这将在`Image`视图中显示一颗星星，字体大小为 200，填充颜色为绿色。注意`.scaleEffect`修饰器将星形图像的大小定义为两个 State 变量的组合。大小为 1 表示图像的当前大小，较小的值会缩小图像，较大的值会放大图像。

5. 为`Image`添加一个`.gesture`修饰器，因为这是我们要使用缩放手势调整大小的对象，如下所示：

    ```
    var body: some View {
    VStack {
    Image(systemName: "star.fill")
    .font(.system(size: 200))
    .foregroundColor(.green)
    .scaleEffect(finalValue + tempValue)
    .gesture(
    )
    }
    }
    ```

    这使`Image`视图能够识别手势。现在我们只需定义要识别哪种手势。

6. 在`.gesture()`括号内添加`MagnificationGesture`，并定义`.onChanged`和`.onEnded`修饰器，如下所示：

    ```
    var body: some View {
    VStack {
    Image(systemName: "star.fill")
    .font(.system(size: 200))
    .foregroundColor(.green)
    .scaleEffect(finalValue + tempValue)
    .gesture(
    MagnificationGesture()
    .onChanged { amount in
    tempValue = amount - 1
    }
    .onEnded { amount in
    finalValue += tempValue
    tempValue = 0
    }
    )
    }
    }
    ```



`onChanged`修饰器测量两个指尖之间的距离，并减去 1 来计算`tempValue`状态变量的值。如果用户将两个指尖分开，`amount`的值将大于 1，因此减去 1 后会保留用于调整`Image`大小的额外量。

`onEnded`修饰器使用用户两个指尖最后定义的距离，并将该值存储在`finalValue`状态变量中，然后将`tempValue`状态变量清零。

整个`ContentView`文件应如下所示：

```swift
import SwiftUI
struct ContentView: View {
    @State private var tempValue: CGFloat = 0
    @State private var finalValue: CGFloat = 1
    var body: some View {
        VStack {
            Image(systemName: "star.fill")
                .font(.system(size: 200))
                .foregroundColor(.green)
                .scaleEffect(finalValue + tempValue)
                .gesture(
                    MagnificationGesture()
                        .onChanged { amount in
                            tempValue = amount - 1
                        }
                        .onEnded { amount in
                            finalValue += tempValue
                            tempValue = 0
                        }
                )
        }
    }
}
struct ContentView_Previews: PreviewProvider {
    static var previews: some View {
        ContentView()
    }
}
```

7.  点击画布面板中的 Live 图标。
8.  按住 Option 键，点击画布面板中显示的模拟 iOS 设备屏幕。按住 Option 键的同时点击鼠标左键，会显示两个灰色圆点，模拟两个指尖按压在屏幕上，如图 11-1 所示。
1.  按住 Option 键的同时拖动鼠标，模拟将两个指尖靠近或远离。注意，当您拖动鼠标时，星形图像会相应缩小或放大以匹配缩放手势。

## 检测旋转手势

旋转手势与缩放手势类似，因为它们都使用两个指尖。主要区别在于，旋转手势检测两个指尖顺时针或逆时针做圆周运动。

由于旋转手势会改变视图的角度，您需要定义一个`Double`数据类型的`State`变量来测量旋转角度，如下所示：

```swift
@State private var degree = 0.0
```

要旋转视图，旋转手势需要与您想要旋转的视图上的`rotationEffect`修饰器配合使用，例如：

```swift
Image(systemName: "star.fill")
    .font(.system(size: 200))
    .foregroundColor(.green)
    .rotationEffect(Angle.degrees(degree))
```

然后，您需要将`gesture`修饰器附加到您想要旋转的视图上，并使用旋转手势，如下所示：

```swift
.gesture(
)
```

在`gesture`修饰器的括号内，您需要定义旋转手势并检测其变化，如下所示：

```swift
.gesture(
    RotationGesture()
        .onChanged({ angle in
            degree = angle.degrees
        })
)
```

`onChanged`修饰器测量用户将两个指尖顺时针或逆时针旋转了多少。要了解如何检测旋转手势，请按照以下步骤操作：

1.  创建一个新的 SwiftUI iOS App 项目，并为其命名，例如 "RotationGesture"。
2.  在导航器窗格中点击 `ContentView` 文件。
3.  在 `struct ContentView: View` 一行下方，添加以下`State`变量：
4.  像这样在`body`中添加一个包含`Image`的`VStack`：

    ```swift
    var body: some View {
        VStack {
            Image(systemName: "star.fill")
                .font(.system(size: 200))
                .foregroundColor(.green)
                .rotationEffect(Angle.degrees(degree))
        }
    }
    ```

    这定义了一个`Image`视图，以字体大小 200 和绿色显示一颗星。然后它使用`rotationEffect`修饰器旋转`Image`。

5.  为`Image`添加一个`gesture`修饰器，因为我们希望使用旋转手势来旋转它，如下所示：

    ```swift
    var body: some View {
        VStack {
            Image(systemName: "star.fill")
                .font(.system(size: 200))
                .foregroundColor(.green)
                .rotationEffect(Angle.degrees(degree))
                .gesture(
                )
        }
    }
    ```

6.  在`.gesture()`括号内添加`RotationGesture`，并同时定义一个`onChanged`修饰器，如下所示：

    ```swift
    var body: some View {
        VStack {
            Image(systemName: "star.fill")
                .font(.system(size: 200))
                .foregroundColor(.green)
                .rotationEffect(Angle.degrees(degree))
                .gesture(
                    RotationGesture()
                        .onChanged({ angle in
                            degree = angle.degrees
                        })
                )
        }
    }
    ```

    `onChanged`修饰器测量用户在屏幕上旋转两个指尖手势的角度。整个`ContentView`文件应如下所示：

    ```swift
    import SwiftUI
    struct ContentView: View {
        @State private var degree = 0.0
        var body: some View {
            VStack {
                Image(systemName: "star.fill")
                    .font(.system(size: 200))
                    .foregroundColor(.green)
                    .rotationEffect(Angle.degrees(degree))
                    .gesture(
                        RotationGesture()
                            .onChanged({ angle in
                                degree = angle.degrees
                            })
                    )
            }
        }
    }
    struct ContentView_Previews: PreviewProvider {
        static var previews: some View {
            ContentView()
        }
    }
    ```

4.  点击画布面板中的 Live 图标。
5.  按住 Option 键，点击画布面板中显示的绿色星星内部。按住 Option 键的同时点击鼠标左键，会显示两个灰色圆点，模拟两个指尖按压在屏幕上（见图 11-1）。
6.  按住 Option 键的同时拖动鼠标，模拟两个指尖旋转。注意，当您拖动鼠标时，星形图像会相应旋转以匹配旋转手势。



### 检测拖拽手势

拖拽手势发生在用户按压并在屏幕上滑动指尖时。拖拽手势通常用于将屏幕上的某个项目移动到新位置。

由于拖拽手势会改变视图的位置，你需要定义一个用来测量位置的 `State` 变量，类型为 `CGPoint`，如下所示：

```
@State private var circlePosition = CGPoint(x: 50, y: 50)
```

要移动一个视图，拖拽手势需要与你要移动的视图上的 `.position` 修饰符配合使用，例如：

```
Circle()
.fill(Color.blue)
.frame(width: 100, height: 100)
.position(circlePosition)
```

然后，你需要使用拖拽手势将要移动的视图与 `.gesture` 修饰符关联起来，如下所示：

```
.gesture(
)
```

在 `.gesture` 修饰符的圆括号内，定义拖拽手势并检测其变化，如下所示：

```
.gesture(DragGesture()
.onChanged({ value in
// 要运行的代码
}))
```

`.onChanged` 修饰符测量用户在屏幕上滑动指尖的距离。要了解如何检测拖拽手势，请按照以下步骤操作：

1.  创建一个新的 SwiftUI iOS App 项目，并随意命名，例如 "DragGesture"。

2.  在导航器窗格中点击 `ContentView` 文件。

3.  在 `struct ContentView: View` 代码行下方添加以下 `State` 变量：

    ```
    @State private var circlePosition = CGPoint(x: 50, y: 50)
    @State private var circleLabel = "50, 50"
    ```

4.  在 `body` 中添加一个包含 `Text` 视图和 `Circle` 的 `VStack`，如下所示：

    ```
    var body: some View {
    VStack {
    Text(circleLabel)
    .padding()
    Circle()
    .fill(Color.blue)
    .frame(width: 100, height: 100)
    .opacity(0.8)
    .position(circlePosition)
    }
    }
    ```

    这段代码定义了一个 `Text` 视图来显示 `circleLabel` 状态变量的内容，并定义了一个蓝色的 `Circle`。这个 `Circle` 使用 `.position` 修饰符来配合拖拽手势。

5.  为 `Circle` 添加 `.gesture` 修饰符，因为这是我们想要通过拖拽手势移动的对象，如下所示：

    ```
    var body: some View {
    VStack {
    Text(circleLabel)
    .padding()
    Circle()
    .fill(Color.blue)
    .frame(width: 100, height: 100)
    .opacity(0.8)
    .position(circlePosition)
    .gesture(
    )
    }
    }
    ```

6.  在 `.gesture()` 的圆括号内添加 `DragGesture`，同时定义一个 `.onChanged` 修饰符，如下所示：

    ```
    var body: some View {
    VStack {
    Text(circleLabel)
    .padding()
    Circle()
    .fill(Color.blue)
    .frame(width: 100, height: 100)
    .opacity(0.8)
    .position(circlePosition)
    .gesture(DragGesture()
    .onChanged({ value in
    circlePosition = value.location
    circleLabel = "\(Int(value.location.x)), \(Int(value.location.y))"
    }))
    }
    }
    ```

    `.onChanged` 修饰符测量用户指尖在屏幕上的位置。然后它将 x 和 y 的位置值存储在 `circleLabel` 中，以便在 `Text` 视图中显示。整个 `ContentView` 文件应如下所示：

    ```
    import SwiftUI
    struct ContentView: View {
    @State private var circlePosition = CGPoint(x: 50, y: 50)
    @State private var circleLabel = "50, 50"
    var body: some View {
    VStack {
    Text(circleLabel)
    .padding()
    Circle()
    .fill(Color.blue)
    .frame(width: 100, height: 100)
    .opacity(0.8)
    .position(circlePosition)
    .gesture(DragGesture()
    .onChanged({ value in
    circlePosition = value.location
    circleLabel = "\(Int(value.location.x)), \(Int(value.location.y))"
    }))
    }
    }
    }
    struct ContentView_Previews: PreviewProvider {
    static var previews: some View {
    ContentView()
    }
    }
    ```

7.  在画布窗格中点击"实时"图标。

8.  将鼠标指针移到画布窗格中模拟 iOS 设备上的 `Circle` 上，然后拖动鼠标。注意，拖动鼠标时，`Text` 视图会不断更新 `Circle` 的 x 和 y 位置。

## 定义手势优先级与同时触发的手势

你可以向任何视图添加触摸手势。由于堆栈也是一种视图，这意味着你也可以向 `VStack`、`HStack` 和 `ZStack` 添加触摸手势。如果你向堆栈内部的某个视图添加一个手势，然后向堆栈本身添加第二个相同的（同类型）手势，哪个手势会被识别？

当一个手势修饰整个堆栈时，该堆栈内的每个视图都可以识别该手势。但是，如果该堆栈内部的某个视图有自己的手势修饰符，那么这个视图的手势修饰符将优先被识别。要了解其工作原理，请按照以下步骤操作：

1.  创建一个新的 SwiftUI iOS App 项目，并随意命名，例如 "TwoGestures"。

2.  在导航器窗格中点击 `ContentView` 文件。

3.  在 `struct ContentView: View` 代码行下方添加以下 `State` 变量：

    ```
    @State var message = ""
    ```

4.  在 `body` 中添加一个包含 `Text` 视图、`Circle` 和 `Spacer` 的 `VStack`，如下所示：

    ```
    var body: some View {
    VStack {
    Text(message)
    .padding()
    Circle()
    .frame(width: 125, height: 125)
    .foregroundColor(.blue)
    Spacer()
    }.background(Color.yellow)
    }
    ```

    这会创建一个位于顶部的 `Text` 视图和一个位于其下方的蓝色 `Circle`。然后 `Spacer` 推动 `VStack` 的边界向下延伸，并将其背景色设置为黄色以便于观察，如图 11-2 所示。

5.  为 `Circle` 添加一个 `.onTapGesture` 修饰符，如下所示：

    ```
    Circle()
    .frame(width: 125, height: 125)
    .foregroundColor(.blue)
    .onTapGesture {
    message = "Circle tapped"
    }
    ```

    当用户点击 `Circle` 时，`message` 状态变量将保存字符串 "Circle tapped"（圆圈被点击）。

6.  为整个 `VStack` 添加一个 `.onTapGesture` 修饰符，如下所示：

    ```
    var body: some View {
    VStack {
    Text(message)
    .padding()
    Circle()
    .frame(width: 125, height: 125)
    .foregroundColor(.blue)
    .onTapGesture {
    message = "Circle tapped"
    }
    Spacer()
    }.background(Color.yellow)
    .onTapGesture {
    message = "VStack tapped"
    }
    ```

    如果你点击 `Circle`，`Circle` 的 `.onTapGesture` 修饰符（显示 "Circle tapped"）将被识别。如果你点击 `VStack` 中的其他任何位置，`VStack` 的 `.onTapGesture` 修饰符（显示 "VStack tapped"）将被识别。整个 `ContentView` 文件应如下所示：

    ```
    import SwiftUI
    struct ContentView_Previews: PreviewProvider {
    static var previews: some View {
    ContentView()
    }
    }
    ```

7.  点击画布窗格中的"实时"图标，然后点击蓝色的 `Circle`。注意"Circle tapped"会出现在 `Text` 视图中。即使 `Circle` 位于 `VStack` 内部，`VStack` 的 `.onTapGesture` 修饰符也会被忽略。

8.  点击 `VStack` 的黄色背景。注意"VStack tapped"会出现在 `Text` 视图中。



### 定义高优先级手势

堆栈内视图上的手势总会优先于堆栈上的手势被识别。如果你希望堆栈的手势修饰符优先于堆栈内视图上的手势修饰符执行，则需要使用 `.highPriorityGesture` 修饰符。

这个 `.highPriorityGesture` 修饰符的作用就是明确哪个手势应具有更高优先级。在本例中，我们希望堆栈的手势修饰符先执行，因此需要将其包裹在 `.highPriorityGesture` 修饰符中，如下所示：

```
.highPriorityGesture(
    TapGesture()
        .onEnded { _ in
            message = "VStack tapped"
        })
```

请注意，`TapGesture` 使用 `.onEnded` 修饰符来检测点击手势何时结束，从而将 `"VStack tapped"` 存储到 `message` 这个 `State` 变量中。

要了解 `.highPriorityGesture` 修饰符的工作方式，请遵循以下步骤：

1.  创建一个新的 SwiftUI iOS App 项目，并为其任意命名，例如 `"HighPriorityGestures"`。
2.  在导航器面板中点击 `ContentView` 文件。
3.  在 `struct ContentView: View` 代码行下方添加以下 `State` 变量：
    ```
    @State var message = ""
    ```
4.  在 body 内部添加一个包含 `Text` 视图、`Circle` 和 `Spacer` 的 `VStack`，如下所示：
    ```
    var body: some View {
        VStack {
            Text(message)
                .padding()
            Circle()
                .frame(width: 125, height: 125)
                .foregroundColor(.blue)
            Spacer()
        }.background(Color.yellow)
    }
    ```
5.  为 `Circle` 添加一个 `.onTapGesture` 修饰符，如下所示：
    ```
    Circle()
        .frame(width: 125, height: 125)
        .foregroundColor(.blue)
        .onTapGesture {
            message = "Circle tapped"
        }
    ```
    通常，当用户点击 `Circle` 时，`message` `State` 变量将包含字符串 `"Circle tapped"`。
6.  为整个 `VStack` 添加一个 `.highPriorityGesture` 修饰符，如下所示：
    ```
    var body: some View {
        VStack {
            Text(message)
                .padding()
            Circle()
                .frame(width: 125, height: 125)
                .foregroundColor(.blue)
                .onTapGesture {
                    message = "Circle tapped"
                }
            Spacer()
        }.background(Color.yellow)
            .highPriorityGesture(
                TapGesture()
                    .onEnded { _ in
                        message = "VStack tapped"
                    })
    }
    ```
    如果你点击 `Circle`，`Circle` 的 `.onTapGesture` 修饰符（`"Circle tapped"`）通常会被识别。然而，`.highPriorityGesture` 修饰符定义了 `VStack` 上的 `TapGesture` 被优先识别。整个 `ContentView` 文件应如下所示：
    ```
    import SwiftUI
    struct ContentView: View {
        @State var message = ""
        var body: some View {
            VStack {
                Text(message)
                    .padding()
                Circle()
                    .frame(width: 125, height: 125)
                    .foregroundColor(.blue)
                    .onTapGesture {
                        message = "Circle tapped"
                    }
                Spacer()
            }.background(Color.yellow)
                .highPriorityGesture(
                    TapGesture()
                        .onEnded { _ in
                            message = "VStack tapped"
                        })
        }
    }
    struct ContentView_Previews: PreviewProvider {
        static var previews: some View {
            ContentView()
        }
    }
    ```
7.  在画布面板中点击“Live”图标，然后点击蓝色 Circle。注意，`Text` 视图中会显示 `"VStack tapped"`，这是因为 `VStack` 上的 `.highPriorityGesture` 修饰符覆盖了 `Circle` 的 `.onTapGesture` 修饰符。
8.  点击 `VStack` 的黄色背景。注意，`Text` 视图中仍然显示 `"VStack tapped"`。

### 定义同时手势

通常，附加到单个视图上的手势修饰符会优先于附加到堆栈上的任何手势修饰符执行。如果你使用 `.highPriorityGesture` 修饰符，则可以让堆栈的手势优先于附加到单个视图上的任何手势修饰符执行。但是，如果你希望附加到视图和附加到堆栈上的两个手势都执行，该怎么办呢？

这时你可以使用 `.simultaneousGesture` 修饰符来确保堆栈的手势修饰符与附加到单个视图上的手势修饰符同时执行。要定义同时执行的手势，请将通常会被忽略的手势包裹在 `.simultaneousGesture` 修饰符中，如下所示：

```
.simultaneousGesture(
    TapGesture()
        .onEnded { _ in
            message = "VStack tapped"
        })
```

要了解 `.simultaneousGesture` 修饰符的工作方式，请遵循以下步骤：

1.  创建一个新的 SwiftUI iOS App 项目，并为其任意命名，例如 `"SimultaneousGestures"`。
2.  在导航器面板中点击 `ContentView` 文件。
3.  在 `struct ContentView: View` 代码行下方添加以下 `State` 变量：
    ```
    @State var message = ""
    ```
4.  在 body 内部添加一个包含 `Text` 视图、`Circle` 和 `Spacer` 的 `VStack`，如下所示：
    ```
    var body: some View {
        VStack {
            Text(message)
                .padding()
            Circle()
                .frame(width: 125, height: 125)
                .foregroundColor(.blue)
            Spacer()
        }.background(Color.yellow)
    }
    ```
5.  为 `Circle` 添加一个 `.onTapGesture` 修饰符，如下所示：
    ```
    Circle()
        .frame(width: 125, height: 125)
        .foregroundColor(.blue)
        .onTapGesture {
            message += "Circle tapped"
        }
    ```
    请注意，当 `Circle` 识别到点击手势时，它会将 `"Circle tapped"` 追加（`+=`）到 `message` `State` 变量的当前值中。
6.  为整个 `VStack` 添加一个 `.simultaneousGesture` 修饰符，如下所示：
    ```
    var body: some View {
        VStack {
            Text(message)
                .padding()
            Circle()
                .frame(width: 125, height: 125)
                .foregroundColor(.blue)
                .onTapGesture {
                    message += "Circle tapped"
                }
            Spacer()
        }.background(Color.yellow)
            .simultaneousGesture(
                TapGesture()
                    .onEnded { _ in
                        message = "VStack tapped"
                    })
    }
    ```
    如果你点击 `Circle`，`Circle` 的 `.onTapGesture` 修饰符（`"Circle tapped"`）通常会被识别。然而，`.simultaneousGesture` 修饰符定义了 `VStack` 上的 `TapGesture` 被优先识别，然后再识别 `Circle` 上的手势修饰符。整个 `ContentView` 文件应如下所示：
    ```
    import SwiftUI
    struct ContentView: View {
        @State var message = ""
        var body: some View {
            VStack {
                Text(message)
                    .padding()
                Circle()
                    .frame(width: 125, height: 125)
                    .foregroundColor(.blue)
                    .onTapGesture {
                        message += "Circle tapped"
                    }
                Spacer()
            }.background(Color.yellow)
                .simultaneousGesture(
                    TapGesture()
                        .onEnded { _ in
                            message = "VStack tapped"
                        })
        }
    }
    struct ContentView_Previews: PreviewProvider {
        static var previews: some View {
            ContentView()
        }
    }
    ```
7.  在画布面板中点击“Live”图标，然后点击蓝色 Circle。注意，`Text` 视图中会显示 `"VStack tappedCircle tapped"`，这是因为 `VStack` 上的 `.simultaneousGesture` 修饰符使其在 `Circle` 的 `.onTapGesture` 修饰符之前执行。
8.  点击 `VStack` 的黄色背景。注意，`Text` 视图中仍然显示 `"VStack tapped"`。

## 总结

触摸手势无需在用户界面上放置按钮等对象即可向应用发送指令。手势通常可以用一个或多个指尖来操作，例如双击或三击。

在添加多个手势时，请记住：附加到单个视图上的手势会优先于附加到堆栈上的手势执行。如果你希望堆栈的手势修饰符先执行，请将其包裹在 `.highPriorityGesture` 修饰符中。如果你希望堆栈和单个视图的手势修饰符依次执行，请将堆栈修饰符包裹在 `.simultaneousGesture` 修饰符中。

通过让用户直接操作屏幕上的对象，为应用添加手势可以使用户界面更加简洁、直观。



