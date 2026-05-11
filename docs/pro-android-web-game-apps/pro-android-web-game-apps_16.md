# 格式排版后的内容

最近的颜色停止点（坐标为 0 的点）是黄色。最后两行创建了阴影效果。由于最后一次颜色变化的区域非常小（仅占渐变区域的 5%），用户会看到一条细边框，看起来更像阴影。请查看图 2-23 中的图像。普通的描边效果并不那么理想，因为描边在路径的每个线段上都具有相同的宽度，而渐变在“受光”侧略薄，在“阴影”侧略厚。

**图 2-23.** *使用径向渐变模拟光照和阴影效果*
现在我们可以修改绘制棋子的代码，使棋子看起来具有光泽。该技术类似于清单 2-11 中描述的技术，不同之处在于渐变的内圆和外圆的位置取决于棋子本身的位置。代码看起来稍微复杂一些（见清单 2-12）。

**清单 2-12.** *使用渐变填充渲染棋子*

```
// data 变量是存储棋盘状态的数组，如清单 2-6 所示
for (var i = 0; i < data.length; i++) {
  for (var j = 0; j < data[i].length; j++) {
    var value = data[i][j];
    if (!value)
      continue;
    var color;
    switch (value) {
      case 1:
        color = "red";
        break;
      case 2:
        color = "green";
        break;
    }
    var x = (j + 0.5)*cellSize;
    var y = (i + 0.5)*cellSize;
    // 棋子半径
    var radius = cellSize*0.4;
    // 渐变中心
    var gradientX = x + cellSize*0.1;
    var gradientY = y - cellSize*0.1;
    var gradient = ctx.createRadialGradient(
      gradientX, gradientY, cellSize*0.1, // 内圆（高光）
      gradientX, gradientY, radius*1.2); // 外圆
    gradient.addColorStop(0, "yellow"); // “光照”的颜色
    gradient.addColorStop(1, color); // 棋子的颜色
    ctx.fillStyle = gradient;
    ctx.beginPath();
    ctx.arc(x, y, cellSize/2 - 5, 0, 2*Math.PI, false);
    ctx.fill();
  }
}
```

如您所见，径向渐变使用了两种颜色：黄色用于高光效果，红色或绿色用于棋子的主色。再次注意，我们没有硬编码任何坐标；它们都根据棋子的位置和单元格大小进行计算。这种方法允许在同一代码中适配各种屏幕尺寸。只要`cellSize`保存了正确的值，我们就不关心实际的屏幕尺寸。结果是值得努力的。请查看图 2-24，它展示了应用径向渐变后棋子的效果。

`![](img/index-78_1.jpg)`

`![](img/index-78_2.jpg)`

**图 2-24.** *对棋子应用径向渐变填充*

#### 图案

图案是最后一种描边和填充类型。图案不处理颜色。图案获取一张图像，并用它覆盖图形的空间，就像厨房地板上的瓷砖一样。图案的工作方式如图 2-25 所示。

**图 2-25.** *使用图案作为填充*

要创建图案，您需要一张图像——JPEG、PNG 或任何浏览器可以处理的其他网页兼容格式。我们的场景只在页面加载后重绘一次。这意味着在开始绘制任何内容之前，我们需要一张完全加载好的图像。

问题在于浏览器以异步方式加载图像；除非您专门跟踪，否则永远无法确定它是否已在内存中。幸运的是，实现这个逻辑并不难；我们只需要对初始 HTML 框架进行一些小的调整。首先，我们想从服务器下载一个漂亮的 Android Logo 图像（见清单 2-13）。

***清单 2-13.** 预加载图像*

```
var logo = new Image();
logo.onload = function() {
  // 一旦图像加载完成，浏览器将调用此函数
};
logo.onerror = function() {
  // 始终添加错误处理代码，你永远不知道它何时能为你节省调试时间
  alert("Could not load the image!");
};
logo.src = "img/logo.png";
```

当您为图像对象分配`src`参数时，浏览器开始加载图像。



从给定路径加载图像。我们的`onload`代码将在加载完成后触发常规的重绘流程。这样，我们就能保证当上下文方法执行时，图像是可用的。

**注意：** 在真实游戏中，创建一个`ImageManager`通常很有用，这是一个单独的类，可以处理多个图像并将所有实现细节从游戏逻辑中隐藏起来。此外，如果你有大量图像需要预加载，你将需要向用户显示某种进度条，让他知道你的游戏没有卡住。就我们的目的而言，清单 2-13 足以完成任务，但我们将在第 4 章回到这个主题，并制作一个更好的图像加载代码版本。现在，让我们回到图案。

图案比渐变更容易。你可以使用`createPattern()`方法创建图案。它接受两个参数：一个图像和一个重复策略，该策略告诉上下文如果图像小于要填充的区域应该怎么做。它的工作方式与 CSS 属性`background-repeat`完全相同，并接受以下相同的选项集：

- `repeat`: 在两个方向上重复图像（默认）
- `repeat-x`: 仅在水平方向重复
- `repeat-y`: 仅在垂直方向重复
- `no-repeat`: 不重复图像

清单 2-14 是图案的示例。为了让代码更有趣和更简洁，我将图像加载代码包装成一个单独的函数，该函数在加载完成后执行回调。这里的`logo`变量是全局的；在生产环境中，保留这些“神奇全局变量”是一个非常糟糕的主意。你迟早会发现它们难以管理和记忆。但对于这个例子来说，这是可以的。

**清单 2-14\. 使用图案**

```
var logo;

function init() {
    loadImage("img/logo.png", function(loadedImg) {
        logo = loadedImg;
        drawScene();
    });
}

function loadImage(imageUrl, callback) {
    var image = new Image();
    image.onload = function() {
        callback(image);
    };
    image.onerror = function() {
        alert("Could not load the image!");
    };
    image.src = imageUrl;
}

function drawScene() {
    var canvas = document.getElementById("mainCanvas");
    var ctx = canvas.getContext('2d');
    var pattern = ctx.createPattern(logo, "repeat");
    ctx.fillStyle = pattern;
    ctx.lineWidth = 10;
    ctx.fillRect(0, 0, 800, 600);
}
```

粗体行演示了如何创建图案并将其用作填充。让我们再次回顾这段代码，因为它的结构与最初的 HTML5 骨架相比发生了变化。现在我们有了三个方法：`init()`、`loadImage()`和`drawScene()`。第一个方法在页面加载完成后立即执行。它是应用程序的入口点。它开始加载稍后将用作图案的图像。当`loadImage()`完成时，它会调用匿名回调函数并将加载的`Image`对象作为参数传递。回调执行`drawScene()`，图像被用于图案，图案被用作默认填充。图 2-26 展示了结果，一个由 Android logo 拼接而成的漂亮平铺背景。

![图 2-26](img/index-81_1.jpg)

**图 2-26\. 使用图案**

如果你愿意，你可以为游戏板的背景使用图案（我试过，但我最后还是更喜欢渐变）。确保图像的颜色不要太亮，否则会分散玩家对游戏本身的注意力。

### 上下文状态与变换

本章的最后一部分专门讨论二维上下文的变换。非正式地说，变换是应用于上下文中任何绘图函数的规则。例如，如果你调用`ctx.scale(2, 2)`，那么之后你绘制的所有内容都会显得大一倍。上下文支持三种类型的变换：平移（`translate`）、缩放（`scale`）和旋转（`rotate`）。

更正式地说，变换会改变上下文的坐标系；例如，在`scale(2, 2)`之后，坐标空间的每个“单位”对应两个像素。因此，坐标为`(10, 10, 5, 5)`的矩形实际上会被绘制成`(20, 20, 10, 10)`的样子。



