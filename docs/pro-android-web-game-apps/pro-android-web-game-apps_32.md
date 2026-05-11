# 事件处理与用户输入

点击事件的坐标或按钮的状态——具体取决于场景和事件类型。

**图 5-2.** 事件机制解析：发射器将名为“事件”的特殊对象发送给已注册的监听器，这样监听器就能获知重要事件的发生。

**注意：** 多年来，跨浏览器事件处理一直是令人头疼的问题。各浏览器采用完全不同的处理模型。过去，JavaScript 书籍通常需要专门用一两章来讲解如何通过已知的"hack 技巧"和变通方案，搭建通往完美跨浏览器事件处理的康庄大道。如今情况已大为改善。

Download from Wow! eBook <www.wowebook.com>

首先，市面上有许多高质量库已经实现了完善的事件处理机制。事实上，所有定位为"开发者必备工具集"的严肃库都具备此功能。

其次，浏览器厂商也终于开始统一标准化进程。

尽管如此，问题仍未完全解决。即便是本章创建的简单应用程序中，我们仍需实现一个小 hack 来支持移动端 Firefox（第 9 章我们将使用 Firefox 测试 WebGL）。

事件对象还包含多个用于控制事件行为的方法（稍后详述）。

### 注册事件：DOM 属性

现在让我们编写代码，看看事件处理的实际运作。清单 5-1 展示了一种事件处理方式。

**清单 5-1.** 事件处理：传统方式

```
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
<script src="js/utils.js"></script>
<script>
function init() {
console.log("document is loaded");
}
</script>
</head>
<body onload="init()">
<canvas id="mainCanvas" width="20px" height="20px"></canvas>
</body>
</html>
```

等等！这不就是我们从第 2 章开始一直使用的代码吗？！没错！完全脱离事件处理来编写实用 JavaScript 程序几乎不可能——我不得不在正式介绍前就先使用事件，实属汗颜。

让我们回顾这段代码：我们在 HTML 文档的`body`标签中添加了`onload`参数，其值是一个 JavaScript 代码片段。虽然可以在此放置多个用分号分隔的函数调用，但我们只调用了`init()`函数。这种技术可以（但不应该）用于大多数 DOM 元素：找到事件名称，在其前面加上`on`，然后输入所需代码：

```javascript
onclick="alert('bad style')";
```

为什么说这是不好的编码风格？开发者总是力求结构化与分离。HTML 本质是标记语言，负责描述文档的语义和结构，不应承担其他职责：动态行为（应由 JavaScript 负责）或样式（应由 CSS 负责）。将事件处理代码直接嵌入 HTML 违反了这一原则，代码会很快变得混乱且难以维护。

从纯实践角度而言，这种方式的主要局限是无法添加多个事件处理器——DOM 树不允许存在两个同名属性。

### 注册事件：事件监听器

现在我们来学习更好的事件订阅方式，如清单 5-2 所示。

**清单 5-2.** 正确的事件处理方式

```javascript
var el = document.getElementById("mainCanvas");

function onCanvasTouchStart(e) {
console.log("touch");
}

el.addEventListener("touchstart", onCanvasTouchStart, false);
```

每个 DOM 元素都具有`addEventListener()`函数，其功能正如其名：将第二个参数传入的函数注册为该 DOM 元素的事件监听器。在清单 5-2 中，`el`代表 canvas 元素。



## 第 5 章：事件处理与用户输入

默认行为是浏览器对输入的正常响应；例如，点击 `<a>` 标签的默认行为是跳转到新页面。`Tab` 键的默认行为是将焦点移到下一个可聚焦元素。对于普通网页，这些行为正是用户所期望的；但对于网页游戏来说则不然。点击画布会高亮整个元素，比如给它加上边框并播放“点击”音效，且伴有明显恼人的延迟。这在用户玩“四球”游戏时绝对不是他们想要的。

浏览器有两个控制事件正常流程的函数：

- **`stopPropagation()`** 阻止事件向 DOM 层级结构的下一级传递
- **`preventDefault()`** 确保浏览器不执行默认行为，相当于直接忽略该事件

同时调用这两个函数的代码示例如下：

```javascript
a.addEventListener("click", function(e) {
  e.stopPropagation();
  e.preventDefault();
}, false);
```

如果某个变量是一个 `<a>` DOM 节点，那么浏览器将永远不会因 `preventDefault()` 而跳转该链接。同时，由于 `stopPropagation()`，`<a>` 的父节点将不会接收到发生在 `<a>` 上的点击事件。我们将在 API 中使用这种技术来限制那些仅针对主画布元素的事件的正常流程。

### 从事件中获取更多信息

这个作为参数传递给每个事件监听器的小小 `e` 中隐藏着许多有趣的信息。对于 `touchstart` 事件，`e` 包含一个名为 `targetTouches` 的数组。而 `targetTouches` 中的每个元素又包含 `pageX` 和 `pageY` 这两个参数，分别表示触摸点相对于页面左上角的坐标。

现在让我们运用这一知识，创建一个简单的应用：当用户点击画布时，在上面绘制一个弹孔（见清单 5-3）。这是一个简单但相当有用的练习；你可以立即看到事件处理是否正常工作，以及能从中获得什么：你手中的设备是否支持多点触控，如果支持，它最多能同时处理多少个触摸点？

## 清单 5-3. 检测 DOM 元素上的触摸事件

```html
<!DOCTYPE html>
<html lang="zh">
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
      var ctx = canvas.getContext("2d");
      canvas.addEventListener("touchstart", function(e) {
        for (var i = 0; i < e.targetTouches.length; i++) {
          drawBullet(
              e.targetTouches[i].pageX,
              e.targetTouches[i].pageY, ctx);
        }
        e.stopPropagation();
        e.preventDefault();
      }, false);
    }

    function drawBullet(x, y, ctx) {
      ctx.fillStyle = "darkred";
      ctx.strokeStyle = "black";
    }
  </script>
```

监听函数 `onCanvasTouchStart()` 将接收新的监听器。事件类型为 `touchstart`，事件监听器是 `onCanvasTouchStart()` —— 一个向控制台输出消息的函数。现在，每当在 `el` 上触发新的 `touchstart` 事件时，就会调用 `onCanvasTouchStart()`。

请注意，监听函数也有一个参数：它被称为 `e`。这是单个字母作为参数名完全没问题的情况之一。每个人都知道 `e` 代表异常或事件，而且 99% 的情况下，根据上下文就能明确其含义。

事件被注册在元素上的监听器处理之后，会被发送到其直接父节点。然后该事件会触发父节点上注册的相应类型的所有监听器。接着事件会继续向上传递到父节点的父节点，以此类推，直到到达 DOM 层次结构的顶部。这种行为被称为 **冒泡** —— 事件就像一杯水中的气泡，从底部移动到表面。但不同的是，事件不是穿过水层，而是穿过 DOM 层次结构的层级：从一层到另一层，直到到达顶部。然后浏览器会为这个事件执行“默认行为”。



```javascript
ctx.lineWidth = 4;

ctx.beginPath();

ctx.arc(x, y, 15, 0, 2*Math.PI, false);

ctx.fill();

ctx.stroke();

}

function initFullScreenCanvas(canvasId) { /* 已省略 */ }

function resizeCanvas(canvas) { /* 已省略 */ }

第五章：事件处理与用户输入

</script>

</head>

<body onload="init()">

<canvas id="mainCanvas" width="100" height="100"></canvas>

</body>

</html>
```

代码最重要的部分已被加粗。对于检测到的每一次触摸，我们都会在用户手指正下方绘制一个小圆。如果你用两根手指点击画布，却只看到一个圆，则说明该浏览器不支持多点触控。用于此简单触控测试的代码可在本章源代码文件 `01.touch_test.html` 中找到。

**注意：** `targetTouches` 看起来虽然像数组，但它实际上并非真正的数组。它没有 `forEach` 或 `splice` 等数组方法。这类对象有时被称为*伪数组*。

清单 5-3 中的代码仅适用于支持触控的设备。该示例无法在常规台式机上运行，因为台式机浏览器不会触发触摸事件，而是触发点击事件。此外，你只有一个鼠标指针，因此事件中不会包含 `targetTouches` 坐标数组，而只有一对 `(x, y)` 坐标。

### 处理触控与鼠标界面的差异

通常，我们希望代码不仅能运行在 Android 触控设备上，也能在常规台式机系统上运行。除了让更多用户能够体验你的游戏这一显而易见的原因外，程序员也习惯使用台式机浏览器进行大部分开发和调试工作。因此，正确处理两种平台上的输入至关重要。

让我们稍微更新一下代码，使其也能在 Google Chrome 中运行。首先，我们需要检测当前设备是常规设备还是支持触控的设备。技巧在于检查浏览器是否了解触摸事件（参见清单 5-4）。如果支持，那么你很可能正在使用触控设备。

**清单 5-4.** *检查设备类型：触控设备 vs. 常规浏览器*

```javascript
/**
* 检查我们正在使用的是触摸屏还是
* 常规的桌面浏览器。用于确定应该使用
* 哪种类型的事件：
* 鼠标事件还是触摸事件。
*/
function isTouchDevice() {
    return ('ontouchstart' in document.documentElement);
}
```

重要的代码行已被加粗。我们正在检查当前上下文中是否提供了 `ontouchstart` 监听器。如果可用，则 `document.documentElement` 的键列表中会包含 `ontouchstart`。这意味着该浏览器能够识别并支持触摸事件。将这段函数代码添加到项目中的 `utils.js` 文件里，因为我们之后会经常用到它。后续依赖于此函数的代码清单将假定该函数已经可用。

**注意：** 还有很多其他方法可以测试触摸屏支持情况。请访问 [`modernizr.github.com/Modernizr/touch.html`](http://modernizr.github.com/Modernizr/touch.html) 了解其他方法。该页面是 Modernizr 项目的一部分，这是一个轻量级的特性检测库。如果你需要检测更多浏览器特性（例如 WebSockets 或 XHR2 支持），可以考虑使用这个小巧的工具：[`modernizr.com`](http://modernizr.com)。

现在让我们修改代码，使得在 Google Chrome 中点击画布也能生效（参见清单 5-5）。完整示例（名为 `02.touch_and_mouse.html`）包含在本章的源代码中。

**清单 5-5.** *添加对桌面浏览器的支持*

```javascript
function init() {
    var canvas = initFullScreenCanvas("mainCanvas");
    var ctx = canvas.getContext("2d");
    if (isTouchDevice()) {
        canvas.addEventListener("touchstart", function(e) {
            for (var i = 0; i < e.targetTouches.length; i++) {
                drawBullet(e.targetTouches[i].pageX, e.targetTouches[i].pageY, ctx);
            }
            e.stopPropagation();
            e.preventDefault();
        }, false);
    } else {
        canvas.addEventListener("mousedown", function(e) {
            drawBullet(e.pageX, e.pageY, ctx);
            e.stopPropagation();
            e.preventDefault();
        }, false);
    }
}
```



## 第 5 章：事件处理与用户输入

鼠标事件每个事件只关联一对坐标。这就是为什么它们没有像 `targetTouches` 这样的特殊对象。坐标直接存储在事件对象中。

`pageX` 和 `pageY` 是事件相对于页面（而非画布）的坐标。只要画布位于页面左上角（大多数情况如此），这种方法就能正常工作。如果你添加了其他元素（如调试用 div、FPS 计数器或简单的边距），我们的演示就会出问题。我们需要额外做一些工作来计算画布在页面中的正确坐标。

清单 5-6 展示了实现这一功能的代码。

**清单 5-6.** *获取画布不在屏幕左上角时的事件坐标*

```
if (isTouchDevice()) {
    canvas.addEventListener("touchstart", function(e) {
        for (var i = 0; i < e.targetTouches.length; i++) {
            drawBullet(e.targetTouches[i].pageX - canvas.offsetLeft,
                        e.targetTouches[i].pageY - canvas.offsetTop, ctx);
        }
        e.stopPropagation();
        e.preventDefault();
    }, false);
} else {
    canvas.addEventListener("mousedown", function(e) {
        drawBullet(e.pageX - canvas.offsetLeft, e.pageY - canvas.offsetTop, ctx);
        e.stopPropagation();
        e.preventDefault();
    }, false);
}
```

新代码考虑了画布在页面上的位置：`offsetLeft` 和 `offsetTop` 是保存元素相对于页面位置的参数。现在演示在原生浏览器上可以正常运行……但在移动版 Firefox 上却不行。为什么？因为 Firefox 没有 `pageX` 和 `pageY` 属性。它有一对完全无用的 `clientX` 和 `clientY` 坐标，保存的是事件相对于当前浏览器视口的位置。如果页面发生滚动，演示就会再次失效。这时就需要使用特定浏览器的 hack 技巧。我们演示的最终版本如清单 5-7 所示。为了证明滚动不会破坏坐标，我必须从第 3 章使用的游戏页面骨架（见清单 3-3）切换回普通的 HTML5 骨架，后者不限制滚动，也不强制全屏画布（如清单 3-1 所示）。

**清单 5-7.** *输入演示的最终版本*

```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="utf-8" />
    <script src="js/utils.js"></script>
    <script>
        function init() {
            var canvas = document.getElementById("mainCanvas");
            var ctx = canvas.getContext("2d");
            // 填充画布以便查看其位置
            ctx.fillStyle = "lightgray";
            ctx.fillRect(0, 0, canvas.width, canvas.height);

            if (isTouchDevice()) {
                canvas.addEventListener("touchstart", function(e) {
                    for (var i = 0; i < e.targetTouches.length; i++) {
                        var coords = getInputCoordinates(e.targetTouches[i], canvas);
                        drawBullet(coords.x, coords.y, ctx);
                    }
                    e.stopPropagation();
                    e.preventDefault();
                }, false);
            } else {
                canvas.addEventListener("mousedown", function(e) {
                    var coords = getInputCoordinates(e, canvas);
                    drawBullet(coords.x, coords.y, ctx);
                    e.stopPropagation();
                    e.preventDefault();
                }, false);
            }
        }

        function getInputCoordinates(e, element) {
            return {
                x: (e.pageX || e.clientX + document.body.scrollLeft) - element.offsetLeft,
                y: (e.pageY || e.clientY + document.body.scrollTop) - element.offsetTop
            }
        }

        function drawBullet(x, y, ctx) {
            ctx.fillStyle = "darkred";
            ctx.strokeStyle = "black";
            ctx.lineWidth = 4;
            ctx.beginPath();
            ctx.arc(x, y, 15, 0, 2*Math.PI, false);
            ctx.fill();
            ctx.stroke();
        }
    </script>
</head>
<body onload="init()">
    <div style="height: 250px"></div>
    <canvas id="mainCanvas" width="400" height="400"></canvas>
</body>
</html>
```

如你所见，我们创建了一个专用函数来从事件对象中提取点击坐标。在桌面浏览器的情况下，它使用事件本身；而对于触摸界面，则使用 `targetTouches` 中的一个。此示例的代码包含在本章源码的 `03.click_coordinates.html` 文件中。



**注意：** 在第 3 章中，我提到游戏最典型的 HTML 页面结构是一个占据所有可用空间的`canvas`。在这种页面中出现滚动条的情况相当罕见，尤其是在移动设备上。不过，拥有一个也能处理这些情况的更“可靠”的代码是很好的，尤其是当只需要添加几行代码时。

上一个清单中的代码看起来并不美观：其中有大量样板代码，应该被隐藏在一个良好的 API 之下。除此之外，触摸事件和鼠标事件看起来非常相似，但它们的处理方式不同。例如，`touchstart`的工作原理与`mousedown`完全相同。从语义上讲，它们都表示“交互开始”。其他事件也是相关的：`mouseup`类似于`touchend`，因为它们都表示“交互结束”；`mousemove`类似于`touchmove`，它们都表示“拖拽”。如果我们能消除这些差异，并创建一个仅包含三个事件——`up`、`down`和`move`——的 API，那么事件处理代码将缩减到只有几行，例如：

```
input.on("down", function(e) {
  drawBullet(e.x, e.y, ctx);
});
```

为此，我们需要引入我们自己的事件，不同于浏览器默认提供的事件。

### 自定义事件

具有复杂内部逻辑的应用程序通常需要单独的机制来处理其自定义事件。想象一个典型的休闲游戏：角色在关卡中跳跃，躲避敌人并收集硬币以获得更多分数。这种系统可能使用的事件类型包括`coin_collected`、`level_completed`或`level_restarted`。这些事件在游戏引擎之外没有价值；它们与`mousemove`或`touchstart`事件无关。它们不必传播到其他 DOM 组件或以任何其他方式由浏览器处理。

**注意：** 有两个不同的概念共享“自定义事件”这个名称。第一个概念是`CustomEvent`，即 DOM Level 3 Events 规范中描述的接口（[www.w3.org/TR/DOM-Level-3-Events/#interface-CustomEvent](http://www.w3.org/TR/DOM-Level-3-Events/#interface-CustomEvent)）。它用于向 DOM 添加新类型的事件。这个术语的另一个含义是“完全独立于 DOM 的事件 API”。在本节中，我们讨论的是第二种事件类型，尽管我们的目标可以通过两种方法实现。从纯粹的架构角度来看，使用`CustomEvent`会稍微好一些，但为了解释这个重要的概念，我选择了第二种方法。

让我们看看清单 5-8 中的代码，它展示了我们如何在游戏中使用自定义事件（支持该功能的功能将在本章下一节中实现）。`levelManager`是一个加载关卡并跟踪玩家何时完成关卡的对象。它有两个自定义事件：`loaded`和`finished`。客户端 API 利用这些事件来执行特殊操作，例如在关卡中放置硬币和报告关卡进度。一个对象可以为单一事件类型拥有多个监听器。在清单 5-8 中，有两个`loaded`事件的监听器。一个放置硬币，另一个将一些调试输出打印到控制台。

**清单 5-8.** *自定义事件示例*

```
// "on" 是 "addEventListener" 的别名
// 它读起来更自然
levelManager.on("loaded", function(e) {
  var coins = getCoins(e.levelNumber);
  placeCoins(coins);
});

levelManager.on("loaded", function(e) {
  console.log("level " + e.levelName + " started");
});

levelManager.on("finished", function(e) {
  var numCoins = getCoins(e.coinsCollected);
  console.log("You have found " + numCoins + " coins");
});
```

显然，您的自定义事件不必代表用户输入或以某种方式与浏览器事件相关联。现在，让我们编写代码来支持这个功能。



#### 自定义事件监听器与发射器

构建自定义事件处理系统通常并非难事。在着手实现之前，你需要做出一些架构决策，但即便在最复杂的情况下，这项任务也并非极具挑战性。在本节中，我们将构建这样一个系统。让我们从`EventEmitter`开始，它是所有可触发事件实体的基类。

#### EventEmitter：基类

首先思考未来解决方案的架构。事件系统的设计决定了其在代码中的自然度、编写的简易程度以及额外功能的丰富性。以下是需要做出的几个决策：

- 实现事件冒泡还是使用“普通”订阅模型？
- 监听器应仅接收一个参数（事件对象），还是允许任意数量的参数？
- 是否需要实现内存泄漏保护？

让我们逐一审视这些要点。当你的游戏组件形成复杂层级结构（如浏览器的 DOM 树）时，第一点就变得有意义——底层事件需要“冒泡”到高层。第二点更多是个人偏好问题：你是希望允许使用`onNewCoin(x, y, coinType)`这样的事件监听器，还是`onNewCoin(newCoinEvent)`？多参数方法看起来更直观，因为它在监听器声明中直接指定了参数名称，但也有其缺点。如果监听器只关心最后一个参数，它仍然需要声明所有参数。此外，这还会增加额外的耦合度；发射事件的组件必须了解监听器所期望的参数数量和顺序。本文作者个人偏好使用单参数方法。

最后一点灵感来自 Node.js（我们将在第 10 章介绍这个出色的工具）。Node 的`EventEmitter`实现设有一个监听器阈值，即被视为“安全”的最大监听器数量。如果超过这个数量，就可能存在内存泄漏风险，此时`EventEmitter`会抛出异常。这种技术有时有助于检测编程错误；超过十个监听器往往意味着存在清理问题，从而导致内存泄漏。

清单 5-9 展示了如何实现一个不包含任何额外功能的基础`EventEmitter`类。如果你的项目需要更多功能，可以在此类基础上进行扩展。`EventEmitter`为每种事件类型维护一个监听器数组，并在触发`emit`时按顺序调用它们。该类允许你添加监听器、移除单个监听器、移除特定事件类型的所有监听器，或移除所有监听器。代码非常直观且注释详尽，相信你能轻松理解。

### 清单 5-9. EventEmitter——事件触发对象的基类

```javascript
/**
 * EventEmitter 类负责
 * 维护监听器注册表
 * 并在新事件发生时通知它们
 */
function EventEmitter() {
  this._listeners = {};
}

_p = EventEmitter.prototype;

/**
 * 将函数注册为监听器，用于接收
 * 指定类型事件的通知
 * @param type 事件类型
 * @param listener 要添加到监听器列表的函数
 */
_p.addListener = _p.on = function(type, listener) {
  if (typeof listener !== "function")
    throw "监听器必须是一个函数";
  if (!this._listeners[type]) {
    this._listeners[type] = [];
  }
  this._listeners[type].push(listener)
};

/**
 * 从指定事件类型中取消订阅给定的监听器
 * @param type 事件类型
 * @param listener 要从监听器列表中移除的函数
 */
_p.removeListener = function(type, listener) {
  if (typeof listener !== "function")
    throw "监听器必须是一个函数";
  if (!this._listeners[type])
    return;
  var position = this._listeners[type].indexOf(listener);
  if (position != -1)
    this._listeners[type].splice(position, 1);
};

/**
 * 移除指定事件类型注册的所有监听器
 */
```


`_p.removeAllListeners` = function(type) {
if (type) {
this._listeners[type] = [];
} else {
this._listeners = {};
}
};

/**
 * 通知所有订阅了给定事件类型的监听器，
 * 并将事件对象作为参数传递
 * @param type 事件类型
 * @param event 事件对象
 */
`_p.emit` = function(type, event) {
if (!(this._listeners[type] && this._listeners[type].length)) {
return;
}
for (var i = 0; i < this._listeners[type].length; i++) {
this._listeners[type][i].apply(this, event);
}
};

当 `EventEmitter` 准备就绪后，我们来查看清单 5-10 中基于它构建的示例发射器。

**第 5 章：事件处理与用户输入**

**191**

**清单 5-10.** *实现具体的 EventEmitter*

```
function LevelManager() {
  EventEmitter.call(this);
}
extend(LevelManager, EventEmitter);
_p = LevelManager.prototype;
_p.loadLevel = function(levelNumber) {
  // 执行关卡加载
  this.emit("loaded", {levelNumber: levelNumber, levelName: levelNames[levelNumber]});
};
```

如你所见，现在只需几行代码就可以在应用中触发自定义事件。

**注意：** 这是本书第一次使用继承。如果你对这段代码感到不太适应，请参考第 1 章的“面向对象编程”部分。`extend` 函数应该已经存在于你的 `utils.js` 文件中。如果没有，你可以在此章的其他源代码中找到 `utils.js` 的副本。

**事件与回调**

在第 4 章中，我们使用了一种略有不同的方法来解耦 API 的各个组件并发送通知。还记得使用 `ImageManager` 类加载图片的代码吗？

```
this._imageManager.load({
  "house-1": "img/house-1.png",
  "house-2": "img/house-2.png",
}, function() {
  /* 加载完成，继续执行 */
});
```

`ImageManager` 并未发送事件，而是接受了一个特殊参数：回调函数，用于通知外部 API 图片已加载完成。这两种方法之间有什么区别吗？它们都依赖于一个特定的函数（监听器或回调）来向外界通知重要事件，并且两种方法都服务于同一个目标：将发射器与监听器解耦。它们看起来非常相似。

实际上，可以轻松地将 `ImageManager` 重写为我们刚刚创建的 `EventEmitter` 的用法。那么客户端代码将如清单 5-11 所示。

**第 5 章：事件处理与用户输入**

**清单 5-11.** *ImageManager，使用事件代替回调的版本*

```
var onDone = function(event) {
  // 加载完成时执行某些操作；
};
imageManager.addEventListener("done", onDone)
imageManager.addImage("star", "img/star.png");
imageManager.loadImages();
```

这些方法之间的区别在于它们的使用场景。回调的目标是将单个异步函数调用的结果传递给程序。回调通常与方法的单次调用相关联，并且在调用执行完毕后不会被保留。事件监听器则更持久：它与对象相关联，而不是与函数调用相关联。

当然，你可以在单个类中混合使用这两种方法。例如，`ImageManager` 可以在有新图片添加到列表时触发事件。同时，它也可以为每张图片传递回调函数。这样，客户端既可以（借助回调）跟踪特定图片的加载情况，也可以（借助监听器）跟踪整体的加载进度，如清单 5-12 所示。

**清单 5-12.** *ImageManager 同时使用回调和事件的示例*

```
var onImageAdded = function(event) {
  console.log(event.imageName + " 已添加到列表中 ");
};
imageManager.addEventListener("imageAdded", onImageAdded);
// 需要跟踪这张图片，一旦它加载完成，我们将显示它
imageManager.addImage("main", "img/main.png", function(ok) {
  if (ok) {
    // 主屏幕已加载，显示它
  }
});
// 这里没有回调，这张图片没那么重要
imageManager.addImage("star", "img/star.png");
```



