# 第 13 章

使用触摸与手势事件

与当今许多便携设备一样，iPhone、iPad 和 iPod touch 将手指作为唯一的指点设备。苹果的设备甚至支持多个同时接触点，结合简单直观的图形界面，使其使用流畅、高效，并且总体令用户感到愉悦。正如我们在前面章节中所述，这在用户和设备之间建立了一种近乎亲密的联系。这种特殊关系有利于逼真的设计；例如，在构建扑克游戏应用时，你可以允许用户通过简单的手指轻扫，在桌上“扔”出扑克牌，增强了真实感。

从操作系统版本 2.0 开始，你可以使用 Mobile Safari 的多点触控 API，将用户与你的 Web 应用之间的交互提升到一个新的水平。在本章中，我们将解释如何实现此 API 中的功能来捕获触摸事件，如何解读绝大多数用户移动操作，并最终利用这些知识构建一个简单的手写识别 Web 应用。

### 如何处理事件

为了使用 JavaScript 捕获事件，开发者通常使用直接在元素上的 `onclick` 属性（在 HTML5 规范中已标准化）或 DOM 的 `addEventListener()` 方法，有时并未真正理解监听器应如何组织或捕获事件的含义。透彻理解这一点对于高效处理事件至关重要。

#### 处理程序的调用优先级

在不考虑事件捕获的情况下，处理程序之间没有实际的优先级；它们只是按照定义的顺序被调用。因此，HTML 标记中的事件属性通常比脚本更早被解释，事件发生时，通过属性引入的代码将首先被执行，如下例所示：

```html
<div onclick="console.log('attribute')">点击我</div>

<script>

var div = document.getElementsByTagName("div")[0];

div.addEventListener("click", function() { console.log("listener") }, false);

</script>
```

**--- 结果 ---**

```
> attribute
> listener
```

然而，如果使用脚本中的 `element.onclick` 定义了新的点击事件，`onclick` 属性将被覆盖，之前的处理程序将被移除，并创建一个新的处理程序：

```html
<div onclick="console.log('attribute')">点击我</div>

<script>

var div = document.getElementsByTagName("div")[0];

div.addEventListener("click", function() { console.log("listener") }, false);

div.onclick = function() { console.log("property") };

</script>
```

**--- 结果 ---**

```
> listener
> property
```

属性基本上只允许你一次定义一个处理程序。但这并不意味着你在定义新处理程序时无法保留之前由属性定义的处理程序。以下是实现此目的的一种方法：

```javascript
/* 首先，我们从 onclick 属性读取处理程序 */

var old = div.onclick;

/* 接着，我们将所有相关函数组合到一个全局处理程序中 */

div.onclick = function() {

old();

console.log("property");

};
```

**--- 结果 ---**

```
> listener
> attribute
> property
```



显然，这个方法并不灵活，而且它意味着你所有的处理函数只会在全局处理函数被调用时触发。在大多数情况下，应该优先使用`addEventListener()`和`removeEventListener()`方法。

## 第 13 章：使用触摸和手势事件

### 捕获阶段

`addEventListener()`方法的第三个参数指定是否仅为捕获阶段添加监听器，这意味着它不会在目标阶段和冒泡阶段被触发。捕获阶段是一种事件在分配给目标本身之前，可以被目标的父级处理的过程。

```
<div>0<div>1<div>2<div>3<div>4
<div>5</div>
</div></div></div></div></div>

<script>
var div = document.getElementsByTagName("div");
div[0].addEventListener("click", handleEvent, false);
div[1].addEventListener("click", handleEvent, false);
div[2].addEventListener("click", handleEvent, false);
div[3].addEventListener("click", handleEvent, false);
div[4].addEventListener("click", handleEvent, false);
div[5].addEventListener("click", handleEvent, false);

function handleEvent(event) {
    console.log("(" + event.eventPhase + ") " + this.firstChild.nodeValue);
}
</script>
```

在这段代码中，每个`<div>`都是下一个`<div>`的父级。因此，每次点击一个数字都会先到达直接目标（目标阶段），然后沿着 DOM 树向上移动到最顶层的祖先（冒泡阶段）。例如，点击 4 将会在控制台输出 4, 3, 2, 1, 0。当然，只有存在监听器时，祖先才会接收到事件。

为了精确识别事件目标，你可以依赖`Event`对象的两个属性。

- `target`属性保存了事件的真实目标；在前面的例子中点击 4，它会返回一个指向包含字符串“4”的元素的引用。
- `currentTarget`属性是一个引用，指向在冒泡阶段或捕获阶段接收事件的对象。

在像前面定义的这样一个处理函数中，`this`和`currentTarget`是相等的；相反，如果我们的处理函数被绑定到了一个对象，`this`将指向那个对象。此外，你可以使用`eventPhase`属性简单地确定与接收事件相关的阶段，该属性的可能取值在表 13-1 中列出。

#### 表 13-1. 不同事件阶段使用的常量

| 常量 | 描述 |
| :--- | :--- |
| `event.CAPTURING_PHASE` (1) | 当前事件阶段是捕获阶段。 |
| `event.AT_TARGET` (2) | 当前事件阶段是目标阶段。 |
| `event.BUBBLING_PHASE` (3) | 当前事件阶段是冒泡阶段。 |

现在，如果你将第一个监听器的第三个参数从`false`改为`true`，日志的顺序将会改变，因为事件将首先处于捕获阶段。控制台将显示 0, 4, 3, 2, 1。捕获处理函数总是被添加到调用栈的顶部。

### 控制事件传播

如果你想精确控制 Web 应用程序中的每一次点击——例如，我们在第 4 章中为保持应用程序全屏模式所做的那样——你需要使用捕获（将第三个参数设置为`true`）。例如，如果用户未登录，捕获可以让你阻止用户点击某些元素。`Event`对象的`stopPropagation()`方法允许你在任何选择的时刻阻止事件的传播。这样，尝试修改我们之前的处理函数如下：

```
div[0].addEventListener("click", handleEvent, true);
...
function handleEvent(event) {
    console.log("(" + event.eventPhase + ") " + this.firstChild.nodeValue);
    if (event.eventPhase == event.CAPTURING_PHASE) {
        event.stopPropagation();
    }
}
```

只有值 0 会被记录到控制台，这对应于第一个`<div>`的文本，因为任何子`<div>`的传播在捕获阶段就被直接停止了。这并不意味着使用相同`currentTarget`并在同一阶段定义的监听器不会被调用，但使用此方法意味着无法保证你的处理函数会被触发，因为该过程可能在任何时刻被更高层级的另一个处理程序中断。

### 阻止默认行为

通常，局部地阻止浏览器对某些事件的默认行为也是可取的，典型情况包括在点击链接或提交按钮时更改 URL，或使用`touchstart`事件阻止手势效果。这可以通过使用`preventDefault()`方法来实现，前提是该事件可以被取消。你可以通过读取`Event`对象的`cancelable`属性来检查这一点。请注意，此方法具有不停止传播的优点。

### 处理函数和对象方法

在某些情况下，你需要使用基于对象方法的处理函数，并且必须恢复调用上下文，如第 10 章所示，并将该方法封装在一个新的`Function`对象中。这当然意味着更多的内存使用。为了解决这个问题，从 DOM Level 2 开始，你可以使用`EventListener`接口的`handleEvent()`方法。要利用此方法的优势，你应该简单地使用`this`关键字或任何其他实现该函数的对象实例，来代替通常作为参数传递给`addEventListener()`方法的`Function`对象。

```
someObject.addEventListener("click", this, false);
```

通过这种方式，脚本引擎将寻找一个附加到`this`上的`handleEvent()`方法，并调用该方法，将`Event`派生的对象（`MouseEvent`，`TouchEvent`...）及其所有属性传递给它。当`handleEvent()`位于 window 级别或直接位于对象定义下时，可以使用关键字`this`。在其他情况下，你需要传递一个对象实例，如下所示：

```
<div>Click Me</div>
<script>
/* 一个示例对象 */
var Example = function(name) {
    this.name = name;
}

Example.prototype.handleEvent = function(event) {
    switch (event.type) {
        case "mousedown":
        case "mouseup":
            thisevent.type + "Handler";
    }
}

Example.prototype.mousedownHandler = function(event) {
    console.log("The 'mousedown' handler was called with " + this.name + ".");
}

Example.prototype.mouseupHandler = function(event) {
    console.log("The 'mouseup' handler was called with " + this.name + ".");
}

/* 创建 2 个对象并将它们用作事件处理函数 */
var div = document.getElementsByTagName("div")[0];
var obj1 = new Example("Object 1");
var obj2 = new Example("Object 2");
div.addEventListener("mousedown", obj1, false);
div.addEventListener("mouseup", obj2, false);
</script>
```

**--- 结果 ---**

```
> The 'mousedown' handler was called with Object 1.
> The 'mouseup' handler was called with Object 2.
```

在这段代码中，事件的处理被委托给了`Example`的两个实例。如你所见，执行上下文得以维持，每个事件都使用预期的`name`值触发一条日志。为了方便维护，子处理函数已被外部化为特定的方法，这些方法利用 JavaScript 通过哈希表提供对象属性的能力来调用。当然，如果你使用的代码很短，你可以直接使用`switch...case`而无需任何额外的方法。

### Mobile Safari 中的经典事件

虽然苹果便携设备不支持外部指点设备，但通常与鼠标相关的事件在 Mobile Safari 中是受支持的。然而，这用处有限，因为它们只是被模拟出来的，并且其反应可能与桌面环境中的预期有所不同。

#### 鼠标事件的行为



### 触摸事件与多点触控

尽管鼠标事件会被触发，但它们仅在用户释放手指压力后才被发送。此外，所有事件同时发送，这意味着，例如，典型的系列事件（如`mouseover`、`mousemove`、`mousedown`、`mouseup`和`click`）会在最后一个事件结束时按此精确顺序同时发送。`mouseout`事件仅当用户指向一个新的、追踪鼠标事件的元素且之前已记录过`mouseover`事件时才会发送。最后，`mousemove`事件将在手指压力结束时发送，这意味着你无法像使用真正的鼠标事件那样追踪移动。

这些事件都是默认`touchstart`事件行为的一部分。由于触摸事件（如我们所见）总是在常规鼠标事件之前发送，因此可以在`touchstart`事件上使用本章前面介绍的`preventDefault()`方法来阻止鼠标事件的发送。这将允许你为移动版 Safari 和经典设备浏览器定义差异化的行为。

#### 滚动信息

另一个有用的`scroll`事件。例如，它可以用于模拟 CSS 的`position: fixed`规则，因为这在 iPhone 上不被支持。在桌面浏览器上，该事件会随着滚动条位置的变化而重复发送，而在移动版 Safari 上，该事件仅在用户手指离开屏幕表面时发送。这一切都表明需要特定的事件来实现与用户的交互。自 iOS 2.0 版本起，已提供适当的触摸事件。

## 多点触控事件

苹果公司便携设备上触摸事件相对于鼠标事件的优越效率绝非必然，其他便携设备制造商更倾向于更精确地实现鼠标事件。然而，在 iPhone、iPod touch 和 iPad 上，对触摸事件（更准确地说是使用多个同时触点的多点触控事件）的青睐，为流畅、自然且高效的导航体验奠定了基础。

#### 新的交互过程

手指的作用类似于插入终端的多个鼠标；每个手指可以从一个点“跳”到另一个点，而鼠标只能沿着桌面移动，可能触发`mousemove`事件。此外，鼠标*通常*有几个不同的按钮，你可以通过`MouseEvent`对象的`button`属性来识别其点击。使用触摸事件，一次轻触自然只能有一种状态。

触摸事件基本上有三种状态。每个触摸事件以`touchstart`事件开始，以`touchend`事件结束，中间可能经过一个`touchmove`事件。与`mousemove`不同，`touchmove`在`touchstart`事件之后触发，并且无论使用多少根手指（尽管限制似乎是 11 个同时触点），都会考虑每一个新的接触点，直到发送`touchend`事件。

### 处理多点触控事件

触摸事件应该像其他任何事件一样，使用`addEventListener()`和`removeEventListener()`方法来监听。但务必小心，不要随意阻止浏览过程中必要的某些手势，例如平移。一个像下面这样简单的示例会阻止用户平移以发现视口外的页面部分，或显示隐藏的地址栏：

```javascript
document.addEventListener("touchstart", function(event) {
    event.preventDefault();
}, false);
```

iOS 有几个预定义的多点触控手势，例如捏合（缩放）或旋转。大多数手势可以使用触摸事件来覆盖。我们很快会详细讨论手势。

**警告**：作为一般规则，在修改浏览器和操作系统界面的默认行为时，务必极其小心。用户有其习惯和预期，当这些被改变或辜负时，会严重损害用户体验的质量。

### 无限触点



可以同时捕获的事件数量理论上不应成为开发过程中的限制，因为每根手指都可以舒适地处理一个事件。每个手指的位置可以通过新的`TouchEvent`对象及其`touches`属性获取。该属性提供一个`TouchList`，其中包含每个接触点的`Touch`对象，无论事件目标如何。每当检测到新的手指按压时，就会触发新的`touchstart`事件，列表也会随之更新，添加一个新的`Touch`对象。

还有另外两个使用`TouchList`对象的属性：`targetTouches`，它包含所有与触发事件的目标相关的`Touch`对象；以及`changedTouches`，应按如下示例使用：

```html
<!DOCTYPE html>
<html>
<head>
<title>Multi-Touch Demo</title>
<meta name="viewport" content="width=device-width; initial-scale=1.0; maximum-scale=1.0; user-scalable=no">
<style>
div {
  -webkit-user-select: none;
  position: absolute;
  width: 44px;
  height: 44px;
  text-align: center;
  background-color: black;
  -webkit-border-radius: 22px;
}
div span {
  display: block;
  font: bold 9px/15px sans-serif;
  margin-top: -15px;
}
</style>
<script>
document.addEventListener("touchstart", handleTouch, false);
document.addEventListener("touchmove", handleTouch, false);
document.addEventListener("touchend", handleTouch, false);
function handleTouch(event) {
  var touch = event.changedTouches;
  for (var n = 0; n < touch.length; n++) {
    var id = "i" + touch[n].identifier;
    var div = document.getElementById(id);
    if (!div) {
      div = document.createElement("div");
      div.innerHTML = "<span>" + id.substr(1) + "<span>"; div.id = id;
      document.body.appendChild(div);
    }
    div.style.left = (touch[n].pageX - 22) + "px";
    div.style.top = (touch[n].pageY - 22) + "px";
    div.style.opacity = (event.type == "touchend") ? 0.05 : 1.0;
  }
  event.preventDefault();
}
</script>
</head>
<body></body>
</html>
```

这段代码会为每个新的接触点（图 13-1）添加一个圆盘状的`<div>`，并跟随手指移动，实现拖放效果。为了确定新的接触点，我们使用了`changedTouches`属性，该属性包含了所有触发事件的、新增或修改的`Touch`对象。这同样与初始对象目标无关，允许你仅删除相关的元素。

**图 13-1.** iPad 屏幕上的每次手指触摸都由一个圆圈表示

> **警告：** 从 iOS 3.2 版本开始，`touchend`事件的触摸属性变得不可靠，`touches`和`targetTouches`将始终为空。这意味着只有`changedTarget`属性能够让你适当地处理触摸释放，但它会包含所有当前的触摸点，使其使用变得困难，甚至不可能。如果你需要使用此事件，请在 iPad 上彻底测试，以免应用上线时出现意外问题。

`Touch`对象有一个`identifier`属性，为每个实例保存一个唯一标识符。由于触摸发生的顺序不固定，且用户可能按特定顺序触摸屏幕却无序地移开手指，我们使用该属性来识别之前创建的`<div>`。因为系统可能会回收和重用标识符，我们不会删除之前的圆盘；相反，当标识符被回收时，我们通过改变其透明度来观察其移动。

**表 13-2.** `Touch`对象的属性

| 属性 | 描述 |
| :--- | :--- |
| `touch.pageX` 和 `touch.pageY` | 表示触摸事件相对于完整页面的位置。 |
| `touch.clientX` 和 `touch.clientY` | 应表示事件相对于客户端区域的位置，但返回与`pageX`和`pageY`相同的值。 |
| `touch.screenX` 和 `touch.screenY` | 应表示事件相对于屏幕的位置，但返回与`pageX`和`pageY`相同的值。但会根据视口的缩放比例进行调整。 |
| `touch.identifier` | `Touch`实例的唯一标识符。 |



`*.target`

该触摸事件的事件目标。

表 13-2 描述了`Touch`对象可用的所有属性。如你所见，尽管该对象包含三个不同属性以根据需求精确定位事件，但它们返回的值均相同。另请注意，虽然`TouchEvent`对象包含`pageX`和`pageY`属性，但这两个属性始终返回`0`，因此你必须始终依赖`Touch`对象本身的属性。

### 被取消的触摸事件

操作系统可随时终止触摸序列，此时会触发`touchcancel`事件。例如，当操作系统启动内部事件（如长按触发复制/粘贴操作并选中用户指向的元素）时就会发生这种情况。使用此事件可让你正确终止代码执行，因为任何当前或待处理的操作都不会继续。

### 基于触摸与变换构建的页面视图

在经典网页中，通常将事件与标记变化（如下拉菜单显示）关联。使用移动版 Safari 时，你可以在元素移动时利用流畅的 CSS 过渡效果。在以下示例中，我们将使用多点触摸事件和过渡实现拖放功能，以达成类似 iPhone 相册应用的行为。在 Cocoa Touch 中，这可通过`UIPageView`对象实现。

#### 我们将要做什么

相册中通常包含多张图片，但无需为每张图片创建占位，因为一次只聚焦一张图片。当用户向左或向右移动图片时，会露出上一张或下一张图片的部分内容。这意味着我们只需要三个显示图片的区域，如图 13-2 所示。

**图 13-2.** 页面视图模板

每次向右移动时，左侧的图片节点将移至聚焦区域，右侧区域将移至聚焦区域左侧（向左移动则相反）。每次移动后，隐藏区域的占位符需更新为显示对应的新图片。

#### 容器

我们希望动态创建这三个必要区域，以便更轻松地处理页面视图。我们只需定义一个带尺寸的容器（使用`.pageview-wrapper`类）。以下是设置这些区域所需的样式：

```css
.pageview-wrapper {
position: relative;
overflow: hidden;
}
.pageview-wrapper div {
position: absolute;
top: 0;
left: 0;
width: 100%;
height: 100%;
}
.pageview-group div {
border: solid 5px black;
-webkit-box-sizing: border-box;
}
.pageview-group div:first-child { left: -100%; }
.pageview-group div:last-child { left: +100%; }
```

我们的三个区域采用绝对定位以便于布局，并将它们分组到同一个容器（`.pageview-group`）中，这样它们无需独立移动，所需代码也更少。为了让区域即使未显示图片也可见，我们为区域添加了边框。接下来，只需为容器设置尺寸：

```css
html, body {
margin: 0;
height: 100%;
}
.box {
width: 100%;
height: 100%;
background-color: lightgrey;
}
```

在本示例中，我们为容器设置了相对宽度，使其占据测试设备的所有可用空间。当然，你可以根据需要设置任意尺寸。

HTML 标记中的容器定义非常简单：

```html
<div class="box pageview-wrapper"></div>
```

目前，这只会显示一个灰色矩形。组和区域将在我们即将创建的`PageView`对象初始化时添加。

#### 引入元素与交互


```javascript
var PageView = function(target) {

  this.target = target;

  this.state = this.WAITING;

  /* 初始化阶段 */

  this.createElements();

  this.registerEvents();

}

/* PageView 正在等待用户操作 */

PageView.prototype.__defineGetter__("WAITING", function() { return 0 });

/* 用户指向了 PageView 容器 */

PageView.prototype.__defineGetter__("ATTACHED", function() { return 1 });

/* 用户释放容器，PageView 正在移动到新位置 */

PageView.prototype.__defineGetter__("DETACHING", function() { return 2 });
```

这里创建的 `PageView` 对象会添加必要的元素，以便在我们的容器中实现分页功能，并处理与图片区域移动相关的用户事件。

容器作为参数传递给构造函数，并存储在 `target` 属性中。然后，通过 getter 中定义的常量将页面视图状态设置为 `WAITING`。随后构造函数调用初始化方法。

```javascript
PageView.prototype.createElements = function() {

  /* 创建页面组 */

  this.group = document.createElement("div");

  this.group.className = "pageview-group";

  this.target.appendChild(this.group);

  /* 添加 3 个页面 */

  for (var n = 0; n < 3; n++) {

    var div = document.createElement("div");

    this.group.appendChild(div);

  }

}

PageView.prototype.registerEvents = function() {

  this.target.addEventListener("touchstart", this, false);

  this.target.addEventListener("touchmove", this, false);

  this.target.addEventListener("touchend", this, false);

}
```

`createElements()` 方法创建页面组，当用户想要改变焦点图片时，该页面组会被移动。三个页面被添加到这个容器中，并且触摸事件监听器被附加到主容器上。因为我们传递了 `this` 作为处理器，所以需要定义一个 `handleEvent()` 方法，如前所述。

```javascript
PageView.prototype.handleEvent = function(event) {

  switch (event.type) {

    case "touchstart":

    case "touchmove":

    case "touchend":

      var handler = event.type + "Handler";

      thishandler;

  }

}
```

然后，每个事件被定向到相应的函数。

```javascript
PageView.prototype.touchstartHandler = function(event) {

  if (this.state == this.DETACHING) {

    return;

  }

  this.state = this.ATTACHED;

  this.origin = event.touches[0].pageX;

  event.preventDefault();

}
```

当用户触摸 `PageView` 容器时，状态变为 `ATTACHED`，表示对象应该捕获用户操作。手指移动的起点被记录下来，`origin` 属性将让我们在 `touchmove` 事件期间计算移动距离。

为了阻止任何不相关行为，我们还取消了浏览器的任何默认操作。

```javascript
PageView.prototype.touchmoveHandler = function(event) {

  if (this.state != this.ATTACHED) {

    return;

  }

  var distance = event.touches[0].pageX - this.origin;

  this.group.style.webkitTransform = "translate3d(" + distance + "px, 0, 0)";

}
```

当用户沿着屏幕移动手指时，我们调整页面组的位置以使其同步移动。如第 9 章所述，`translateX()` 方法目前因缺乏流畅性而不可靠。我们依赖于没有此问题的 `translate3d()` 方法。

当用户释放压力时，我们需要激活新聚焦的图片。

因为我们不希望页面视图从一个区域突然跳到另一个区域，所以我们使用 CSS 过渡。这样，区域就能平滑地移动到它们的新位置。使用过渡要求将整个控制（时间、缓动函数）转移到样式表中，这使得过渡比在 JavaScript 中硬编码的值更容易维护。

```css
.pageview-group {

  -webkit-transition: -webkit-transform 0.35s ease-out;

}
```

为了防止在 `touchmove` 事件期间发生过渡，有必要修改 `createElements()` 方法，将时间初始化为 0，这将阻止过渡的发生。

```javascript
PageView.prototype.createElements = function() {

  /* 创建页面组 */

  this.group = document.createElement("div");

  this.group.className = "pageview-group";

  this.group.style.webkitTransitionDuration = 0;

  this.target.appendChild(this.group);

  ...

}
```

此外，如果没有在全局范围内对绝对定位元素进行光栅化处理，动画期间可能会出现闪烁。从一个状态到另一个状态的转换偶尔会导致渲染近似，特别是对于大型元素。这种差异在 iPad 上尤为明显。这可以通过简单地将以下 CSS 属性添加到要动画化的元素来解决。

> **注意：** 当处理非常大的元素时，如果不需要让元素的背面可见，将 `backface-visibility` 属性设置为 `hidden` 将提高性能。

```css
.pageview-wrapper div {

  -webkit-transform: translate3d(0, 0, 0);

  position: absolute;

  ...

}
```

我们现在可以定义 `touchend` 事件的处理器：

```javascript
PageView.prototype.touchendHandler = function(event) {

  if (this.state == this.DETACHING) {

    return;

  }

  var matrix = new WebKitCSSMatrix(this.group.style.webkitTransform);

  if (matrix.e == 0) {

    this.state = this.WAITING;

  } else {

    var jump = this.target.offsetWidth;

    matrix.e = (matrix.e > 0) ? +jump : -jump;

    this.group.addEventListener("webkitTransitionEnd", this, false);

    this.group.style.webkitTransitionDuration = "";

    this.group.style.webkitTransform = matrix;

    this.state = this.DETACHING;

  }

}
```

`touchendHandler()` 方法从当前矩阵收集数据，以确定是否发生了移动。如果没有发生移动，对象简单地返回到 `WAITING` 状态；否则，该方法确定移动的方向并修改矩阵，以便在正确的方向上应用一个等同于容器宽度的距离的平移。会恢复在样式表中定义的平移持续时间，以便动画正确执行，并将对象的状态设置为 `DETACHING`。这通知其他方法正在运行动画，因此不应监听任何事件。

为了终止移动，为 `transitionend` 事件添加了一个监听器。我们需要修改 `handleEvent()` 方法来考虑这一点：

```javascript
PageView.prototype.handleEvent = function(event) {

  switch (event.type) {

    case "webkitTransitionEnd":

      this.moveNodes();

      break;

    case "touchstart":

      ...

  }

}
```

一旦动画完成，就会调用 `moveNodes()` 方法。这个方法也会读取矩阵以确定方向，然后适当地移动节点。

最后，删除监听器，并将组重置为其初始状态。对象状态返回到 `WAITING`，等待用户进行新的操作。

```javascript
PageView.prototype.moveNodes = function() {

  var matrix = new WebKitCSSMatrix(this.group.style.webkitTransform);

  var first = this.group.firstChild;

  if (matrix.e < 0) {

    this.group.appendChild(first);

  } else {

    var last = this.group.lastChild;

    this.group.insertBefore(last, first);

  }

  this.group.removeEventListener("webkitTransitionEnd", this, false);

  this.group.style.webkitTransitionDuration = 0;

  this.group.style.webkitTransform = "";

  this.state = this.WAITING;

}
```

剩下的就是将对象与主容器关联起来，这可以像下面这样简单地完成：

```javascript
var wrap = document.querySelector(".pageview-wrapper");

var view = new PageView(wrap);
```

如您所见，这段代码非常简短，并且使得定义过渡和尺寸非常灵活，因为所有内容都由样式表处理。如果您现在运行这段代码，您已经可以使区域从右向左以及从左向右移动。当然，这还不太令人满意，因为目前定义的区域中没有显示任何内容。

#### 创建自定义事件


为了处理不同区域中图像的渲染，我们将创建新的事件。第一个事件是`PageChanged`，它将初始化页面视图图像。另外两个事件是`PageMovedLeft`和`PageMovedRight`，当图像向左或向右移动时，将发送这两个事件。首先通过创建一个`sendEvent()`方法来完成此操作，如下所示：

```
PageView.prototype.sendEvent = function(dir) {
    /* Create a new Event instance */
    var type, event = document.createEvent("Event");

    /* Prepare the event type */
    if (dir > 0) {
        type = "PageMovedRight";
    } else if (dir < 0) {
        type = "PageMovedLeft";
    } else {
        type = "PageChanged";
    }

    /* Initialize the new event */
    var vendor = "book";
    event.initEvent(vendor + type, false, false);

    /* Add a 'pages' property to the Event object */
    var nodes = this.group.childNodes;
    event.pages = {
        left: nodes[0],
        view: nodes[1],
        right: nodes[2]
    };

    /* Dispatch the event to the target */
    this.target.dispatchEvent(event);
}
```

`DOM DocumentEvent`接口的`createEvent()`方法返回作为参数传递的对象的实例。在这里，我们通过传递字符串`Event`来创建一个基本事件。我们也可以传递`MouseEvent`、`TouchEvent`等来创建这些事件的实例。

每个实现`Event`接口的对象都有自己的初始化方法。对于`Event`对象，该方法是`initEvent()`，它只接受三个参数。例如，`MouseEvent`的初始化方法参数明显更多。`initEvent()`方法具有以下签名：

```
event.initEvent( *eventType, canBubble, cancelable*);
```

我们的事件不能被取消，因为它是在标记构建完成并且组已移动之后发送的：默认行为无法取消，因为它已经被执行了。前缀`book`被添加到我们的事件类型中，以确保与原生事件的向前兼容性。当然，您可以使用来自 DOM 的事件名称，例如模拟一次点击。

一旦`Event`对象被初始化，我们向它添加一个新的`pages`属性，这将允许处理程序随时访问和修改区域。然后事件被发送到它的目标，即主容器。

现在，我们可以调用新的`sendEvent()`函数。我们只需修改构造函数，使客户端代码通过`PageChanged`事件初始化区域的内容。

```
var PageView = function(target) {
    ...
    this.sendEvent();
}
```

接下来，我们需要在过渡完成后通知客户端代码。因此，我们修改`moveNodes()`方法，在其末尾添加一个函数调用。

```
PageView.prototype.moveNodes = function() {
    ...
    this.sendEvent(matrix.e);
}
```

`PageView`对象现在已经准备好向客户端代码发送其自定义事件。

#### 处理自定义事件

在不进行过于冗长的开发的情况下，我们只需更改区域的背景颜色。如果您希望在具有适当图像的应用程序中使用此功能，您可以使用第 6 章中介绍的背景定位和大小调整规则，以便在保持相关比例的同时充分利用所有可用空间。

捕获自定义事件的方式与捕获原生事件完全相同。我们将使用新的 CSS `hsl()`函数，以便在颜色之间无缝循环，而无需处理颜色变量的值。

第一个要捕获的事件是`PageChanged`，它将指示何时初始化三个区域的颜色。

```
var color = 0;

function makeColor(hue) {
    return "hsl(" + hue + ", 100%, 50%)";
}

wrap.addEventListener("bookPageChanged", function(e) {
    e.pages.left.style.backgroundColor = makeColor(color - 20);
    e.pages.view.style.backgroundColor = makeColor(color);
    e.pages.right.style.backgroundColor = makeColor(color + 20);
}, false);
```

接下来，我们希望随着用户将区域从一侧移动到另一侧，逐步改变颜色。

```
wrap.addEventListener("bookPageMovedLeft", function(e) {
    color += 20; // New active color
    e.pages.right.style.backgroundColor = makeColor(color + 20);
}, false);
```



