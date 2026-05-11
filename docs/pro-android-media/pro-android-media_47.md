# 第 4 章：图形与触摸事件

到目前为止，我们已经探讨了如何捕获和处理照片图像。当然，这并非 Android 在图像方面的全部能力。在本章中，我们将稍作调整，探讨如何通过在 `Canvas` 上绘制图形元素和文本元素来创建图像。与此相关，我们将探索 Android 为处理触摸屏提供的功能。特别地，我们将构建一个触摸屏绘图应用。

## Canvas 绘图

从上一章我们知道，可以在 `Canvas` 对象上绘制 `Bitmap` 图像。不过，Android 中的 `Canvas` 类提供的功能远不止于此。它还支持矢量绘图和文本绘制。我们可以像上一章那样，将 `Canvas` 对象与 `Bitmap` 对象结合使用，也可以将其与 `View` 结合使用。首先，我们将使用 `Canvas` 来创建或修改一个 `Bitmap`，然后进一步使用 `View` 进行一些非常简单的基于 `Canvas` 的动画。

### Bitmap 创建

按照之前使用过的方式，我们可以从一个可变的 `Bitmap` 构建一个 `Canvas` 对象。要创建一个可变的 `Bitmap`（即可以被修改的 `Bitmap`），我们必须提供宽度、高度和配置。该配置通常是 `Bitmap.Config` 类中定义的一个常量值。

下面的代码片段创建了一个可变 `Bitmap` 对象，其宽度和高度设置为屏幕的尺寸，配置使用 `Bitmap.Config.ARGB_8888` 常量。

```java
Bitmap bitmap = Bitmap.createBitmap((int)
getWindowManager().getDefaultDisplay().getWidth(), (int)
getWindowManager().getDefaultDisplay().getHeight(), Bitmap.Config.ARGB_8888);
```

### Bitmap 配置

`ARGB_8888` 配置常量定义了位图的创建方式：每个颜色通道使用 8 位内存，“A”或 alpha 通道 8 位，“R”或红色通道 8 位，“G”或绿色通道 8 位，“B”或蓝色通道 8 位。这意味着图像中的每个像素的每个颜色（包括 alpha）都由 0 到 255 之间的数值表示。这意味着每个像素由 32 位表示，可表示的不同颜色总数超过 1670 万。

还有其他配置常量可用，它们使用更少的内存，因此处理速度更快，但会影响图像质量。

- `ALPHA_8`：用于充当 alpha 遮罩的 `Bitmap`，仅 alpha 通道 8 位。无其他颜色。
- `ARGB_4444`：每个颜色通道（包括 alpha）4 位。允许 4096 种独特颜色，16 级 alpha 值。图 4–1 使用彩虹渐变说明了此设置。
- `ARGB_8888`：每个颜色通道（包括 alpha）8 位。允许约 1670 万种独特颜色，256 级 alpha 值。图 4–2 使用彩虹渐变说明了此设置。
- `RGB_565`：红色通道 5 位，绿色通道 6 位，蓝色通道 5 位（无 alpha）。允许 65,535 种不同颜色。如图 4–3 所示，此设置的质量几乎与 `ARGB_8888` 一样高，但占用的内存空间小得多。

**图 4–1.** *在 `ARGB_4444` 模式下的 `Bitmap` 上显示的彩虹渐变；请注意深蓝到浅蓝以及黄色到橙色部分的色带。`ARGB_4444` 无法表示使这些过渡平滑所需的颜色。*

**图 4–2.** *在 `ARGB_8888` 模式下的 `Bitmap` 上显示的彩虹渐变*

**图 4–3.** *在 `RGB_565` 模式下的 `Bitmap` 上显示的彩虹渐变*

### 创建 Canvas

现在我们已经有了要绘制的 `Bitmap` 图像，接下来需要创建用于实际绘图的 `Canvas` 对象。为此，我们只需将新的 `Bitmap` 对象传入 `Canvas` 的构造方法即可。

```java
Bitmap bitmap = Bitmap.createBitmap((int)
getWindowManager().getDefaultDisplay().getWidth(), (int)
getWindowManager().getDefaultDisplay().getHeight(), Bitmap.Config.ARGB_8888);
Canvas canvas = new Canvas(bitmap);
```

### 使用 Paint

在进行任何绘图之前，我们需要构建一个 `Paint` 对象。`Paint` 对象允许我们定义绘制时使用的颜色、描边宽度以及描边样式。因此，我们可以将 `Paint` 视为颜料和画笔的结合体。

```java
Paint paint = new Paint();
paint.setColor(Color.GREEN);
paint.setStyle(Paint.Style.STROKE);
paint.setStrokeWidth(10);
```

上述代码片段创建了一个 `Paint` 对象，将其颜色设置为绿色，定义我们想要绘制形状的轮廓而不是填充它们，并将描边宽度设置为 10 像素。

我们来逐一检查这些方法。

#### 颜色

通过 `Paint` 对象的 `setColor` 方法，我们可以传入一个 `Color` 对象。`Color` 类将一系列颜色定义为 32 位整数的常量：

- `Color.BLACK`
- `Color.BLUE`
- `Color.RED`

完整列表可以参阅 `Color` 类的在线参考文档，网址为 [`developer.android.com/reference/android/graphics/Color.html`](http://developer.android.com/reference/android/graphics/Color.html)。

```java
Paint paint = new Paint();
paint.setColor(Color.GREEN);
```

我们也可以通过调用静态方法 `Color.argb` 来构造特定颜色，传入 alpha、红色、绿色和蓝色的值（介于 0 和 255 之间）。此方法返回一个代表该颜色的 32 位整数，然后我们将其传递给 `setColor`。

```java
Paint paint = new Paint();
int myColor = Color.argb(255,128,64,32);
paint.setColor(myColor);
```

实际上，如果我们要定义精确的值，可以完全跳过颜色创建步骤：

```java
Paint paint = new Paint();
paint.setARGB(255,128,64,32);
```

#### 样式

通过 `setStyle` 方法定义绘画样式时，我们确定绘制的形状是填充还是仅描边。可能的样式在 `Paint.Style` 类中定义为常量。

- `Paint.Style.STROKE`：仅绘制形状的轮廓
- `Paint.Style.FILL`：仅填充形状
- `Paint.Style.FILL_AND_STROKE`：填充并绘制形状的轮廓

#### 描边宽度

最后，我们可以使用 `Paint` 对象的 `setStrokeWidth` 方法来指定描边（用于勾勒形状轮廓）的宽度。设置值为 0 仍然会产生 1 像素的描边。要完全移除描边，应使用 `setStyle` 方法并传入 `Paint.Style.FILL`。

### 绘制形状

`Canvas` 类定义了几个绘图方法。我们来介绍其中几个。

#### 点

最简单的是绘制一个点。要绘制一个点，我们使用 `Canvas` 对象的 `drawPoint` 方法，传入 x 和 y 坐标以及一个 `Paint` 对象。

```java
canvas.drawPoint(199,201,paint);
```

绘制点的大小取决于 `Paint` 对象的描边宽度。以下代码将渲染出如图 4–4 所示的效果。

```java
Paint paint = new Paint();
paint.setColor(Color.GREEN);
paint.setStrokeWidth(100);
canvas.drawPoint(199,201,paint);
```



