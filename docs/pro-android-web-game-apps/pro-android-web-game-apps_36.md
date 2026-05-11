# 事件处理与用户输入

如果在其他地方释放鼠标，画布将不会收到 `mouseup` 事件，而 `MouseInputHandler` 会仍然认为鼠标按钮处于按下状态。当您将光标移回时，画布会再次报告每次移动。幸运的是，一旦光标离开画布，重置标志位非常容易，如清单 5-22 所示。

**清单 5-22.** *当鼠标离开画布时重置按钮状态*
```
_p._attachDomListeners = function() {
    var el = this._element;
    el.addEventListener("mousedown", this._onDownDomEvent.bind(this), false);
    el.addEventListener("mouseup", this._onUpDomEvent.bind(this), false);
    el.addEventListener("mousemove", this._onMoveDomEvent.bind(this));
    el.addEventListener("mouseout", this._onMouseOut.bind(this));
};

_p._onMouseOut = function() {
    this._mouseDown = false;
};
```

但如果鼠标按钮没有在画布外释放呢？嗯，用户将不得不释放并再次按下按钮。但这是个小问题：当用户离开画布时，他会意识到自己不再与之交互。要恢复交互，典型用户会从头重复这个过程：将光标移动到画布，按下按钮，然后拖动。在实践中，这比“卡住”的 `_mouseDown` 标志要烦人得多。

当您完成这个类的实现后，可以立即测试新事件处理的工作方式！当然，该演示仅在带有鼠标的桌面设备上运行。触摸部分尚待构建。但请看清单 5-23 中的新代码是多么简洁！

**清单 5-23.** *自定义输入事件的测试*
```
function init() {
    canvas = document.getElementById("mainCanvas");
    ctx = canvas.getContext("2d");
    var canvasInputHandler = new MouseInputHandler(canvas);
    canvasInputHandler.on("up", function(e) {
        drawBullet(e.x, e.y);
    });
    resizeCanvas();
}

function drawBullet(x, y) {
    ctx.fillStyle = "lightblue";
    ctx.strokeStyle = "darkgray";
    ctx.lineWidth = 5;
    ctx.beginPath();
    ctx.arc(x, y, 15, 0, Math.PI*2, false);
    ctx.fill();
    ctx.stroke();
}
```

处理事件所需的代码已从 20 行减少到仅 4 行。此外，浏览器特定的代码已移至 API 内部。`MouseInputHandler` 的实现可在本章的源代码中找到，文件名为 `MouseInputHandler.js`。

最后，我们可以实现 `TouchInputHandler`，这是一个负责支持触摸的浏览器的类。

### 创建 TouchInputHandler

`TouchInputHandler` 解决了一个不同的问题。标准的浏览器 `touchend` 事件不包含任何关联坐标；`targetTouches` 只是空的。这有时很不方便。例如，在前面的清单中，我们使用了 `up` 事件来将标记放置在画布上。如果我们使用标准的 `touchend`，我们将不得不跟踪每一个其他事件！我们必须使用 `touchstart` 和 `touchmove` 来跟踪最后已知的坐标，并使用它们来绘制标记。这是核心游戏不应该关心的很多代码。让我们将其放入单独的类中。

其背后的想法与 `MouseInputHandler` 相同，但不是跟踪按钮的状态，而是跟踪交互的最后已知坐标。清单 5-24 中的代码展示了类的实现。完整代码可以在本书随附的其他示例中找到，文件名为 `TouchInputHandler.js`。这个类使用了与 `MouseInputHandler` 相同的理念：获取事件监听器的基础实现，并根据自身需求进行修改。但不是跟踪按钮状态，而是跟踪输入事件的最后已知坐标。

**清单 5-24.** *TouchInputHandler 必须跟踪输入的最后已知坐标，以便在 up 事件中报告它们*
```
function TouchInputHandler(element) {
    this._lastInteractionCoordinates = null;
    InputHandlerBase.call(this, element);
    this._attachDomListeners();
}

extend(TouchInputHandler, InputHandlerBase);

_p = TouchInputHandler.prototype;

_p._attachDomListeners = function() {
    var el = this._element;
```


```javascript
el.addEventListener("touchstart", this._onDownDomEvent.bind(this), false);
el.addEventListener("touchend", this._onUpDomEvent.bind(this), false);
el.addEventListener("touchmove", this._onMoveDomEvent.bind(this), false);
```

```javascript
_p._onDownDomEvent = function(e) {
    this._lastInteractionCoordinates = this._getInputCoordinates(e);
    InputHandlerBase.prototype._onDownDomEvent.call(this, e);
};
```

```javascript
_p._onUpDomEvent = function(e) {
    this.emit("up", {
        x: this._lastInteractionCoordinates.x,
        y: this._lastInteractionCoordinates.y,
        moved: this._moving,
        domEvent: e
    });
    this._stopEventIfRequired(e);
    this._lastInteractionCoordinates = null;
    this._moving = false;
};
```

```javascript
_p._onMoveDomEvent = function(e) {
    this._lastInteractionCoordinates = this._getInputCoordinates(e);
    InputHandlerBase.prototype._onMoveDomEvent.call(this, e);
}
```

现在我们已经有了两种输入模型对应的类。最后一步是根据浏览器的能力来选择使用哪一个。创建一个名为 `InputHandler.js` 的新文件，并添加下面这一行代码：

```javascript
var InputHandler = isTouchDevice() ? TouchInputHandler : MouseInputHandler;
```

这是一个在 JavaScript 中偶尔会用到的小技巧。根据环境的不同，`InputHandler` 要么是 `TouchInputHandler`，要么是 `MouseInputHandler`。这样，你就不必在核心游戏文件中纠结于选择正确的实现了。由于输入处理是一个独立的 API，我们将实现它的四个文件都放在一个名为 `input` 的独立文件夹中。

## 第 5 章：事件处理与用户输入

### 高级输入

构建输入层来向游戏核心逻辑隐藏浏览器 API，仅仅是拥有良好游戏控制的第一步。我们已经学会了如何处理“原子”输入动作（触摸、释放和移动），现在是时候看一些稍微高级一些的例子了。

#### 拖放

拖放是一种与为触摸屏设计的游戏进行交互的非常方便的方式。例如，在一个拥有大地图的游戏中，拖动地图是浏览世界的自然方式。此外，拖放还可以用于在英雄的仓库和背包之间移动物品，或者为孩子们构建拼图游戏。通过拖放移动拼图碎片是与它们交互的第二自然的方式（仅次于用手抓取）。这种输入方式用于我们下一个游戏实验——第 7 章中的等距游戏。

拖放非常容易实现。该方法包含以下三个步骤：

1. 当用户开始交互时，检查他是否选中了一个可拖动的组件。如果没有，则中止该过程，因为没有东西可以拖动。如果有很多可拖动组件，请标记当前处于活动状态的那个。我们称这一步为**拾取**。
2. 监听移动事件并相应地调整组件的位置。我们称这一步为**移动**。
3. 当用户完成交互时，完成拖动：执行拖放背后的游戏逻辑（例如，在背包中移动物品），然后取消标记“活动”组件。我们称这一步为**释放**。

让我们构建另一个演示来说明这个概念（参见清单 5-25）。我们将在屏幕上放置几个矩形，并允许用户拖动它们。所有这三个阶段都可以使用我们的 API 轻松实现（完整代码位于本章示例文件夹中的 `02.drag_and_drop.html` 文件中）。

**清单 5-25.** *实现拖放*

```javascript
// 拾取阶段
input.on("down", function(e) {
    rectangles.forEach(function(rect, index) {
        if (insideRectangle(e, rect))
            selectedIndex = index;
    });
});

// 移动阶段
input.on("move", function(e) {
    if (selectedIndex > -1) {
        rectangles[selectedIndex].x += e.deltaX;
        rectangles[selectedIndex].y += e.deltaY;
    }
});

// 释放阶段
input.on("up", function(e) {
    selectedIndex = -1;
});
```

移动阶段和释放阶段很简单；然而，拾取阶段有一些陷阱。通过坐标进行检查相当容易判断点


### 位于圆形或正方形内。  
多边形稍微复杂一些，但仍然可以实现。但如果场景中的物体形状更复杂呢？如果你想选取一张图像，其中包含不应被视为"材质"的透明区域呢？显然，有些情况仅使用简单的数学算法是无法实现的。针对此类情况，本章下一节将介绍一种"像素级精确"的选择方法。实现拖拽功能的完整源代码可在文件 `04.drag_and_drop.html` 中找到。

**注意：** 我们在本书中创建的游戏不需要像素级精确的选取。然而，这类输入是许多游戏开发者面临的常见问题。因此，我决定专门用一节来讨论这个问题。例如，拼图类游戏如果没有这种技术几乎无法正确实现。

#### 像素级精确选取与图像蒙版

用户能够选择的最小单位是一个像素。与 Canvas API 不同，浏览器事件不处理像素的分数部分：无法选中"像素的左侧"或"像素的底部角落"。选取复杂形状的任务可以用略微不同的方式表述：确定给定像素属于场景中的哪个图形。显然，如果我们能做到这一点，就能以完美的精度选取任何图形。事实证明，只需相对较少的努力就能实现这样的算法。

每个像素都有一种颜色。假设我们制作了一个游戏，其中每个形状都有一种独特的纯色。例如，三角形是蓝色，正方形是红色，圆形是绿色。当用户点击屏幕时，我们可以通过获取像素的颜色并将其映射到图形，立即知道用户手指下的是哪个图形。这可以通过 Canvas API 轻松实现。首先，我们需要获取一个像素的颜色；如果用户在坐标 `(51, 73)` 处点击了屏幕，获取该像素值的代码如下所示：

```javascript
var imageData = ctx.getImageData(51, 73, 1, 1).data;
```

`imageData` 是一个包含四个元素的数组：颜色的红色值、绿色值、蓝色值和 alpha 值。有了这个原始像素数据，我们可以判断选中了哪个图像：

```javascript
if (imageData[2] == 255) {
    console.log("蓝色三角形");
} else if (imageData[0] == 255) {
    console.log("红色正方形");
} else if (imageData[1] == 255) {
    console.log("绿色圆形");
} else {
    console.log("空白区域");
}
```

但我们制作的大多数游戏都包含各种形状和多种颜色。大多数精灵都有多种颜色！这是否意味着这种技术毫无用处？绝非如此。即使图像和形状有不同的颜色，我们也可以将它们绘制成被纯色"覆盖"的样子。在进一步讨论之前，让我们先看看这意味着什么。图 5-6 展示了这一概念。我们有两张卡通房屋图像，每张都有一个调色板（左图）。为了让我们的技术生效，我们必须重新绘制房屋图像，为每张赋予一种独特的纯色（右图）。

**图 5-6.** *原始图像（左）和蒙版（右）*

这种技术被称为蒙版，而图形的纯色轮廓通常被称为蒙版。稍后我们将看到如何创建蒙版；现在，我们先假设这是可行的。

每个像素都有一种颜色。颜色由一个 32 位数字表示，分为四个 8 位分量：红色、绿色、蓝色和 alpha。我们可以轻松地将颜色转换为其整数值，反之亦然。如果我们为场景中的每个图形赋予一个唯一的 ID，然后将这个 ID 编码为颜色，会发生什么？没错。我们就能为场景中的每个图形获得唯一的颜色。如果我们用这种独特的颜色绘制每个图形的蒙版呢？那么像素的颜色就代表了占据该像素的图形的 ID！当用户点击屏幕时，我们…



