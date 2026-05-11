# 第 3 段

最后一部分是处理用户输入——追踪点击和拖拽事件，将事件传播到正确的图层，并执行更改地形或添加对象等操作。我们将再次审视事件架构，构建一个不引人注目的通知系统，使引擎的各组件尽可能地相互独立。事件系统就位后，我们将为项目添加最后润色——实现核心游戏机制（更改地形、添加和移除对象、改变游戏状态）。

### 准备工作

我们需要使用前几章创建的几个类：`ImageManager`、`utils.js`、`EventEmitter`以及与用户输入相关的所有内容。请从之前的章节复制这些文件，或从本章素材中获取。接下来，将图片复制到`img`文件夹，并创建一个名为`Game.js`的空文件——我们很快会将游戏逻辑放入其中。完成这些步骤后，目录结构应如图 7-3 所示。

**图 7-3.** *项目初始结构*

### 基础代码

`index.html`文件的代码与前几章的初始代码相比变化不大。为完整起见，清单 7-1 展示了该代码。页面加载完成后，`Game`类获取控制权（缩放行为除外，它被保留在外部，因为`Game`不应知道底层画布缩放的原因和时机）。

**清单 7-1.** *`index.html`文件：游戏入口*

```html
<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="utf-8" />
<meta name="viewport"
content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no, target-densitydpi=device-dpi"/>
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
<script src="js/ImageManager.js"></script>
<script src="js/EventEmitter.js"></script>
<script src="js/input/InputHandlerBase.js"></script>
<script src="js/input/MouseInputHandler.js"></script>
<script src="js/input/TouchInputHandler.js"></script>
<script src="js/input/InputHandler.js"></script>
<script src="js/Game.js"></script>
<script>
var game;
function init() {
game = new Game(initFullScreenCanvas("mainCanvas"));
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
game && game.resize();
}
</script>
</head>
<body onload="init()">
<canvas id="mainCanvas" width="200" height="200"></canvas>
</body>
</html>
```

`Game.js`的初始版本包含一组桩代码和空函数。该类处理核心机制并将各组件编排为工作机制，但由于目前尚无组件，也无需编排任何内容，因此它仍然是空的。不过，`Game`类确实执行了一些有用的操作：加载图像和注册事件监听器。在本章中，我们只处理两种事件类型：拖拽和抬起，因此我们将立即注册监听器，但在它们能够执行实际工作之前，先保持为空。清单 7-2 显示了该类的初始代码。该代码使用了我们尚未了解的`bind()`函数。我稍后会进行解释。



### **清单 7-2** *游戏类：未来游戏的入口点*

```js
function Game(canvas) {
  this._canvas = canvas;
  this._ctx = this._canvas.getContext("2d");
  this._boundAnimate = this._animate.bind(this);
  this._imageManager = new ImageManager();
  this._imageManager.load({
    "terrain": "img/terrain.png",
    "arch-left": "img/arch-left.png",
    "arch-right": "img/arch-right.png",
    "house-1": "img/house-1.png",
    "house-2": "img/house-2.png",
    "ball": "img/ball.png",
    "ui": "img/ui.png"
  }, this._onImagesLoaded.bind(this));
}

_p = Game.prototype;
```

### 第 7 章：制作等距引擎

```js
/** 所有图片加载完成后调用 */
_p._onImagesLoaded = function() {
  this.resize();
  var inputHandler = new InputHandler(this._canvas);
  inputHandler.on("move", this._onMove.bind(this));
  inputHandler.on("up", this._onUp.bind(this));
  this._clearBg();
  this._animate();
};

/** 在世界中移动视口 */
_p.move = function(deltaX, deltaY) { };

/** 调整大小处理 */
_p.resize = function() { };

/** 事件处理 */
_p._onMove = function(e) {};
_p._onUp = function(e) {};

/** 更新对象 - 移动、动画等 */
_p._updateWorld = function() { };

/** 在上下文中渲染帧 */
_p._renderFrame = function() { };

/** 每帧调用，更新对象并在需要时重新渲染帧 */
_p._animate = function() {
  requestAnimationFrame(this._boundAnimate);
  this._updateWorld();
  this._renderFrame();
};

/** 用黑色填充背景 */
_p._clearBg = function() {
  this._ctx.fillStyle = "black";
  this._ctx.fillRect(0, 0, this._canvas.width, this._canvas.height);
};
```

一旦资源准备就绪，`Game` 就会初始化包含两部分的动画循环：`_updateWorld()`（用于逻辑更新、AI 等）和 `_renderFrame()`（在画布上输出结果）。我们已经知道如何处理动画和定时，因此在此项目中不会实现这一部分。如果你愿意，在所有其他部分完成后，添加动画角色是一个非常直接的任务。

### 第 7 章：制作等距引擎

**263**

#### 工具函数

等距引擎是一个复杂的项目。为了保持代码整洁，值得在开始时投入一点时间，将通用和可复用的功能提取到单独的类中，并探索一些能让代码更易读的有用技巧。我们将从探索用于解决事件监听器中 `this` 关键字丢失问题的 `bind()` 函数开始。

##### `bind()`、`apply()` 和 `call()` 函数

正如你在前几章所见，新函数体内使用的 `this` 关键字可能指向与外部 `this` 关键字不同的对象。以下代码展示了 `this` 的行为如何取决于其具体使用位置；该示例取自 `ImageManager` 类：

```js
_p._loadItem = function(queueItem, itemCounter, onDone, onProgress) {
  // this 关键字指向当前 ImageManager 实例
  var img = new Image();
  img.onload = function() {
    // this 关键字指向 img
  };
  img.onerror = function() {
    // this 关键字指向 img
  };
  // this 关键字再次指向 ImageManager
};
```

`this` 的值取决于函数的执行上下文。执行上下文（除其他事项外）决定了函数“看到”哪些变量，以及 `this` 背后是哪个对象。例如，考虑以下代码：

```js
var a = {
  name: "a",
  f: function() {
    console.log(this.name);
  }
}

a.f(); // 输出 "a"

var b = {
  name: "b",
  f: a.f
}
```

### 第 7 章：制作等距引擎

```js
b.f(); // 输出 "b"
```

`f()` 函数最初是为 `a` 对象定义的；它通过 `this` 引用输出对象的名称。但这并不意味着 `this` 关键字背后的对象始终是 `a`。在下一行中，我们将该函数赋值给 `b` 对象，然后通过 `b.f()` 调用它。在第二次调用期间，函数体内 `this` 的值是 `b`。尽管该函数最初是在另一个对象上定义的，但它输出了当前“所有者”——`b`——的名称。

`img.onload()` 回调的工作方式类似。



`assigned`给`Image`对象，一旦图片加载完成，它将会从该对象中被调用。因此，`this`的值将是`img`，而不是`ImageManager`实例。

但如果我们希望`this`始终指向某个特定对象，无论函数如何被调用，该怎么办？JavaScript 提供了一种方法：`bind()`函数正是为此而生。请看以下代码：

```
var a = {
  name: "a",
}

var b = {
  name: "b"
}

f = (function() {
  console.log(this.name);
}).bind(a);

a.f = b.f = f;

a.f(); // 输出 "a"
b.f(); // 输出 "a"! 因为我们使用了"绑定"函数
f(); // 即使不通过任何对象调用，仍然输出 "a"
```

`bind()`返回一个新函数，其行为与原函数完全相同，但`this`关键字被绑定到给定的对象。在上述代码中，函数`f`被绑定到了对象`a`，因此无论以何种方式调用——`a.f()`、`b.f()`，甚至不通过对象直接调用`f()`——它始终输出相同的值：`"a"`。

需要理解的是，`bind()`不会以任何方式修改原函数；它只是创建了函数的一个新版本并返回给调用者。请看以下代码：

```
var f = function() {
  console.log(this.name);
};

var boundF = f.bind(a);
```

只有名为`boundF()`的函数将`this`关键字绑定到了某个对象。原函数`f()`的行为保持不变，因为它在`bind()`调用之后并未发生改变。

**注意：** `this`关键字背后的机制在以下 MDN 文章中有详细描述：
[`developer.mozilla.org/en/JavaScript/Reference/Operators/this`](https://developer.mozilla.org/en/JavaScript/Reference/Operators/this)

`bind()`函数是在 ECMAScript 第 5 版中引入的，目前尚无法在所有移动浏览器中使用。为保证代码的跨浏览器兼容性，请将以下 shim（见清单 7-3）添加到您的`utils.js`文件中。

**清单 7-3.** *确保旧版浏览器也能使用`bind()`函数的 shim*

```
if (!Function.prototype.bind) {
  Function.prototype.bind = function (oThis) {
    if (typeof this !== "function") {
      // 最接近 ECMAScript 5
      // 内部 IsCallable 函数的实现
      throw new TypeError("Function.prototype.bind – " +
        "what is trying to be bound is not callable");
    }

    var fSlice = Array.prototype.slice,
        aArgs = fSlice.call(arguments, 1),
        fToBind = this,
        fNOP = function () {},
        fBound = function () {
          return fToBind.apply(this instanceof fNOP
            ? this
            : oThis || window,
            aArgs.concat(fSlice.call(arguments)));
        };

    fNOP.prototype = this.prototype;
    fBound.prototype = new fNOP();

    return fBound;
  };
}
```

**注意：** 上述 shim 代码来自 MDN 的以下文章：
[`developer.mozilla.org/en/JavaScript/Reference/Global_Objects/Function/bind`](https://developer.mozilla.org/en/JavaScript/Reference/Global_Objects/Function/bind)

现在应该很容易理解为什么我们在清单 7-2 中使用了下面这行代码：
`this._boundAnimate = this._animate.bind(this);`

`animate()`函数是作为`requestAnimationFrame()`的回调被调用的。直接使用它时，会在第一次调用后丢失`this`的值。使用`_boundAnimate()`代替`_animate()`可以确保`this`始终指向`Game`的实例。

关于`this`关键字和执行上下文的讨论，如果不涉及两个极其有用的函数——`apply()`和`call()`——就不算完整。这两个函数允许你用任意的`this`值执行任意函数，甚至无需将函数赋值给该对象。

```
var a = {
  name: "a",
}

var b = {
  name: "b"
}

f: function() {
  console.log(this.name);
}

f.call(a); // 执行 f，this 将指向对象 a
f.apply(b, []); // 执行 f，this 将指向对象 b
```

`call()`和`apply()`让你显式地提供`this`关键字所需的对象。两者唯一的区别在于接受函数参数的方式——`apply()`接受参数数组，而`call()`则将参数作为自身参数逐个接收：

```
fun.apply(newThis, [arg1, arg2, arg3]);
fun.call(newThis, arg1, arg2, arg3);
```



