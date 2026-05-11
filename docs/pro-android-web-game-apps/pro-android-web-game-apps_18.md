# 第三章：创建首个游戏

在概述了为何需要改变的原因后，我们将探讨如何用 `HTML`、`CSS` 和 `JavaScript` 实现新的骨架。在本节中，我们将先构建一个包含所需基本功能的骨架。其中一项功能是支持优雅的屏幕旋转。不过有时你可能更偏好特定方向——竖屏或横屏——因此我们也会探讨如何管理“强制方向”。

#### 标准骨架

普通网页可能包含许多元素，如果空间不足以显示所有内容，浏览器会添加滚动条。面向游戏的移动端网页通常只有一个 `HTML` 元素：即占据全部可用屏幕空间的 `canvas` 元素。

移动端游戏应尽力防止浏览器显示滚动条。滚动条不仅占用宝贵的空间，还会“窃取”游戏中的移动事件。例如，在策略游戏中，滑动操作可视为“滚动世界地图”——这正是用户滑动屏幕时的预期行为。但如果页面无法适应单屏显示，同样的操作就必须被解读为“滚动浏览器页面”。这种情况下，游戏引擎无法使用该移动事件，因为它已被浏览器占用。

移动端网页游戏不应允许用户或浏览器进行缩放。如今，移动设备种类繁多，像素密度和分辨率各不相同。移动浏览器会尝试智能缩放网页，使其在特定设备上呈现最佳效果。这对普通页面很有意义：字体既不会过小也不会过大，页面在新旧设备、智能手机和平板电脑上看起来大致相同。但游戏引擎则不同。它们不需要浏览器“自作聪明”，因为游戏本身足够智能。游戏常由光栅图像构成，无论浏览器如何努力，这些图像缩放效果都不佳。因此，最好从一开始就关闭此功能。

**注：** 想要了解更多关于浏览器这种行为的原因和影响，可参阅精彩博文《像素不再是像素》（原文：`www.quirksmode.org/blog/archives/2010/04/a_pixel_is_not.html`）[`http://www.quirksmode.org/blog/archives/2010/04/a_pixel_is_not.html`](http://www.quirksmode.org/blog/archives/2010/04/a_pixel_is_not.html)。

该骨架应能优雅地处理方向变化。一旦用户旋转设备，`canvas` 将被调整大小以适应屏幕的新比例，游戏会重新渲染自身，并将新尺寸纳入考虑。

我们从第 2 章的 `HTML5` 骨架开始，添加几行代码来实现上述功能。清单 3-1 展示了初始骨架。

**清单 3-1.** *初始 HTML5 骨架*

```
<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="utf-8" />
<style>
</style>
<script>
function init() {
}
</script>
</head>
<body onload="init()">
</body>
</html>
```

首先，我们来限制缩放行为。在 `meta charset` 之后立即添加另一个 meta 标签，如清单 3-2 所示。

**清单 3-2.** *视口 Meta 标签：为网页设置缩放选项*

```
...
<meta charset="utf-8" />
<meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no, target-densitydpi=device-dpi"/>
<style>
...
```

这段代码设置了视口的参数；换句话说，它为浏览器提供了若干关于如何渲染页面的提示。我们指示浏览器将初始缩放和最大缩放比例设置为 `1.0`（即不缩放），忽略用户缩放页面的尝试（`user-scalable=no`），最后，提供真实的像素，使得 `1px` 名副其实——即设备屏幕的一个物理像素（`target-densitydpi=device-dpi`）。

然而，页面可能仍会出现滚动条。为了防止它们出现，请添加清单 3-3 中的样式声明。

**清单 3-3.** *页面 CSS：无滚动条、外边距、内边距或边框*

```
<style>
html, body {
overflow: hidden;
width: 100%;
height: 100%;
margin:0;
padding:0;
border: 0;
```



## 第 3 章：创建第一个游戏

- **93**

`overflow: hidden`指令让浏览器“切掉”超出视觉页面边界的所有内容，从而从不显示滚动条。其他行确保页面主体使用每一个可用的像素：宽度和高度各为`100%`，没有边距、边框或内边距。

Canvas 的缩放是最棘手的部分。不幸的是，我们不能用纯 CSS 来实现，因此必须编写几行 JavaScript。清单 3-4 中的代码使用了浏览器事件，我们将在第 5 章详细讨论这些事件。目前，你只需要相信它能工作就行。

**清单 3-4.** *监听浏览器事件，在页面缩放后调整 Canvas 大小*

```javascript
function init() {
    var canvas = initFullScreenCanvas("mainCanvas");
}

/**
 * Resizes the canvas element once the window is resized.
 * @param canvasId – string id of the canvas element
 */
function initFullScreenCanvas(canvasId) {
    var canvas = document.getElementById(canvasId);
    resizeCanvas(canvas);
    window.addEventListener("resize", function() {
        resizeCanvas(canvas);
    });
    return canvas;
}
```

清单 3-4 中的代码包含两个函数。第一个函数`init()`你已经熟悉了：浏览器在页面主体加载完毕后执行它。第二个函数`initFullScreenCanvas()`让 Canvas 占据所有可用的屏幕空间，即使窗口被缩放或设备更改了方向也是如此。它会“监听”`resize`事件，每次检测到该事件时，就调用`resizeCanvas()`。接下来，`resizeCanvas()`负责具体的缩放：它找出页面的新尺寸，并相应地更新 Canvas 的大小。这个函数的代码如清单 3-5 所示，同样相当简单。

**清单 3-5.** *resizeCanvas 函数，用于找出页面的新尺寸并将 Canvas 适配到该尺寸*

```javascript
/**
 * Does the actual resize
 */
function resizeCanvas(canvas) {
    canvas.width = document.width || document.body.clientWidth;
    canvas.height = document.height || document.body.clientHeight;
    // Notify the main game class that canvas is resized
}
```

当你给`canvas.width`或`canvas.height`赋值时，Canvas 会清除所有内容并变为空白，即使新值与之前的相同且实际上没有发生缩放也是如此。在这种情况下，游戏必须立即重新绘制场景。这就是最后一行注释的由来。一旦我们有了知道如何重新绘制场景的类，我们就通知它每次缩放事件，以便它有机会重绘棋盘。目前，我们将在这里保留注释，以提醒我们此函数尚未完成。

**注意：** 尺寸检测代码依赖于两个属性：`document.width`和`document.body.clientWidth`。为什么同时使用这两个？这种方法是为了跨浏览器兼容。在本书的过程中，我们将使用除默认 Android 浏览器之外的其他浏览器，因此最好从一开始就保持代码的跨浏览器版本。

骨架的最终版本如清单 3-6 所示。

**清单 3-6.** *为游戏开发需求修改的 HTML5 骨架*

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="utf-8" />
    <meta name="viewport"
          content="width=device-width, initial-scale=1.0, maximum-scale=1.0,
                   user-scalable=no, target-densitydpi=device-dpi"/>
    <style>
        html, body {
            overflow: hidden;
            width: 100%;
            height: 100%;
            margin:0;
            padding:0;
            border: 0;
        }
    </style>
    <script>
        function init() {
            var canvas = initFullScreenCanvas("mainCanvas");
        }

        function initFullScreenCanvas(canvasId) {
            var canvas = document.getElementById(canvasId);
            resizeCanvas(canvas);
            window.addEventListener("resize", function() {
                resizeCanvas(canvas);
            });
            return canvas;
        }

        function resizeCanvas(canvas) {
            canvas.width = document.width || document.body.clientWidth;
            canvas.height = document.height || document.body.clientHeight;
            // Notify the main game class that Canvas is resized
        }
    </script>
</head>
<body onload="init()">
    <canvas id="mainCanvas" width="100" height="100"></canvas>
</body>
</html>
```

- **95**



### 强制屏幕方向

清单 3-6 中的骨架代码并不会强制锁定屏幕方向，它只是在检测到窗口尺寸变化时重新绘制画布内容。众所周知，许多原生安卓游戏会根据游戏最佳体验，将屏幕固定在竖屏或横屏模式。但在浏览器环境中，强制锁定方向是不可行的。这种做法被认为过于侵入性，可能会损害用户体验。毫无疑问，游戏与展示新闻或天气预报的典型网页截然不同，因此对某类网站有害的做法，对其他类型网站或许正是良策。

网页游戏本质上是在浏览器内渲染的网页，用户期望它具备浏览器的常规行为：当设备旋转时，浏览器应随之调整方向。若要改变这一行为，你必须有非常充分的理由。但且慢，我刚刚提到无法锁定屏幕方向，那我们又如何改变这一行为呢？

安卓浏览器通过`'orientationchange'`事件通知页面设备方向已改变。当前方向值存储在`window.orientation`属性中。结合这两者，你可以构建一种机制，友好地提示用户：以另一种屏幕方向游玩本游戏效果更佳。例如，你可以创建一个对话框（最好不是警告框，而是模拟游戏 UI 风格的对话框），输出类似“提示：《四球大作战》最佳体验为竖屏模式”的建议性文本。记得添加“不再提示”复选框或类似功能，以便用户取消订阅该通知。

另一种可行的方案是模拟锁定方向的效果。借助上下文变换（context transformation），当用户旋转屏幕时，你可以旋转画布的坐标系统，使游戏元素始终保持正确方向。清单 3-7 中的代码展示了如何更新骨架代码，从而实现游戏方向的“锁定”效果。

## 清单 3-7. 锁定游戏方向

```
<script>
var canvas;
var ctx;

function init() {
    canvas = initFullScreenCanvas("mainCanvas");
    ctx = canvas.getContext("2d");
    repaint();
}

function initFullScreenCanvas(canvasId) {
    var canvas = document.getElementById(canvasId);
    resizeCanvas(canvas);
    window.addEventListener("resize", function() {
        resizeCanvas(canvas);
    });
    return canvas;
}

function resizeCanvas(canvas) {
    canvas.width = document.width || document.body.clientWidth;
    canvas.height = document.height || document.body.clientHeight;
    // 绘制一些内容以观察方向变化的效果
    repaint();
}

function repaint() {
    if (!ctx)
        return;
    // 清除背景
    ctx.fillStyle = "white";
    ctx.fillRect(0, 0, canvas.width, canvas.height);
    reorient();
    ctx.fillStyle = "darkgreen";
    ctx.fillRect(10, 10, 250, 30);
}

function reorient() {
    var angle = window.orientation;
    if (angle) {
        var rot = -Math.PI*(angle/180);
        ctx.translate(angle == -90 ? canvas.width : 0,
                      angle == 90 ? canvas.height : 0);
        ctx.rotate(rot);
    }
}
</script>
```

新增的代码以粗体显示。我们添加了一个`repaint()`函数，它会绘制一个形似字母 T 的绿色图形。在执行绘制代码之前，该函数会调用`reorient()`——当设备方向改变时，这个函数会修改画布上下文的坐标系统。效果是：无论用户如何旋转智能手机，屏幕看起来都像被“锁定”在竖屏模式。绿色的“T”会始终吸附在设备顶部。图 3-2 对比了浏览器的正常行为与“锁定”行为。

## 图 3-2. 竖屏锁定页面（上方）与正常页面（下方）的行为对比

图 3-2 左侧的图像展示了页面在竖屏方向下的初始状态。顶部两幅图像展示了锁定版本在旋转时的行为。如你所见，唯一随方向变化的元素是...




