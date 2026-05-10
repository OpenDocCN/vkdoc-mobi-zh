# 第 13 章：使用触摸与手势事件

`wrap.addEventListener("bookPageMovedRight", function(e) {

color -= 20; // 新的活动颜色

e.pages.left.style.backgroundColor = makeColor(color - 20);

}, false);`

我们所做的只是简单地对`color`变量进行递增或递减，并根据方向适当改变对面节点的颜色。监听器需要在实例化`PageView`对象之前添加，否则`PageChanged`事件会在创建监听器之前被发送出去。该事件在页面视图初始化完成后由构造函数发送。

### 使用预计算手势

为了让经典 iPhone 手势的解析更简便，苹果添加了封装`TouchEvent`的更高层级事件，让你能只专注于这些手势。

使用`GestureEvent`时，没有`TouchList`数据，只有与旋转和缩放相关的信息（轴心和捏合手势）。

**注意：** 旋转和缩放数据也可通过`TouchEvent`对象获取。然而，手势事件让你只专注于这些信息，而触摸事件由于`touchmove`事件的存在会持续发送更新，即使实际上并没有进行手势操作。因此你可以在两者之间选择，要知道`GestureEvent`对资源消耗更少。

`GestureEvent`对象的`scale`和`rotation`属性允许你通过`gesturestart`访问它们各自手势的状态。当`touchstart`事件发送时，它们初始设置为`1`（缩放）和`0`（旋转），然后随着用户在屏幕上移动手指而变化。

**注意：** 在 iOS 3.0 中，如果`gestureend`事件紧跟在`gesturestart`事件之后发生，则`scale`和`rotation`的值会不准确。在这种情况下，你会收到`scale`为`0`且旋转角度错误的值。如果你想使用`gestureend`，务必检查返回值是否一致；例如，你可以测试是否发送了`gesturechange`事件。

这些手势需要使用两根手指。因此，当第二根手指接触屏幕表面时，`gesturestart`事件恰好在`touchstart`事件之前发送。手势事件总是在关联的触摸事件之前发送。因此当用户松开两根手指中的一根时同理：`gestureend`事件在`touchend`事件之前发送。同样，`gesturechange`在`touchmove`之前发送。

以下示例说明了这些事件的使用：

```html
<!DOCTYPE html>
<html>
<head>
<title>手势演示</title>
<meta name="viewport" content="width=device-width;
initial-scale=1.0; maximum-scale=1.0; user-scalable=0;">
<style>
body { margin-top: 50% }
div {
margin:-125px auto 0;
text-align: center;
color: white;
font: bold 20px/250px sans-serif;
width: 250px;
background: rgba(0,0,255,0.5);
border: solid 1px blue;
-webkit-border-radius: 32px;
}
</style>
</head>
<body>
<div>方形</div>
<script>
var div = document.getElementsByTagName("div")[0];
div.addEventListener("gesturestart", startHandler, false);
div.addEventListener("gesturechange", changeHandler, false);
function startHandler(event) {
if (event.target != event.currentTarget) {
return;
}
var computed = window.getComputedStyle(event.target);
event.target._originalMatrix =
new WebKitCSSMatrix(computed.webkitTransform);
}
function changeHandler(event) {
if (event.target != event.currentTarget) {
return;
}
event.target.style.webkitTransform = event.target._originalMatrix
.scale(event.scale).rotate(event.rotation);
event.preventDefault();
}
</script>
</body>
</html>
```

不出所料，我们首先添加适当的处理程序来捕获事件。由于`<div>`元素包含一个文本节点，当手指经过它时，同一个手势变化会触发两个事件：一个针对主目标，另一个针对`<div>`的内容。为了避免重复应用相同的变换，我们过滤目标，只关注主目标。

为了确保变换是基于对象当前状态应用的，`startHandler()`函数会收集目标的计算样式，并将其存储在`_originalMatrix`属性中，该属性是动态添加到目标节点上的。

由此，当发送`gesturechange`事件时，可以使用`CSSMatrix`对象的`scale()`方法计算新矩阵，并使用`rotate()`应用旋转。因为这些方法不会改变原始矩阵，所以每个新的`gesturechange`事件都会对缩放和旋转值进行正确的变换，这些值会随着用户在一个手势会话中手指的移动而变化。

最后，由于该事件可以被取消，为了确保用户执行手势时视口不会移动，我们对事件使用了`preventDefault()`。

### 创建你自己的手势

使用手势事件功能丰富，且让解析经典的 iOS 手势变得容易得多，因为要应用的值已经计算好了。然而，这也有局限性，因为只有两个预定义的手势可用。

一旦你想要独立定义每个手势，手势解析很快就会变成一件麻烦事。流行的滑动删除手势处理起来相当简单，因为你只需要记录`touchstart`和`touchend`事件之间手指的位置，并检查这些点之间的连线大致是水平的。你甚至可以检查移动的方向。然而，尝试处理更复杂的动作，比如画字母或图形，则完全是另一回事了。

#### 一个代码，多种笔画

我们将构建一个系统，该系统只需一段代码即可识别任何笔画。如果这适用于你的 Web 应用，可以使其使用效率大大提高。该实现系统的灵感来源于`LibStroke`或`Xstroke`（在 Linux 系统上使用 C/C++）等库，以及曾经在 PalmOS 上使用的 Graffiti 字符识别应用（图 13-3），该应用最近已移植到 Android 设备。请注意，iPhone 已经有一个类似的系统来书写汉字。

**图 13-3.** PalmOS 上的 Graffiti 帮助屏幕和 iPhone 识别界面

iPhone 上使用的动作通常不会超过一根手指，因为其他动作通常已由手势事件解析，如前所述。因此，我们将使用触摸事件，仅考虑第一根手指。脚本首先会记录手指经过的所有点，然后尝试解析这个序列。

解析过程将通过将动作投影到一个编号为 1 到 9 的 3x3 块状网格上进行，这将让我们提取出与特定形状关联的签名。如图 13-4 所示，该网格没有固定尺寸，其尺寸将由动作的幅度决定；边界框会随着新点的添加而逐步调整。

**图 13-4.** 字母 C 和 E 分别生成签名 3214789 和 321454789

为了提高精度，有必要调整某些形状。实际上，如果某个形状在某个方向上明显更大，解析可能不精确。在这种情况下，网格将通过缩放最小的跨度来调整，以居中绘制（图 13-5）。

**图 13-5.** 一个高度远大于宽度的手势需要调整

此外，某些字符——例如加号——很可能会在某个或某些阶段存在抬笔动作。这就是为什么我们会在多个连续序列发生在特定时间长度内时将其存储起来，并累加它们以返回关联的签名。

### 边界框对象



边界框是一个简单的矩形，其构建基于以下定义：

```javascript
var Rect = function() {
  var i = Infinity;
  this.coords = new WebKitPoint(+i, +i);
  this.extent = new WebKitPoint(-i, -i);
}
```

```javascript
Rect.prototype.adjust = function(x, y) {
  if (x < this.coords.x) {
    this.coords.x = x;
  }
  if (x > this.extent.x) {
    this.extent.x = x;
  }
  if (y < this.coords.y) {
    this.coords.y = y;
  }
  if (y > this.extent.y) {
    this.extent.y = y;
  }
}
```

```javascript
Rect.prototype.fit = function() {
  var w = (this.extent.x - this.coords.x + 1);
  var h = (this.extent.y - this.coords.y + 1);
  if ((w / h) > 4 || (h / w) > 4) {
    var ax = w < h ? w : 0;
    var ay = w < h ? 0 : h;
    this.coords.x -= ax;
    this.extent.x += ax;
    this.coords.y -= ay;
    this.extent.y += ay;
  }
}
```

为便于阅读，我们使用了 `WebKitPoint` 对象，它代表一个具有 `x` 和 `y` 坐标的点。当手指划过屏幕时，随着坐标被传入，`adjust()` 方法会相应地调整边界框。最后，如果边界框在某个方向上大至少四倍，`fit()` 方法会对其进行调整。

## 第十三章：使用触摸与手势事件

### 记录用户笔画

笔画被记录下来，签名由 `Recognizer` 对象生成。

```javascript
var Recognizer = function(element, interpreter) {
  this.element = element;
  this.element.addEventListener("touchstart", this, false);
  this.interpreter = interpreter;
  this.strokes = [];
  this.autoEndTimer = null;
  this.strokeSource = null;
}
```

构造函数将捕获触摸的对象作为其第一个参数。它可以是一个具有明确尺寸的 `HTML` 元素，也可以是 `document` 对象，后者允许你捕获设备屏幕上的所有事件。第二个参数是一个用于解释签名的函数。

用于确定最终签名的连续移动轨迹会存储在 `strokes` 数组中。为了结束图形并判断一个笔划是当前序列的一部分还是开启新序列，我们使用了一个定时器。这个定时器的标识符存储在 `autoEndTimer` 中，以便对其进行控制。

```javascript
Recognizer.prototype.handleEvent = function(event) {
  var x = event.changedTouches[0].pageX;
  var y = event.changedTouches[0].pageY;
  var t = event.type;
  if (event.touches.length != 1) {
    t = "touchend";
  }
  switch (t) {
    case "touchstart":
      this.saveSource(event.touches[0].target);
      this.startStroke();
      this.savePoint(x, y);
      this.doAutoEnd();
      break;
    case "touchmove":
      this.savePoint(x, y);
      this.doAutoEnd();
      break;
    case "touchend":
      this.endStroke();
      this.savePoint(x, y);
      this.doAutoEnd();
      break;
  }
  event.preventDefault();
}
```

### 第十三章：使用触摸与手势事件

```javascript
Recognizer.prototype.saveSource = function(node) {
  if (!this.strokeSource) {
    if (node.nodeType == node.TEXT_NODE) {
      node = node.parentNode;
    }
    this.strokeSource = node;
  }
}
```

```javascript
Recognizer.prototype.startStroke = function() {
  this.strokes.push({
    points: [],
    bound : new Rect()
  });
  this.element.addEventListener("touchmove", this, false);
  this.element.addEventListener("touchend", this, false);
}
```

记录从 `touchstart` 事件开始。仅当此事件被发送时，我们才注册新的监听器以启动一个序列，这可以防止记录元素外部的初始点。

一个新的对象被添加到 `strokes` 数组中，用于存储所有点及其边界框。随着点的添加，边界框会逐步调整大小，这避免了在处理签名时进行循环。对于每个事件，至少会保存一个点，并且 `autoEndTimer` 定时器会被重置。

```javascript
Recognizer.prototype.savePoint = function(x, y) {
  var stroke = this.strokes[this.strokes.length - 1];
  stroke.points.push(new WebKitPoint(x, y));
  stroke.bound.adjust(x, y);
}
```

```javascript
Recognizer.prototype.doAutoEnd = function() {
  if (this.autoEndTimer) {
    window.clearTimeout(this.autoEndTimer);
  }
  var that = this;
  this.autoEndTimer = window.setTimeout(function() {
    that.endStroke();
    that.finalizeStrokes();
  }, 500);
}
```



